---
title: Autenticazione e autorizzazione in ASP.NET Core SignalR
author: bradygaster
description: Informazioni su come usare l'autenticazione e autorizzazione in ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/09/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: e8f9dc48be780fb91bdec6ea4d579f5e4f16197b
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65516945"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>Autenticazione e autorizzazione in ASP.NET Core SignalR

Da [Andrew Stanton-Nurse](https://twitter.com/anurse)

[Visualizzare o scaricare codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(come scaricare)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>Eseguire l'autenticazione di utenti che si connettono a un hub SignalR

Può essere utilizzato con SignalR [autenticazione ASP.NET Core](xref:security/authentication/identity) per associare un utente a ogni connessione. In un hub, i dati di autenticazione è possibile accedere dal [ `HubConnectionContext.User` ](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) proprietà. L'autenticazione consente l'hub chiamare i metodi in tutte le connessioni associate a un utente (vedere [gestire utenti e gruppi in SignalR](xref:signalr/groups) per altre informazioni). Più connessioni possono essere associate a un singolo utente.

Di seguito è riportato un esempio di `Startup.Configure` che utilizza l'autenticazione di SignalR e ASP.NET Core:

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();
    
    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> È importante l'ordine in cui si registra il middleware di autenticazione SignalR e ASP.NET Core. Chiamare sempre `UseAuthentication` prima `UseSignalR` SignalR presenta un utente di `HttpContext`.

### <a name="cookie-authentication"></a>Autenticazione tramite cookie

In un'app basata su browser, l'autenticazione tramite cookie consente le credenziali utente esistenti propagare automaticamente per le connessioni SignalR. Quando si usa il browser client, è necessaria alcuna configurazione aggiuntiva. Se l'utente è connesso all'App, la connessione SignalR eredita automaticamente questa autenticazione.

I cookie sono un modo di specifiche del browser per inviare i token di accesso, ma è possono inviare loro i client non basati su browser. Quando si usa la [Client .NET](xref:signalr/dotnet-client), il `Cookies` proprietà può essere configurata nel `.WithUrl` chiamata per fornire un cookie. Tuttavia, utilizzando l'autenticazione dei cookie del client .NET richiede l'app per fornire un'API per lo scambio di dati di autenticazione per un cookie.

### <a name="bearer-token-authentication"></a>Autenticazione del bearer token

Il client può fornire un token di accesso invece di utilizzare un cookie. Il server convalida il token e lo usa per identificare l'utente. Questa convalida viene eseguita solo quando viene stabilita la connessione. Nel corso della durata della connessione, il server non viene automaticamente riconvalidare per verificare la revoca dei token.

Nel server di autenticazione del bearer token viene configurato usando le [middleware del Bearer token JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

Nel client JavaScript, il token può essere specificato con il [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) opzione.

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

Nel client di .NET, è presente un simile [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) proprietà che può essere usata per configurare il token:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Prima viene chiamata la funzione di token di accesso è fornire **ogni** richiesta HTTP effettuata da SignalR. Se è necessario rinnovare il token per mantenere attiva la connessione (in quanto può scadere durante la connessione), all'interno di questa funzione e restituire il token aggiornato.

Nell'API web standard, vengono inviati i token di connessione in un'intestazione HTTP. SignalR è, tuttavia, non è possibile impostare queste intestazioni nel browser quando si usano alcuni trasporti. Quando si usa WebSocket e Server-Sent eventi, il token viene trasmesso come un parametro di stringa di query. Per supportare questa funzionalità nel server, è necessaria un'ulteriore configurazione:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a>Cookie e token di connessione 

Poiché i cookie sono specifici per i browser, inviarli rispetto agli altri tipi di client aggiunge complessità rispetto all'invio dei token di connessione. Per questo motivo, l'autenticazione dei cookie non è consigliato, a meno che l'app è sufficiente autenticare gli utenti dal browser client. Autenticazione del bearer token è l'approccio consigliato quando si usano client diversi da client browser.

### <a name="windows-authentication"></a>Autenticazione di Windows

Se [autenticazione di Windows](xref:security/authentication/windowsauth) è configurato nell'app, SignalR può usare tale identità per proteggere gli hub. Tuttavia, per inviare messaggi a singoli utenti, è necessario aggiungere un provider personalizzato di ID utente. Questo avviene perché il sistema di autenticazione di Windows non fornisce l'attestazione "Name Identifier" che usa SignalR per determinare il nome utente.

Aggiungere una nuova classe che implementa `IUserIdProvider` e recuperare una delle attestazioni da parte dell'utente da utilizzare come identificatore. Ad esempio, per usare l'attestazione "Name" (ovvero il nome utente di Windows nel formato `[Domain]\[Username]`), creare la classe seguente:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

Anziché `ClaimTypes.Name`, è possibile usare qualsiasi valore compreso il `User` (ad esempio l'identificatore SID di Windows, ecc.).

> [!NOTE]
> Il valore che scelto deve essere univoco tra tutti gli utenti nel sistema. In caso contrario, un messaggio destinato a un utente può arrivare passare a un altro utente.

Registrare il componente nel `Startup.ConfigureServices` (metodo).

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

Nel Client .NET, l'autenticazione di Windows deve essere abilitata impostando il [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) proprietà:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

L'autenticazione di Windows è supportata solo per il client del browser quando si utilizza Microsoft Internet Explorer o Microsoft Edge.

### <a name="use-claims-to-customize-identity-handling"></a>Usare le attestazioni per personalizzare la gestione delle identità

Un'app che autentica gli utenti può derivare gli ID utente di SignalR alle attestazioni utente. Per specificare la modalità di creazione ID utente SignalR, implementare `IUserIdProvider` e registrare l'implementazione.

Il codice di esempio viene illustrato come utilizzare le attestazioni per selezionare l'indirizzo di posta elettronica dell'utente come la proprietà identificativa. 

> [!NOTE]
> Il valore che scelto deve essere univoco tra tutti gli utenti nel sistema. In caso contrario, un messaggio destinato a un utente può arrivare passare a un altro utente.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

La registrazione dell'account aggiunge un'attestazione con tipo `ClaimsTypes.Email` al database di identità di ASP.NET.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Registrare il componente nel `Startup.ConfigureServices`.

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Autorizzare gli utenti per hub di accesso e i metodi dell'hub

Per impostazione predefinita, tutti i metodi in un hub possono essere chiamati da un utente non autenticato. Per richiedere l'autenticazione, si applicano i [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attributo all'hub:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

È possibile usare le proprietà della e argomenti del costruttore di `[Authorize]` attributo per limitare l'accesso solo agli utenti di corrispondenza specifiche [i criteri di autorizzazione](xref:security/authorization/policies). Ad esempio, se si dispone di un criterio di autorizzazione personalizzato chiamato `MyAuthorizationPolicy` è possibile garantire che solo gli utenti che Criteri di corrispondenza possono accedere all'hub usando il codice seguente:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub: Hub
{
}
```

Metodi dell'hub sul singolo possono avere il `[Authorize]` attributo viene applicato anche. Se l'utente corrente non corrisponde i criteri applicati al metodo, viene restituito un errore al chiamante:

```csharp
[Authorize]
public class ChatHub: Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

## <a name="additional-resources"></a>Risorse aggiuntive

* [Autenticazione basata su Token di connessione in ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
