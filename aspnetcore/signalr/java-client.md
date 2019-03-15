---
title: Client ASP.NET Core SignalR Java
author: mikaelm12
description: Informazioni su come usare il client Java di ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 03/14/2019
uid: signalr/java-client
ms.openlocfilehash: 09e5ce23ddcc250d212a8cdf1176f39531a9c0ba
ms.sourcegitcommit: d913bca90373c07f89b1d1df01af5fc01fc908ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57978490"
---
# <a name="aspnet-core-signalr-java-client"></a><span data-ttu-id="5396f-103">Client ASP.NET Core SignalR Java</span><span class="sxs-lookup"><span data-stu-id="5396f-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="5396f-104">Da [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="5396f-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="5396f-105">Il client Java consente la connessione a un server di ASP.NET Core SignalR dal codice Java, incluse le app Android.</span><span class="sxs-lookup"><span data-stu-id="5396f-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="5396f-106">Ad esempio la [client JavaScript](xref:signalr/javascript-client) e il [client .NET](xref:signalr/dotnet-client), il client Java consente di ricevere e inviare messaggi a un hub in tempo reale.</span><span class="sxs-lookup"><span data-stu-id="5396f-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="5396f-107">Il client Java è disponibile in ASP.NET Core 2.2 e successive.</span><span class="sxs-lookup"><span data-stu-id="5396f-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="5396f-108">L'app console Java di esempio citato nel presente articolo usa il client Java di SignalR.</span><span class="sxs-lookup"><span data-stu-id="5396f-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="5396f-109">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5396f-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-java-client-package"></a><span data-ttu-id="5396f-110">Installare il pacchetto client Java di SignalR</span><span class="sxs-lookup"><span data-stu-id="5396f-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="5396f-111">Il *signalr 1.0.0* file con estensione JAR consente ai client di connettersi a un hub SignalR.</span><span class="sxs-lookup"><span data-stu-id="5396f-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="5396f-112">Per trovare il numero di versione di file con estensione JAR più recente, vedere la [risultati della ricerca di Maven](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="5396f-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="5396f-113">Se si usa Gradle, aggiungere la riga seguente al `dependencies` sezione il *Build. gradle* file:</span><span class="sxs-lookup"><span data-stu-id="5396f-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="5396f-114">Se con Maven, aggiungere le seguenti righe all'interno di `<dependencies>` elemento delle *POM. XML* file:</span><span class="sxs-lookup"><span data-stu-id="5396f-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="5396f-115">Connettersi a un hub</span><span class="sxs-lookup"><span data-stu-id="5396f-115">Connect to a hub</span></span>

<span data-ttu-id="5396f-116">Per stabilire una `HubConnection`, il `HubConnectionBuilder` deve essere utilizzato.</span><span class="sxs-lookup"><span data-stu-id="5396f-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="5396f-117">Il livello di log e URL hub può essere configurato durante la compilazione di una connessione.</span><span class="sxs-lookup"><span data-stu-id="5396f-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="5396f-118">Configurare le opzioni necessarie, chiamare uno dei `HubConnectionBuilder` metodi prima `build`.</span><span class="sxs-lookup"><span data-stu-id="5396f-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="5396f-119">Avviare la connessione con `start`.</span><span class="sxs-lookup"><span data-stu-id="5396f-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="5396f-120">Chiamare i metodi dell'hub da client</span><span class="sxs-lookup"><span data-stu-id="5396f-120">Call hub methods from client</span></span>

<span data-ttu-id="5396f-121">Una chiamata a `send` richiama un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="5396f-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="5396f-122">Passare il nome del metodo dell'hub e gli argomenti definiti nel metodo dell'hub per `send`.</span><span class="sxs-lookup"><span data-stu-id="5396f-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="5396f-123">Se si usa il servizio Azure SignalR in *modalità senza server*, non è possibile chiamare i metodi dell'hub da un client.</span><span class="sxs-lookup"><span data-stu-id="5396f-123">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="5396f-124">Per altre informazioni, vedere la [documentazione di SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="5396f-124">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="5396f-125">Chiamare i metodi client hub</span><span class="sxs-lookup"><span data-stu-id="5396f-125">Call client methods from hub</span></span>

