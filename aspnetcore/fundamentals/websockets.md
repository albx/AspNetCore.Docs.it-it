---
title: Supporto di WebSocket in ASP.NET Core
author: rick-anderson
description: Introduzione all'uso di oggetti WebSocket in ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/10/2019
uid: fundamentals/websockets
ms.openlocfilehash: 5d4d9b02bd45e6650aa56448a3663cad06b3b45e
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975449"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="6756b-103">Supporto di WebSocket in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6756b-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="6756b-104">Di [Tom Dykstra](https://github.com/tdykstra) e [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="6756b-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="6756b-105">L'articolo contiene l'introduzione all'uso di oggetti WebSocket in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6756b-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="6756b-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) è un protocollo che consente canali di comunicazione persistente bidirezionale su connessioni TCP.</span><span class="sxs-lookup"><span data-stu-id="6756b-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="6756b-107">Viene usato nelle app che sfruttano comunicazioni veloci e in tempo reale, ad esempio le app di chat, i dashboard e le app di gioco.</span><span class="sxs-lookup"><span data-stu-id="6756b-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="6756b-108">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6756b-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="6756b-109">[Come eseguire](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="6756b-109">[How to run](#sample-app).</span></span>

## <a name="signalr"></a><span data-ttu-id="6756b-110">SignalR</span><span class="sxs-lookup"><span data-stu-id="6756b-110">SignalR</span></span>

<span data-ttu-id="6756b-111">[ASP.NET Core SignalR](xref:signalr/introduction) è una libreria che consente di aggiungere in modo più semplice funzionalità Web in tempo reale alle app.</span><span class="sxs-lookup"><span data-stu-id="6756b-111">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="6756b-112">Laddove possibile, usa oggetti WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6756b-112">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="6756b-113">Per la maggior parte delle applicazioni, è consigliabile preferire SignalR a oggetti WebSocket non elaborati.</span><span class="sxs-lookup"><span data-stu-id="6756b-113">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="6756b-114">SignalR fornisce il fallback per il trasporto per gli ambienti in cui i WebSocket non sono disponibili.</span><span class="sxs-lookup"><span data-stu-id="6756b-114">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="6756b-115">Fornisce inoltre un modello di app Remote Procedure Call semplice.</span><span class="sxs-lookup"><span data-stu-id="6756b-115">It also provides a simple remote procedure call app model.</span></span> <span data-ttu-id="6756b-116">Nella maggior parte degli scenari, inoltre, l'uso di SignalR non genera alcuno svantaggio significativo in termini di prestazioni rispetto all'uso di oggetti WebSocket non elaborati.</span><span class="sxs-lookup"><span data-stu-id="6756b-116">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6756b-117">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="6756b-117">Prerequisites</span></span>

* <span data-ttu-id="6756b-118">ASP.NET Core 1.1 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="6756b-118">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="6756b-119">Qualsiasi sistema operativo che supporta ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6756b-119">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="6756b-120">Windows 7/Windows Server 2008 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="6756b-120">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="6756b-121">Linux</span><span class="sxs-lookup"><span data-stu-id="6756b-121">Linux</span></span>
  * <span data-ttu-id="6756b-122">macOS</span><span class="sxs-lookup"><span data-stu-id="6756b-122">macOS</span></span>
  
