---
title: Debug ASP.NET Core Blazor webassembly
author: guardrex
description: Informazioni su come eseguire il debug di app Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 5dbc900ab68682068a7f9e3ffdaabef89a0c7798
ms.sourcegitcommit: 6ffb583991d6689326605a24565130083a28ef85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80306477"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="ae40a-103">Debug ASP.NET Core Blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="ae40a-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="ae40a-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="ae40a-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ae40a-105">è possibile eseguire il debug delle app webassembly Blazor usando gli strumenti di sviluppo del browser nei browser basati su cromo (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="ae40a-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="ae40a-106">In alternativa è possibile eseguire il debug dell'app usando Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ae40a-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="ae40a-107">Gli scenari disponibili includono:</span><span class="sxs-lookup"><span data-stu-id="ae40a-107">Available scenarios include:</span></span>

* <span data-ttu-id="ae40a-108">Impostare e rimuovere punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="ae40a-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="ae40a-109">Eseguire l'app con supporto per il debug in Visual Studio e Visual Studio Code (supporto<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="ae40a-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="ae40a-110">Singolo passaggio (<kbd>F10</kbd>) tramite il codice.</span><span class="sxs-lookup"><span data-stu-id="ae40a-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="ae40a-111">Riprendere l'esecuzione del codice con <kbd>F8</kbd> in un browser o <kbd>F5</kbd> in Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ae40a-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="ae40a-112">Nella visualizzazione variabili *locali* osservare i valori delle variabili locali.</span><span class="sxs-lookup"><span data-stu-id="ae40a-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="ae40a-113">Vedere lo stack di chiamate, incluse le catene di chiamate che passano da JavaScript a .NET e da .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ae40a-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="ae40a-114">Per il momento *non è possibile*:</span><span class="sxs-lookup"><span data-stu-id="ae40a-114">For now, you *can't*:</span></span>

* <span data-ttu-id="ae40a-115">Controllare le matrici.</span><span class="sxs-lookup"><span data-stu-id="ae40a-115">Inspect arrays.</span></span>
* <span data-ttu-id="ae40a-116">Passare il mouse per esaminare i membri.</span><span class="sxs-lookup"><span data-stu-id="ae40a-116">Hover to inspect members.</span></span>
* <span data-ttu-id="ae40a-117">Eseguire il debug all'interno o all'esterno del codice gestito.</span><span class="sxs-lookup"><span data-stu-id="ae40a-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="ae40a-118">Disporre del supporto completo per il controllo dei tipi di valore.</span><span class="sxs-lookup"><span data-stu-id="ae40a-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="ae40a-119">Interrompi in corrispondenza di eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="ae40a-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="ae40a-120">Raggiunge i punti di interruzione durante l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="ae40a-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="ae40a-121">Eseguire il debug di un'app con un ruolo di lavoro del servizio.</span><span class="sxs-lookup"><span data-stu-id="ae40a-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="ae40a-122">Si continuerà a migliorare l'esperienza di debug nelle prossime versioni.</span><span class="sxs-lookup"><span data-stu-id="ae40a-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ae40a-123">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="ae40a-123">Prerequisites</span></span>

<span data-ttu-id="ae40a-124">Il debug richiede uno dei seguenti browser:</span><span class="sxs-lookup"><span data-stu-id="ae40a-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="ae40a-125">Microsoft Edge (versione 80 o successiva)</span><span class="sxs-lookup"><span data-stu-id="ae40a-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="ae40a-126">Google Chrome (versione 70 o successiva)</span><span class="sxs-lookup"><span data-stu-id="ae40a-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="ae40a-127">Abilitare il debug per Visual Studio e Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ae40a-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="ae40a-128">Il debug viene abilitato automaticamente per i nuovi progetti creati usando il modello di progetto ASP.NET Core 3,2 Preview 3 o versioni successive Blazor webassembly.</span><span class="sxs-lookup"><span data-stu-id="ae40a-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template.</span></span>

<span data-ttu-id="ae40a-129">Per abilitare il debug per un'app webassembly Blazor esistente, aggiornare il file *launchSettings. JSON* nel progetto di avvio per includere la proprietà `inspectUri` seguente in ogni profilo di avvio:</span><span class="sxs-lookup"><span data-stu-id="ae40a-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="ae40a-130">Al termine dell'aggiornamento, il file *launchSettings. JSON* dovrebbe avere un aspetto simile all'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="ae40a-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="ae40a-131">Proprietà `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="ae40a-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="ae40a-132">Consente all'IDE di rilevare che l'app è un'app webassembly Blazor.</span><span class="sxs-lookup"><span data-stu-id="ae40a-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="ae40a-133">Indica all'infrastruttura di debug degli script di connettersi al browser tramite il proxy di debug di Blazor.</span><span class="sxs-lookup"><span data-stu-id="ae40a-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="ae40a-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae40a-134">Visual Studio</span></span>

