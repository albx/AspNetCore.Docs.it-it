---
title: Autenticazione e autorizzazione in gRPC per ASP.NET Core
author: jamesnk
description: Informazioni su come usare l'autenticazione e l'autorizzazione in gRPC per ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/13/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: e8dd384ec43a66e56891925dcaa529085fa200c7
ms.sourcegitcommit: 6d26ab647ede4f8e57465e29b03be5cb130fc872
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2019
ms.locfileid: "71999851"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Autenticazione e autorizzazione in gRPC per ASP.NET Core

Di [James Newton-King](https://twitter.com/jamesnk)

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(procedura per il download)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Autenticare gli utenti che chiamano un servizio gRPC

gRPC può essere usato con [l'autenticazione ASP.NET Core](xref:security/authentication/identity) per associare un utente a ogni chiamata.

Di seguito è riportato un esempio di `Startup.Configure` che usa l'autenticazione di gRPC e ASP.NET Core:

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        routes.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> L'ordine in cui si registra il middleware di autenticazione ASP.NET Core è importante. Chiamare sempre `UseAuthentication` e `UseAuthorization` dopo `UseRouting` e prima di `UseEndpoints`.

Il meccanismo di autenticazione usato dall'app durante una chiamata deve essere configurato. La configurazione dell'autenticazione viene aggiunta in `Startup.ConfigureServices` e sarà diversa a seconda del meccanismo di autenticazione usato dall'app. Per esempi di come proteggere le app ASP.NET Core, vedere [esempi di autenticazione](xref:security/authentication/samples).

Al termine dell'installazione dell'autenticazione, l'utente può accedere ai metodi del servizio gRPC tramite il `ServerCallContext`.

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Autenticazione del token di porta

Il client può fornire un token di accesso per l'autenticazione. Il server convalida il token e lo usa per identificare l'utente.

Nel server bearer token autenticazione viene configurata con il [middleware di JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

Nel client .NET gRPC, il token può essere inviato con chiamate come intestazione:

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

La configurazione di `ChannelCredentials` su un canale rappresenta un metodo alternativo per inviare il token al servizio con chiamate gRPC. La credenziale viene eseguita ogni volta che viene effettuata una chiamata gRPC, evitando la necessità di scrivere codice in più posizioni per passare il token.

La credenziale nell'esempio seguente configura il canale per l'invio del token con ogni chiamata a gRPC:

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // Channels that aren't using TLS should use ChannelCredentials.Insecure instead.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>Autenticazione del certificato client

Un client può in alternativa fornire un certificato client per l'autenticazione. L' [autenticazione del certificato](https://tools.ietf.org/html/rfc5246#section-7.4.4) viene eseguita a livello di TLS, molto prima che venga mai ASP.NET Core. Quando la richiesta entra ASP.NET Core, il [pacchetto di autenticazione del certificato client](xref:security/authentication/certauth) consente di risolvere il certificato in un `ClaimsPrincipal`.

> [!NOTE]
> L'host deve essere configurato per accettare i certificati client. Vedere [configurare l'host per richiedere i certificati](xref:security/authentication/certauth#configure-your-host-to-require-certificates) per informazioni sull'accettazione dei certificati client in gheppio, IIS e Azure.

Nel client .NET gRPC, il certificato client viene aggiunto a `HttpClientHandler` che viene quindi usato per creare il client di gRPC:

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>Altri meccanismi di autenticazione

Molti ASP.NET Core meccanismi di autenticazione supportati funzionano con gRPC:

* Azure Active Directory
* Certificato client
* IdentityServer
* Token JWT
* OAuth 2,0
* OpenID Connect
* WS-Federation

Per ulteriori informazioni sulla configurazione dell'autenticazione nel server, vedere [ASP.NET Core Authentication](xref:security/authentication/identity).

La configurazione del client di gRPC per l'uso dell'autenticazione dipende dal meccanismo di autenticazione usato. Gli esempi di bearer token e del certificato client precedenti illustrano due modi per configurare il client gRPC per l'invio di metadati di autenticazione con chiamate gRPC:

* I client gRPC fortemente tipizzati utilizzano internamente `HttpClient`. L'autenticazione può essere configurata in [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler)oppure aggiungendo istanze [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) personalizzate al `HttpClient`.
* Ogni chiamata a gRPC ha un argomento facoltativo `CallOptions`. Le intestazioni personalizzate possono essere inviate tramite la raccolta di intestazioni dell'opzione.

> [!NOTE]
> Non è possibile usare l'autenticazione di Windows (NTLM/Kerberos/Negotiate) con gRPC. gRPC richiede HTTP/2 e HTTP/2 non supporta l'autenticazione di Windows.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autorizzare gli utenti ad accedere ai servizi e ai metodi del servizio

Per impostazione predefinita, tutti i metodi di un servizio possono essere chiamati da utenti non autenticati. Per richiedere l'autenticazione, applicare l'attributo [[autorizzate]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) al servizio:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

È possibile utilizzare gli argomenti e le proprietà del costruttore dell'attributo `[Authorize]` per limitare l'accesso solo agli utenti che corrispondono a [criteri di autorizzazione](xref:security/authorization/policies)specifici. Se, ad esempio, si dispone di un criterio di autorizzazione personalizzato denominato `MyAuthorizationPolicy`, assicurarsi che solo gli utenti che corrispondono a tale criterio possano accedere al servizio utilizzando il codice seguente:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

È possibile applicare anche l'attributo `[Authorize]` ai singoli metodi del servizio. Se l'utente corrente non corrisponde ai criteri applicati **sia** al metodo che alla classe, al chiamante viene restituito un errore:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>Risorse aggiuntive

* [Autenticazione del token di porta in ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Configurare l'autenticazione del certificato client in ASP.NET Core](xref:security/authentication/certauth)
