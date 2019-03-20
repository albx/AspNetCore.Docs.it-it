---
title: Eseguire la migrazione da ASP.NET ad ASP.NET Core
author: isaac2004
description: Indicazioni sulla migrazione di app ASP.NET MVC o Web API esistenti ad ASP.NET Core.web
ms.author: scaddie
ms.date: 12/11/2018
uid: migration/proper-to-2x/index
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="6fc0a-103">Eseguire la migrazione da ASP.NET ad ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6fc0a-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="6fc0a-104">Di [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="6fc0a-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="6fc0a-105">Questo articolo offre una guida di riferimento per la migrazione delle app ASP.NET ad ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6fc0a-106">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="6fc0a-106">Prerequisites</span></span>

[<span data-ttu-id="6fc0a-107">.NET Core SDK 2.2 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="6fc0a-107">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download)

## <a name="target-frameworks"></a><span data-ttu-id="6fc0a-108">Framework di destinazione</span><span class="sxs-lookup"><span data-stu-id="6fc0a-108">Target frameworks</span></span>

<span data-ttu-id="6fc0a-109">I progetti di ASP.NET Core offrono agli sviluppatori la flessibilità necessaria per scegliere .NET Core, .NET Framework o entrambi come destinazione.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-109">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="6fc0a-110">Vedere [Scelta di .NET Core o .NET Framework per le app server](/dotnet/standard/choosing-core-framework-server) per determinare quale framework di destinazione è più appropriato.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-110">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="6fc0a-111">Quando la destinazione è .NET Framework, i progetti devono fare riferimento a singoli pacchetti NuGet.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-111">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="6fc0a-112">La scelta di .NET Core come destinazione consente di eliminare numerosi riferimenti espliciti ai pacchetti, grazie al [metapacchetto](xref:fundamentals/metapackage-app) di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-112">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="6fc0a-113">Installare il metapacchetto `Microsoft.AspNetCore.App` nel progetto:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-113">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="6fc0a-114">Quando si usa il metapacchetto, con l'app non viene distribuito alcun pacchetto a cui si fa riferimento nel metapacchetto.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-114">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="6fc0a-115">L'archivio di runtime di .NET Core include questi asset, che vengono precompilati per migliorare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-115">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="6fc0a-116">Per altri dettagli, vedere [Metapacchetto Microsoft.AspNetCore.All per ASP.NET Core](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="6fc0a-116">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="6fc0a-117">Differenze di struttura del progetto</span><span class="sxs-lookup"><span data-stu-id="6fc0a-117">Project structure differences</span></span>

<span data-ttu-id="6fc0a-118">Il formato di file *CSPROJ* è stato semplificato in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-118">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="6fc0a-119">Alcune modifiche importanti includono:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-119">Some notable changes include:</span></span>

- <span data-ttu-id="6fc0a-120">L'inclusione esplicita dei file non è necessaria affinché i file vengano considerati parte del progetto.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-120">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="6fc0a-121">In questo modo si riduce il rischio di conflitti di merge XML quando si lavora con team di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-121">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="6fc0a-122">Non sono presenti riferimenti basati su GUID ad altri progetti e questo migliora la leggibilità dei file.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-122">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="6fc0a-123">Il file può essere modificato senza scaricarlo in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-123">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Modificare l'opzione CSPROJ del menu di scelta rapida in Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="6fc0a-125">Sostituzione di file Global.asax</span><span class="sxs-lookup"><span data-stu-id="6fc0a-125">Global.asax file replacement</span></span>

<span data-ttu-id="6fc0a-126">In ASP.NET Core è stato introdotto un nuovo meccanismo per l'avvio automatico delle app.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-126">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="6fc0a-127">Il punto di ingresso per le applicazioni ASP.NET è il file *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-127">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="6fc0a-128">Attività quali la configurazione della route e le registrazioni di area e filtro vengono gestite nel file *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-128">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="6fc0a-129">Con questo approccio l'applicazione e il server a cui viene distribuita vengono accoppiati in un modo che interferisce con l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-129">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="6fc0a-130">Al fine di disaccoppiare gli elementi, è stata introdotta la funzionalità [OWIN](http://owin.org/) che offre un modo più semplice di usare più framework insieme.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-130">In an effort to decouple, [OWIN](http://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="6fc0a-131">OWIN offre una pipeline per aggiungere solo i moduli necessari.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-131">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="6fc0a-132">L'ambiente host accetta una funzione di [avvio](xref:fundamentals/startup) per configurare i servizi e la pipeline delle richieste dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-132">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="6fc0a-133">`Startup` registra un set di middleware con l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-133">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="6fc0a-134">Per ogni richiesta, l'applicazione chiama ognuno dei componenti middleware con il puntatore iniziale di un elenco collegato a un set esistente di gestori.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-134">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="6fc0a-135">Ogni componente middleware può aggiungere uno o più gestori alla pipeline di gestione delle richieste.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-135">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="6fc0a-136">Questa operazione viene eseguita restituendo un riferimento al gestore che rappresenta il nuovo inizio dell'elenco.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-136">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="6fc0a-137">Ogni gestore è responsabile della memorizzazione e della chiamata del gestore successivo nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-137">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="6fc0a-138">Con ASP.NET Core, il punto di ingresso a un'applicazione è `Startup` e non esiste più una dipendenza da *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-138">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="6fc0a-139">Quando si usa OWIN con .NET Framework, usare una pipeline simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-139">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="6fc0a-140">Ciò consente di configurare le route predefinite e impostare come predefinito XmlSerialization per Json.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-140">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="6fc0a-141">Aggiungere altro middleware a questa pipeline in base alle esigenze, ad esempio caricamento di servizi, impostazioni di configurazione, file statici e così via.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-141">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="6fc0a-142">ASP.NET Core usa un approccio simile, ma non si basa su OWIN per gestire la voce.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-142">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="6fc0a-143">L'operazione viene invece eseguita usando il metodo *Program.cs* `Main` (simile alle applicazioni console) e `Startup` viene caricato da tale posizione.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-143">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="6fc0a-144">`Startup` deve includere un metodo `Configure`.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-144">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="6fc0a-145">In `Configure` aggiungere il middleware necessario alla pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-145">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="6fc0a-146">Nell'esempio seguente, estratto dal modello di sito Web predefinito, i metodi di estensione configurano la pipeline con il supporto per:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-146">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="6fc0a-147">Pagine di errore</span><span class="sxs-lookup"><span data-stu-id="6fc0a-147">Error pages</span></span>
- <span data-ttu-id="6fc0a-148">Protocollo HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="6fc0a-148">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="6fc0a-149">Reindirizzamento HTTP a HTTPS</span><span class="sxs-lookup"><span data-stu-id="6fc0a-149">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="6fc0a-150">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="6fc0a-150">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="6fc0a-151">L'host e applicazione sono stati disaccoppiati e questo offre la possibilità di passare a una piattaforma diversa in futuro.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="6fc0a-152">Per un riferimento più completo relativo all'avvio e al middleware di ASP.NET Core, vedere [Startup in ASP.NET Core](xref:fundamentals/startup) (Operazioni iniziali in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="6fc0a-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="6fc0a-153">Configurazioni di archiviazione</span><span class="sxs-lookup"><span data-stu-id="6fc0a-153">Store configurations</span></span>

<span data-ttu-id="6fc0a-154">ASP.NET supporta le impostazioni di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="6fc0a-155">Tali impostazioni vengono usate, ad esempio, per supportare l'ambiente in cui vengono distribuite le applicazioni.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="6fc0a-156">Una prassi comune era archiviare tutte le coppie chiave-valore personalizzate nella sezione `<appSettings>` del file *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="6fc0a-157">Le applicazioni leggono queste impostazioni usando la raccolta `ConfigurationManager.AppSettings` nello spazio dei nomi `System.Configuration`:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="6fc0a-158">ASP.NET Core è in grado di archiviare i dati di configurazione per l'applicazione in tutti i file e di caricarli come parte dell'avvio automatico del middleware.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="6fc0a-159">Il file predefinito usato nei modelli di progetto è *appSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-159">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="6fc0a-160">Il caricamento del file in un'istanza di `IConfiguration` all'interno dell'applicazione viene eseguito in *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="6fc0a-161">L'app legge da `Configuration` per ottenere le impostazioni:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="6fc0a-162">Sono disponibili estensioni di questo approccio che aumentano l'efficacia del processo, ad esempio l'uso dell'[inserimento delle dipendenze](xref:fundamentals/dependency-injection) per caricare un servizio con questi valori.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="6fc0a-163">L'approccio con inserimento delle dipendenze offre un set fortemente tipizzato di oggetti di configurazione.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

````csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
````

> [!NOTE]
> <span data-ttu-id="6fc0a-164">Per informazioni più dettagliate sulla configurazione di ASP.NET Core, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6fc0a-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="6fc0a-165">Inserimento delle dipendenze nativo</span><span class="sxs-lookup"><span data-stu-id="6fc0a-165">Native dependency injection</span></span>

<span data-ttu-id="6fc0a-166">Un obiettivo importante nella compilazione di applicazioni scalabili di grandi dimensioni è l'accoppiamento libero di componenti e servizi.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="6fc0a-167">L'[inserimento delle dipendenze](xref:fundamentals/dependency-injection) è una tecnica comune che consente di raggiungerlo ed è un componente nativo di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="6fc0a-168">Nelle app ASP.NET gli sviluppatori si affidano a una libreria di terze parti per implementare l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="6fc0a-169">Una di queste librerie è [Unity](https://github.com/unitycontainer/unity) e fa parte di Modelli e procedure Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="6fc0a-170">Un esempio di configurazione dell'inserimento delle dipendenze con Unity è l'implementazione di `IDependencyResolver` che esegue il wrapping di un oggetto `UnityContainer`:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="6fc0a-171">Creare un'istanza di `UnityContainer`, registrare il servizio e impostare il sistema di risoluzione delle dipendenze di `HttpConfiguration` sulla nuova istanza di `UnityResolver` per il contenitore:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="6fc0a-172">Inserire `IProductRepository` dove necessario:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="6fc0a-173">Poiché l'inserimento delle dipendenze fa parte di ASP.NET Core, è possibile aggiungere il servizio nel metodo `ConfigureServices` di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="6fc0a-174">Il repository può essere inserito in qualsiasi posizione, analogamente a Unity.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="6fc0a-175">Per altre informazioni sull'inserimento delle dipendenze, vedere [Inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6fc0a-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="6fc0a-176">Usare i file statici</span><span class="sxs-lookup"><span data-stu-id="6fc0a-176">Serve static files</span></span>

<span data-ttu-id="6fc0a-177">Una parte importante dello sviluppo Web è la possibilità di distribuire asset statici sul lato client.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="6fc0a-178">Gli esempi più comuni di file statici sono HTML, CSS, Javascript e immagini.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="6fc0a-179">Questi file devono essere salvati nella posizione di pubblicazione dell'app (o della rete CDN) con riferimenti che ne consentano il caricamento da parte di una richiesta.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="6fc0a-180">Questo processo è stato modificato in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="6fc0a-181">In ASP.NET i file statici vengono archiviati in directory diverse e viene fatto riferimento ai file nelle viste.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="6fc0a-182">In ASP.NET Core i file statici vengono archiviati nella "radice Web" (*&lt;radice contenuto&gt;/wwwroot*), a meno che la configurazione non sia diversa.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-182">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="6fc0a-183">I file vengono caricati nella pipeline delle richieste chiamando il metodo di estensione `UseStaticFiles` da `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="6fc0a-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="6fc0a-184">Se la destinazione è .NET Framework, installare il pacchetto NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="6fc0a-185">Ad esempio, un asset immagine nella cartella *wwwroot/images* è accessibile al browser in corrispondenza di una posizione come `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="6fc0a-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="6fc0a-186">Per informazioni più dettagliate sulla gestione dei file statici in ASP.NET Core, vedere [File statici](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="6fc0a-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6fc0a-187">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6fc0a-187">Additional resources</span></span>

- [<span data-ttu-id="6fc0a-188">Portabilità in .NET Core - Librerie</span><span class="sxs-lookup"><span data-stu-id="6fc0a-188">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
