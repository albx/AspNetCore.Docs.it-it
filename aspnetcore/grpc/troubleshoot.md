---
title: Risolvere i problemi di gRPC in .NET Core
author: jamesnk
description: Risolvere gli errori quando si usa gRPC in .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/troubleshoot
ms.openlocfilehash: 86517ec90c1df782da796e4dcf74cdc426565eb6
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773651"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Risolvere i problemi di gRPC in .NET Core

Di [James Newton-King](https://twitter.com/jamesnk)

Questo documento illustra i problemi comuni riscontrati durante lo sviluppo di app gRPC in .NET.

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>Mancata corrispondenza tra la configurazione SSL/TLS del client e del servizio

Il modello e gli esempi di gRPC usano [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) per proteggere i servizi gRPC per impostazione predefinita. i client gRPC devono usare una connessione sicura per chiamare correttamente i servizi gRPC protetti.

È possibile verificare che ASP.NET Core servizio gRPC stia usando TLS nei log scritti all'avvio dell'app. Il servizio sarà in ascolto su un endpoint HTTPS:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

Il client .NET Core deve usare `https` nell'indirizzo del server per effettuare chiamate con una connessione protetta:

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Tutte le implementazioni client di gRPC supportano TLS. i client gRPC di altri linguaggi richiedono in genere il canale `SslCredentials`configurato con. `SslCredentials`Specifica il certificato che verrà utilizzato dal client e deve essere utilizzato al posto di credenziali non sicure. Per esempi di configurazione delle diverse implementazioni client di gRPC per l'uso di TLS, vedere [autenticazione gRPC](https://www.grpc.io/docs/guides/auth/).

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Chiamare un servizio gRPC con un certificato non attendibile/non valido

Il client gRPC .NET richiede che il servizio disponga di un certificato attendibile. Quando si chiama un servizio gRPC senza un certificato attendibile, viene restituito il messaggio di errore seguente:

> Eccezione non gestita. System .NET. http. HttpRequestexception: Non è stato possibile stabilire la connessione SSL. vedere l'eccezione interna.
> ---> System. Security. Authentication. AuthenticationException: Il certificato remoto non è valido in base alla procedura di convalida.

Questo errore può essere visualizzato se si sta testando l'app in locale e il certificato di sviluppo HTTPS ASP.NET Core non è attendibile. Per istruzioni su come risolvere questo problema, vedere [Considerare attendibile il certificato di sviluppo di ASP.NET Core HTTPS in Windows e macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

Se si chiama un servizio gRPC in un altro computer e non è possibile considerare attendibile il certificato, il client gRPC può essere configurato in modo da ignorare il certificato non valido. Il codice seguente usa [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) per consentire le chiamate senza un certificato attendibile:

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = (message, cert, chain, errors) => true;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> I certificati non attendibili devono essere usati solo durante lo sviluppo di app. Le app di produzione devono sempre usare certificati validi.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Chiamare i servizi gRPC non sicuri con il client .NET Core

È necessaria una configurazione aggiuntiva per chiamare i servizi gRPC non protetti con il client .NET Core. Il client gRPC deve impostare l' `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` opzione su `true` e usare `http` nell'indirizzo del server:

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>Non è possibile avviare ASP.NET Core app gRPC in macOS

Gheppio non supporta HTTP/2 con TLS in macOS e versioni precedenti di Windows, ad esempio Windows 7. Per impostazione predefinita, il ASP.NET Core modello e gli esempi di gRPC usano TLS. Quando si tenta di avviare il server gRPC, viene visualizzato il messaggio di errore seguente:

> Impossibile eseguire il binding https://localhost:5001 a nell'interfaccia loopback IPv4: ' HTTP/2 su TLS non è supportato in macOS perché manca il supporto per ALPN .'.

Per risolvere questo problema, configurare gheppio e il client gRPC per l'uso di HTTP/2 *senza* TLS. Questa operazione deve essere eseguita solo durante lo sviluppo. Se non si usa TLS, i messaggi gRPC vengono inviati senza crittografia.

Gheppio deve configurare un endpoint HTTP/2 senza TLS in *Program.cs*:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

Quando un endpoint HTTP/2 viene configurato senza TLS, l'endpoint [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) deve essere impostato su `HttpProtocols.Http2`. `HttpProtocols.Http1AndHttp2`non può essere usato perché TLS è necessario per negoziare HTTP/2. Senza TLS, tutte le connessioni all'endpoint vengono predefinite a HTTP/1.1 e le chiamate a gRPC hanno esito negativo.

Il client di gRPC deve essere configurato anche per non usare TLS. Per altre informazioni, vedere [chiamare i servizi gRPC non sicuri con il client .NET Core](#call-insecure-grpc-services-with-net-core-client).

> [!WARNING]
> HTTP/2 senza TLS deve essere usato solo durante lo sviluppo di app. Le app di produzione devono sempre usare la sicurezza del trasporto. Per ulteriori informazioni, vedere [considerazioni sulla sicurezza in gRPC per ASP.NET Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>gli C# asset gRPC non sono codice generato da  *\*file. proto*

per la generazione di codice gRPC di client concreti e classi di base del servizio è necessario fare riferimento a file e strumenti protobuf da un progetto. È necessario includere:

* file con *estensione proto* che si desidera utilizzare nel `<Protobuf>` gruppo di elementi. Il progetto deve fare riferimento ai [file *. proto* importati](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) .
* Riferimento al pacchetto per il pacchetto di [strumenti GRPC gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).

Per ulteriori informazioni sulla generazione di C# asset gRPC, <xref:grpc/basics>vedere.

Per impostazione predefinita, `<Protobuf>` un riferimento genera un client concreto e una classe di base del servizio. L' `GrpcServices` attributo dell'elemento di riferimento può essere usato per C# limitare la generazione di asset. Le `GrpcServices` opzioni valide sono:

* `Both`(impostazione predefinita quando non è presente)
* `Server`
* `Client`
* `None`

Un ASP.NET Core app Web che ospita i servizi gRPC richiede solo la classe di base del servizio generata:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Un'app client gRPC che effettua chiamate gRPC richiede solo il client concreto generato:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
