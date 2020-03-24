---
title: Host generico .NET
author: rick-anderson
description: Informazioni sull'host generico .NET Core, che gestisce l'avvio e la durata delle app.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 0f8f03dabf65f2cbfe4c41d36b02a25d7902cefb
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219220"
---
# <a name="net-generic-host"></a><span data-ttu-id="9f699-103">Host generico .NET</span><span class="sxs-lookup"><span data-stu-id="9f699-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9f699-104">Questo articolo introduce l'host generico .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) e fornisce indicazioni su come usarlo.</span><span class="sxs-lookup"><span data-stu-id="9f699-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="9f699-105">Che cos'è un host?</span><span class="sxs-lookup"><span data-stu-id="9f699-105">What's a host?</span></span>

<span data-ttu-id="9f699-106">Un *host* è un oggetto che incapsula le risorse di un'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9f699-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="9f699-107">Inserimento di dipendenze (DI)</span><span class="sxs-lookup"><span data-stu-id="9f699-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="9f699-108">Registrazione</span><span class="sxs-lookup"><span data-stu-id="9f699-108">Logging</span></span>
* <span data-ttu-id="9f699-109">Configurazione</span><span class="sxs-lookup"><span data-stu-id="9f699-109">Configuration</span></span>
* <span data-ttu-id="9f699-110">`IHostedService` Implementazioni</span><span class="sxs-lookup"><span data-stu-id="9f699-110">`IHostedService` implementations</span></span>

<span data-ttu-id="9f699-111">Quando un host viene avviato, chiama `IHostedService.StartAsync` in ogni implementazione di <xref:Microsoft.Extensions.Hosting.IHostedService> che trova nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="9f699-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="9f699-112">In un'app Web, una delle implementazioni di `IHostedService` è un servizio web che avvia un'[implementazione del server HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="9f699-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="9f699-113">Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.</span><span class="sxs-lookup"><span data-stu-id="9f699-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="9f699-114">Nelle versioni di ASP.NET Core precedenti alla 3.0, il [Web Host](xref:fundamentals/host/web-host) viene usato per i carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f699-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="9f699-115">L'host Web non è più consigliato per le app Web e resta disponibile solo per compatibilità con le versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="9f699-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9f699-116">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="9f699-116">Set up a host</span></span>

<span data-ttu-id="9f699-117">L'host è in genere configurato, compilato ed eseguito da codice nella classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="9f699-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="9f699-118">Il metodo `Main`:</span><span class="sxs-lookup"><span data-stu-id="9f699-118">The `Main` method:</span></span>

* <span data-ttu-id="9f699-119">Chiama un metodo `CreateHostBuilder` per creare e configurare un oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="9f699-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="9f699-120">Chiamate `Build` e metodi `Run` nell'oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="9f699-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="9f699-121">Di seguito è riportato il codice *Program.cs* per un carico di lavoro non HTTP, con un'unica implementazione di `IHostedService` aggiunta al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="9f699-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="9f699-122">Per un carico di lavoro HTTP, il metodo `Main` è lo stesso ma `CreateHostBuilder` chiama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="9f699-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="9f699-123">Se l'app usa Entity Framework Core, non modificare il nome o la firma del metodo `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9f699-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="9f699-124">Gli [strumenti di Entity Framework Core](/ef/core/miscellaneous/cli/) si aspettano di trovare un metodo `CreateHostBuilder` che configura l'host senza eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="9f699-125">Per altre informazioni, vedere [Creazione DbContext in fase di progettazione](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="9f699-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="9f699-126">Impostazioni predefinite del generatore</span><span class="sxs-lookup"><span data-stu-id="9f699-126">Default builder settings</span></span>

<span data-ttu-id="9f699-127">Il metodo <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="9f699-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="9f699-128">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) sul percorso restituito da <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="9f699-129">Carica la configurazione dell'host da:</span><span class="sxs-lookup"><span data-stu-id="9f699-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="9f699-130">Le variabili di ambiente precedute dal prefisso `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="9f699-130">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="9f699-131">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="9f699-131">Command-line arguments.</span></span>
* <span data-ttu-id="9f699-132">Carica la configurazione dell'app da:</span><span class="sxs-lookup"><span data-stu-id="9f699-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="9f699-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9f699-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="9f699-134">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="9f699-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="9f699-135">[Segreti del manager](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="9f699-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="9f699-136">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="9f699-136">Environment variables.</span></span>
  * <span data-ttu-id="9f699-137">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="9f699-137">Command-line arguments.</span></span>
* <span data-ttu-id="9f699-138">Aggiunge i provider di [log](xref:fundamentals/logging/index) seguenti:</span><span class="sxs-lookup"><span data-stu-id="9f699-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="9f699-139">Console</span><span class="sxs-lookup"><span data-stu-id="9f699-139">Console</span></span>
  * <span data-ttu-id="9f699-140">Debug</span><span class="sxs-lookup"><span data-stu-id="9f699-140">Debug</span></span>
  * <span data-ttu-id="9f699-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="9f699-141">EventSource</span></span>
  * <span data-ttu-id="9f699-142">EventLog (solo quando è in esecuzione su Windows)</span><span class="sxs-lookup"><span data-stu-id="9f699-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="9f699-143">Abilita la [convalida dell'ambito](xref:fundamentals/dependency-injection#scope-validation) e la [convalida delle dipendenze](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando l'ambiente è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="9f699-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="9f699-144">Il metodo `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="9f699-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="9f699-145">Carica la configurazione host dalle variabili di ambiente precedute dal prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="9f699-145">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="9f699-146">Imposta il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web e lo configura usando i provider di configurazione dell'host dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="9f699-147">Per le opzioni predefinite del server Kestrel, vedere <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="9f699-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="9f699-148">Aggiunge [il middleware dell'applicazione di filtri dell'host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="9f699-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="9f699-149">Aggiunge il [middleware delle intestazioni con inoltri](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è uguale a `true`.</span><span class="sxs-lookup"><span data-stu-id="9f699-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="9f699-150">Abilita l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="9f699-150">Enables IIS integration.</span></span> <span data-ttu-id="9f699-151">Per le opzioni predefinite di IIS, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="9f699-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="9f699-152">Le sezioni [Impostazioni per tutti i tipi di app](#settings-for-all-app-types) e [Impostazioni per le app Web](#settings-for-web-apps) più avanti in questo articolo mostrano come eseguire l'override delle impostazioni predefinite del generatore.</span><span class="sxs-lookup"><span data-stu-id="9f699-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="9f699-153">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="9f699-153">Framework-provided services</span></span>

<span data-ttu-id="9f699-154">I servizi seguenti vengono registrati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="9f699-154">The following services are registered automatically:</span></span>

* [<span data-ttu-id="9f699-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="9f699-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="9f699-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="9f699-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="9f699-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="9f699-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="9f699-158">Per ulteriori informazioni sui servizi forniti dal Framework, vedere <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="9f699-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="9f699-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="9f699-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="9f699-160">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (in precedenza `IApplicationLifetime`) in qualsiasi classe per gestire le attività post-avvio e di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="9f699-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="9f699-161">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi del gestore dell'evento di avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="9f699-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="9f699-162">L'interfaccia include anche un metodo `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="9f699-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="9f699-163">L'esempio seguente è un'implementazione di `IHostedService` che registra eventi `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="9f699-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="9f699-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="9f699-164">IHostLifetime</span></span>

<span data-ttu-id="9f699-165">L'implementazione <xref:Microsoft.Extensions.Hosting.IHostLifetime> controlla quando l'host viene avviato e quando si arresta.</span><span class="sxs-lookup"><span data-stu-id="9f699-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="9f699-166">Viene usata l'ultima implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="9f699-166">The last implementation registered is used.</span></span>

<span data-ttu-id="9f699-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è l'implementazione `IHostLifetime` predefinita.</span><span class="sxs-lookup"><span data-stu-id="9f699-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="9f699-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="9f699-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="9f699-169">Ascolta la <kbd>combinazione di tasti Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="9f699-169">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="9f699-170">Sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="9f699-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="9f699-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="9f699-171">IHostEnvironment</span></span>

<span data-ttu-id="9f699-172">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostEnvironment> in una classe per ottenere informazioni sulle impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="9f699-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="9f699-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9f699-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="9f699-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9f699-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="9f699-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9f699-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="9f699-176">Le app Web implementano l'interfaccia `IWebHostEnvironment`, che eredita `IHostEnvironment` e aggiunge [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="9f699-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9f699-177">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="9f699-177">Host configuration</span></span>

