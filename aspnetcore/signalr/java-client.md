---
title: ASP.NET Core SignalR client Java
author: mikaelm12
description: Informazioni su come usare il client ASP.NET Core SignalR Java.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/java-client
ms.openlocfilehash: d7143b2c22ecdc4e68f484aa4c244e1c520beae0
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963787"
---
# <a name="aspnet-core-opno-locsignalr-java-client"></a>ASP.NET Core SignalR client Java

Di [Mikael Mengistu](https://twitter.com/MikaelM_12)

Il client Java consente la connessione a un ASP.NET Core Server SignalR dal codice Java, incluse le app Android. Come il client [JavaScript](xref:signalr/javascript-client) e il [client .NET](xref:signalr/dotnet-client), il client Java consente di ricevere e inviare messaggi a un hub in tempo reale. Il client Java è disponibile in ASP.NET Core 2,2 e versioni successive.

L'app console Java di esempio a cui si fa riferimento in questo articolo usa il client Java SignalR.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-java-client-package"></a>Installare il pacchetto client Java di SignalR

Il file jar *SignalR-1.0.0* consente ai client di connettersi agli hub SignalR. Per trovare il numero di versione del file JAR più recente, vedere i [Risultati della ricerca di Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Se si usa Gradle, aggiungere la riga seguente alla sezione `dependencies` del file *Build. Gradle* :

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Se si usa Maven, aggiungere le righe seguenti all'interno dell'elemento `<dependencies>` del file *POM. XML* :

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Connettersi a un hub

Per stabilire una `HubConnection`, è necessario usare il `HubConnectionBuilder`. È possibile configurare l'URL dell'hub e il livello di registrazione durante la compilazione di una connessione. Configurare le opzioni necessarie chiamando uno dei metodi di `HubConnectionBuilder` prima di `build`. Avviare la connessione con `start`.

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>Chiamare i metodi dell'hub dal client

Una chiamata a `send` richiama un metodo dell'hub. Passare il nome del metodo dell'hub e gli eventuali argomenti definiti nel metodo hub per `send`.

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Se si usa il servizio SignalR di Azure in *modalità senza server*, non è possibile chiamare i metodi dell'hub da un client. Per ulteriori informazioni, vedere la [documentazione del servizioSignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Chiamare i metodi client dall'hub

Usare `hubConnection.on` per definire i metodi sul client che l'hub può chiamare. Definire i metodi dopo la compilazione, ma prima di avviare la connessione.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Aggiungi registrazione

Il client Java SignalR usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica. Il frammento di codice seguente illustra come usare `java.util.logging` con il client Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Questa operazione può essere ignorata in modo sicuro.

## <a name="android-development-notes"></a>Note sullo sviluppo per Android

Per quanto riguarda Android SDK compatibilità per le funzionalità client di SignalR, quando si specifica la versione Android SDK di destinazione considerare gli elementi seguenti:

* Il client Java SignalR eseguirà il livello 16 dell'API Android e versioni successive.
* La connessione tramite il servizio SignalR di Azure richiede il livello 20 dell'API Android e versioni successive perché il [servizio azure SignalR](/azure/azure-signalr/signalr-overview) richiede TLS 1,2 e non supporta i pacchetti di crittografia basati su SHA-1. Android ha [aggiunto il supporto per i pacchetti di crittografia SHA-256 (e versioni successive)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) nel livello API 20.

## <a name="configure-bearer-token-authentication"></a>Configurare l'autenticazione bearer token

Nel client SignalR Java è possibile configurare una bearer token da usare per l'autenticazione fornendo una "Factory di token di accesso" a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per [fornire una](https://github.com/ReactiveX/RxJava) [> stringa\<Single](https://reactivex.io/documentation/single.html). Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>Limitazioni note

::: moniker range=">= aspnetcore-3.0"

* È supportato solo il protocollo JSON.
* Il trasporto del fallback del trasporto e degli eventi inviati dal server non è supportato.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* È supportato solo il protocollo JSON.
* È supportato solo il trasporto WebSocket.
* Il flusso non è ancora supportato.

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* [Informazioni di riferimento sulle API Java](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Documentazione senza server del servizio SignalR di Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