<span data-ttu-id="ae40a-135">Per eseguire il debug di un'app webassembly Blazor in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="ae40a-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="ae40a-136">Assicurarsi di aver [installato la versione di anteprima più recente di Visual Studio 2019 16,6](https://visualstudio.com/preview) (Preview 2 o versione successiva).</span><span class="sxs-lookup"><span data-stu-id="ae40a-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="ae40a-137">Creare una nuova app ASP.NET Core Hosted Blazor webassembly.</span><span class="sxs-lookup"><span data-stu-id="ae40a-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="ae40a-138">Premere <kbd>F5</kbd> per eseguire l'app nel debugger.</span><span class="sxs-lookup"><span data-stu-id="ae40a-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="ae40a-139">Impostare un punto di interruzione in *Counter. Razor* nel metodo `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="ae40a-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="ae40a-140">Passare alla scheda **contatore** e selezionare il pulsante per raggiungere il punto di interruzione:</span><span class="sxs-lookup"><span data-stu-id="ae40a-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Contatore debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="ae40a-142">Verificare il valore del campo `currentCount` nella finestra variabili locali:</span><span class="sxs-lookup"><span data-stu-id="ae40a-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Visualizza variabili locali](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="ae40a-144">Premere <kbd>F5</kbd> per continuare l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ae40a-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="ae40a-145">Quando si esegue il debug dell'app webassembly Blazor, è anche possibile eseguire il debug del codice del server:</span><span class="sxs-lookup"><span data-stu-id="ae40a-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="ae40a-146">Impostare un punto di interruzione nella pagina *fetchData. Razor* in `OnInitializedAsync`.</span><span class="sxs-lookup"><span data-stu-id="ae40a-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="ae40a-147">Impostare un punto di interruzione nel `WeatherForecastController` nel metodo di azione `Get`.</span><span class="sxs-lookup"><span data-stu-id="ae40a-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="ae40a-148">Passare alla scheda **Recupera dati** per raggiungere il primo punto di interruzione nel componente `FetchData` appena prima di eseguire una richiesta HTTP al server:</span><span class="sxs-lookup"><span data-stu-id="ae40a-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Eseguire il debug dei dati di recupero](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="ae40a-150">Premere <kbd>F5</kbd> per continuare l'esecuzione e quindi raggiungere il punto di interruzione sul server nel `WeatherForecastController`:</span><span class="sxs-lookup"><span data-stu-id="ae40a-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Server di debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="ae40a-152">Premere di nuovo <kbd>F5</kbd> per consentire l'esecuzione continua e visualizzare la tabella delle previsioni meteorologiche sottoposta a rendering.</span><span class="sxs-lookup"><span data-stu-id="ae40a-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

## <a name="visual-studio-code"></a><span data-ttu-id="ae40a-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ae40a-153">Visual Studio Code</span></span>

<span data-ttu-id="ae40a-154">Per eseguire il debug di un'app webassembly Blazor in Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="ae40a-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="ae40a-155">Installare l' [ C# estensione](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) e il [debugger JavaScript (notturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` impostato su `true`.</span><span class="sxs-lookup"><span data-stu-id="ae40a-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Estensioni](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Debugger di anteprima JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="ae40a-158">Aprire un'app webassembly Blazor esistente con il debug abilitato.</span><span class="sxs-lookup"><span data-stu-id="ae40a-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="ae40a-159">Se si riceve la notifica seguente che è necessaria un'installazione aggiuntiva per abilitare il debug, verificare che siano state installate le estensioni corrette e che sia stato abilitato il debug dell'anteprima JavaScript e quindi ricaricare la finestra:</span><span class="sxs-lookup"><span data-stu-id="ae40a-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Obbligatorie di installazione aggiuntivi](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="ae40a-161">Una notifica consente di aggiungere le risorse necessarie all'app per la compilazione e il debug.</span><span class="sxs-lookup"><span data-stu-id="ae40a-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="ae40a-162">Selezionare **Sì**:</span><span class="sxs-lookup"><span data-stu-id="ae40a-162">Select **Yes**:</span></span>

     ![Aggiungi asset necessari](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="ae40a-164">L'avvio dell'app nel debugger è un processo in due passaggi:</span><span class="sxs-lookup"><span data-stu-id="ae40a-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="ae40a-165">1 \.</span><span class="sxs-lookup"><span data-stu-id="ae40a-165">1\.</span></span> <span data-ttu-id="ae40a-166">Per **prima cosa**, avviare l'app usando la configurazione di avvio di **.net core (Blazor autonoma)** .</span><span class="sxs-lookup"><span data-stu-id="ae40a-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="ae40a-167">2 \.</span><span class="sxs-lookup"><span data-stu-id="ae40a-167">2\.</span></span> <span data-ttu-id="ae40a-168">**Dopo che l'app è stata avviata**, avviare il browser usando il **debug di .NET Core Blazor assembly Web in Chrome** Launch Configuration (richiede Chrome).</span><span class="sxs-lookup"><span data-stu-id="ae40a-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="ae40a-169">Per usare Edge anziché Chrome, modificare il `type` della configurazione di avvio in *. VSCODE/Launch. JSON* da `pwa-chrome` a `pwa-edge`.</span><span class="sxs-lookup"><span data-stu-id="ae40a-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-edge`.</span></span>

1. <span data-ttu-id="ae40a-170">Impostare un punto di interruzione nel metodo `IncrementCount` nel componente `Counter`, quindi selezionare il pulsante per raggiungere il punto di interruzione:</span><span class="sxs-lookup"><span data-stu-id="ae40a-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Esegui il debug del contatore in VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="ae40a-172">Debug nel browser</span><span class="sxs-lookup"><span data-stu-id="ae40a-172">Debug in the browser</span></span>

1. <span data-ttu-id="ae40a-173">Eseguire una build di debug dell'app nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="ae40a-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="ae40a-174">Premere <kbd>maiusc</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="ae40a-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="ae40a-175">Il browser deve essere eseguito con il debug remoto abilitato.</span><span class="sxs-lookup"><span data-stu-id="ae40a-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="ae40a-176">Se il debug remoto è disabilitato, viene generata una pagina di errore della **scheda del browser di cui è possibile eseguire il debug** .</span><span class="sxs-lookup"><span data-stu-id="ae40a-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="ae40a-177">La pagina di errore contiene le istruzioni per eseguire il browser con la porta di debug aperta, in modo che il proxy di debug Blazor possa connettersi all'app.</span><span class="sxs-lookup"><span data-stu-id="ae40a-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="ae40a-178">*Chiudere tutte le istanze del browser* e riavviare il browser come indicato.</span><span class="sxs-lookup"><span data-stu-id="ae40a-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="ae40a-179">Quando il browser è in esecuzione con il debug remoto abilitato, il tasto di scelta rapida di debug apre una nuova scheda del debugger. Dopo qualche istante la scheda **Sources (origini** ) Mostra un elenco degli assembly .NET nell'app.</span><span class="sxs-lookup"><span data-stu-id="ae40a-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="ae40a-180">Espandere ogni assembly e trovare i file di origine *. cs*/ *. Razor* disponibili per il debug.</span><span class="sxs-lookup"><span data-stu-id="ae40a-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="ae40a-181">Impostare i punti di interruzione, tornare alla scheda dell'app e i punti di interruzione vengono raggiunti quando viene eseguito il codice.</span><span class="sxs-lookup"><span data-stu-id="ae40a-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="ae40a-182">Quando viene raggiunto un punto di interruzione, l'esecuzione<kbd>F10</kbd>del codice in un singolo passaggio (F10<kbd>) viene</kbd>eseguito normalmente.</span><span class="sxs-lookup"><span data-stu-id="ae40a-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="ae40a-183"> fornisce un proxy di debug che implementa il [protocollo devtools di Chrome](https://chromedevtools.github.io/devtools-protocol/) e potenzia il protocollo con. Informazioni specifiche del NET.</span><span class="sxs-lookup"><span data-stu-id="ae40a-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="ae40a-184">Quando si preme il tasto di scelta rapida per il debug, Blazor punta il DevTools di Chrome sul proxy.</span><span class="sxs-lookup"><span data-stu-id="ae40a-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="ae40a-185">Il proxy si connette alla finestra del browser che si sta tentando di eseguire il debug, quindi è necessario abilitare il debug remoto.</span><span class="sxs-lookup"><span data-stu-id="ae40a-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="ae40a-186">Mappe di origine del browser</span><span class="sxs-lookup"><span data-stu-id="ae40a-186">Browser source maps</span></span>

<span data-ttu-id="ae40a-187">Le mappe di origine del browser consentono al browser di eseguire il mapping dei file compilati ai file di origine originali e vengono comunemente usati per il debug sul lato client.</span><span class="sxs-lookup"><span data-stu-id="ae40a-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="ae40a-188">Tuttavia, non Blazor attualmente viene C# effettuato il mapping direttamente a JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="ae40a-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="ae40a-189">Al contrario, Blazor l'interpretazione del nel browser, quindi le mappe di origine non sono rilevanti.</span><span class="sxs-lookup"><span data-stu-id="ae40a-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ae40a-190">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="ae40a-190">Troubleshoot</span></span>

<span data-ttu-id="ae40a-191">Se si verificano errori, è possibile che venga utile il suggerimento seguente:</span><span class="sxs-lookup"><span data-stu-id="ae40a-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="ae40a-192">Nella scheda **debugger** aprire gli strumenti di sviluppo nel browser.</span><span class="sxs-lookup"><span data-stu-id="ae40a-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="ae40a-193">Nella console di eseguire `localStorage.clear()` per rimuovere tutti i punti di interruzione.</span><span class="sxs-lookup"><span data-stu-id="ae40a-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