* <span data-ttu-id="6756b-123">Se l'app viene eseguita su Windows con IIS:</span><span class="sxs-lookup"><span data-stu-id="6756b-123">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="6756b-124">Windows 8/Windows Server 2012 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="6756b-124">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="6756b-125">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="6756b-125">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="6756b-126">Gli oggetti WebSocket devono essere abilitati (vedere la sezione [Supporto di IIS/IIS Express](#iisiis-express-support)).</span><span class="sxs-lookup"><span data-stu-id="6756b-126">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="6756b-127">Se l'app viene eseguita su [HTTP.sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="6756b-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="6756b-128">Windows 8/Windows Server 2012 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="6756b-128">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="6756b-129">Per i browser supportati, vedere https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="6756b-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a><span data-ttu-id="6756b-130">Pacchetto NuGet</span><span class="sxs-lookup"><span data-stu-id="6756b-130">NuGet package</span></span>

<span data-ttu-id="6756b-131">Installare il pacchetto [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/).</span><span class="sxs-lookup"><span data-stu-id="6756b-131">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>

::: moniker-end

## <a name="configure-the-middleware"></a><span data-ttu-id="6756b-132">Configurare il middleware</span><span class="sxs-lookup"><span data-stu-id="6756b-132">Configure the middleware</span></span>


<span data-ttu-id="6756b-133">Aggiungere il middleware degli oggetti WebSocket nel metodo `Configure` della classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="6756b-133">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6756b-134">È possibile configurare le impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="6756b-134">The following settings can be configured:</span></span>

* <span data-ttu-id="6756b-135">`KeepAliveInterval`: la frequenza di invio di frame "ping" al client per garantire che i proxy tengano aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="6756b-135">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="6756b-136">Il valore predefinito è due minuti.</span><span class="sxs-lookup"><span data-stu-id="6756b-136">The default is two minutes.</span></span>
* <span data-ttu-id="6756b-137">`ReceiveBufferSize`: le dimensioni del buffer usato per ricevere dati.</span><span class="sxs-lookup"><span data-stu-id="6756b-137">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="6756b-138">Gli utenti avanzati possono avere l'esigenza di modificare questa impostazione per ottimizzare le prestazioni in base alle dimensioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="6756b-138">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="6756b-139">L'impostazione predefinita è 4 KB.</span><span class="sxs-lookup"><span data-stu-id="6756b-139">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6756b-140">È possibile configurare le impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="6756b-140">The following settings can be configured:</span></span>

* <span data-ttu-id="6756b-141">`KeepAliveInterval`: la frequenza di invio di frame "ping" al client per garantire che i proxy tengano aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="6756b-141">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="6756b-142">Il valore predefinito è due minuti.</span><span class="sxs-lookup"><span data-stu-id="6756b-142">The default is two minutes.</span></span>
* <span data-ttu-id="6756b-143">`ReceiveBufferSize`: le dimensioni del buffer usato per ricevere dati.</span><span class="sxs-lookup"><span data-stu-id="6756b-143">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="6756b-144">Gli utenti avanzati possono avere l'esigenza di modificare questa impostazione per ottimizzare le prestazioni in base alle dimensioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="6756b-144">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="6756b-145">L'impostazione predefinita è 4 KB.</span><span class="sxs-lookup"><span data-stu-id="6756b-145">The default is 4 KB.</span></span>
* <span data-ttu-id="6756b-146">`AllowedOrigins`: elenco di valori dell'intestazione Origin consentiti per le richieste WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6756b-146">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="6756b-147">Per impostazione predefinita, tutte le origini sono consentite.</span><span class="sxs-lookup"><span data-stu-id="6756b-147">By default, all origins are allowed.</span></span> <span data-ttu-id="6756b-148">Per informazioni dettagliate, vedere più avanti "Restrizione per le origini WebSocket".</span><span class="sxs-lookup"><span data-stu-id="6756b-148">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="6756b-149">Accettare le richieste WebSocket</span><span class="sxs-lookup"><span data-stu-id="6756b-149">Accept WebSocket requests</span></span>

<span data-ttu-id="6756b-150">In un momento successivo nel ciclo di vita della richiesta, più avanti nel metodo `Configure` o in un metodo di azione, ad esempio, verificare che si tratti di una richiesta WebSocket e accettarla.</span><span class="sxs-lookup"><span data-stu-id="6756b-150">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="6756b-151">L'esempio seguente è tratto da un punto successivo nel metodo `Configure`:</span><span class="sxs-lookup"><span data-stu-id="6756b-151">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="6756b-152">Una richiesta WebSocket può arrivare in qualsiasi URL, ma questo codice di esempio accetta solo le richieste per `/ws`.</span><span class="sxs-lookup"><span data-stu-id="6756b-152">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="6756b-153">Quando si usa un oggetto WebSocket, è **necessario** mantenere la pipeline middleware in esecuzione per la durata della connessione.</span><span class="sxs-lookup"><span data-stu-id="6756b-153">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="6756b-154">Se si cerca di inviare o ricevere un messaggio WebSocket dopo che la pipeline middleware è terminata, si potrebbe ottenere un'eccezione simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="6756b-154">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="6756b-155">Se si sta usando un servizio in background per scrivere i dati in un oggetto WebSocket, assicurarsi di mantenere la pipeline middleware in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="6756b-155">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="6756b-156">A tale scopo, usare <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="6756b-156">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="6756b-157">Passare `TaskCompletionSource` al servizio in background e fare in modo che chiami <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> al termine dell'uso dell'oggetto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6756b-157">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="6756b-158">Quindi assegnare `await` alla proprietà <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> durante la richiesta, come mostrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="6756b-158">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
<span data-ttu-id="6756b-159">L'eccezione di chiusura WebSocket può verificarsi anche se il ritorno da un metodo di azione avviene troppo presto.</span><span class="sxs-lookup"><span data-stu-id="6756b-159">The WebSocket closed exception can also happen if you return too soon from an action method.</span></span> <span data-ttu-id="6756b-160">Se si accetta un socket in un metodo di azione, attendere che il codice che usa il socket sia stato completato prima di tornare dal metodo dell'azione.</span><span class="sxs-lookup"><span data-stu-id="6756b-160">If you accept a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="6756b-161">Per attendere il completamento del socket, non utilizzare mai `Task.Wait()`, `Task.Result` o chiamate di blocco simili perché ciò può provocare gravi problemi di threading.</span><span class="sxs-lookup"><span data-stu-id="6756b-161">Never use `Task.Wait()`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="6756b-162">Usare sempre `await`.</span><span class="sxs-lookup"><span data-stu-id="6756b-162">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="6756b-163">Inviare e ricevere messaggi</span><span class="sxs-lookup"><span data-stu-id="6756b-163">Send and receive messages</span></span>

<span data-ttu-id="6756b-164">Il metodo `AcceptWebSocketAsync` consente di aggiornare la connessione TCP a una connessione WebSocket e offre un oggetto [WebSocket](/dotnet/core/api/system.net.websockets.websocket).</span><span class="sxs-lookup"><span data-stu-id="6756b-164">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="6756b-165">Usare l'oggetto `WebSocket` per inviare e ricevere messaggi.</span><span class="sxs-lookup"><span data-stu-id="6756b-165">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="6756b-166">Il codice illustrato in precedenza che accetta la richiesta WebSocket passa l'oggetto `WebSocket` a un metodo `Echo`.</span><span class="sxs-lookup"><span data-stu-id="6756b-166">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="6756b-167">Il codice riceve un messaggio e lo reinvia immediatamente.</span><span class="sxs-lookup"><span data-stu-id="6756b-167">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="6756b-168">I messaggi vengono inviati e ricevuti in un ciclo, fino a quando il client non chiude la connessione:</span><span class="sxs-lookup"><span data-stu-id="6756b-168">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="6756b-169">Quando si accetta la connessione WebSocket prima che inizi il ciclo, la pipeline del middleware termina.</span><span class="sxs-lookup"><span data-stu-id="6756b-169">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="6756b-170">Dopo la chiusura del socket, la pipeline si arresta,</span><span class="sxs-lookup"><span data-stu-id="6756b-170">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="6756b-171">In altri termini, quando viene accettato il WebSocket, lo spostamento in avanti della richiesta nella pipeline si interrompe.</span><span class="sxs-lookup"><span data-stu-id="6756b-171">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="6756b-172">Quando il ciclo viene completato e il socket viene chiuso, la richiesta torna ad avanzare nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="6756b-172">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="6756b-173">Gestire le disconnessioni del client</span><span class="sxs-lookup"><span data-stu-id="6756b-173">Handle client disconnects</span></span>

<span data-ttu-id="6756b-174">Il server non viene informato automaticamente quando il client si disconnette a causa della perdita di connettività.</span><span class="sxs-lookup"><span data-stu-id="6756b-174">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="6756b-175">Il server riceve un messaggio di disconnessione solo se inviato dal client, ma questo non è possibile in caso di interruzione della connessione Internet.</span><span class="sxs-lookup"><span data-stu-id="6756b-175">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="6756b-176">Se si vuole intervenire quando si verifica una situazione di questo tipo, impostare un timeout per segnalare che non sono stati ricevuti messaggi dal client entro un determinato intervallo di tempo.</span><span class="sxs-lookup"><span data-stu-id="6756b-176">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="6756b-177">Se il client non invia messaggi con una certa frequenza e non si vuole impostare un timeout solo perché la connessione diventa inattiva, configurare un timer nel client in modo da inviare un messaggio ping ogni X secondi.</span><span class="sxs-lookup"><span data-stu-id="6756b-177">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="6756b-178">Nel server, se un messaggio non è arrivato entro 2\*X secondi dal precedente, terminare la connessione e segnalare che il client si è disconnesso.</span><span class="sxs-lookup"><span data-stu-id="6756b-178">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="6756b-179">Attendere il doppio del tempo previsto per tenere conto di eventuali ritardi della rete che potrebbero trattenere il messaggio ping.</span><span class="sxs-lookup"><span data-stu-id="6756b-179">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="6756b-180">Restrizione per le origini WebSocket</span><span class="sxs-lookup"><span data-stu-id="6756b-180">WebSocket origin restriction</span></span>

<span data-ttu-id="6756b-181">La protezione fornita da CORS non si applica agli oggetti WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6756b-181">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="6756b-182">I browser **non**:</span><span class="sxs-lookup"><span data-stu-id="6756b-182">Browsers do **not**:</span></span>

* <span data-ttu-id="6756b-183">Eseguono richieste CORS preventive.</span><span class="sxs-lookup"><span data-stu-id="6756b-183">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="6756b-184">Rispettano le restrizioni specificate nelle intestazioni `Access-Control` quando eseguono richieste WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6756b-184">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="6756b-185">I browser, tuttavia, inviano l'intestazione `Origin` quando rilasciano richieste WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6756b-185">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="6756b-186">Le applicazioni devono essere configurate per la convalida di queste intestazioni per assicurarsi che siano consentiti solo WebSocket provenienti dalle origini previste.</span><span class="sxs-lookup"><span data-stu-id="6756b-186">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="6756b-187">Se si ospita il server in "https://server.com" e il client in "https://client.com", aggiungere "https://client.com" all'elenco `AllowedOrigins` per consentire la verifica dei WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6756b-187">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="6756b-188">L'intestazione `Origin` viene controllata dal client e, come l'intestazione `Referer`, può essere falsificata.</span><span class="sxs-lookup"><span data-stu-id="6756b-188">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="6756b-189">**Non** usare queste intestazioni come meccanismo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="6756b-189">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="6756b-190">Supporto di IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="6756b-190">IIS/IIS Express support</span></span>

<span data-ttu-id="6756b-191">Windows Server 2012 o versioni successive e Windows 8 o versioni successive con IIS/IIS Express 8 o versioni successive includono il supporto per il protocollo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6756b-191">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="6756b-192">Gli oggetti WebSocket sono sempre abilitati quando si usa IIS Express.</span><span class="sxs-lookup"><span data-stu-id="6756b-192">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="6756b-193">Abilitazione di oggetti WebSocket in IIS</span><span class="sxs-lookup"><span data-stu-id="6756b-193">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="6756b-194">Per abilitare il supporto per il protocollo WebSocket in Windows Server 2012 o versioni successive:</span><span class="sxs-lookup"><span data-stu-id="6756b-194">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="6756b-195">Questi passaggi non sono necessari quando si usa IIS Express</span><span class="sxs-lookup"><span data-stu-id="6756b-195">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="6756b-196">Usare la procedura guidata **Aggiungi ruoli e funzionalità** accessibile tramite il menu **Gestisci** o il collegamento in **Server Manager**.</span><span class="sxs-lookup"><span data-stu-id="6756b-196">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="6756b-197">Selezionare **Installazione basata su ruoli o basata su funzionalità**.</span><span class="sxs-lookup"><span data-stu-id="6756b-197">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="6756b-198">Scegliere **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6756b-198">Select **Next**.</span></span>
1. <span data-ttu-id="6756b-199">Selezionare il server appropriato (il server locale è selezionato per impostazione predefinita).</span><span class="sxs-lookup"><span data-stu-id="6756b-199">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="6756b-200">Scegliere **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6756b-200">Select **Next**.</span></span>
1. <span data-ttu-id="6756b-201">Espandere **Server Web (IIS)** nella struttura **Ruoli**, espandere **Server Web**, quindi **Sviluppo applicazioni**.</span><span class="sxs-lookup"><span data-stu-id="6756b-201">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="6756b-202">Selezionare **Protocollo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="6756b-202">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="6756b-203">Scegliere **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6756b-203">Select **Next**.</span></span>
1. <span data-ttu-id="6756b-204">Se non sono necessarie le funzionalità aggiuntive, selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="6756b-204">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="6756b-205">Selezionare **Installa**.</span><span class="sxs-lookup"><span data-stu-id="6756b-205">Select **Install**.</span></span>
1. <span data-ttu-id="6756b-206">Al termine dell'installazione, selezionare **Chiudi** per chiudere la procedura guidata.</span><span class="sxs-lookup"><span data-stu-id="6756b-206">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="6756b-207">Per abilitare il supporto per il protocollo WebSocket in Windows 8 o versioni successive:</span><span class="sxs-lookup"><span data-stu-id="6756b-207">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="6756b-208">Questi passaggi non sono necessari quando si usa IIS Express</span><span class="sxs-lookup"><span data-stu-id="6756b-208">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="6756b-209">Passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità** > **Attiva o disattiva funzionalità di Windows** (sul lato sinistro dello schermo).</span><span class="sxs-lookup"><span data-stu-id="6756b-209">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="6756b-210">Aprire i nodi seguenti: **Internet Information Services** > **Servizi Web** > **Funzionalità per lo sviluppo di applicazioni**.</span><span class="sxs-lookup"><span data-stu-id="6756b-210">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="6756b-211">Selezionare la funzionalità **Protocollo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="6756b-211">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="6756b-212">Scegliere **OK**.</span><span class="sxs-lookup"><span data-stu-id="6756b-212">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="6756b-213">Disabilitare WebSocket quando si usa socket.io su node.js</span><span class="sxs-lookup"><span data-stu-id="6756b-213">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="6756b-214">Se si usa il supporto WebSocket in [socket.io](https://socket.io/) su [Node.js](https://nodejs.org/), disabilitare il modulo WebSocket IIS predefinito mediante l'elemento `webSocket` in *web.config* o in *applicationHost.config*. Se non viene eseguito questo passaggio, il modulo IIS WebSocket prova a gestire le comunicazioni WebSocket anziché Node.js e l'app.</span><span class="sxs-lookup"><span data-stu-id="6756b-214">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="6756b-215">App di esempio</span><span class="sxs-lookup"><span data-stu-id="6756b-215">Sample app</span></span>

<span data-ttu-id="6756b-216">L'[app di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) inclusa in questo articolo è un'app echo.</span><span class="sxs-lookup"><span data-stu-id="6756b-216">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="6756b-217">Ha una pagina Web che consente la connessione WebSocket e il server reinvia al client tutti i messaggi ricevuti.</span><span class="sxs-lookup"><span data-stu-id="6756b-217">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="6756b-218">Eseguire l'app da un prompt dei comandi, in quanto non è configurata per l'esecuzione da Visual Studio con IIS Express, quindi passare a http://localhost:5000.</span><span class="sxs-lookup"><span data-stu-id="6756b-218">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="6756b-219">Nella parte superiore sinistra della pagina Web viene visualizzato lo stato della connessione:</span><span class="sxs-lookup"><span data-stu-id="6756b-219">The web page shows the connection status in the upper left:</span></span>

![Stato iniziale della pagina Web](websockets/_static/start.png)

<span data-ttu-id="6756b-221">Selezionare **Connect** (Connetti) per inviare una richiesta WebSocket per l'URL indicato.</span><span class="sxs-lookup"><span data-stu-id="6756b-221">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="6756b-222">Immettere un messaggio di prova e selezionare **Send** (Invia).</span><span class="sxs-lookup"><span data-stu-id="6756b-222">Enter a test message and select **Send**.</span></span> <span data-ttu-id="6756b-223">Al termine dell'operazione, selezionare **Close Socket** (Chiudi socket).</span><span class="sxs-lookup"><span data-stu-id="6756b-223">When done, select **Close Socket**.</span></span> <span data-ttu-id="6756b-224">La sezione **Comminication Log** (Registrazione comunicazione) riporta tutte le azioni di apertura, invio e chiusura nel momento in cui vengono eseguite.</span><span class="sxs-lookup"><span data-stu-id="6756b-224">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Stato iniziale della pagina Web](websockets/_static/end.png)

