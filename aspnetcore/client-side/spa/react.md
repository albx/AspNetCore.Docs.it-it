---
title: Usare il modello di progetto per React con ASP.NET Core
author: SteveSandersonMS
description: Informazioni su come iniziare a usare il modello di progetto per applicazioni a pagina singola di ASP.NET Core per React e create-react-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: spa/react
ms.openlocfilehash: 91a71498574d6d96c2c06e896283fed801e8adb3
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265233"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="6a718-103">Usare il modello di progetto per React con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a718-103">Use the React project template with ASP.NET Core</span></span>

<span data-ttu-id="6a718-104">Il modello di progetto aggiornato per React fornisce un ottimo punto di partenza per le app ASP.NET Core che usano le convenzioni React e [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) per implementare un'interfaccia utente avanzata sul lato client.</span><span class="sxs-lookup"><span data-stu-id="6a718-104">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="6a718-105">Il modello è equivalente alla creazione di un progetto ASP.NET Core che opera come un back-end API e un progetto React CRA standard che opera come un'interfaccia utente, ma con la praticità di ospitare entrambi in un singolo progetto di app, che può essere creato e pubblicato come una singola unità.</span><span class="sxs-lookup"><span data-stu-id="6a718-105">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="6a718-106">Creare una nuova app</span><span class="sxs-lookup"><span data-stu-id="6a718-106">Create a new app</span></span>

<span data-ttu-id="6a718-107">Se ASP.NET Core 2.1 è installato, non è necessario installare il modello di progetto React.</span><span class="sxs-lookup"><span data-stu-id="6a718-107">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

<span data-ttu-id="6a718-108">Creare un nuovo progetto da un prompt dei comandi usando il comando `dotnet new react` in una directory vuota.</span><span class="sxs-lookup"><span data-stu-id="6a718-108">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="6a718-109">Ad esempio, i comandi seguenti creano l'app in una directory *my-new-app* e passano a tale directory:</span><span class="sxs-lookup"><span data-stu-id="6a718-109">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```console
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="6a718-110">Eseguire l'app da Visual Studio o dall'interfaccia della riga di comando di .NET Core:</span><span class="sxs-lookup"><span data-stu-id="6a718-110">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6a718-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a718-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6a718-112">Aprire il file con estensione *csproj* generato ed eseguire l'app come di consueto da tale posizione.</span><span class="sxs-lookup"><span data-stu-id="6a718-112">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="6a718-113">Alla prima esecuzione, il processo di compilazione ripristina le dipendenze di npm. Tale operazione può richiedere alcuni minuti.</span><span class="sxs-lookup"><span data-stu-id="6a718-113">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="6a718-114">Le compilazioni successive sono molto più veloci.</span><span class="sxs-lookup"><span data-stu-id="6a718-114">Subsequent builds are much faster.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6a718-115">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="6a718-115">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6a718-116">Assicurarsi di disporre di una variabile di ambiente denominata `ASPNETCORE_Environment` con valore `Development`.</span><span class="sxs-lookup"><span data-stu-id="6a718-116">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="6a718-117">In Windows (in prompt non PowerShell) eseguire `SET ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="6a718-117">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="6a718-118">In Linux o Mac OS eseguire `export ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="6a718-118">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="6a718-119">Eseguire [dotnet build](/dotnet/core/tools/dotnet-build) per verificare che l'app venga compilata correttamente.</span><span class="sxs-lookup"><span data-stu-id="6a718-119">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="6a718-120">Alla prima esecuzione, il processo di compilazione ripristina le dipendenze di npm. Tale operazione può richiedere alcuni minuti.</span><span class="sxs-lookup"><span data-stu-id="6a718-120">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="6a718-121">Le compilazioni successive sono molto più veloci.</span><span class="sxs-lookup"><span data-stu-id="6a718-121">Subsequent builds are much faster.</span></span>

