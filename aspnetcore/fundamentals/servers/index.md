---
title: Implementazioni di server Web in ASP.NET Core
author: rick-anderson
description: Individuare i server Web Kestrel e HTTP.sys per ASP.NET Core. Informazioni su come scegliere un server e quando usare un server proxy inverso.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2019
uid: fundamentals/servers/index
ms.openlocfilehash: 3bdc2bf776946b8fae8886a37ecd3ed5e3f860fe
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259832"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="9dd06-104">Implementazioni di server Web in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9dd06-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="9dd06-105">Di [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) e [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="9dd06-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="9dd06-106">Un'app ASP.NET Core viene eseguita con un'implementazione del server HTTP in-process.</span><span class="sxs-lookup"><span data-stu-id="9dd06-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="9dd06-107">L'implementazione del server attende le richieste HTTP e le rende visibili all'app come un set di [funzionalità di richiesta](xref:fundamentals/request-features) combinate in un <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="9dd06-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="9dd06-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="9dd06-108">Kestrel</span></span>

<span data-ttu-id="9dd06-109">Kestrel è il server Web predefinito incluso nei modelli di progetto di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9dd06-109">Kestrel is the default web server included in ASP.NET Core project templates.</span></span>

<span data-ttu-id="9dd06-110">Usare Kestrel:</span><span class="sxs-lookup"><span data-stu-id="9dd06-110">Use Kestrel:</span></span>