<span data-ttu-id="9f699-178">La configurazione dell'host viene usata per le proprietà dell'implementazione <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="9f699-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="9f699-179">La configurazione dell'host è disponibile da [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) all'interno di <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="9f699-180">Dopo `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` viene sostituito con la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="9f699-181">Per aggiungere la configurazione dell'host, chiamare <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9f699-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9f699-182">`ConfigureHostConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="9f699-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9f699-183">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="9f699-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9f699-184">Il provider di variabili di ambiente con prefisso `DOTNET_` e argomenti della riga di comando sono inclusi `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9f699-184">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9f699-185">Per le app Web, viene aggiunto il provider di variabili di ambiente con prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="9f699-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="9f699-186">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="9f699-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9f699-187">Ad esempio, il valore della variabile di ambiente per `ASPNETCORE_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="9f699-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9f699-188">L'esempio seguente crea la configurazione host:</span><span class="sxs-lookup"><span data-stu-id="9f699-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="9f699-189">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="9f699-189">App configuration</span></span>

<span data-ttu-id="9f699-190">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9f699-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9f699-191">`ConfigureAppConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="9f699-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9f699-192">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="9f699-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="9f699-193">La configurazione creata da `ConfigureAppConfiguration` è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e come servizio da DI.</span><span class="sxs-lookup"><span data-stu-id="9f699-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="9f699-194">La configurazione dell'host viene aggiunta anche alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="9f699-195">Per altre informazioni, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="9f699-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="9f699-196">Impostazioni per tutti i tipi di app</span><span class="sxs-lookup"><span data-stu-id="9f699-196">Settings for all app types</span></span>

<span data-ttu-id="9f699-197">Questa sezione elenca le impostazioni dell'host che si applicano ai carichi di lavoro HTTP e non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f699-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="9f699-198">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="9f699-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="9f699-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9f699-199">ApplicationName</span></span>

