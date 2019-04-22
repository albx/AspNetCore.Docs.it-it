---
title: Client .NET di ASP.NET Core SignalR
author: bradygaster
description: Informazioni relative al Client .NET di ASP.NET Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/dotnet-client
ms.openlocfilehash: 640d75157e42ffa6d78235c5be03e4846e8dcde9
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982946"
---
# <a name="aspnet-core-signalr-net-client"></a>Client .NET di ASP.NET Core SignalR

La libreria client .NET di ASP.NET Core SignalR consente di comunicare con gli hub SignalR dalle app .NET.

> [!NOTE]
> Xamarin sono previsti requisiti speciali per la versione di Visual Studio. Per altre informazioni, vedere [SignalR Client 2.1.1 in Xamarin](https://github.com/aspnet/Announcements/issues/305).

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([procedura per il download](xref:index#how-to-download-a-sample))

Nell'esempio di codice in questo articolo è un'app WPF che utilizza il client .NET di ASP.NET Core SignalR.

## <a name="install-the-signalr-net-client-package"></a>Installare il pacchetto del client .NET di SignalR

Il `Microsoft.AspNetCore.SignalR.Client` pacchetto è necessario per i client .NET per connettersi a un hub SignalR. Per installare la libreria client, eseguire il comando seguente nel **Console di gestione pacchetti** finestra:

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a>Connettersi a un hub

Per stabilire una connessione, creare un `HubConnectionBuilder` e chiamare `Build`. L'URL dell'hub, protocollo, il tipo di trasporto, livello di registrazione, le intestazioni e altre opzioni possono essere configurati durante la compilazione di una connessione. Configurare le opzioni necessarie mediante l'inserimento di uno qualsiasi dei `HubConnectionBuilder` metodi in `Build`. Avviare la connessione con `StartAsync`.

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Handle di connessione è stata interrotta

Usare il <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> eventi per rispondere a una connessione è stata persa. Ad esempio, è possibile automatizzare la riconnessione.

Il `Closed` eventi richiede un delegato che restituisce un `Task`, che consente di eseguire senza usare codice asincrono `async void`. Per soddisfare la firma del delegato in un `Closed` gestore dell'evento che viene eseguito in modo sincrono, restituisce `Task.CompletedTask`:

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

Il motivo principale per il supporto asincrono è in modo che sia possibile riavviare la connessione. L'avvio di una connessione è un'azione asincrona.

In un `Closed` gestore che riavvia la connessione, è consigliabile attendere un ritardo casuale impedire il sovraccarico del server, come illustrato nell'esempio seguente:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Chiamare i metodi dell'hub da client

`InvokeAsync` chiama i metodi dell'hub. Passare il nome del metodo dell'hub e gli argomenti definiti nel metodo dell'hub per `InvokeAsync`. SignalR è asincrona, utilizzare `async` e `await` durante le chiamate.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

Il `InvokeAsync` metodo restituisce un `Task` che viene completato quando restituito dal metodo server. Il valore restituito, se presente, viene fornito come risultato del `Task`. Tutte le eccezioni generate dal metodo nel server di producono un errore `Task`. Uso `await` sintassi per attendere il completamento del metodo server e `try...catch` sintassi per la gestione degli errori.

Il `SendAsync` metodo restituisce un `Task` che viene completato quando il messaggio è stato inviato al server. Viene specificato alcun valore restituito poiché `Task` non attendere fino al completamento del metodo server. Tutte le eccezioni generate nel client durante l'invio del messaggio di producono un errore `Task`. Uso `await` e `try...catch` sintassi per gestire errori di invio.

> [!NOTE]
> Se si usa il servizio Azure SignalR in *modalità senza server*, non è possibile chiamare i metodi dell'hub da un client. Per altre informazioni, vedere la [documentazione di SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Chiamare i metodi client hub

Definire i metodi dell'hub le chiamate effettuate tramite `connection.On` dopo la compilazione, ma prima di avviare la connessione.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

Il codice precedente in `connection.On` viene eseguito quando viene chiamato codice lato server usando il `SendAsync` (metodo).

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>La registrazione e la gestione degli errori

Gestire gli errori con un'istruzione try-catch. Esaminare il `Exception` oggetto per determinare l'azione appropriata da eseguire dopo che si verifica un errore.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [Hub](xref:signalr/hubs)
* [Client JavaScript](xref:signalr/javascript-client)
* [Pubblicare in Azure](xref:signalr/publish-to-azure-web-app)
* [Documentazione senza server di Azure SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config)