<span data-ttu-id="5396f-126">Usare `hubConnection.on` per definire i metodi sul client che può chiamare l'hub.</span><span class="sxs-lookup"><span data-stu-id="5396f-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="5396f-127">Definire i metodi dopo la compilazione, ma prima di avviare la connessione.</span><span class="sxs-lookup"><span data-stu-id="5396f-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="5396f-128">Aggiungere la registrazione</span><span class="sxs-lookup"><span data-stu-id="5396f-128">Add logging</span></span>

<span data-ttu-id="5396f-129">Il client SignalR Java Usa il [SLF4J](https://www.slf4j.org/) libreria per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="5396f-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="5396f-130">È un'API di registrazione di alto livello che consente agli utenti della libreria scegliere la propria implementazione di registrazione specifici, grazie alla disponibilità di una dipendenza di registrazione specifici.</span><span class="sxs-lookup"><span data-stu-id="5396f-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="5396f-131">Il frammento di codice seguente viene illustrato come utilizzare `java.util.logging` con il client Java di SignalR.</span><span class="sxs-lookup"><span data-stu-id="5396f-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="5396f-132">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger di alcuna operazione predefinita con il messaggio di avviso seguente:</span><span class="sxs-lookup"><span data-stu-id="5396f-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="5396f-133">Ciò può essere ignorato.</span><span class="sxs-lookup"><span data-stu-id="5396f-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="5396f-134">Note di sviluppo per Android</span><span class="sxs-lookup"><span data-stu-id="5396f-134">Android development notes</span></span>

<span data-ttu-id="5396f-135">Per quanto riguarda la compatibilità di Android SDK per le funzionalità client SignalR, prendere in considerazione gli elementi seguenti quando si specifica la versione di Android SDK di destinazione:</span><span class="sxs-lookup"><span data-stu-id="5396f-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="5396f-136">In Android API Level 16 e versioni successive, verrà eseguito il Client di Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="5396f-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="5396f-137">La connessione tramite il servizio Azure SignalR richiederanno Android livello API 20 e versioni successiva poiché il [servizio Azure SignalR](/azure/azure-signalr/signalr-overview) richiede TLS 1.2 e non supporta i pacchetti di crittografia basate su SHA-1.</span><span class="sxs-lookup"><span data-stu-id="5396f-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="5396f-138">Android [aggiunti i pacchetti di crittografia di supporto per SHA-256 (e versioni successive)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) nel livello API 20.</span><span class="sxs-lookup"><span data-stu-id="5396f-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="5396f-139">Configurare l'autenticazione del bearer token</span><span class="sxs-lookup"><span data-stu-id="5396f-139">Configure bearer token authentication</span></span>

<span data-ttu-id="5396f-140">Nel client Java di SignalR, è possibile configurare un token di connessione da utilizzare per l'autenticazione, fornendo una "token factory l'accesso" per il [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="5396f-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="5396f-141">Uso [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un' [RxJava](https://github.com/ReactiveX/RxJava) [singolo<String>](http://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="5396f-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single<String>](http://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="5396f-142">Con una chiamata a [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="5396f-142">With a call to [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="5396f-143">Limitazioni note</span><span class="sxs-lookup"><span data-stu-id="5396f-143">Known limitations</span></span>

* <span data-ttu-id="5396f-144">È supportato solo il protocollo JSON.</span><span class="sxs-lookup"><span data-stu-id="5396f-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="5396f-145">È supportato solo il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5396f-145">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="5396f-146">Lo streaming non è ancora supportato.</span><span class="sxs-lookup"><span data-stu-id="5396f-146">Streaming isn't supported yet.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5396f-147">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5396f-147">Additional resources</span></span>

* [<span data-ttu-id="5396f-148">Informazioni di riferimento sulle API Java</span><span class="sxs-lookup"><span data-stu-id="5396f-148">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="5396f-149">Documentazione senza server di Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="5396f-149">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