<span data-ttu-id="9f699-200">La proprietà [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="9f699-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="9f699-201">**Chiave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="9f699-201">**Key**: `applicationName`</span></span>  
<span data-ttu-id="9f699-202">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-202">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-203">**Impostazione predefinita**: il nome dell'assembly che contiene il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="9f699-204">**Variabile di ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="9f699-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="9f699-205">per impostare questo valore usare la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="9f699-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="9f699-206">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9f699-206">ContentRootPath</span></span>

<span data-ttu-id="9f699-207">La proprietà [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina il punto in cui l'host inizia a cercare i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="9f699-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="9f699-208">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="9f699-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="9f699-209">**Chiave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="9f699-209">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="9f699-210">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-210">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-211">**Impostazione predefinita**: la cartella in cui risiede l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="9f699-212">**Variabile di ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="9f699-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="9f699-213">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseContentRoot` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="9f699-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="9f699-214">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="9f699-214">For more information, see:</span></span>

* [<span data-ttu-id="9f699-215">Nozioni fondamentali: radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="9f699-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="9f699-216">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9f699-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="9f699-217">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9f699-217">EnvironmentName</span></span>

<span data-ttu-id="9f699-218">La proprietà [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) può essere impostata su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="9f699-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="9f699-219">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="9f699-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9f699-220">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="9f699-220">Values aren't case-sensitive.</span></span>

<span data-ttu-id="9f699-221">**Chiave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="9f699-221">**Key**: `environment`</span></span>  
<span data-ttu-id="9f699-222">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-222">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-223">**Default**: `Production`</span><span class="sxs-lookup"><span data-stu-id="9f699-223">**Default**: `Production`</span></span>  
<span data-ttu-id="9f699-224">**Variabile di ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="9f699-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="9f699-225">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseEnvironment` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="9f699-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="9f699-226">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="9f699-226">ShutdownTimeout</span></span>

<span data-ttu-id="9f699-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9f699-228">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="9f699-228">The default value is five seconds.</span></span>  <span data-ttu-id="9f699-229">Durante il periodo di timeout, l'host:</span><span class="sxs-lookup"><span data-stu-id="9f699-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="9f699-230">attiva [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="9f699-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="9f699-231">Tenta di arrestare i servizi ospitati, registrando gli errori per i servizi che non si interrompono.</span><span class="sxs-lookup"><span data-stu-id="9f699-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="9f699-232">Se il periodo di timeout scade prima che siano stati arrestati tutti i servizi ospitati, gli eventuali servizi attivi rimanenti vengono interrotti quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="9f699-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="9f699-233">I servizi si arrestano anche se non hanno completato l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="9f699-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="9f699-234">Se l'arresto dei servizi richiede più tempo, aumentare il valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="9f699-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="9f699-235">**Chiave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="9f699-235">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="9f699-236">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="9f699-236">**Type**: `int`</span></span>  
<span data-ttu-id="9f699-237">**Impostazione predefinita**: 5 secondi</span><span class="sxs-lookup"><span data-stu-id="9f699-237">**Default**: 5 seconds</span></span>  
<span data-ttu-id="9f699-238">**Variabile di ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="9f699-238">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="9f699-239">Per impostare questo valore, usare la variabile di ambiente o configurare `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="9f699-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="9f699-240">Nell'esempio seguente il timeout viene impostato su 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="9f699-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="9f699-241">Disabilitare il ricaricamento della configurazione dell'app durante la modifica</span><span class="sxs-lookup"><span data-stu-id="9f699-241">Disable app configuration reload on change</span></span>

<span data-ttu-id="9f699-242">Per [impostazione predefinita](xref:fundamentals/configuration/index#default), *appSettings. JSON* e *appSettings. { L'ambiente}. JSON* viene ricaricato quando il file viene modificato.</span><span class="sxs-lookup"><span data-stu-id="9f699-242">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="9f699-243">Per disabilitare questo comportamento di ricaricamento in ASP.NET Core 5,0 Preview 3 o versione successiva, impostare la chiave di `hostBuilder:reloadConfigOnChange` su `false`.</span><span class="sxs-lookup"><span data-stu-id="9f699-243">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="9f699-244">**Chiave**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="9f699-244">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="9f699-245">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="9f699-245">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9f699-246">**Default**: `true`</span><span class="sxs-lookup"><span data-stu-id="9f699-246">**Default**: `true`</span></span>  
<span data-ttu-id="9f699-247">**Argomento della riga di comando**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="9f699-247">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="9f699-248">**Variabile di ambiente**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="9f699-248">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="9f699-249">Il separatore dei due punti (`:`) non funziona con le chiavi gerarchiche delle variabili di ambiente in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="9f699-249">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="9f699-250">Per altre informazioni, vedere [variabili di ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="9f699-250">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="9f699-251">Impostazioni per le app Web</span><span class="sxs-lookup"><span data-stu-id="9f699-251">Settings for web apps</span></span>

<span data-ttu-id="9f699-252">Alcune impostazioni host si applicano solo ai carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f699-252">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="9f699-253">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="9f699-253">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="9f699-254">Per queste impostazioni sono disponibili i metodi di estensione su `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9f699-254">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="9f699-255">Gli esempi di codice che illustrano come chiamare i metodi di estensione presumono che `webBuilder` sia un'istanza di `IWebHostBuilder`, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="9f699-255">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="9f699-256">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="9f699-256">CaptureStartupErrors</span></span>

<span data-ttu-id="9f699-257">Quando è `false`, gli errori durante l'avvio causano la chiusura dell'host.</span><span class="sxs-lookup"><span data-stu-id="9f699-257">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="9f699-258">Quando è `true`, l'host acquisisce le eccezioni durante l'avvio e tenta di avviare il server.</span><span class="sxs-lookup"><span data-stu-id="9f699-258">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="9f699-259">**Chiave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="9f699-259">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="9f699-260">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="9f699-260">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9f699-261">**Impostazione predefinita**: il valore predefinito è `false` a meno che l'app non venga eseguita con Kestrel in IIS, in tal caso il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="9f699-261">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="9f699-262">**Variabile di ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="9f699-262">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="9f699-263">Per impostare questo valore, usare la configurazione o chiamare `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="9f699-263">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="9f699-264">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="9f699-264">DetailedErrors</span></span>

<span data-ttu-id="9f699-265">Quando è abilitata o quando l'ambiente è `Development`, l'app acquisisce gli errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="9f699-265">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="9f699-266">**Chiave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="9f699-266">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="9f699-267">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="9f699-267">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9f699-268">**Default**: `false`</span><span class="sxs-lookup"><span data-stu-id="9f699-268">**Default**: `false`</span></span>  
<span data-ttu-id="9f699-269">**Variabile di ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="9f699-269">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="9f699-270">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="9f699-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="9f699-271">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="9f699-271">HostingStartupAssemblies</span></span>

<span data-ttu-id="9f699-272">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da caricare all'avvio.</span><span class="sxs-lookup"><span data-stu-id="9f699-272">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="9f699-273">Sebbene il valore di configurazione predefinito sia una stringa vuota, gli assembly di avvio dell'hosting includono sempre l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-273">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="9f699-274">Quando vengono specificati, gli assembly di avvio dell'hosting vengono aggiunti all'assembly dell'app per essere caricati quando l'app compila i servizi comuni durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="9f699-274">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="9f699-275">**Chiave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9f699-275">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="9f699-276">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-276">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-277">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="9f699-277">**Default**: Empty string</span></span>  
<span data-ttu-id="9f699-278">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9f699-278">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="9f699-279">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="9f699-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="9f699-280">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="9f699-280">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="9f699-281">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da escludere all'avvio.</span><span class="sxs-lookup"><span data-stu-id="9f699-281">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="9f699-282">**Chiave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9f699-282">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="9f699-283">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-283">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-284">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="9f699-284">**Default**: Empty string</span></span>  
<span data-ttu-id="9f699-285">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9f699-285">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="9f699-286">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="9f699-286">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="9f699-287">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="9f699-287">HTTPS_Port</span></span>

<span data-ttu-id="9f699-288">Porta di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9f699-288">The HTTPS redirect port.</span></span> <span data-ttu-id="9f699-289">Usata per [imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="9f699-289">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="9f699-290">**Chiave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="9f699-290">**Key**: `https_port`</span></span>  
<span data-ttu-id="9f699-291">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-291">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-292">**Impostazione predefinita**: un valore predefinito non è impostato.</span><span class="sxs-lookup"><span data-stu-id="9f699-292">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="9f699-293">**Variabile di ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="9f699-293">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="9f699-294">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="9f699-294">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="9f699-295">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="9f699-295">PreferHostingUrls</span></span>

<span data-ttu-id="9f699-296">Indica se l'host deve essere in ascolto sugli URL configurati con il `IWebHostBuilder` anziché gli URL configurati con l'implementazione di `IServer`.</span><span class="sxs-lookup"><span data-stu-id="9f699-296">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="9f699-297">**Chiave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="9f699-297">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="9f699-298">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="9f699-298">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9f699-299">**Default**: `true`</span><span class="sxs-lookup"><span data-stu-id="9f699-299">**Default**: `true`</span></span>  
<span data-ttu-id="9f699-300">**Variabile di ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="9f699-300">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="9f699-301">Per impostare questo valore, usare la variabile di ambiente o chiamare `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="9f699-301">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="9f699-302">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="9f699-302">PreventHostingStartup</span></span>

<span data-ttu-id="9f699-303">Impedisce il caricamento automatico degli assembly di avvio dell'hosting, inclusi gli assembly di avvio dell'hosting configurati dall'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-303">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="9f699-304">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9f699-304">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="9f699-305">**Chiave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="9f699-305">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="9f699-306">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="9f699-306">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9f699-307">**Default**: `false`</span><span class="sxs-lookup"><span data-stu-id="9f699-307">**Default**: `false`</span></span>  
<span data-ttu-id="9f699-308">**Variabile di ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="9f699-308">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="9f699-309">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="9f699-309">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="9f699-310">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="9f699-310">StartupAssembly</span></span>

<span data-ttu-id="9f699-311">L'assembly per la ricerca della classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="9f699-311">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="9f699-312">**Chiave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="9f699-312">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="9f699-313">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-313">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-314">**Impostazione predefinita**: assembly dell'app</span><span class="sxs-lookup"><span data-stu-id="9f699-314">**Default**: The app's assembly</span></span>  
<span data-ttu-id="9f699-315">**Variabile di ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="9f699-315">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="9f699-316">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="9f699-316">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="9f699-317">`UseStartup` può richiedere un nome di assembly (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="9f699-317">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="9f699-318">Se vengono chiamati più metodi `UseStartup`, l'ultimo metodo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="9f699-318">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="9f699-319">URL</span><span class="sxs-lookup"><span data-stu-id="9f699-319">URLs</span></span>

<span data-ttu-id="9f699-320">Elenco delimitato da punto e virgola degli indirizzi IP o gli indirizzi host con le porte e protocolli su cui il server deve eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="9f699-320">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="9f699-321">Ad esempio: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="9f699-321">For example, `http://localhost:123`.</span></span> <span data-ttu-id="9f699-322">Usare "\*" per indicare che il server deve eseguire l'ascolto per le richieste su tutti gli indirizzi IP o nomi host usando la porta e il protocollo specificati (ad esempio, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="9f699-322">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="9f699-323">Il protocollo (`http://` o `https://`) deve essere incluso con ogni URL.</span><span class="sxs-lookup"><span data-stu-id="9f699-323">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="9f699-324">I formati supportati variano a seconda del server.</span><span class="sxs-lookup"><span data-stu-id="9f699-324">Supported formats vary among servers.</span></span>

<span data-ttu-id="9f699-325">**Chiave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="9f699-325">**Key**: `urls`</span></span>  
<span data-ttu-id="9f699-326">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-326">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-327">**Impostazione predefinita**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="9f699-327">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="9f699-328">**Variabile di ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="9f699-328">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="9f699-329">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="9f699-329">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="9f699-330">Kestrel ha una propria API di configurazione degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="9f699-330">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="9f699-331">Per altre informazioni, vedere <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9f699-331">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="9f699-332">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9f699-332">WebRoot</span></span>

<span data-ttu-id="9f699-333">Il percorso relativo degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-333">The relative path to the app's static assets.</span></span>

<span data-ttu-id="9f699-334">**Chiave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="9f699-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="9f699-335">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-335">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-336">**Impostazione predefinita**: il valore predefinito è `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="9f699-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="9f699-337">Il percorso di *{radice del contenuto}/wwwroot* deve esistere.</span><span class="sxs-lookup"><span data-stu-id="9f699-337">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="9f699-338">Se il percorso non esiste, viene usato un provider di file no-op.</span><span class="sxs-lookup"><span data-stu-id="9f699-338">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="9f699-339">**Variabile di ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="9f699-339">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="9f699-340">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseWebRoot`:</span><span class="sxs-lookup"><span data-stu-id="9f699-340">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="9f699-341">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="9f699-341">For more information, see:</span></span>

* [<span data-ttu-id="9f699-342">Nozioni fondamentali: radice Web</span><span class="sxs-lookup"><span data-stu-id="9f699-342">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="9f699-343">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9f699-343">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="9f699-344">Gestire la durata dell'host</span><span class="sxs-lookup"><span data-stu-id="9f699-344">Manage the host lifetime</span></span>

<span data-ttu-id="9f699-345">Chiamare metodi sull'implementazione <xref:Microsoft.Extensions.Hosting.IHost> incorporata per avviare e arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-345">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="9f699-346">Questi metodi influiscono su tutte le implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che vengono registrate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="9f699-346">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9f699-347">Esegui</span><span class="sxs-lookup"><span data-stu-id="9f699-347">Run</span></span>

<span data-ttu-id="9f699-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host.</span><span class="sxs-lookup"><span data-stu-id="9f699-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="9f699-349">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-349">RunAsync</span></span>

<span data-ttu-id="9f699-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="9f699-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="9f699-351">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-351">RunConsoleAsync</span></span>

<span data-ttu-id="9f699-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> Abilita il supporto della console, compila e avvia l'host e attende la chiusura di <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="9f699-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="9f699-353">Inizia</span><span class="sxs-lookup"><span data-stu-id="9f699-353">Start</span></span>

<span data-ttu-id="9f699-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="9f699-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="9f699-355">StartAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-355">StartAsync</span></span>

<span data-ttu-id="9f699-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'host e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="9f699-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="9f699-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di `StartAsync`, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="9f699-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9f699-358">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="9f699-358">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="9f699-359">StopAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-359">StopAsync</span></span>

<span data-ttu-id="9f699-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="9f699-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="9f699-361">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="9f699-361">WaitForShutdown</span></span>

<span data-ttu-id="9f699-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocca il thread chiamante finché l'arresto non viene attivato da IHostLifetime, ad esempio tramite <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="9f699-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="9f699-363">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-363">WaitForShutdownAsync</span></span>

<span data-ttu-id="9f699-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="9f699-365">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="9f699-365">External control</span></span>

<span data-ttu-id="9f699-366">Il controllo diretto della durata dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="9f699-366">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="9f699-367">Questo articolo introduce l'host generico .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) e fornisce indicazioni su come usarlo.</span><span class="sxs-lookup"><span data-stu-id="9f699-367">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="9f699-368">Che cos'è un host?</span><span class="sxs-lookup"><span data-stu-id="9f699-368">What's a host?</span></span>

<span data-ttu-id="9f699-369">Un *host* è un oggetto che incapsula le risorse di un'app, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9f699-369">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="9f699-370">Inserimento di dipendenze (DI)</span><span class="sxs-lookup"><span data-stu-id="9f699-370">Dependency injection (DI)</span></span>
* <span data-ttu-id="9f699-371">Registrazione</span><span class="sxs-lookup"><span data-stu-id="9f699-371">Logging</span></span>
* <span data-ttu-id="9f699-372">Configurazione</span><span class="sxs-lookup"><span data-stu-id="9f699-372">Configuration</span></span>
* <span data-ttu-id="9f699-373">`IHostedService` Implementazioni</span><span class="sxs-lookup"><span data-stu-id="9f699-373">`IHostedService` implementations</span></span>

<span data-ttu-id="9f699-374">Quando un host viene avviato, chiama `IHostedService.StartAsync` in ogni implementazione di <xref:Microsoft.Extensions.Hosting.IHostedService> che trova nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="9f699-374">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="9f699-375">In un'app Web, una delle implementazioni di `IHostedService` è un servizio web che avvia un'[implementazione del server HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="9f699-375">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="9f699-376">Il motivo principale per cui tutte le risorse interdipendenti dell'app sono incluse in un unico oggetto è la gestione del ciclo di vita, vale a dire il controllo sull'avvio dell'app e sull'arresto normale.</span><span class="sxs-lookup"><span data-stu-id="9f699-376">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="9f699-377">Nelle versioni di ASP.NET Core precedenti alla 3.0, il [Web Host](xref:fundamentals/host/web-host) viene usato per i carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f699-377">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="9f699-378">L'host Web non è più consigliato per le app Web e resta disponibile solo per compatibilità con le versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="9f699-378">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9f699-379">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="9f699-379">Set up a host</span></span>

<span data-ttu-id="9f699-380">L'host è in genere configurato, compilato ed eseguito da codice nella classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="9f699-380">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="9f699-381">Il metodo `Main`:</span><span class="sxs-lookup"><span data-stu-id="9f699-381">The `Main` method:</span></span>

* <span data-ttu-id="9f699-382">Chiama un metodo `CreateHostBuilder` per creare e configurare un oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="9f699-382">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="9f699-383">Chiamate `Build` e metodi `Run` nell'oggetto generatore.</span><span class="sxs-lookup"><span data-stu-id="9f699-383">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="9f699-384">Di seguito è riportato il codice *Program.cs* per un carico di lavoro non HTTP, con un'unica implementazione di `IHostedService` aggiunta al contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="9f699-384">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="9f699-385">Per un carico di lavoro HTTP, il metodo `Main` è lo stesso ma `CreateHostBuilder` chiama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="9f699-385">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="9f699-386">Se l'app usa Entity Framework Core, non modificare il nome o la firma del metodo `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9f699-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="9f699-387">Gli [strumenti di Entity Framework Core](/ef/core/miscellaneous/cli/) si aspettano di trovare un metodo `CreateHostBuilder` che configura l'host senza eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="9f699-388">Per altre informazioni, vedere [Creazione DbContext in fase di progettazione](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="9f699-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="9f699-389">Impostazioni predefinite del generatore</span><span class="sxs-lookup"><span data-stu-id="9f699-389">Default builder settings</span></span>

<span data-ttu-id="9f699-390">Il metodo <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="9f699-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="9f699-391">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) sul percorso restituito da <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="9f699-392">Carica la configurazione dell'host da:</span><span class="sxs-lookup"><span data-stu-id="9f699-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="9f699-393">Le variabili di ambiente precedute dal prefisso `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="9f699-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="9f699-394">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="9f699-394">Command-line arguments.</span></span>
* <span data-ttu-id="9f699-395">Carica la configurazione dell'app da:</span><span class="sxs-lookup"><span data-stu-id="9f699-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="9f699-396">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9f699-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="9f699-397">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="9f699-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="9f699-398">[Segreti del manager](xref:security/app-secrets) quando l'app viene eseguita nell'ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="9f699-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="9f699-399">Variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="9f699-399">Environment variables.</span></span>
  * <span data-ttu-id="9f699-400">Argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="9f699-400">Command-line arguments.</span></span>
* <span data-ttu-id="9f699-401">Aggiunge i provider di [log](xref:fundamentals/logging/index) seguenti:</span><span class="sxs-lookup"><span data-stu-id="9f699-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="9f699-402">Console</span><span class="sxs-lookup"><span data-stu-id="9f699-402">Console</span></span>
  * <span data-ttu-id="9f699-403">Debug</span><span class="sxs-lookup"><span data-stu-id="9f699-403">Debug</span></span>
  * <span data-ttu-id="9f699-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="9f699-404">EventSource</span></span>
  * <span data-ttu-id="9f699-405">EventLog (solo quando è in esecuzione su Windows)</span><span class="sxs-lookup"><span data-stu-id="9f699-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="9f699-406">Abilita la [convalida dell'ambito](xref:fundamentals/dependency-injection#scope-validation) e la [convalida delle dipendenze](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando l'ambiente è lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="9f699-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="9f699-407">Il metodo `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="9f699-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="9f699-408">Carica la configurazione host dalle variabili di ambiente precedute dal prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="9f699-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="9f699-409">Imposta il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web e lo configura usando i provider di configurazione dell'host dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="9f699-410">Per le opzioni predefinite del server Kestrel, vedere <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="9f699-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="9f699-411">Aggiunge [il middleware dell'applicazione di filtri dell'host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="9f699-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="9f699-412">Aggiunge il [middleware delle intestazioni con inoltri](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è uguale a `true`.</span><span class="sxs-lookup"><span data-stu-id="9f699-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="9f699-413">Abilita l'integrazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="9f699-413">Enables IIS integration.</span></span> <span data-ttu-id="9f699-414">Per le opzioni predefinite di IIS, vedere <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="9f699-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="9f699-415">Le sezioni [Impostazioni per tutti i tipi di app](#settings-for-all-app-types) e [Impostazioni per le app Web](#settings-for-web-apps) più avanti in questo articolo mostrano come eseguire l'override delle impostazioni predefinite del generatore.</span><span class="sxs-lookup"><span data-stu-id="9f699-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="9f699-416">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="9f699-416">Framework-provided services</span></span>

<span data-ttu-id="9f699-417">I servizi seguenti vengono registrati automaticamente:</span><span class="sxs-lookup"><span data-stu-id="9f699-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="9f699-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="9f699-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="9f699-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="9f699-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="9f699-420">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="9f699-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="9f699-421">Per ulteriori informazioni sui servizi forniti dal Framework, vedere <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="9f699-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="9f699-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="9f699-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="9f699-423">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (in precedenza `IApplicationLifetime`) in qualsiasi classe per gestire le attività post-avvio e di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="9f699-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="9f699-424">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi del gestore dell'evento di avvio e di arresto.</span><span class="sxs-lookup"><span data-stu-id="9f699-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="9f699-425">L'interfaccia include anche un metodo `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="9f699-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="9f699-426">L'esempio seguente è un'implementazione di `IHostedService` che registra eventi `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="9f699-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="9f699-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="9f699-427">IHostLifetime</span></span>

<span data-ttu-id="9f699-428">L'implementazione <xref:Microsoft.Extensions.Hosting.IHostLifetime> controlla quando l'host viene avviato e quando si arresta.</span><span class="sxs-lookup"><span data-stu-id="9f699-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="9f699-429">Viene usata l'ultima implementazione registrata.</span><span class="sxs-lookup"><span data-stu-id="9f699-429">The last implementation registered is used.</span></span>

<span data-ttu-id="9f699-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è l'implementazione `IHostLifetime` predefinita.</span><span class="sxs-lookup"><span data-stu-id="9f699-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="9f699-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="9f699-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="9f699-432">Ascolta la <kbd>combinazione di tasti Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="9f699-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="9f699-433">Sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="9f699-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="9f699-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="9f699-434">IHostEnvironment</span></span>

<span data-ttu-id="9f699-435">Inserire il servizio <xref:Microsoft.Extensions.Hosting.IHostEnvironment> in una classe per ottenere informazioni sulle impostazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="9f699-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="9f699-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9f699-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="9f699-437">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9f699-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="9f699-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9f699-438">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="9f699-439">Le app Web implementano l'interfaccia `IWebHostEnvironment`, che eredita `IHostEnvironment` e aggiunge [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="9f699-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9f699-440">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="9f699-440">Host configuration</span></span>

<span data-ttu-id="9f699-441">La configurazione dell'host viene usata per le proprietà dell'implementazione <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="9f699-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="9f699-442">La configurazione dell'host è disponibile da [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) all'interno di <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="9f699-443">Dopo `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` viene sostituito con la configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="9f699-444">Per aggiungere la configurazione dell'host, chiamare <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9f699-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9f699-445">`ConfigureHostConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="9f699-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9f699-446">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="9f699-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9f699-447">Il provider di variabili di ambiente con prefisso `DOTNET_` e argomenti della riga di comando sono inclusi `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9f699-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9f699-448">Per le app Web, viene aggiunto il provider di variabili di ambiente con prefisso `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="9f699-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="9f699-449">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="9f699-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9f699-450">Ad esempio, il valore della variabile di ambiente per `ASPNETCORE_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="9f699-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9f699-451">L'esempio seguente crea la configurazione host:</span><span class="sxs-lookup"><span data-stu-id="9f699-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="9f699-452">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="9f699-452">App configuration</span></span>

<span data-ttu-id="9f699-453">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> su `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9f699-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9f699-454">`ConfigureAppConfiguration` può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="9f699-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9f699-455">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="9f699-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="9f699-456">La configurazione creata da `ConfigureAppConfiguration` è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e come servizio da DI.</span><span class="sxs-lookup"><span data-stu-id="9f699-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="9f699-457">La configurazione dell'host viene aggiunta anche alla configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="9f699-458">Per altre informazioni, vedere [Configurazione in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="9f699-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="9f699-459">Impostazioni per tutti i tipi di app</span><span class="sxs-lookup"><span data-stu-id="9f699-459">Settings for all app types</span></span>

<span data-ttu-id="9f699-460">Questa sezione elenca le impostazioni dell'host che si applicano ai carichi di lavoro HTTP e non-HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f699-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="9f699-461">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="9f699-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="9f699-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9f699-462">ApplicationName</span></span>

<span data-ttu-id="9f699-463">La proprietà [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="9f699-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="9f699-464">**Chiave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="9f699-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="9f699-465">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-465">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-466">**Impostazione predefinita**: il nome dell'assembly che contiene il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="9f699-467">**Variabile di ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="9f699-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="9f699-468">per impostare questo valore usare la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="9f699-468">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="9f699-469">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9f699-469">ContentRootPath</span></span>

<span data-ttu-id="9f699-470">La proprietà [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina il punto in cui l'host inizia a cercare i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="9f699-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="9f699-471">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="9f699-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="9f699-472">**Chiave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="9f699-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="9f699-473">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-473">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-474">**Impostazione predefinita**: la cartella in cui risiede l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="9f699-475">**Variabile di ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="9f699-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="9f699-476">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseContentRoot` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="9f699-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="9f699-477">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="9f699-477">For more information, see:</span></span>

* [<span data-ttu-id="9f699-478">Nozioni fondamentali: radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="9f699-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="9f699-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9f699-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="9f699-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9f699-480">EnvironmentName</span></span>

<span data-ttu-id="9f699-481">La proprietà [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) può essere impostata su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="9f699-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="9f699-482">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="9f699-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9f699-483">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="9f699-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="9f699-484">**Chiave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="9f699-484">**Key**: `environment`</span></span>  
<span data-ttu-id="9f699-485">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-485">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-486">**Default**: `Production`</span><span class="sxs-lookup"><span data-stu-id="9f699-486">**Default**: `Production`</span></span>  
<span data-ttu-id="9f699-487">**Variabile di ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="9f699-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="9f699-488">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseEnvironment` su `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="9f699-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="9f699-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="9f699-489">ShutdownTimeout</span></span>

<span data-ttu-id="9f699-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9f699-491">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="9f699-491">The default value is five seconds.</span></span>  <span data-ttu-id="9f699-492">Durante il periodo di timeout, l'host:</span><span class="sxs-lookup"><span data-stu-id="9f699-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="9f699-493">attiva [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="9f699-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="9f699-494">Tenta di arrestare i servizi ospitati, registrando gli errori per i servizi che non si interrompono.</span><span class="sxs-lookup"><span data-stu-id="9f699-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="9f699-495">Se il periodo di timeout scade prima che siano stati arrestati tutti i servizi ospitati, gli eventuali servizi attivi rimanenti vengono interrotti quando l'app viene arrestata.</span><span class="sxs-lookup"><span data-stu-id="9f699-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="9f699-496">I servizi si arrestano anche se non hanno completato l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="9f699-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="9f699-497">Se l'arresto dei servizi richiede più tempo, aumentare il valore di timeout.</span><span class="sxs-lookup"><span data-stu-id="9f699-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="9f699-498">**Chiave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="9f699-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="9f699-499">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="9f699-499">**Type**: `int`</span></span>  
<span data-ttu-id="9f699-500">**Impostazione predefinita**: 5 secondi</span><span class="sxs-lookup"><span data-stu-id="9f699-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="9f699-501">**Variabile di ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="9f699-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="9f699-502">Per impostare questo valore, usare la variabile di ambiente o configurare `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="9f699-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="9f699-503">Nell'esempio seguente il timeout viene impostato su 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="9f699-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="9f699-504">Impostazioni per le app Web</span><span class="sxs-lookup"><span data-stu-id="9f699-504">Settings for web apps</span></span>

<span data-ttu-id="9f699-505">Alcune impostazioni host si applicano solo ai carichi di lavoro HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f699-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="9f699-506">Per impostazione predefinita, le variabili di ambiente usate per configurare queste impostazioni possono avere un prefisso `DOTNET_` o `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="9f699-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="9f699-507">Per queste impostazioni sono disponibili i metodi di estensione su `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="9f699-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="9f699-508">Gli esempi di codice che illustrano come chiamare i metodi di estensione presumono che `webBuilder` sia un'istanza di `IWebHostBuilder`, come nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="9f699-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="9f699-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="9f699-509">CaptureStartupErrors</span></span>

<span data-ttu-id="9f699-510">Quando è `false`, gli errori durante l'avvio causano la chiusura dell'host.</span><span class="sxs-lookup"><span data-stu-id="9f699-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="9f699-511">Quando è `true`, l'host acquisisce le eccezioni durante l'avvio e tenta di avviare il server.</span><span class="sxs-lookup"><span data-stu-id="9f699-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="9f699-512">**Chiave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="9f699-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="9f699-513">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="9f699-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9f699-514">**Impostazione predefinita**: il valore predefinito è `false` a meno che l'app non venga eseguita con Kestrel in IIS, in tal caso il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="9f699-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="9f699-515">**Variabile di ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="9f699-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="9f699-516">Per impostare questo valore, usare la configurazione o chiamare `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="9f699-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="9f699-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="9f699-517">DetailedErrors</span></span>

<span data-ttu-id="9f699-518">Quando è abilitata o quando l'ambiente è `Development`, l'app acquisisce gli errori dettagliati.</span><span class="sxs-lookup"><span data-stu-id="9f699-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="9f699-519">**Chiave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="9f699-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="9f699-520">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="9f699-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9f699-521">**Default**: `false`</span><span class="sxs-lookup"><span data-stu-id="9f699-521">**Default**: `false`</span></span>  
<span data-ttu-id="9f699-522">**Variabile di ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="9f699-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="9f699-523">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="9f699-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="9f699-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="9f699-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="9f699-525">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da caricare all'avvio.</span><span class="sxs-lookup"><span data-stu-id="9f699-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="9f699-526">Sebbene il valore di configurazione predefinito sia una stringa vuota, gli assembly di avvio dell'hosting includono sempre l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="9f699-527">Quando vengono specificati, gli assembly di avvio dell'hosting vengono aggiunti all'assembly dell'app per essere caricati quando l'app compila i servizi comuni durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="9f699-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="9f699-528">**Chiave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9f699-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="9f699-529">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-529">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-530">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="9f699-530">**Default**: Empty string</span></span>  
<span data-ttu-id="9f699-531">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9f699-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="9f699-532">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="9f699-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="9f699-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="9f699-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="9f699-534">Una stringa delimitata da punto e virgola di assembly di avvio dell'hosting da escludere all'avvio.</span><span class="sxs-lookup"><span data-stu-id="9f699-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="9f699-535">**Chiave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9f699-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="9f699-536">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-536">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-537">**Impostazione predefinita**: stringa vuota</span><span class="sxs-lookup"><span data-stu-id="9f699-537">**Default**: Empty string</span></span>  
<span data-ttu-id="9f699-538">**Variabile di ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9f699-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="9f699-539">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="9f699-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="9f699-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="9f699-540">HTTPS_Port</span></span>

<span data-ttu-id="9f699-541">Porta di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9f699-541">The HTTPS redirect port.</span></span> <span data-ttu-id="9f699-542">Usata per [imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="9f699-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="9f699-543">**Chiave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="9f699-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="9f699-544">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-544">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-545">**Impostazione predefinita**: un valore predefinito non è impostato.</span><span class="sxs-lookup"><span data-stu-id="9f699-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="9f699-546">**Variabile di ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="9f699-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="9f699-547">Per impostare questo valore, usare la configurazione o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="9f699-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="9f699-548">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="9f699-548">PreferHostingUrls</span></span>

<span data-ttu-id="9f699-549">Indica se l'host deve essere in ascolto sugli URL configurati con il `IWebHostBuilder` anziché gli URL configurati con l'implementazione di `IServer`.</span><span class="sxs-lookup"><span data-stu-id="9f699-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="9f699-550">**Chiave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="9f699-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="9f699-551">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="9f699-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9f699-552">**Default**: `true`</span><span class="sxs-lookup"><span data-stu-id="9f699-552">**Default**: `true`</span></span>  
<span data-ttu-id="9f699-553">**Variabile di ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="9f699-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="9f699-554">Per impostare questo valore, usare la variabile di ambiente o chiamare `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="9f699-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="9f699-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="9f699-555">PreventHostingStartup</span></span>

<span data-ttu-id="9f699-556">Impedisce il caricamento automatico degli assembly di avvio dell'hosting, inclusi gli assembly di avvio dell'hosting configurati dall'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="9f699-557">Per altre informazioni, vedere <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9f699-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="9f699-558">**Chiave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="9f699-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="9f699-559">**Tipo**: `bool` (`true` o `1`)</span><span class="sxs-lookup"><span data-stu-id="9f699-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9f699-560">**Default**: `false`</span><span class="sxs-lookup"><span data-stu-id="9f699-560">**Default**: `false`</span></span>  
<span data-ttu-id="9f699-561">**Variabile di ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="9f699-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="9f699-562">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="9f699-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="9f699-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="9f699-563">StartupAssembly</span></span>

<span data-ttu-id="9f699-564">L'assembly per la ricerca della classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="9f699-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="9f699-565">**Chiave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="9f699-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="9f699-566">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-566">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-567">**Impostazione predefinita**: assembly dell'app</span><span class="sxs-lookup"><span data-stu-id="9f699-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="9f699-568">**Variabile di ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="9f699-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="9f699-569">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="9f699-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="9f699-570">`UseStartup` può richiedere un nome di assembly (`string`) o un tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="9f699-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="9f699-571">Se vengono chiamati più metodi `UseStartup`, l'ultimo metodo ha la precedenza.</span><span class="sxs-lookup"><span data-stu-id="9f699-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="9f699-572">URL</span><span class="sxs-lookup"><span data-stu-id="9f699-572">URLs</span></span>

<span data-ttu-id="9f699-573">Elenco delimitato da punto e virgola degli indirizzi IP o gli indirizzi host con le porte e protocolli su cui il server deve eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="9f699-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="9f699-574">Ad esempio: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="9f699-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="9f699-575">Usare "\*" per indicare che il server deve eseguire l'ascolto per le richieste su tutti gli indirizzi IP o nomi host usando la porta e il protocollo specificati (ad esempio, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="9f699-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="9f699-576">Il protocollo (`http://` o `https://`) deve essere incluso con ogni URL.</span><span class="sxs-lookup"><span data-stu-id="9f699-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="9f699-577">I formati supportati variano a seconda del server.</span><span class="sxs-lookup"><span data-stu-id="9f699-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="9f699-578">**Chiave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="9f699-578">**Key**: `urls`</span></span>  
<span data-ttu-id="9f699-579">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-579">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-580">**Impostazione predefinita**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="9f699-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="9f699-581">**Variabile di ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="9f699-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="9f699-582">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="9f699-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="9f699-583">Kestrel ha una propria API di configurazione degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="9f699-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="9f699-584">Per altre informazioni, vedere <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9f699-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="9f699-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9f699-585">WebRoot</span></span>

<span data-ttu-id="9f699-586">Il percorso relativo degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-586">The relative path to the app's static assets.</span></span>

<span data-ttu-id="9f699-587">**Chiave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="9f699-587">**Key**: `webroot`</span></span>  
<span data-ttu-id="9f699-588">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-588">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-589">**Impostazione predefinita**: il valore predefinito è `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="9f699-589">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="9f699-590">Il percorso di *{radice del contenuto}/wwwroot* deve esistere.</span><span class="sxs-lookup"><span data-stu-id="9f699-590">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="9f699-591">Se il percorso non esiste, viene usato un provider di file no-op.</span><span class="sxs-lookup"><span data-stu-id="9f699-591">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="9f699-592">**Variabile di ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="9f699-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="9f699-593">Per impostare questo valore, usare la variabile di ambiente o chiamare `UseWebRoot`:</span><span class="sxs-lookup"><span data-stu-id="9f699-593">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="9f699-594">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="9f699-594">For more information, see:</span></span>

* [<span data-ttu-id="9f699-595">Nozioni fondamentali: radice Web</span><span class="sxs-lookup"><span data-stu-id="9f699-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="9f699-596">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9f699-596">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="9f699-597">Gestire la durata dell'host</span><span class="sxs-lookup"><span data-stu-id="9f699-597">Manage the host lifetime</span></span>

<span data-ttu-id="9f699-598">Chiamare metodi sull'implementazione <xref:Microsoft.Extensions.Hosting.IHost> incorporata per avviare e arrestare l'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="9f699-599">Questi metodi influiscono su tutte le implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che vengono registrate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="9f699-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9f699-600">Esegui</span><span class="sxs-lookup"><span data-stu-id="9f699-600">Run</span></span>

<span data-ttu-id="9f699-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host.</span><span class="sxs-lookup"><span data-stu-id="9f699-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="9f699-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-602">RunAsync</span></span>

<span data-ttu-id="9f699-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="9f699-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="9f699-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-604">RunConsoleAsync</span></span>

<span data-ttu-id="9f699-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> Abilita il supporto della console, compila e avvia l'host e attende la chiusura di <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="9f699-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="9f699-606">Inizia</span><span class="sxs-lookup"><span data-stu-id="9f699-606">Start</span></span>

<span data-ttu-id="9f699-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="9f699-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="9f699-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-608">StartAsync</span></span>

<span data-ttu-id="9f699-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'host e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto.</span><span class="sxs-lookup"><span data-stu-id="9f699-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="9f699-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di `StartAsync`, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="9f699-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9f699-611">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="9f699-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="9f699-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-612">StopAsync</span></span>

<span data-ttu-id="9f699-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="9f699-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="9f699-614">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="9f699-614">WaitForShutdown</span></span>

<span data-ttu-id="9f699-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocca il thread chiamante finché l'arresto non viene attivato da IHostLifetime, ad esempio tramite <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="9f699-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="9f699-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="9f699-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="9f699-618">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="9f699-618">External control</span></span>

<span data-ttu-id="9f699-619">Il controllo diretto della durata dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="9f699-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9f699-620">Le app ASP.NET Core configurano e avviano un host.</span><span class="sxs-lookup"><span data-stu-id="9f699-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="9f699-621">L'host è responsabile della gestione dell'avvio e della durata delle app.</span><span class="sxs-lookup"><span data-stu-id="9f699-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="9f699-622">Questo articolo illustra l'host generico di ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), che si usa per le app che non elaborano le richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f699-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="9f699-623">Lo scopo dell'host generico è separare la pipeline HTTP dall'API dell'host Web, per abilitare una gamma più ampia di scenari host.</span><span class="sxs-lookup"><span data-stu-id="9f699-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="9f699-624">La messaggistica, le attività in background e altri carichi di lavoro non HTTP basati sull'host generico traggono vantaggio dalle funzionalità a montaggio incrociato come la configurazione, l'inserimento di dipendenze (DI) e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="9f699-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="9f699-625">L'host generico è una nuova funzionalità introdotta in ASP.NET Core 2.1 e non è adatto agli scenari di hosting Web.</span><span class="sxs-lookup"><span data-stu-id="9f699-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="9f699-626">Per gli scenari di hosting Web usare l'[host Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="9f699-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="9f699-627">L'host generico sostituirà l'host Web in una versione futura, funzionando come API host principale negli scenari sia HTTP che non HTTP.</span><span class="sxs-lookup"><span data-stu-id="9f699-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="9f699-628">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f699-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9f699-629">Quando si esegue l'app di esempio in [Visual Studio Code](https://code.visualstudio.com/), usare un *terminale esterno o integrato*.</span><span class="sxs-lookup"><span data-stu-id="9f699-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="9f699-630">Non eseguire l'esempio in un ambiente `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="9f699-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="9f699-631">Per impostare la console in Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="9f699-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="9f699-632">Aprire il file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="9f699-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="9f699-633">Nella configurazione **.NET Core Launch (console)** trovare la voce **console**.</span><span class="sxs-lookup"><span data-stu-id="9f699-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="9f699-634">Impostare il valore su `externalTerminal` o `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="9f699-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="9f699-635">Introduzione</span><span class="sxs-lookup"><span data-stu-id="9f699-635">Introduction</span></span>

<span data-ttu-id="9f699-636">La libreria host generico è disponibile nello spazio dei nomi <xref:Microsoft.Extensions.Hosting> e viene specificata dal pacchetto [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="9f699-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="9f699-637">Il pacchetto `Microsoft.Extensions.Hosting` è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o versioni successive).</span><span class="sxs-lookup"><span data-stu-id="9f699-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="9f699-638"><xref:Microsoft.Extensions.Hosting.IHostedService> è il punto di ingresso per l'esecuzione del codice.</span><span class="sxs-lookup"><span data-stu-id="9f699-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="9f699-639">Ogni implementazione <xref:Microsoft.Extensions.Hosting.IHostedService> viene eseguita nell'ordine di [registrazione del servizio in ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="9f699-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="9f699-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> viene chiamato su ogni <xref:Microsoft.Extensions.Hosting.IHostedService> quando viene avviato l'host e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> viene chiamato in ordine di registrazione inverso quando l'host viene chiuso normalmente.</span><span class="sxs-lookup"><span data-stu-id="9f699-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9f699-641">Configurare un host</span><span class="sxs-lookup"><span data-stu-id="9f699-641">Set up a host</span></span>

<span data-ttu-id="9f699-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> è il componente principale che le librerie e le app usano per inizializzare, compilare ed eseguire l'host:</span><span class="sxs-lookup"><span data-stu-id="9f699-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="9f699-643">Opzioni</span><span class="sxs-lookup"><span data-stu-id="9f699-643">Options</span></span>

<span data-ttu-id="9f699-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configura le opzioni per <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="9f699-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="9f699-645">Timeout di arresto</span><span class="sxs-lookup"><span data-stu-id="9f699-645">Shutdown timeout</span></span>

<span data-ttu-id="9f699-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> imposta il timeout per <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9f699-647">Il valore predefinito è cinque secondi.</span><span class="sxs-lookup"><span data-stu-id="9f699-647">The default value is five seconds.</span></span>

<span data-ttu-id="9f699-648">La configurazione dell'opzione seguente in `Program.Main` aumenta il timeout predefinito di cinque secondi di chiusura a 20 secondi:</span><span class="sxs-lookup"><span data-stu-id="9f699-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="9f699-649">Servizi predefiniti</span><span class="sxs-lookup"><span data-stu-id="9f699-649">Default services</span></span>

<span data-ttu-id="9f699-650">I servizi seguenti vengono registrati durante l'inizializzazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="9f699-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="9f699-651">[Ambiente](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="9f699-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="9f699-652">[Configurazione](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="9f699-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="9f699-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="9f699-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="9f699-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="9f699-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="9f699-655">[Opzioni](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="9f699-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="9f699-656">[Registrazione](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="9f699-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9f699-657">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="9f699-657">Host configuration</span></span>

<span data-ttu-id="9f699-658">La configurazione dell'host viene creata tramite:</span><span class="sxs-lookup"><span data-stu-id="9f699-658">Host configuration is created by:</span></span>

* <span data-ttu-id="9f699-659">Chiamata ai metodi di estensione su <xref:Microsoft.Extensions.Hosting.IHostBuilder> per impostare la [radice del contenuto](#content-root) e l'[ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="9f699-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="9f699-660">Lettura della configurazione dai provider di configurazione in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="9f699-661">Metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="9f699-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="9f699-662">Chiave applicazione (nome)</span><span class="sxs-lookup"><span data-stu-id="9f699-662">Application key (name)</span></span>

<span data-ttu-id="9f699-663">La proprietà [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) viene impostata dalla configurazione dell'host durante la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="9f699-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="9f699-664">Per impostare il valore in modo esplicito, usare [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="9f699-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="9f699-665">**Chiave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="9f699-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="9f699-666">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-666">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-667">**Impostazione predefinita**: il nome dell'assembly contenente il punto di ingresso dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="9f699-668">**Impostare usando**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="9f699-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="9f699-669">**Variabile di ambiente**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9f699-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="9f699-670">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="9f699-670">Content root</span></span>

<span data-ttu-id="9f699-671">Questa impostazione determina la posizione da cui l'host inizia la ricerca dei file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="9f699-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="9f699-672">**Chiave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="9f699-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="9f699-673">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-673">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-674">**Impostazione predefinita**: il valore predefinito corrisponde alla cartella contenente l'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="9f699-675">**Impostare usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="9f699-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="9f699-676">**Variabile di ambiente**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9f699-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="9f699-677">Se il percorso non esiste, l'host non verrà avviato.</span><span class="sxs-lookup"><span data-stu-id="9f699-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="9f699-678">Per altre informazioni, vedere [nozioni fondamentali: radice del contenuto](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="9f699-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="9f699-679">Environment</span><span class="sxs-lookup"><span data-stu-id="9f699-679">Environment</span></span>

<span data-ttu-id="9f699-680">Imposta l'[ambiente](xref:fundamentals/environments) per l'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="9f699-681">**Chiave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="9f699-681">**Key**: `environment`</span></span>  
<span data-ttu-id="9f699-682">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="9f699-682">**Type**: `string`</span></span>  
<span data-ttu-id="9f699-683">**Default**: `Production`</span><span class="sxs-lookup"><span data-stu-id="9f699-683">**Default**: `Production`</span></span>  
<span data-ttu-id="9f699-684">**Impostare usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="9f699-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="9f699-685">**Variabile di ambiente**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` è [facoltativo e definito dall'utente](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9f699-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="9f699-686">L'ambiente può essere impostato su qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="9f699-686">The environment can be set to any value.</span></span> <span data-ttu-id="9f699-687">I valori definiti dal framework includono `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="9f699-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9f699-688">I valori non fanno distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="9f699-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="9f699-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="9f699-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="9f699-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per creare un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfiguration> per l'host.</span><span class="sxs-lookup"><span data-stu-id="9f699-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="9f699-691">La configurazione dell'host viene usata per inizializzare l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> da usare nel processo di compilazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="9f699-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="9f699-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="9f699-693">L'host usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="9f699-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9f699-694">Nessun provider è incluso per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="9f699-694">No providers are included by default.</span></span> <span data-ttu-id="9f699-695">È necessario specificare in modo esplicito tutti i provider di configurazione richiesti dall'app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, inclusi:</span><span class="sxs-lookup"><span data-stu-id="9f699-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="9f699-696">Configurazione dei file (ad esempio, da un file *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="9f699-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="9f699-697">Configurazione delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="9f699-697">Environment variable configuration.</span></span>
* <span data-ttu-id="9f699-698">Configurazione degli argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="9f699-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="9f699-699">Tutti gli altri provider di configurazione necessari.</span><span class="sxs-lookup"><span data-stu-id="9f699-699">Any other required configuration providers.</span></span>

<span data-ttu-id="9f699-700">La configurazione file dell'host viene abilitata specificando il percorso di base dell'app con `SetBasePath` seguito da una chiamata a uno dei [provider di configurazione dei file](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f699-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="9f699-701">L'app di esempio usa un file JSON, *hostsettings.json*, e chiama <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> per utilizzare le impostazioni di configurazione host del file.</span><span class="sxs-lookup"><span data-stu-id="9f699-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="9f699-702">Per aggiungere la [configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) dell'host, chiamare <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> sul generatore di host.</span><span class="sxs-lookup"><span data-stu-id="9f699-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="9f699-703">`AddEnvironmentVariables` accetta un prefisso facoltativo definito dall'utente.</span><span class="sxs-lookup"><span data-stu-id="9f699-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="9f699-704">L'app di esempio usa un prefisso di `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="9f699-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="9f699-705">Il prefisso viene rimosso quando vengono lette le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="9f699-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9f699-706">Quando viene configurato l'host dell'app di esempio, il valore della variabile di ambiente per `PREFIX_ENVIRONMENT` diventa il valore di configurazione dell'host per la chiave `environment`.</span><span class="sxs-lookup"><span data-stu-id="9f699-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9f699-707">Durante lo sviluppo quando si usa [Visual Studio](https://visualstudio.microsoft.com) o si esegue un'app con `dotnet run`, le variabili di ambiente possono essere impostate nel file *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9f699-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="9f699-708">Durante lo sviluppo in [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="9f699-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="9f699-709">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="9f699-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="9f699-710">La [configurazione della riga di comando](xref:fundamentals/configuration/index#command-line-configuration-provider) viene aggiunta chiamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="9f699-711">La configurazione della riga di comando viene aggiunta per ultima per consentire agli argomenti della riga di comando di eseguire l'override della configurazione fornita dai provider di configurazione precedenti.</span><span class="sxs-lookup"><span data-stu-id="9f699-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="9f699-712">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="9f699-712">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="9f699-713">È possibile fornire una configurazione aggiuntiva con le chiavi [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="9f699-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="9f699-714">Configurazione di esempio di `HostBuilder` che usa <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="9f699-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="9f699-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="9f699-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="9f699-716">La configurazione dell'app viene creata chiamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> sull'implementazione <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="9f699-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="9f699-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per creare un'interfaccia <xref:Microsoft.Extensions.Configuration.IConfiguration> per l'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="9f699-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="9f699-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="9f699-719">L'app usa l'ultima opzione che imposta un valore in una determinata chiave.</span><span class="sxs-lookup"><span data-stu-id="9f699-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="9f699-720">La configurazione creata da <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> è disponibile in [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) per le operazioni successive e in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="9f699-721">La configurazione dell'app riceve automaticamente la configurazione dell'host fornita da [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="9f699-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="9f699-722">Configurazione app di esempio che usa <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="9f699-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="9f699-723">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="9f699-723">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="9f699-724">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="9f699-724">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="9f699-725">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="9f699-725">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="9f699-726">Per spostare i file delle impostazioni nella directory di output, specificare i file delle impostazioni come [elementi di progetto MSBuild](/visualstudio/msbuild/common-msbuild-project-items) nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="9f699-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="9f699-727">L'app di esempio sposta i file delle impostazioni dell'app JSON e *hostsettings.json* con l'elemento `<Content>` seguente:</span><span class="sxs-lookup"><span data-stu-id="9f699-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="9f699-728">I metodi di estensione di configurazione, ad esempio <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, necessitano di altri pacchetti NuGet, ad esempio [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="9f699-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="9f699-729">A meno che l'app non usi il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), questi pacchetti devono essere aggiunti al progetto oltre al pacchetto [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) di base.</span><span class="sxs-lookup"><span data-stu-id="9f699-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="9f699-730">Per altre informazioni, vedere <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9f699-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="9f699-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="9f699-731">ConfigureServices</span></span>

<span data-ttu-id="9f699-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> aggiunge servizi al contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9f699-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="9f699-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="9f699-734">Un servizio ospitato è una classe con logica di attività in background che implementa l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="9f699-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="9f699-735">Per altre informazioni, vedere <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="9f699-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="9f699-736">L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa il metodo di estensione `AddHostedService` per aggiungere all'app un servizio per gli eventi di durata (`LifetimeEventsHostedService`) e un'attività in background con timeout (`TimedHostedService`):</span><span class="sxs-lookup"><span data-stu-id="9f699-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="9f699-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="9f699-737">ConfigureLogging</span></span>

<span data-ttu-id="9f699-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> aggiunge un delegato per configurare l'interfaccia <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornita.</span><span class="sxs-lookup"><span data-stu-id="9f699-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="9f699-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="9f699-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="9f699-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="9f699-740">UseConsoleLifetime</span></span>

<span data-ttu-id="9f699-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> è in ascolto per la <kbd>combinazione di tasti Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM e chiama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> per avviare il processo di arresto.</span><span class="sxs-lookup"><span data-stu-id="9f699-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="9f699-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> sblocca estensioni come [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="9f699-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="9f699-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` è preregistrata come implementazione di durata predefinita.</span><span class="sxs-lookup"><span data-stu-id="9f699-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="9f699-744">Viene usata l'ultima durata registrata.</span><span class="sxs-lookup"><span data-stu-id="9f699-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="9f699-745">Configurazione contenitore</span><span class="sxs-lookup"><span data-stu-id="9f699-745">Container configuration</span></span>

<span data-ttu-id="9f699-746">Per supportare l'inserimento di altri contenitori, l'host può accettare un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="9f699-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="9f699-747">La disponibilità di una factory non fa parte della registrazione del contenitore DI, ma è una funzionalità intrinseca dell'host usata per creare il contenitore DI concreto.</span><span class="sxs-lookup"><span data-stu-id="9f699-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="9f699-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) esegue l'override della factory predefinita usata per la creazione del provider di servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="9f699-749">La configurazione del contenitore personalizzato è gestita dal metodo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="9f699-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> offre un'esperienza fortemente tipizzata per la configurazione del contenitore sulla base dell'API host sottostante.</span><span class="sxs-lookup"><span data-stu-id="9f699-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="9f699-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> può essere chiamato più volte e i risultati si sommano.</span><span class="sxs-lookup"><span data-stu-id="9f699-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="9f699-752">Creare un contenitore di servizi per l'app:</span><span class="sxs-lookup"><span data-stu-id="9f699-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="9f699-753">Specificare una factory per il contenitore di servizi:</span><span class="sxs-lookup"><span data-stu-id="9f699-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="9f699-754">Usare la factory e configurare il contenitore di servizi personalizzato per l'app:</span><span class="sxs-lookup"><span data-stu-id="9f699-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="9f699-755">Estendibilità</span><span class="sxs-lookup"><span data-stu-id="9f699-755">Extensibility</span></span>

<span data-ttu-id="9f699-756">L'estensibilità host viene eseguita con i metodi di estensione su <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="9f699-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="9f699-757">L'esempio seguente illustra come un metodo di estensione estende un'implementazione <xref:Microsoft.Extensions.Hosting.IHostBuilder> con l'esempio [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) dimostrato in <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="9f699-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="9f699-758">Un'app stabilisce il metodo di estensione `UseHostedService` per registrare il servizio ospitato passato in `T`:</span><span class="sxs-lookup"><span data-stu-id="9f699-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="9f699-759">Gestire l'host</span><span class="sxs-lookup"><span data-stu-id="9f699-759">Manage the host</span></span>

<span data-ttu-id="9f699-760">L'implementazione <xref:Microsoft.Extensions.Hosting.IHost> è responsabile dell'avvio e arresto delle implementazioni <xref:Microsoft.Extensions.Hosting.IHostedService> che sono registrate nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="9f699-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9f699-761">Esegui</span><span class="sxs-lookup"><span data-stu-id="9f699-761">Run</span></span>

<span data-ttu-id="9f699-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> esegue l'app e blocca il thread di chiamata fino all'arresto dell'host:</span><span class="sxs-lookup"><span data-stu-id="9f699-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="9f699-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-763">RunAsync</span></span>

<span data-ttu-id="9f699-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> esegue l'app e restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato il token di annullamento o l'arresto:</span><span class="sxs-lookup"><span data-stu-id="9f699-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="9f699-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-765">RunConsoleAsync</span></span>

<span data-ttu-id="9f699-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> Abilita il supporto della console, compila e avvia l'host e attende la chiusura di <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="9f699-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="9f699-767">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-767">Start and StopAsync</span></span>

<span data-ttu-id="9f699-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> avvia l'host in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="9f699-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="9f699-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> prova ad arrestare l'host entro il timeout specificato.</span><span class="sxs-lookup"><span data-stu-id="9f699-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="9f699-770">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="9f699-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="9f699-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> arresta l'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="9f699-773">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="9f699-773">WaitForShutdown</span></span>

<span data-ttu-id="9f699-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> viene attivato tramite il <xref:Microsoft.Extensions.Hosting.IHostLifetime>, ad esempio `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (in attesa di <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT o SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="9f699-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="9f699-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="9f699-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9f699-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="9f699-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> restituisce un elemento <xref:System.Threading.Tasks.Task> che viene completato quando viene attivato l'arresto tramite il token specificato, quindi chiama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="9f699-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="9f699-778">Controllo esterno</span><span class="sxs-lookup"><span data-stu-id="9f699-778">External control</span></span>

<span data-ttu-id="9f699-779">Il controllo esterno dell'host può essere ottenuto usando metodi che possono essere chiamati dall'esterno:</span><span class="sxs-lookup"><span data-stu-id="9f699-779">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="9f699-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> viene chiamato all'avvio di <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, che attende il completamento prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="9f699-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9f699-781">Questo è utile per ritardare l'avvio fino al segnale trasmesso da un evento esterno.</span><span class="sxs-lookup"><span data-stu-id="9f699-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="9f699-782">Interfaccia IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="9f699-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="9f699-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> include informazioni sull'ambiente di hosting dell'app.</span><span class="sxs-lookup"><span data-stu-id="9f699-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="9f699-784">Usare l'[inserimento di un costruttore](xref:fundamentals/dependency-injection) per ottenere <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> per poterne usare le proprietà e i metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="9f699-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="9f699-785">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="9f699-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="9f699-786">Interfaccia IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="9f699-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="9f699-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> consente attività post-avvio e di arresto, incluse le richieste di arresto normale.</span><span class="sxs-lookup"><span data-stu-id="9f699-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="9f699-788">Tre proprietà nell'interfaccia sono token di annullamento usati per registrare i metodi <xref:System.Action> che definiscono gli eventi di avvio e arresto.</span><span class="sxs-lookup"><span data-stu-id="9f699-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="9f699-789">Token di annullamento</span><span class="sxs-lookup"><span data-stu-id="9f699-789">Cancellation Token</span></span> | <span data-ttu-id="9f699-790">Attivato quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="9f699-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="9f699-791">L'host è stato completamente avviato.</span><span class="sxs-lookup"><span data-stu-id="9f699-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="9f699-792">L'host sta completando un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="9f699-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="9f699-793">Tutte le richieste devono essere elaborate.</span><span class="sxs-lookup"><span data-stu-id="9f699-793">All requests should be processed.</span></span> <span data-ttu-id="9f699-794">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="9f699-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="9f699-795">L'host sta eseguendo un arresto normale.</span><span class="sxs-lookup"><span data-stu-id="9f699-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="9f699-796">È possibile che le richieste siano ancora in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="9f699-796">Requests may still be processing.</span></span> <span data-ttu-id="9f699-797">L'arresto è bloccato fino al completamento di questo evento.</span><span class="sxs-lookup"><span data-stu-id="9f699-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="9f699-798">Inserire tramite costruttore il servizio <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> in qualsiasi classe.</span><span class="sxs-lookup"><span data-stu-id="9f699-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="9f699-799">L'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa l'inserimento di costruttori in una classe `LifetimeEventsHostedService` (implementazione <xref:Microsoft.Extensions.Hosting.IHostedService>) per registrare gli eventi.</span><span class="sxs-lookup"><span data-stu-id="9f699-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="9f699-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="9f699-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="9f699-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> richiede la chiusura dell'applicazione corrente.</span><span class="sxs-lookup"><span data-stu-id="9f699-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="9f699-802">La classe seguente usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> per arrestare normalmente un'app quando viene chiamato il metodo `Shutdown` della classe:</span><span class="sxs-lookup"><span data-stu-id="9f699-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9f699-803">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9f699-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
