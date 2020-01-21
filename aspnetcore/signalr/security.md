---
title: Considerazioni sulla sicurezza in ASP.NET Core SignalR
author: bradygaster
description: Informazioni su come usare l'autenticazione e l'autorizzazione in ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: 4b27d9abb36938ed8161ff0d3535204e3fa68765
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294709"
---
# <a name="security-considerations-in-aspnet-core-opno-locsignalr"></a>Considerazioni sulla sicurezza in ASP.NET Core SignalR

Di [Andrew Stanton-Nurse](https://twitter.com/anurse)

Questo articolo fornisce informazioni sulla sicurezza SignalR.

## <a name="cross-origin-resource-sharing"></a>Condivisione di risorse tra le origini

È possibile usare la [condivisione di risorse tra le origini (CORS)](https://www.w3.org/TR/cors/) per consentire le connessioni SignalR tra le origini nel browser. Se il codice JavaScript è ospitato in un dominio diverso dall'app SignalR, è necessario abilitare il [middleware CORS](xref:security/cors) per consentire a JavaScript di connettersi all'app SignalR. Consenti richieste tra le origini solo da domini che consideri attendibili o controlli. Ad esempio:

* Il sito è ospitato in `http://www.example.com`
* L'app SignalR è ospitata in `http://signalr.example.com`

CORS deve essere configurato nell'app SignalR per consentire solo il `www.example.com`di origine.

Per altre informazioni sulla configurazione di CORS, vedere [abilitare le richieste tra le origini (CORS)](xref:security/cors). SignalR **richiede** i seguenti criteri CORS:

* Consente le origini previste specifiche. Consentire qualsiasi origine è possibile, ma **non** è sicura o consigliata.
* I metodi HTTP `GET` e `POST` devono essere consentiti.
* Le credenziali devono essere consentite affinché le sessioni permanenti basate su cookie funzionino correttamente. Devono essere abilitati anche quando non viene utilizzata l'autenticazione.

<!--
::: moniker range=">= aspnetcore-5.0"  // Moniker here just to make sure this doesn't get missed in the 5.0 version update.
However, in 5.0 we have provided an option in the TypeScript client to not use credentials.
The not to use credentials option should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers)

For more info, see https://github.com/aspnet/AspNetCore.Docs/issues/16003
.-->

Ad esempio, il criterio CORS seguente consente a un client SignalR browser ospitato su `https://example.com` di accedere all'app SignalR ospitata in `https://signalr.example.com`:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

> [!NOTE]
> SignalR non è compatibile con la funzionalità CORS incorporata nel servizio app Azure.

## <a name="websocket-origin-restriction"></a>Restrizione origine WebSocket

::: moniker range=">= aspnetcore-2.2"

La protezione fornita da CORS non si applica agli oggetti WebSocket. Per la restrizione Origin su WebSocket, leggere la [restrizione Origin di WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

La protezione fornita da CORS non si applica agli oggetti WebSocket. I browser **non**:

* Eseguono richieste CORS preventive.
* Rispettano le restrizioni specificate nelle intestazioni `Access-Control` quando eseguono richieste WebSocket.

I browser, tuttavia, inviano l'intestazione `Origin` quando rilasciano richieste WebSocket. Le applicazioni devono essere configurate per la convalida di queste intestazioni per assicurarsi che siano consentiti solo WebSocket provenienti dalle origini previste.

In ASP.NET Core 2,1 e versioni successive, è possibile ottenere la convalida delle intestazioni usando un middleware personalizzato inserito **prima `UseSignalR`e il middleware di autenticazione** in `Configure`:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> L'intestazione `Origin` viene controllata dal client e, come l'intestazione `Referer`, può essere falsificata. Queste intestazioni **non** devono essere usate come meccanismo di autenticazione.

::: moniker-end

## <a name="connectionid"></a>ConnectionId

L'esposizione di `ConnectionId` può causare la rappresentazione dannosa se la versione del server o del client di SignalR è ASP.NET Core 2,2 o versioni precedenti. Se il server SignalR e la versione del client sono ASP.NET Core 3,0 o versioni successive, il `ConnectionToken` anziché il `ConnectionId` deve essere mantenuto segreto. Il `ConnectionToken` non viene esposto intenzionalmente in alcuna API.  Può essere difficile assicurarsi che i client di SignalR meno recenti non si connettano al server, quindi anche se la versione del server SignalR è ASP.NET Core 3,0 o successiva, non è necessario esporre il `ConnectionId`.

## <a name="access-token-logging"></a>Registrazione del token di accesso

Quando si usano WebSocket o eventi inviati dal server, il client browser invia il token di accesso nella stringa di query. La ricezione del token di accesso tramite la stringa di query è generalmente sicura come l'utilizzo dell'intestazione `Authorization` standard. Usare sempre HTTPS per garantire una connessione end-to-end sicura tra il client e il server. Molti server Web registrano l'URL per ogni richiesta, inclusa la stringa di query. La registrazione degli URL può registrare il token di accesso. Per impostazione predefinita, ASP.NET Core registra l'URL per ogni richiesta, che include la stringa di query. Ad esempio:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

In caso di dubbi sulla registrazione di questi dati con i log del server, è possibile disabilitare completamente questa registrazione configurando il logger di `Microsoft.AspNetCore.Hosting` al livello di `Warning` o superiore (questi messaggi vengono scritti a livello di `Info`). Per ulteriori informazioni, vedere [filtro dei log](xref:fundamentals/logging/index#log-filtering) per ulteriori informazioni. Se si vuole comunque registrare determinate informazioni sulle richieste, è possibile [scrivere un middleware](xref:fundamentals/middleware/write) per registrare i dati necessari e filtrare il valore della stringa di query `access_token` (se presente).

## <a name="exceptions"></a>Eccezioni

I messaggi di eccezione vengono in genere considerati dati sensibili che non devono essere rivelati a un client. Per impostazione predefinita, SignalR non invia al client i dettagli di un'eccezione generata da un metodo dell'hub. Il client riceve invece un messaggio generico che indica che si è verificato un errore. Il recapito dei messaggi di eccezione al client può essere sottoposto a override (ad esempio in fase di sviluppo o test) con [EnableDetailedErrors](xref:signalr/configuration#configure-server-options). I messaggi di eccezione non devono essere esposti al client nelle app di produzione.

## <a name="buffer-management"></a>Gestione del buffer

SignalR utilizza i buffer per connessione per gestire i messaggi in ingresso e in uscita. Per impostazione predefinita, SignalR limita questi buffer a 32 KB. Il messaggio più grande che può essere inviato da un client o da un server è 32 KB. La quantità massima di memoria utilizzata da una connessione per i messaggi è 32 KB. Se i messaggi sono sempre minori di 32 KB, è possibile ridurre il limite, che:

* Impedisce a un client di inviare un messaggio di dimensioni maggiori.
* Il server non dovrà mai allocare buffer di grandi dimensioni per accettare messaggi.

Se i messaggi sono maggiori di 32 KB, è possibile aumentare il limite. L'aumento di questo limite significa:

* Il client può causare l'allocazione di buffer di memoria di grandi dimensioni da parte del server.
* L'allocazione dei server di buffer di grandi dimensioni può ridurre il numero di connessioni simultanee.

Sono previsti limiti per i messaggi in ingresso e in uscita. entrambi possono essere configurati nell'oggetto [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) configurato in `MapHub`:

* `ApplicationMaxBufferSize` rappresenta il numero massimo di byte dal client che il server memorizza nel buffer. Se il client tenta di inviare un messaggio di dimensioni superiori a questo limite, è possibile che la connessione venga chiusa.
* `TransportMaxBufferSize` rappresenta il numero massimo di byte che il server può inviare. Se il server tenta di inviare un messaggio (compresi i valori restituiti dai metodi dell'hub) maggiore di questo limite, verrà generata un'eccezione.

Impostando il limite su `0` viene disabilitato il limite. La rimozione del limite consente a un client di inviare un messaggio di qualsiasi dimensione. I client malintenzionati che inviano messaggi di grandi dimensioni possono causare l'allocazione di memoria. Un utilizzo eccessivo della memoria può ridurre significativamente il numero di connessioni simultanee.