* <span data-ttu-id="9dd06-111">Da solo come server perimetrale che elabora le richieste direttamente da una rete, inclusa Internet.</span><span class="sxs-lookup"><span data-stu-id="9dd06-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel comunica direttamente con Internet senza un server proxy inverso](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="9dd06-113">Con un *server proxy inverso*, ad esempio [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="9dd06-113">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="9dd06-114">Il server proxy inverso riceve le richieste HTTP da Internet e le inoltra a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9dd06-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel comunica indirettamente con Internet attraverso un server proxy inverso, ad esempio IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="9dd06-116">È supportata la configurazione host @ no__t-0with o senza un server proxy inverso @ no__t-1is.</span><span class="sxs-lookup"><span data-stu-id="9dd06-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="9dd06-117">Per indicazioni per la configurazione di Kestrel e per informazioni su quando usare Kestrel in una configurazione con proxy inverso, vedere <xref:fundamentals/servers/kestrel>.</span><span class="sxs-lookup"><span data-stu-id="9dd06-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="9dd06-118">Windows</span><span class="sxs-lookup"><span data-stu-id="9dd06-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="9dd06-119">ASP.NET Core include quanto segue:</span><span class="sxs-lookup"><span data-stu-id="9dd06-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="9dd06-120">Il [server Kestrel](xref:fundamentals/servers/kestrel) è l'implementazione di server HTTP multipiattaforma predefinita.</span><span class="sxs-lookup"><span data-stu-id="9dd06-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="9dd06-121">Il server HTTP IIS è un [server in-process](#hosting-models) per IIS.</span><span class="sxs-lookup"><span data-stu-id="9dd06-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="9dd06-122">Il [server HTTP.sys](xref:fundamentals/servers/httpsys) è un server HTTP solo per Windows basato sul [driver del kernel HTTP.sys e l'API HTTP Server](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="9dd06-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="9dd06-123">Quando si usa [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) oppure [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), l'app viene eseguita:</span><span class="sxs-lookup"><span data-stu-id="9dd06-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="9dd06-124">Nello stesso processo del processo di lavoro IIS ([modello di hosting in-process](#hosting-models)) con il server HTTP IIS.</span><span class="sxs-lookup"><span data-stu-id="9dd06-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="9dd06-125">*In-process* è la configurazione consigliata.</span><span class="sxs-lookup"><span data-stu-id="9dd06-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="9dd06-126">In un processo separato dal processo di lavoro IIS ([modello di hosting out-of-process](#hosting-models)) con il [server Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="9dd06-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="9dd06-127">Il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) è un modulo IIS nativo che gestisce le richieste IIS native tra IIS e il server HTTP IIS in-process o il server Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9dd06-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="9dd06-128">Per altre informazioni, vedere <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="9dd06-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="9dd06-129">Modelli di hosting</span><span class="sxs-lookup"><span data-stu-id="9dd06-129">Hosting models</span></span>

<span data-ttu-id="9dd06-130">Se si usa l'hosting in-process, un'app ASP.NET Core esegue lo stesso processo del processo di lavoro IIS.</span><span class="sxs-lookup"><span data-stu-id="9dd06-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="9dd06-131">L'hosting in-process offre prestazioni migliori rispetto all'hosting out-of-process perché le richieste non vengono inserite in proxy sulla scheda di loopback, un'interfaccia di rete che restituisce il traffico di rete in uscita allo stesso computer.</span><span class="sxs-lookup"><span data-stu-id="9dd06-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="9dd06-132">Per gestire il processo, IIS usa il [servizio Attivazione processo Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="9dd06-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="9dd06-133">Quando si usa l'hosting out-of-process, le app ASP.NET Core vengono eseguite in un processo distinto dal processo di lavoro IIS e il modulo esegue la gestione dei processi.</span><span class="sxs-lookup"><span data-stu-id="9dd06-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="9dd06-134">Il modulo avvia il processo per l'app ASP.NET Core quando arriva la prima richiesta e riavvia l'app se viene arrestata o si arresta in modo anomalo.</span><span class="sxs-lookup"><span data-stu-id="9dd06-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="9dd06-135">Si tratta essenzialmente dello stesso comportamento delle app eseguite in-process e gestite dal [servizio Attivazione processo Windows](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="9dd06-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="9dd06-136">Per altre informazioni e indicazioni per la configurazione, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="9dd06-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="9dd06-137">macOS</span><span class="sxs-lookup"><span data-stu-id="9dd06-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="9dd06-138">ASP.NET Core viene fornito con il [server Kestrel](xref:fundamentals/servers/kestrel), ovvero il server HTTP multipiattaforma predefinito.</span><span class="sxs-lookup"><span data-stu-id="9dd06-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="9dd06-139">Linux</span><span class="sxs-lookup"><span data-stu-id="9dd06-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="9dd06-140">ASP.NET Core viene fornito con il [server Kestrel](xref:fundamentals/servers/kestrel), ovvero il server HTTP multipiattaforma predefinito.</span><span class="sxs-lookup"><span data-stu-id="9dd06-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="9dd06-141">Windows</span><span class="sxs-lookup"><span data-stu-id="9dd06-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="9dd06-142">ASP.NET Core include quanto segue:</span><span class="sxs-lookup"><span data-stu-id="9dd06-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="9dd06-143">Il [server kestrel](xref:fundamentals/servers/kestrel) è il server HTTP multipiattaforma predefinito.</span><span class="sxs-lookup"><span data-stu-id="9dd06-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="9dd06-144">Il [server HTTP.sys](xref:fundamentals/servers/httpsys) è un server HTTP solo per Windows basato sul [driver del kernel HTTP.sys e l'API HTTP Server](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="9dd06-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="9dd06-145">Quando si usa [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), l'app viene eseguita in un processo separato dal processo di lavoro IIS (*out-of-process*) con il [server Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="9dd06-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="9dd06-146">Poiché le app ASP.NET Core vengono eseguite in un processo distinto dal processo di lavoro IIS, il modulo esegue la gestione dei processi.</span><span class="sxs-lookup"><span data-stu-id="9dd06-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="9dd06-147">Il modulo avvia il processo per l'app ASP.NET Core quando arriva la prima richiesta e riavvia l'app se viene arrestata o si arresta in modo anomalo.</span><span class="sxs-lookup"><span data-stu-id="9dd06-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="9dd06-148">Si tratta essenzialmente dello stesso comportamento delle app eseguite in-process e gestite dal [servizio Attivazione processo Windows](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="9dd06-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="9dd06-149">Il diagramma seguente illustra la relazione tra IIS, il modulo ASP.NET Core e un'app ospitata out-of-process:</span><span class="sxs-lookup"><span data-stu-id="9dd06-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Modulo ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="9dd06-151">Le richieste arrivano dal Web al driver HTTP.sys in modalità kernel.</span><span class="sxs-lookup"><span data-stu-id="9dd06-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="9dd06-152">Il driver instrada le richieste a IIS sulla porta configurata per il sito Web, in genere 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9dd06-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="9dd06-153">Il modulo inoltra le richieste a Kestrel su una porta casuale per l'app non corrispondente alla porta 80 o 443.</span><span class="sxs-lookup"><span data-stu-id="9dd06-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="9dd06-154">Il modulo specifica la porta tramite una variabile di ambiente all'avvio e il [middleware di integrazione IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura il server per l'ascolto su `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="9dd06-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="9dd06-155">Vengono eseguiti controlli aggiuntivi e le richieste che non provengono dal modulo vengono rifiutate.</span><span class="sxs-lookup"><span data-stu-id="9dd06-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="9dd06-156">Il modulo non supporta l'inoltro HTTPS, pertanto le richieste vengono inoltrate tramite HTTP anche se sono state ricevute da IIS tramite HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9dd06-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="9dd06-157">Dopo che Kestrel ha prelevato la richiesta dal modulo, viene eseguito il push della richiesta nella pipeline middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9dd06-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="9dd06-158">La pipeline middleware gestisce la richiesta e la passa come istanza di `HttpContext` alla logica dell'app.</span><span class="sxs-lookup"><span data-stu-id="9dd06-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="9dd06-159">Il middleware aggiunto dall'integrazione di IIS aggiorna lo schema, l'IP remoto e il percorso di base all'account per l'inoltro della richiesta a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9dd06-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="9dd06-160">La risposta dell'app viene quindi passata a IIS, che ne esegue di nuovo il push al client HTTP che ha avviato la richiesta.</span><span class="sxs-lookup"><span data-stu-id="9dd06-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="9dd06-161">Per indicazioni sulla configurazione di IIS e del modulo ASP.NET Core, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="9dd06-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="9dd06-162">macOS</span><span class="sxs-lookup"><span data-stu-id="9dd06-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="9dd06-163">ASP.NET Core viene fornito con il [server Kestrel](xref:fundamentals/servers/kestrel), ovvero il server HTTP multipiattaforma predefinito.</span><span class="sxs-lookup"><span data-stu-id="9dd06-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="9dd06-164">Linux</span><span class="sxs-lookup"><span data-stu-id="9dd06-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="9dd06-165">ASP.NET Core viene fornito con il [server Kestrel](xref:fundamentals/servers/kestrel), ovvero il server HTTP multipiattaforma predefinito.</span><span class="sxs-lookup"><span data-stu-id="9dd06-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="9dd06-166">Nginx con Kestrel</span><span class="sxs-lookup"><span data-stu-id="9dd06-166">Nginx with Kestrel</span></span>

<span data-ttu-id="9dd06-167">Per informazioni su come usare Nginx in Linux come server proxy inverso per Kestrel, vedere <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="9dd06-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="9dd06-168">Apache con Kestrel</span><span class="sxs-lookup"><span data-stu-id="9dd06-168">Apache with Kestrel</span></span>

<span data-ttu-id="9dd06-169">Per informazioni su come usare Apache in Linux come server proxy inverso per Kestrel, vedere <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="9dd06-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="9dd06-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="9dd06-170">HTTP.sys</span></span>

<span data-ttu-id="9dd06-171">Se si eseguono app ASP.NET Core in Windows, HTTP.sys è un'alternativa a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9dd06-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="9dd06-172">Kestrel è in genere consigliato per ottenere prestazioni ottimali.</span><span class="sxs-lookup"><span data-stu-id="9dd06-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="9dd06-173">HTTP.sys è utilizzabile negli scenari in cui l'app viene esposta a Internet e le funzionalità necessarie sono supportate da HTTP.sys, ma non da Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9dd06-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="9dd06-174">Per altre informazioni, vedere <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="9dd06-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys comunica direttamente con Internet](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="9dd06-176">HTTP.sys può essere usato anche per le app che vengono esposte solo a una rete interna.</span><span class="sxs-lookup"><span data-stu-id="9dd06-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys comunica direttamente con la rete interna](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="9dd06-178">Per indicazioni per la configurazione di HTTP.sys, vedere <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="9dd06-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="9dd06-179">Infrastruttura del server ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9dd06-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="9dd06-180">L'oggetto <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> disponibile nel metodo `Startup.Configure` espone la proprietà <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> del tipo <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span><span class="sxs-lookup"><span data-stu-id="9dd06-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="9dd06-181">Kestrel e HTTP.sys espongono una singola funzionalità ciascuno, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, ma implementazioni server diverse potrebbero esporre funzionalità aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="9dd06-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="9dd06-182">È possibile usare `IServerAddressesFeature` per individuare la porta a cui è associata l'implementazione server in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="9dd06-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="9dd06-183">Server personalizzati</span><span class="sxs-lookup"><span data-stu-id="9dd06-183">Custom servers</span></span>

<span data-ttu-id="9dd06-184">Se i server predefiniti non soddisfano i requisiti dell'app, è possibile creare un'implementazione server personalizzata.</span><span class="sxs-lookup"><span data-stu-id="9dd06-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="9dd06-185">La [guida a Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) spiega come scrivere un'implementazione [Nowin](https://github.com/Bobris/Nowin) di <xref:Microsoft.AspNetCore.Hosting.Server.IServer>.</span><span class="sxs-lookup"><span data-stu-id="9dd06-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="9dd06-186">È necessario implementare solo le interfacce delle funzionalità usate dall'app, anche se è richiesto come minimo il supporto di <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> e <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>.</span><span class="sxs-lookup"><span data-stu-id="9dd06-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="9dd06-187">Avvio del server</span><span class="sxs-lookup"><span data-stu-id="9dd06-187">Server startup</span></span>

<span data-ttu-id="9dd06-188">Il server viene avviato quando l'editor o l'ambiente di sviluppo integrato (IDE) avvia l'app:</span><span class="sxs-lookup"><span data-stu-id="9dd06-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="9dd06-189">[Visual Studio](https://visualstudio.microsoft.com) &ndash; È possibile usare i profili di avvio per avviare l'app e il server con [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[Modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) o la console.</span><span class="sxs-lookup"><span data-stu-id="9dd06-189">[Visual Studio](https://visualstudio.microsoft.com) &ndash; Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="9dd06-190">[Visual Studio Code](https://code.visualstudio.com/) &ndash; L'app e il server vengono avviati da [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), che attiva il debugger CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="9dd06-190">[Visual Studio Code](https://code.visualstudio.com/) &ndash; The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="9dd06-191">[Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; L'app e il server vengono avviati dal [debugger in modalità Mono Soft](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="9dd06-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="9dd06-192">All'avvio dell'app dal prompt dei comandi nella cartella del progetto, [dotnet run](/dotnet/core/tools/dotnet-run) avvia l'app e il server (solo Kestrel e HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="9dd06-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="9dd06-193">La configurazione viene specificata dall'opzione `-c|--configuration`, impostata su `Debug` (valore predefinito) o su `Release`.</span><span class="sxs-lookup"><span data-stu-id="9dd06-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span> <span data-ttu-id="9dd06-194">Se sono presenti profili di avvio in un file *launchSettings.json*, usare l'opzione `--launch-profile <NAME>` per impostare il profilo di avvio (ad esempio, `Development` o `Production`).</span><span class="sxs-lookup"><span data-stu-id="9dd06-194">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile <NAME>` option to set the launch profile (for example, `Development` or `Production`).</span></span> <span data-ttu-id="9dd06-195">Per altre informazioni, vedere [dotnet run](/dotnet/core/tools/dotnet-run) e [Creazione di pacchetti di distribuzione di .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="9dd06-195">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="9dd06-196">Supporto per HTTP/2</span><span class="sxs-lookup"><span data-stu-id="9dd06-196">HTTP/2 support</span></span>

<span data-ttu-id="9dd06-197">[HTTP/2](https://httpwg.org/specs/rfc7540.html) è supportato con ASP.NET Core negli scenari di distribuzione seguenti:</span><span class="sxs-lookup"><span data-stu-id="9dd06-197">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="9dd06-198">Kestrel</span><span class="sxs-lookup"><span data-stu-id="9dd06-198">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="9dd06-199">Sistema operativo</span><span class="sxs-lookup"><span data-stu-id="9dd06-199">Operating system</span></span>
    * <span data-ttu-id="9dd06-200">Windows Server 2016/Windows 10 o versioni successive&dagger;</span><span class="sxs-lookup"><span data-stu-id="9dd06-200">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="9dd06-201">Linux con OpenSSL 1.0.2 o versioni successive (ad esempio, Ubuntu 16.04 o versioni successive)</span><span class="sxs-lookup"><span data-stu-id="9dd06-201">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="9dd06-202">HTTP/2 verrà supportato in macOS in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="9dd06-202">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="9dd06-203">Framework di destinazione: .NET Core 2.2 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="9dd06-203">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="9dd06-204">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="9dd06-204">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="9dd06-205">Windows Server 2016/Windows 10 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="9dd06-205">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="9dd06-206">Framework di destinazione: non applicabile alle distribuzioni HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="9dd06-206">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="9dd06-207">IIS (in-process)</span><span class="sxs-lookup"><span data-stu-id="9dd06-207">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="9dd06-208">Windows Server 2016/Windows 10 o versioni successive; IIS 10 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="9dd06-208">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="9dd06-209">Framework di destinazione: .NET Core 2.2 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="9dd06-209">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="9dd06-210">IIS (out-of-process)</span><span class="sxs-lookup"><span data-stu-id="9dd06-210">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="9dd06-211">Windows Server 2016/Windows 10 o versioni successive; IIS 10 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="9dd06-211">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="9dd06-212">Le connessioni server perimetrali pubbliche usano HTTP/2, mentre la connessione con proxy inverso a Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="9dd06-212">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="9dd06-213">Framework di destinazione: non applicabile alle distribuzioni IIS out-of-process.</span><span class="sxs-lookup"><span data-stu-id="9dd06-213">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="9dd06-214">&dagger;Kestrel ha un supporto limitato per HTTP/2 in Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="9dd06-214">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="9dd06-215">Il supporto è limitato perché l'elenco di suite di crittografia TLS supportate disponibili in questi sistemi operativi è limitato.</span><span class="sxs-lookup"><span data-stu-id="9dd06-215">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="9dd06-216">Un certificato generato con un algoritmo ECDSA potrebbe essere necessario per proteggere le connessioni TLS.</span><span class="sxs-lookup"><span data-stu-id="9dd06-216">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="9dd06-217">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="9dd06-217">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="9dd06-218">Windows Server 2016/Windows 10 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="9dd06-218">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="9dd06-219">Framework di destinazione: non applicabile alle distribuzioni HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="9dd06-219">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="9dd06-220">IIS (out-of-process)</span><span class="sxs-lookup"><span data-stu-id="9dd06-220">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="9dd06-221">Windows Server 2016/Windows 10 o versioni successive; IIS 10 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="9dd06-221">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="9dd06-222">Le connessioni server perimetrali pubbliche usano HTTP/2, mentre la connessione con proxy inverso a Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="9dd06-222">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="9dd06-223">Framework di destinazione: non applicabile alle distribuzioni IIS out-of-process.</span><span class="sxs-lookup"><span data-stu-id="9dd06-223">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="9dd06-224">Una connessione HTTP/2 deve usare [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) e TLS 1.2 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="9dd06-224">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="9dd06-225">Per altre informazioni, vedere gli argomenti relativi agli scenari di distribuzione server.</span><span class="sxs-lookup"><span data-stu-id="9dd06-225">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9dd06-226">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9dd06-226">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