<span data-ttu-id="6a718-122">Eseguire [dotnet run](/dotnet/core/tools/dotnet-run) per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="6a718-122">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="6a718-123">Il modello di progetto crea un'app ASP.NET Core e un'app React.</span><span class="sxs-lookup"><span data-stu-id="6a718-123">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="6a718-124">L'app ASP.NET Core è destinata all'uso per l'accesso ai dati, l'autorizzazione e altri elementi sul lato server.</span><span class="sxs-lookup"><span data-stu-id="6a718-124">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="6a718-125">L'app React, disponibile nella sottodirectory *ClientApp*, è destinata all'uso per tutti gli aspetti relativi all'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="6a718-125">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="6a718-126">Aggiungere pagine, immagini, stili, moduli e così via.</span><span class="sxs-lookup"><span data-stu-id="6a718-126">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="6a718-127">La directory *ClientApp* è un'app React CRA standard.</span><span class="sxs-lookup"><span data-stu-id="6a718-127">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="6a718-128">Per altre informazioni, vedere la [documentazione ufficiale di CRA](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md).</span><span class="sxs-lookup"><span data-stu-id="6a718-128">See the official [CRA documentation](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) for more information.</span></span>

<span data-ttu-id="6a718-129">Vi sono piccole differenze tra l'app React creata tramite questo modello e quella creata tramite CRA stesso. Le funzionalità dell'app restano comunque invariate.</span><span class="sxs-lookup"><span data-stu-id="6a718-129">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="6a718-130">L'app creata tramite il modello contiene un layout basato su [bootstrap](https://getbootstrap.com/) e un esempio di routing di base.</span><span class="sxs-lookup"><span data-stu-id="6a718-130">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="6a718-131">Installa nuovi pacchetti npm</span><span class="sxs-lookup"><span data-stu-id="6a718-131">Install npm packages</span></span>

<span data-ttu-id="6a718-132">Per installare i pacchetti npm di terze parti, usare un prompt dei comandi nella sottodirectory *ClientApp*.</span><span class="sxs-lookup"><span data-stu-id="6a718-132">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="6a718-133">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="6a718-133">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="6a718-134">Pubblicare e distribuire</span><span class="sxs-lookup"><span data-stu-id="6a718-134">Publish and deploy</span></span>

<span data-ttu-id="6a718-135">In fase di sviluppo, l'app viene eseguita in una modalità ottimizzata per gli sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="6a718-135">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="6a718-136">Ad esempio, i bundle JavaScript includono il mapping di origine, in modo da poter visualizzare il codice sorgente originale durante il debug.</span><span class="sxs-lookup"><span data-stu-id="6a718-136">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="6a718-137">L'app controlla le modifiche dei file JavaScript, HTML e CSS su disco, quindi esegue automaticamente la ricompilazione e il ricaricamento quando rileva modifiche dei file.</span><span class="sxs-lookup"><span data-stu-id="6a718-137">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="6a718-138">Nell'ambiente di produzione usare una versione dell'app ottimizzata per le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="6a718-138">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="6a718-139">Questo comportamento è configurato per l'esecuzione automatica.</span><span class="sxs-lookup"><span data-stu-id="6a718-139">This is configured to happen automatically.</span></span> <span data-ttu-id="6a718-140">Quando si esegue la pubblicazione, la configurazione della build genera una compilazione minimizzata con transpile del codice sul lato client.</span><span class="sxs-lookup"><span data-stu-id="6a718-140">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="6a718-141">A differenza della build di sviluppo, la build di produzione non richiede l'installazione di Node.js nel server.</span><span class="sxs-lookup"><span data-stu-id="6a718-141">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="6a718-142">È possibile usare i [metodi standard di hosting e distribuzione di ASP.NET Core](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="6a718-142">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="6a718-143">Eseguire il server CRA in modo indipendente</span><span class="sxs-lookup"><span data-stu-id="6a718-143">Run the CRA server independently</span></span>

<span data-ttu-id="6a718-144">Il progetto è configurato in modo da avviare la propria istanza del server di sviluppo CRA in background all'avvio dell'app ASP.NET Core in modalità di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="6a718-144">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="6a718-145">Ciò risulta utile in quanto evita di dover eseguire manualmente un server distinto.</span><span class="sxs-lookup"><span data-stu-id="6a718-145">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="6a718-146">Questa configurazione predefinita presenta tuttavia uno svantaggio.</span><span class="sxs-lookup"><span data-stu-id="6a718-146">There's a drawback to this default setup.</span></span> <span data-ttu-id="6a718-147">Ogni volta che si modifica il codice C# ed è necessario riavviare l'app ASP.NET Core, il server CRA viene riavviato.</span><span class="sxs-lookup"><span data-stu-id="6a718-147">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="6a718-148">Per avviare il backup sono necessari alcuni secondi.</span><span class="sxs-lookup"><span data-stu-id="6a718-148">A few seconds are required to start back up.</span></span> <span data-ttu-id="6a718-149">Se si apportano frequentemente modifiche al codice C# e non si vuole attendere il riavvio del server CRA, eseguire il server CRA esternamente, in modo indipendente dal processo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6a718-149">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="6a718-150">A tale scopo:</span><span class="sxs-lookup"><span data-stu-id="6a718-150">To do so:</span></span>

1. <span data-ttu-id="6a718-151">Aggiungere un *env* del file per il *ClientApp* sottodirectory con l'impostazione seguente:</span><span class="sxs-lookup"><span data-stu-id="6a718-151">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```

    <span data-ttu-id="6a718-152">Ciò impedirà il web browser di apertura, all'avvio del server CRA esternamente.</span><span class="sxs-lookup"><span data-stu-id="6a718-152">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="6a718-153">In un prompt dei comandi passare alla sottodirectory *ClientApp* e avviare il server di sviluppo CRA:</span><span class="sxs-lookup"><span data-stu-id="6a718-153">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="6a718-154">Modificare l'app ASP.NET Core in modo da usare l'istanza del server CRA esterno anziché avviarne una autonomamente.</span><span class="sxs-lookup"><span data-stu-id="6a718-154">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="6a718-155">Nella classe *Startup* sostituire la chiamata `spa.UseReactDevelopmentServer` con quanto segue:</span><span class="sxs-lookup"><span data-stu-id="6a718-155">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="6a718-156">All'avvio dell'app ASP.NET Core, questa non avvierà un server CRA.</span><span class="sxs-lookup"><span data-stu-id="6a718-156">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="6a718-157">Verrà invece usata l'istanza che è stata avviata manualmente.</span><span class="sxs-lookup"><span data-stu-id="6a718-157">The instance you started manually is used instead.</span></span> <span data-ttu-id="6a718-158">Ciò consente di velocizzare l'avvio e il riavvio.</span><span class="sxs-lookup"><span data-stu-id="6a718-158">This enables it to start and restart faster.</span></span> <span data-ttu-id="6a718-159">Non è più necessario attendere che l'app React venga ricompilata ogni volta.</span><span class="sxs-lookup"><span data-stu-id="6a718-159">It's no longer waiting for your React app to rebuild each time.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6a718-160">"Il rendering lato server" non è una funzionalità supportata di questo modello.</span><span class="sxs-lookup"><span data-stu-id="6a718-160">"Server-side rendering" is not a supported feature of this template.</span></span> <span data-ttu-id="6a718-161">Il nostro obiettivo con questo modello è per soddisfare la parità con "create-react-app".</span><span class="sxs-lookup"><span data-stu-id="6a718-161">Our goal with this template is to meet parity with "create-react-app".</span></span> <span data-ttu-id="6a718-162">Di conseguenza, gli scenari e funzionalità non incluse in un progetto di "Creazione-react-app" (ad esempio SSR) non sono supportate e vengono lasciate come esercizio per l'utente.</span><span class="sxs-lookup"><span data-stu-id="6a718-162">As such, scenarios and features not included in a "create-react-app" project (such as SSR) are not supported and are left as an exercise for the user.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a718-163">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6a718-163">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
