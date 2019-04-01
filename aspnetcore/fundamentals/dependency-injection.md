---
title: Inserimento delle dipendenze in ASP.NET Core
author: guardrex
description: Informazioni su come ASP.NET Core implementa l'inserimento delle dipendenze e su come usare questa funzionalità.
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: cc020d7397b03f8ecd6cebf98a14b4aaebb47940
ms.sourcegitcommit: 687ffb15ebe65379f75c84739ea851d5a0d788b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488689"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="13e4d-103">Inserimento delle dipendenze in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="13e4d-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="13e4d-104">Di [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="13e4d-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="13e4d-105">ASP.NET Core supporta il modello progettuale software per l'inserimento delle dipendenze, ovvero una tecnica per ottenere l'[inversione del controllo (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tra classi e relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="13e4d-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="13e4d-106">Per altre informazioni specifiche per l'inserimento delle dipendenze all'interno di controller MVC, vedere <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="13e4d-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="13e4d-107">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="13e4d-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="13e4d-108">Panoramica dell'inserimento delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="13e4d-108">Overview of dependency injection</span></span>

<span data-ttu-id="13e4d-109">Una *dipendenza* è qualsiasi oggetto richiesto da un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="13e4d-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="13e4d-110">Esaminare la classe `MyDependency` seguente con un metodo `WriteMessage` da cui dipendono altre classi in un'app:</span><span class="sxs-lookup"><span data-stu-id="13e4d-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="13e4d-111">È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe.</span><span class="sxs-lookup"><span data-stu-id="13e4d-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="13e4d-112">La classe `MyDependency` è una dipendenza della classe `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="13e4d-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="13e4d-113">È possibile creare un'istanza della classe `MyDependency` per rendere il metodo `WriteMessage`disponibile per una classe.</span><span class="sxs-lookup"><span data-stu-id="13e4d-113">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="13e4d-114">La classe `MyDependency` è una dipendenza della classe `HomeController`:</span><span class="sxs-lookup"><span data-stu-id="13e4d-114">The `MyDependency` class is a dependency of the `HomeController` class:</span></span>

```csharp
public class HomeController : Controller
{
    MyDependency _dependency = new MyDependency();

    public async Task<IActionResult> Index()
    {
        await _dependency.WriteMessage(
            "HomeController.Index created this message.");

        return View();
    }
}
```

::: moniker-end

<span data-ttu-id="13e4d-115">La classe crea e dipende direttamente dall'istanza `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-115">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="13e4d-116">Le dipendenze del codice (come nell'esempio precedente) sono problematiche e devono essere evitate per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="13e4d-116">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="13e4d-117">Per sostituire `MyDependency` con un'implementazione diversa, la classe deve essere modificata.</span><span class="sxs-lookup"><span data-stu-id="13e4d-117">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="13e4d-118">Se `MyDependency` ha dipendenze, devono essere configurate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="13e4d-118">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="13e4d-119">In un progetto di grandi dimensioni con più classi che dipendono da `MyDependency`, il codice di configurazione diventa sparso in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="13e4d-119">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="13e4d-120">È difficile eseguire unit test di questa implementazione.</span><span class="sxs-lookup"><span data-stu-id="13e4d-120">This implementation is difficult to unit test.</span></span> <span data-ttu-id="13e4d-121">L'app dovrebbe usare una classe `MyDependency` fittizia o stub, ma ciò non è possibile con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="13e4d-121">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="13e4d-122">L'inserimento delle dipendenze consente di risolvere questi problemi tramite:</span><span class="sxs-lookup"><span data-stu-id="13e4d-122">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="13e4d-123">L'uso di un'interfaccia per astrarre l'implementazione delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="13e4d-123">The use of an interface to abstract the dependency implementation.</span></span>
* <span data-ttu-id="13e4d-124">La registrazione della dipendenza in un contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="13e4d-124">Registration of the dependency in a service container.</span></span> <span data-ttu-id="13e4d-125">ASP.NET Core offre il contenitore di servizi predefinito [IServiceProvider](/dotnet/api/system.iserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="13e4d-125">ASP.NET Core provides a built-in service container, [IServiceProvider](/dotnet/api/system.iserviceprovider).</span></span> <span data-ttu-id="13e4d-126">I servizi vengono registrati nel metodo `Startup.ConfigureServices` dell'app.</span><span class="sxs-lookup"><span data-stu-id="13e4d-126">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="13e4d-127">L'*inserimento* del servizio nel costruttore della classe in cui viene usato.</span><span class="sxs-lookup"><span data-stu-id="13e4d-127">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="13e4d-128">Il framework si assume la responsabilità della creazione di un'istanza della dipendenza e della sua eliminazione quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="13e4d-128">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="13e4d-129">Nell'[app di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), l'interfaccia `IMyDependency` definisce un metodo che il servizio fornisce all'app:</span><span class="sxs-lookup"><span data-stu-id="13e4d-129">In the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="13e4d-130">Questa interfaccia viene implementata da un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="13e4d-130">This interface is implemented by a concrete type, `MyDependency`:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="13e4d-131">`MyDependency` richiede un'istanza di [ILogger&lt;TCategoryName&gt; ](/dotnet/api/microsoft.extensions.logging.ilogger-1) nel relativo costruttore.</span><span class="sxs-lookup"><span data-stu-id="13e4d-131">`MyDependency` requests an [ILogger&lt;TCategoryName&gt;](/dotnet/api/microsoft.extensions.logging.ilogger-1) in its constructor.</span></span> <span data-ttu-id="13e4d-132">Non è insolito usare l'inserimento delle dipendenze in modo concatenato.</span><span class="sxs-lookup"><span data-stu-id="13e4d-132">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="13e4d-133">Ogni dipendenza richiesta richiede a sua volta le proprie dipendenze.</span><span class="sxs-lookup"><span data-stu-id="13e4d-133">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="13e4d-134">Il contenitore risolve le dipendenze nel grafico e restituisce il servizio completamente risolto.</span><span class="sxs-lookup"><span data-stu-id="13e4d-134">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="13e4d-135">Il set di dipendenze che devono essere risolte viene generalmente chiamato *albero delle dipendenze* o *grafico dipendenze* o *grafico degli oggetti*.</span><span class="sxs-lookup"><span data-stu-id="13e4d-135">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="13e4d-136">`IMyDependency` e `ILogger<TCategoryName>` devono essere registrati nel contenitore di servizi.</span><span class="sxs-lookup"><span data-stu-id="13e4d-136">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="13e4d-137">`IMyDependency` viene registrato in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-137">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="13e4d-138">`ILogger<TCategoryName>` viene registrato dall'infrastruttura di astrazioni di registrazione, pertanto si tratta di un [servizio fornito dal framework](#framework-provided-services) registrato per impostazione predefinita dal framework.</span><span class="sxs-lookup"><span data-stu-id="13e4d-138">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="13e4d-139">Il contenitore risolve `ILogger<TCategoryName>` avvalendosi di [tipi aperti (generici)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando l'esigenza di registrare ogni singolo [tipo costruito (generico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="13e4d-139">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

<span data-ttu-id="13e4d-140">Nell'app di esempio, il servizio `IMyDependency` viene registrato con il tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-140">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="13e4d-141">La registrazione definisce come ambito della durata di servizio la durata di una singola richiesta.</span><span class="sxs-lookup"><span data-stu-id="13e4d-141">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="13e4d-142">Le [durate dei servizi](#service-lifetimes) sono descritte più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="13e4d-142">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="13e4d-143">Ogni metodo di estensione `services.Add{SERVICE_NAME}` aggiunge (e potenzialmente configura) i servizi.</span><span class="sxs-lookup"><span data-stu-id="13e4d-143">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="13e4d-144">Ad esempio, `services.AddMvc()` aggiunge i servizi richiesti da Razor Pages e MVC.</span><span class="sxs-lookup"><span data-stu-id="13e4d-144">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="13e4d-145">È consigliabile che le app seguano questa convenzione.</span><span class="sxs-lookup"><span data-stu-id="13e4d-145">We recommended that apps follow this convention.</span></span> <span data-ttu-id="13e4d-146">Inserire i metodi di estensione nello spazio dei nomi [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) per incapsulare i gruppi di registrazioni di servizio.</span><span class="sxs-lookup"><span data-stu-id="13e4d-146">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="13e4d-147">Se il costruttore del servizio richiede un [tipo incorporato](/dotnet/csharp/language-reference/keywords/built-in-types-table), ad esempio `string`, è possibile inserirlo usando la [configurazione](xref:fundamentals/configuration/index) o il [modello di opzioni](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="13e4d-147">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="13e4d-148">È richiesta un'istanza del servizio tramite il costruttore di una classe in cui il servizio viene usato e assegnato a un campo privato.</span><span class="sxs-lookup"><span data-stu-id="13e4d-148">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="13e4d-149">Il campo viene usato per accedere al servizio in base alle esigenze per l'intera classe.</span><span class="sxs-lookup"><span data-stu-id="13e4d-149">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="13e4d-150">Nell'app di esempio, viene richiesta l'istanza `IMyDependency` usata per chiamare il metodo `WriteMessage` del servizio:</span><span class="sxs-lookup"><span data-stu-id="13e4d-150">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/MyDependencyController.cs?name=snippet1&highlight=3,5-8,13-14)]

::: moniker-end

## <a name="framework-provided-services"></a><span data-ttu-id="13e4d-151">Servizi forniti dal framework</span><span class="sxs-lookup"><span data-stu-id="13e4d-151">Framework-provided services</span></span>

<span data-ttu-id="13e4d-152">Il metodo `Startup.ConfigureServices` è responsabile della definizione dei servizi usati dall'app, incluse le funzionalità di piattaforma come Entity Framework Core e ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="13e4d-152">The `Startup.ConfigureServices` method is responsible for defining the services the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="13e4d-153">Inizialmente, l'interfaccia `IServiceCollection` fornita a `ConfigureServices` ha i seguenti servizi definiti (in base alla [modalità di configurazione dell'host](xref:fundamentals/index#host)):</span><span class="sxs-lookup"><span data-stu-id="13e4d-153">Initially, the `IServiceCollection` provided to `ConfigureServices` has the following services defined (depending on [how the host was configured](xref:fundamentals/index#host)):</span></span>

| <span data-ttu-id="13e4d-154">Tipo di servizio</span><span class="sxs-lookup"><span data-stu-id="13e4d-154">Service Type</span></span> | <span data-ttu-id="13e4d-155">Durata</span><span class="sxs-lookup"><span data-stu-id="13e4d-155">Lifetime</span></span> |
| ------------ | -------- |
| [<span data-ttu-id="13e4d-156">Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory</span><span class="sxs-lookup"><span data-stu-id="13e4d-156">Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | <span data-ttu-id="13e4d-157">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="13e4d-157">Transient</span></span> |
| [<span data-ttu-id="13e4d-158">Microsoft.AspNetCore.Hosting.IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="13e4d-158">Microsoft.AspNetCore.Hosting.IApplicationLifetime</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | <span data-ttu-id="13e4d-159">Singleton</span><span class="sxs-lookup"><span data-stu-id="13e4d-159">Singleton</span></span> |
| [<span data-ttu-id="13e4d-160">Microsoft.AspNetCore.Hosting.IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="13e4d-160">Microsoft.AspNetCore.Hosting.IHostingEnvironment</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) | <span data-ttu-id="13e4d-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="13e4d-161">Singleton</span></span> |
| [<span data-ttu-id="13e4d-162">Microsoft.AspNetCore.Hosting.IStartup</span><span class="sxs-lookup"><span data-stu-id="13e4d-162">Microsoft.AspNetCore.Hosting.IStartup</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.istartup) | <span data-ttu-id="13e4d-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="13e4d-163">Singleton</span></span> |
| [<span data-ttu-id="13e4d-164">Microsoft.AspNetCore.Hosting.IStartupFilter</span><span class="sxs-lookup"><span data-stu-id="13e4d-164">Microsoft.AspNetCore.Hosting.IStartupFilter</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) | <span data-ttu-id="13e4d-165">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="13e4d-165">Transient</span></span> |
| [<span data-ttu-id="13e4d-166">Microsoft.AspNetCore.Hosting.Server.IServer</span><span class="sxs-lookup"><span data-stu-id="13e4d-166">Microsoft.AspNetCore.Hosting.Server.IServer</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) | <span data-ttu-id="13e4d-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="13e4d-167">Singleton</span></span> |
| [<span data-ttu-id="13e4d-168">Microsoft.AspNetCore.Http.IHttpContextFactory</span><span class="sxs-lookup"><span data-stu-id="13e4d-168">Microsoft.AspNetCore.Http.IHttpContextFactory</span></span>](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextfactory) | <span data-ttu-id="13e4d-169">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="13e4d-169">Transient</span></span> |
| [<span data-ttu-id="13e4d-170">Microsoft.Extensions.Logging.ILogger&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="13e4d-170">Microsoft.Extensions.Logging.ILogger&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.logging.ilogger) | <span data-ttu-id="13e4d-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="13e4d-171">Singleton</span></span> |
| [<span data-ttu-id="13e4d-172">Microsoft.Extensions.Logging.ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="13e4d-172">Microsoft.Extensions.Logging.ILoggerFactory</span></span>](/dotnet/api/microsoft.extensions.logging.iloggerfactory) | <span data-ttu-id="13e4d-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="13e4d-173">Singleton</span></span> |
| [<span data-ttu-id="13e4d-174">Microsoft.Extensions.ObjectPool.ObjectPoolProvider</span><span class="sxs-lookup"><span data-stu-id="13e4d-174">Microsoft.Extensions.ObjectPool.ObjectPoolProvider</span></span>](/dotnet/api/microsoft.extensions.objectpool.objectpoolprovider) | <span data-ttu-id="13e4d-175">Singleton</span><span class="sxs-lookup"><span data-stu-id="13e4d-175">Singleton</span></span> |
| [<span data-ttu-id="13e4d-176">Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="13e4d-176">Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) | <span data-ttu-id="13e4d-177">Temporaneo</span><span class="sxs-lookup"><span data-stu-id="13e4d-177">Transient</span></span> |
| [<span data-ttu-id="13e4d-178">Microsoft.Extensions.Options.IOptions&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="13e4d-178">Microsoft.Extensions.Options.IOptions&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.options.ioptions-1) | <span data-ttu-id="13e4d-179">Singleton</span><span class="sxs-lookup"><span data-stu-id="13e4d-179">Singleton</span></span> |
| [<span data-ttu-id="13e4d-180">System.Diagnostics.DiagnosticSource</span><span class="sxs-lookup"><span data-stu-id="13e4d-180">System.Diagnostics.DiagnosticSource</span></span>](/dotnet/core/api/system.diagnostics.diagnosticsource) | <span data-ttu-id="13e4d-181">Singleton</span><span class="sxs-lookup"><span data-stu-id="13e4d-181">Singleton</span></span> |
| [<span data-ttu-id="13e4d-182">System.Diagnostics.DiagnosticListener</span><span class="sxs-lookup"><span data-stu-id="13e4d-182">System.Diagnostics.DiagnosticListener</span></span>](/dotnet/core/api/system.diagnostics.diagnosticlistener) | <span data-ttu-id="13e4d-183">Singleton</span><span class="sxs-lookup"><span data-stu-id="13e4d-183">Singleton</span></span> |

<span data-ttu-id="13e4d-184">Quando è disponibile un metodo di estensione della raccolta di servizi per la registrazione di un servizio (e dei servizi dipendenti, se necessario), la convenzione consiste nell'usare un singolo metodo di estensione `Add{SERVICE_NAME}` per registrare tutti i servizi richiesti da tale servizio.</span><span class="sxs-lookup"><span data-stu-id="13e4d-184">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="13e4d-185">Il codice seguente è un esempio di come aggiungere servizi aggiuntivi al contenitore usando i metodi di estensione [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity) e [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc):</span><span class="sxs-lookup"><span data-stu-id="13e4d-185">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity), and [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddMvc();
}
```

<span data-ttu-id="13e4d-186">Per altre informazioni, vedere la [classe ServiceCollection](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection) nella documentazione dell'API.</span><span class="sxs-lookup"><span data-stu-id="13e4d-186">For more information, see the [ServiceCollection Class](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection) in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="13e4d-187">Durate dei servizi</span><span class="sxs-lookup"><span data-stu-id="13e4d-187">Service lifetimes</span></span>

<span data-ttu-id="13e4d-188">Scegliere una durata appropriata per ogni servizio registrato.</span><span class="sxs-lookup"><span data-stu-id="13e4d-188">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="13e4d-189">I servizi ASP.NET Core possono essere configurati con le impostazioni di durata seguenti:</span><span class="sxs-lookup"><span data-stu-id="13e4d-189">ASP.NET Core services can be configured with the following lifetimes:</span></span>

<span data-ttu-id="13e4d-190">**Temporaneo**</span><span class="sxs-lookup"><span data-stu-id="13e4d-190">**Transient**</span></span>

<span data-ttu-id="13e4d-191">I servizi con durata temporanea vengono creati ogni volta che vengono richiesti.</span><span class="sxs-lookup"><span data-stu-id="13e4d-191">Transient lifetime services are created each time they're requested.</span></span> <span data-ttu-id="13e4d-192">Questa impostazione di durata è consigliata per i servizi semplici senza stati.</span><span class="sxs-lookup"><span data-stu-id="13e4d-192">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="13e4d-193">**Con ambito**</span><span class="sxs-lookup"><span data-stu-id="13e4d-193">**Scoped**</span></span>

<span data-ttu-id="13e4d-194">I servizi con durata con ambito vengono creati una volta per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="13e4d-194">Scoped lifetime services are created once per request.</span></span>

> [!WARNING]
> <span data-ttu-id="13e4d-195">Quando si usa un servizio con ambito in un middleware, inserire il servizio nel metodo `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-195">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="13e4d-196">Evitare l'inserimento tramite il costruttore, che impone al servizio il comportamento singleton.</span><span class="sxs-lookup"><span data-stu-id="13e4d-196">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="13e4d-197">Per ulteriori informazioni, vedere <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="13e4d-197">For more information, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="13e4d-198">**Singleton**</span><span class="sxs-lookup"><span data-stu-id="13e4d-198">**Singleton**</span></span>

<span data-ttu-id="13e4d-199">I servizi con durata singleton vengono creati la prima volta che vengono richiesti (o quando si esegue `ConfigureServices` e viene specificata un'istanza con la registrazione del servizio).</span><span class="sxs-lookup"><span data-stu-id="13e4d-199">Singleton lifetime services are created the first time they're requested (or when `ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="13e4d-200">Tutte le richieste successive usano la stessa istanza.</span><span class="sxs-lookup"><span data-stu-id="13e4d-200">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="13e4d-201">Se l'app richiede un comportamento singleton, è consigliabile consentire al contenitore del servizio di gestire la durata del servizio.</span><span class="sxs-lookup"><span data-stu-id="13e4d-201">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="13e4d-202">Non implementare lo schema progettuale singleton e fornire codice utente per gestire la durata dell'oggetto nella classe.</span><span class="sxs-lookup"><span data-stu-id="13e4d-202">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="13e4d-203">Può essere pericoloso risolvere un servizio con ambito da un singleton.</span><span class="sxs-lookup"><span data-stu-id="13e4d-203">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="13e4d-204">Ciò potrebbe causare uno stato non corretto per il servizio durante l'elaborazione delle richieste successive.</span><span class="sxs-lookup"><span data-stu-id="13e4d-204">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="13e4d-205">Comportamento dell'inserimento del costruttore</span><span class="sxs-lookup"><span data-stu-id="13e4d-205">Constructor injection behavior</span></span>

<span data-ttu-id="13e4d-206">I servizi possono essere risolti con due meccanismi:</span><span class="sxs-lookup"><span data-stu-id="13e4d-206">Services can be resolved by two mechanisms:</span></span>

* `IServiceProvider`
* <span data-ttu-id="13e4d-207">[ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; Consente la creazione di oggetti senza registrazione del servizio nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="13e4d-207">[ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="13e4d-208">`ActivatorUtilities` viene usato con astrazioni esposte all'utente, ad esempio helper tag, controller MVC e strumenti di associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="13e4d-208">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="13e4d-209">I costruttori possono accettare argomenti non forniti tramite l'inserimento di dipendenze, ma gli argomenti devono assegnare valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="13e4d-209">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="13e4d-210">Quando i servizi vengono risolti da `IServiceProvider` o `ActivatorUtilities`, l'inserimento del costruttore richiede un costruttore *pubblico*.</span><span class="sxs-lookup"><span data-stu-id="13e4d-210">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="13e4d-211">Quando i servizi vengono risolti da `ActivatorUtilities`, l'inserimento del costruttore richiede l'esistenza di un solo costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="13e4d-211">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="13e4d-212">Sebbene siano supportati gli overload dei costruttori, può essere presente un solo overload i cui argomenti possono essere tutti specificati tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="13e4d-212">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="13e4d-213">Contesti di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="13e4d-213">Entity Framework contexts</span></span>

<span data-ttu-id="13e4d-214">I contesti di Entity Framework vengono in genere aggiunti al contenitore di servizi usando la [durata con ambito](#service-lifetimes), perché le operazioni di database delle app Web hanno di solito come ambito la richiesta.</span><span class="sxs-lookup"><span data-stu-id="13e4d-214">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the request.</span></span> <span data-ttu-id="13e4d-215">La durata predefinita è con ambito se non viene specificata una durata con un overload <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> quando si registra il contesto del database.</span><span class="sxs-lookup"><span data-stu-id="13e4d-215">The default lifetime is scoped if a lifetime isn't specified by an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> overload when registering the database context.</span></span> <span data-ttu-id="13e4d-216">I servizi con una durata specificata non devono usare un contesto di database con una durata più breve rispetto al servizio.</span><span class="sxs-lookup"><span data-stu-id="13e4d-216">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="13e4d-217">Opzioni di durata e di registrazione</span><span class="sxs-lookup"><span data-stu-id="13e4d-217">Lifetime and registration options</span></span>

<span data-ttu-id="13e4d-218">Per illustrare la differenza tra le opzioni di durata e registrazione, si considerino le interfacce seguenti che rappresentano le attività come un'operazione con un identificatore univoco, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-218">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="13e4d-219">A seconda del modo in cui la durata di un servizio operativo viene configurata per le interfacce seguenti, il contenitore fornisce la stessa istanza o un'istanza diversa del servizio quando richiesto da una classe:</span><span class="sxs-lookup"><span data-stu-id="13e4d-219">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="13e4d-220">Le interfacce vengono implementate nella classe `Operation`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-220">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="13e4d-221">Il costruttore `Operation` genera un GUID se non ne viene fornito uno:</span><span class="sxs-lookup"><span data-stu-id="13e4d-221">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="13e4d-222">Viene registrato un `OperationService` che dipende da ognuno degli altri tipi `Operation`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-222">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="13e4d-223">Quando viene richiesto `OperationService` tramite l'inserimento delle dipendenze, riceve una nuova istanza di ogni servizio o un'istanza esistente in base alla durata del servizio dipendente.</span><span class="sxs-lookup"><span data-stu-id="13e4d-223">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="13e4d-224">Se vengono creati servizi temporanei su richiesta, l'`OperationId` del servizio `IOperationTransient` è diverso dall'`OperationId` di `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-224">If transient services are created when requested, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="13e4d-225">`OperationService` riceve una nuova istanza della classe `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-225">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="13e4d-226">La nuova istanza restituisce un `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="13e4d-226">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="13e4d-227">Se sono stati creati servizi con ambito per ogni richiesta, l'`OperationId` del servizio `IOperationScoped` è uguale a quello di `OperationService` all'interno di una richiesta.</span><span class="sxs-lookup"><span data-stu-id="13e4d-227">If scoped services are created per request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a request.</span></span> <span data-ttu-id="13e4d-228">In tutte le richieste, entrambi i servizi condividono un valore `OperationId` diverso.</span><span class="sxs-lookup"><span data-stu-id="13e4d-228">Across requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="13e4d-229">Se servizi singleton e con istanza singleton vengono creati una sola volta e usati per tutte le richieste e tutti i servizi, l'`OperationId` rimane costante tra tutte le richieste di servizio.</span><span class="sxs-lookup"><span data-stu-id="13e4d-229">If singleton and singleton-instance services are created once and used across all requests and all services, the `OperationId` is constant across all service requests.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="13e4d-230">In `Startup.ConfigureServices`, ogni tipo viene aggiunto al contenitore in base alla durata denominata:</span><span class="sxs-lookup"><span data-stu-id="13e4d-230">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=12-15,18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

<span data-ttu-id="13e4d-231">Il servizio `IOperationSingletonInstance` usa un'istanza specifica con un ID noto di `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-231">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="13e4d-232">È chiaro quando questo tipo è in uso (il relativo GUID è composto da tutti zero).</span><span class="sxs-lookup"><span data-stu-id="13e4d-232">It's clear when this type is in use (its GUID is all zeroes).</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="13e4d-233">L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste.</span><span class="sxs-lookup"><span data-stu-id="13e4d-233">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="13e4d-234">L'`IndexModel` dell'app di esempio richiede ogni genere di tipo `IOperation` e `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-234">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="13e4d-235">La pagina visualizza quindi tutti i valori `OperationId` della classe del modello di pagina e del servizio tramite assegnazioni di proprietà:</span><span class="sxs-lookup"><span data-stu-id="13e4d-235">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="13e4d-236">L'app di esempio dimostra le durate degli oggetti all'interno e tra le singole richieste.</span><span class="sxs-lookup"><span data-stu-id="13e4d-236">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="13e4d-237">L'app di esempio include un `OperationsController` che richiede ogni genere di tipo `IOperation` e `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-237">The sample app includes an `OperationsController` that requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="13e4d-238">L'azione `Index` imposta i servizi in `ViewBag` per la visualizzazione dei valori `OperationId` del servizio:</span><span class="sxs-lookup"><span data-stu-id="13e4d-238">The `Index` action sets the services into the `ViewBag` for display of the service's `OperationId` values:</span></span>

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/OperationsController.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="13e4d-239">L'output seguente mostra i risultati delle due richieste:</span><span class="sxs-lookup"><span data-stu-id="13e4d-239">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="13e4d-240">**Prima richiesta:**</span><span class="sxs-lookup"><span data-stu-id="13e4d-240">**First request:**</span></span>

<span data-ttu-id="13e4d-241">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="13e4d-241">Controller operations:</span></span>

<span data-ttu-id="13e4d-242">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="13e4d-242">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="13e4d-243">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="13e4d-243">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="13e4d-244">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="13e4d-244">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="13e4d-245">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="13e4d-245">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="13e4d-246">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="13e4d-246">`OperationService` operations:</span></span>

<span data-ttu-id="13e4d-247">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="13e4d-247">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="13e4d-248">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="13e4d-248">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="13e4d-249">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="13e4d-249">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="13e4d-250">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="13e4d-250">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="13e4d-251">**Seconda richiesta:**</span><span class="sxs-lookup"><span data-stu-id="13e4d-251">**Second request:**</span></span>

<span data-ttu-id="13e4d-252">Operazioni del controller:</span><span class="sxs-lookup"><span data-stu-id="13e4d-252">Controller operations:</span></span>

<span data-ttu-id="13e4d-253">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="13e4d-253">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="13e4d-254">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="13e4d-254">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="13e4d-255">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="13e4d-255">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="13e4d-256">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="13e4d-256">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="13e4d-257">Operazioni di `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="13e4d-257">`OperationService` operations:</span></span>

<span data-ttu-id="13e4d-258">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="13e4d-258">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="13e4d-259">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="13e4d-259">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="13e4d-260">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="13e4d-260">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="13e4d-261">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="13e4d-261">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="13e4d-262">Osservare i valori `OperationId` che variano all'interno della richiesta e da una richiesta e l'altra:</span><span class="sxs-lookup"><span data-stu-id="13e4d-262">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="13e4d-263">Gli oggetti *temporanei* sono sempre diversi.</span><span class="sxs-lookup"><span data-stu-id="13e4d-263">*Transient* objects are always different.</span></span> <span data-ttu-id="13e4d-264">Si noti che il valore `OperationId` temporaneo per la prima e la seconda richiesta è diverso per entrambe le operazioni `OperationService` e in tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="13e4d-264">Note that the transient `OperationId` value for both the first and second requests are different for both `OperationService` operations and across requests.</span></span> <span data-ttu-id="13e4d-265">Viene fornita una nuova istanza per ogni servizio e richiesta.</span><span class="sxs-lookup"><span data-stu-id="13e4d-265">A new instance is provided to each service and request.</span></span>
* <span data-ttu-id="13e4d-266">Gli oggetti *con ambito* sono gli stessi all'interno di una richiesta, ma variano nelle diverse richieste.</span><span class="sxs-lookup"><span data-stu-id="13e4d-266">*Scoped* objects are the same within a request but different across requests.</span></span>
* <span data-ttu-id="13e4d-267">Gli oggetti *singleton* sono gli stessi per ogni oggetto e ogni richiesta, indipendentemente dal fatto che venga fornita un'istanza `Operation` in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-267">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="13e4d-268">Chiamare i servizi da main</span><span class="sxs-lookup"><span data-stu-id="13e4d-268">Call services from main</span></span>

<span data-ttu-id="13e4d-269">Creare un [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) con [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) per risolvere un servizio con ambito nell'ambito dell'app.</span><span class="sxs-lookup"><span data-stu-id="13e4d-269">Create an [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) with [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="13e4d-270">Questo approccio è utile per l'accesso a un servizio con ambito all'avvio e per l'esecuzione di attività di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="13e4d-270">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="13e4d-271">L'esempio seguente indica come ottenere un contesto per `MyScopedService` in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="13e4d-271">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = CreateWebHostBuilder(args).Build();

    using (var serviceScope = host.Services.CreateScope())
    {
        var services = serviceScope.ServiceProvider;

        try
        {
            var serviceContext = services.GetRequiredService<MyScopedService>();
            // Use the context here
        }
        catch (Exception ex)
        {
            var logger = services.GetRequiredService<ILogger<Program>>();
            logger.LogError(ex, "An error occurred.");
        }
    }

    host.Run();
}
```

## <a name="scope-validation"></a><span data-ttu-id="13e4d-272">Convalida dell'ambito</span><span class="sxs-lookup"><span data-stu-id="13e4d-272">Scope validation</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="13e4d-273">Quando l'app viene eseguita nell'ambiente di sviluppo, il provider di servizi predefinito esegue controlli per verificare che:</span><span class="sxs-lookup"><span data-stu-id="13e4d-273">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="13e4d-274">I servizi con ambito non vengano risolti direttamente o indirettamente dal provider di servizi radice.</span><span class="sxs-lookup"><span data-stu-id="13e4d-274">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="13e4d-275">I servizi con ambito non vengano inseriti direttamente o indirettamente in singleton.</span><span class="sxs-lookup"><span data-stu-id="13e4d-275">Scoped services aren't directly or indirectly injected into singletons.</span></span>

::: moniker-end

<span data-ttu-id="13e4d-276">Il provider di servizi radice venga creato con la chiamata di [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="13e4d-276">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="13e4d-277">La durata del provider di servizi radice corrisponde alla durata dell'app/server quando il provider viene avviato con l'app e viene eliminato alla chiusura dell'app.</span><span class="sxs-lookup"><span data-stu-id="13e4d-277">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="13e4d-278">I servizi con ambito vengono eliminati dal contenitore che li ha creati.</span><span class="sxs-lookup"><span data-stu-id="13e4d-278">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="13e4d-279">Se un servizio con ambito viene creato nel contenitore radice, la durata del servizio viene di fatto convertita in singleton, perché il servizio viene eliminato solo dal contenitore radice alla chiusura dell'app/server.</span><span class="sxs-lookup"><span data-stu-id="13e4d-279">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="13e4d-280">La convalida degli ambiti servizio rileva queste situazioni nel contesto della chiamata di `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-280">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="13e4d-281">Per ulteriori informazioni, vedere <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="13e4d-281">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="13e4d-282">Servizi di richiesta</span><span class="sxs-lookup"><span data-stu-id="13e4d-282">Request Services</span></span>

<span data-ttu-id="13e4d-283">I servizi disponibili all'interno di una richiesta ASP.NET Core da `HttpContext` vengono esposti tramite la raccolta [HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices).</span><span class="sxs-lookup"><span data-stu-id="13e4d-283">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices) collection.</span></span>

<span data-ttu-id="13e4d-284">I servizi di richiesta rappresentano i servizi configurati e richiesti come parte dell'app.</span><span class="sxs-lookup"><span data-stu-id="13e4d-284">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="13e4d-285">Quando gli oggetti specificano dipendenze, le dipendenze sono soddisfatte dai tipi individuati in `RequestServices`, non in `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-285">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="13e4d-286">In generale, l'app non deve usare queste proprietà direttamente.</span><span class="sxs-lookup"><span data-stu-id="13e4d-286">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="13e4d-287">Richiedere invece i tipi richiesti dalle classi tramite i costruttori di classi e consentire al framework di inserire le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="13e4d-287">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="13e4d-288">Si ottengono classi più facili da testare.</span><span class="sxs-lookup"><span data-stu-id="13e4d-288">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="13e4d-289">È consigliabile richiedere le dipendenze come parametri del costruttore per l'accesso alla raccolta `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-289">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="13e4d-290">Progettare i servizi per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="13e4d-290">Design services for dependency injection</span></span>

<span data-ttu-id="13e4d-291">Le procedure consigliate prevedono di:</span><span class="sxs-lookup"><span data-stu-id="13e4d-291">Best practices are to:</span></span>

* <span data-ttu-id="13e4d-292">Progettare i servizi per usare l'inserimento delle dipendenze per ottenere le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="13e4d-292">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="13e4d-293">Evitare chiamate di metodo statico, con stato.</span><span class="sxs-lookup"><span data-stu-id="13e4d-293">Avoid stateful, static method calls.</span></span>
* <span data-ttu-id="13e4d-294">Evitare la creazione diretta di istanze delle classi dipendenti all'interno di servizi.</span><span class="sxs-lookup"><span data-stu-id="13e4d-294">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="13e4d-295">La creazione diretta di istanze associa il codice a una determinata implementazione.</span><span class="sxs-lookup"><span data-stu-id="13e4d-295">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="13e4d-296">Fare in modo che le classi siano piccole, con factoring corretto e facili da testare.</span><span class="sxs-lookup"><span data-stu-id="13e4d-296">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="13e4d-297">Se una classe sembra avere troppe dipendenze inserite, ciò significa in genere che la classe ha un numero eccessivo di responsabilità e sta violando il [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (principio di responsabilità singola).</span><span class="sxs-lookup"><span data-stu-id="13e4d-297">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="13e4d-298">Tentare di effettuare il refactoring della classe spostando alcune delle responsabilità in una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="13e4d-298">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="13e4d-299">Tenere presente che le classi del modello di pagina Razor Pages e le classi del controller MVC devono essere incentrate sulle problematiche dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="13e4d-299">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="13e4d-300">Di conseguenza, le regole business e i dettagli di implementazione di accesso ai dati devono essere inseriti in classi appropriate per queste [problematiche separate](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="13e4d-300">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="13e4d-301">Eliminazione dei servizi</span><span class="sxs-lookup"><span data-stu-id="13e4d-301">Disposal of services</span></span>

<span data-ttu-id="13e4d-302">Il contenitore chiama `Dispose` per i tipi `IDisposable` creati.</span><span class="sxs-lookup"><span data-stu-id="13e4d-302">The container calls `Dispose` for the `IDisposable` types it creates.</span></span> <span data-ttu-id="13e4d-303">Se un'istanza viene aggiunta al contenitore dal codice utente, non viene eliminata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="13e4d-303">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

```csharp
// Services that implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public interface ISomeService {}
public class SomeServiceImplementation : ISomeService, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // The container creates the following instances and disposes them automatically:
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<ISomeService>(sp => new SomeServiceImplementation());

    // The container doesn't create the following instances, so it doesn't dispose of
    // the instances automatically:
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

::: moniker range="= aspnetcore-1.0"

> [!NOTE]
> <span data-ttu-id="13e4d-304">In ASP.NET Core 1.0 il contenitore chiama Dispose per *tutti* gli oggetti `IDisposable`, inclusi quelli non creati dal contenitore.</span><span class="sxs-lookup"><span data-stu-id="13e4d-304">In ASP.NET Core 1.0, the container calls dispose on *all* `IDisposable` objects, including those it didn't create.</span></span>

::: moniker-end

## <a name="default-service-container-replacement"></a><span data-ttu-id="13e4d-305">Sostituzione del contenitore di servizi predefinito</span><span class="sxs-lookup"><span data-stu-id="13e4d-305">Default service container replacement</span></span>

<span data-ttu-id="13e4d-306">Il contenitore di servizi predefinito è progettato per soddisfare le esigenze del framework e della maggior parte delle app.</span><span class="sxs-lookup"><span data-stu-id="13e4d-306">The built-in service container is meant to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="13e4d-307">È consigliabile usare il contenitore predefinito, a meno che non sia necessaria una funzionalità specifica non supportata.</span><span class="sxs-lookup"><span data-stu-id="13e4d-307">We recommend using the built-in container unless you need a specific feature that it doesn't support.</span></span> <span data-ttu-id="13e4d-308">Alcune delle funzionalità supportate nei contenitori di terze parti non trovati nel contenitore predefinito:</span><span class="sxs-lookup"><span data-stu-id="13e4d-308">Some of the features supported in 3rd party containers not found in the built-in container:</span></span>

* <span data-ttu-id="13e4d-309">Inserimento di proprietà</span><span class="sxs-lookup"><span data-stu-id="13e4d-309">Property injection</span></span>
* <span data-ttu-id="13e4d-310">Inserimento in base al nome</span><span class="sxs-lookup"><span data-stu-id="13e4d-310">Injection based on name</span></span>
* <span data-ttu-id="13e4d-311">Contenitori figlio</span><span class="sxs-lookup"><span data-stu-id="13e4d-311">Child containers</span></span>
* <span data-ttu-id="13e4d-312">Gestione della durata personalizzata</span><span class="sxs-lookup"><span data-stu-id="13e4d-312">Custom lifetime management</span></span>
* <span data-ttu-id="13e4d-313">Supporto di `Func<T>` per l'inizializzazione differita</span><span class="sxs-lookup"><span data-stu-id="13e4d-313">`Func<T>` support for lazy initialization</span></span>

<span data-ttu-id="13e4d-314">Vedere il [file Dependency Injection readme.md](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection) per un elenco di alcuni dei contenitori che supportano gli adattatori.</span><span class="sxs-lookup"><span data-stu-id="13e4d-314">See the [Dependency Injection readme.md file](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection) for a list of some of the containers that support adapters.</span></span>

<span data-ttu-id="13e4d-315">L'esempio seguente consente di sostituire il contenitore predefinito con [Autofac](https://autofac.org/):</span><span class="sxs-lookup"><span data-stu-id="13e4d-315">The following sample replaces the built-in container with [Autofac](https://autofac.org/):</span></span>

* <span data-ttu-id="13e4d-316">Installare i pacchetti contenitore appropriati:</span><span class="sxs-lookup"><span data-stu-id="13e4d-316">Install the appropriate container package(s):</span></span>

  * [<span data-ttu-id="13e4d-317">Autofac</span><span class="sxs-lookup"><span data-stu-id="13e4d-317">Autofac</span></span>](https://www.nuget.org/packages/Autofac/)
  * [<span data-ttu-id="13e4d-318">Autofac.Extensions.DependencyInjection</span><span class="sxs-lookup"><span data-stu-id="13e4d-318">Autofac.Extensions.DependencyInjection</span></span>](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* <span data-ttu-id="13e4d-319">Configurare il contenitore in `Startup.ConfigureServices` e restituire `IServiceProvider`:</span><span class="sxs-lookup"><span data-stu-id="13e4d-319">Configure the container in `Startup.ConfigureServices` and return an `IServiceProvider`:</span></span>

    ```csharp
    public IServiceProvider ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        // Add other framework services

        // Add Autofac
        var containerBuilder = new ContainerBuilder();
        containerBuilder.RegisterModule<DefaultModule>();
        containerBuilder.Populate(services);
        var container = containerBuilder.Build();
        return new AutofacServiceProvider(container);
    }
    ```

    <span data-ttu-id="13e4d-320">Per usare un contenitore di terze parti, `Startup.ConfigureServices` deve restituire `IServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="13e4d-320">To use a 3rd party container, `Startup.ConfigureServices` must return `IServiceProvider`.</span></span>

* <span data-ttu-id="13e4d-321">Configurare Autofac in `DefaultModule`:</span><span class="sxs-lookup"><span data-stu-id="13e4d-321">Configure Autofac in `DefaultModule`:</span></span>

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

<span data-ttu-id="13e4d-322">In fase di esecuzione, Autofac viene usato per risolvere i tipi e inserire le dipendenze.</span><span class="sxs-lookup"><span data-stu-id="13e4d-322">At runtime, Autofac is used to resolve types and inject dependencies.</span></span> <span data-ttu-id="13e4d-323">Per altre informazioni sull'uso di Autofac con ASP.NET Core, vedere la [documentazione di Autofac](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span><span class="sxs-lookup"><span data-stu-id="13e4d-323">To learn more about using Autofac with ASP.NET Core, see the [Autofac documentation](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span></span>

### <a name="thread-safety"></a><span data-ttu-id="13e4d-324">Thread safety</span><span class="sxs-lookup"><span data-stu-id="13e4d-324">Thread safety</span></span>

<span data-ttu-id="13e4d-325">I servizi singleton devono essere thread-safe.</span><span class="sxs-lookup"><span data-stu-id="13e4d-325">Singleton services need to be thread safe.</span></span> <span data-ttu-id="13e4d-326">Se un servizio singleton ha una dipendenza in un servizio temporaneo, è necessario che anche il servizio temporaneo sia thread-safe, a seconda di come viene usato dal singleton.</span><span class="sxs-lookup"><span data-stu-id="13e4d-326">If a singleton service has a dependency on a transient service, the transient service may also need to be thread safe depending how it's used by the singleton.</span></span>

<span data-ttu-id="13e4d-327">Non è necessario che il metodo factory del singolo servizio, ad esempio il secondo argomento per [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__), sia thread-safe.</span><span class="sxs-lookup"><span data-stu-id="13e4d-327">The factory method of single service, such as the second argument to [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__), doesn't need to be thread-safe.</span></span> <span data-ttu-id="13e4d-328">Come nel caso di un costruttore di tipo (`static`), è garantito che venga chiamato una sola volta da un singolo thread.</span><span class="sxs-lookup"><span data-stu-id="13e4d-328">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="13e4d-329">Suggerimenti</span><span class="sxs-lookup"><span data-stu-id="13e4d-329">Recommendations</span></span>

* <span data-ttu-id="13e4d-330">La risoluzione basata sui servizi `async/await` e `Task` non è supportata.</span><span class="sxs-lookup"><span data-stu-id="13e4d-330">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="13e4d-331">Poiché C# non supporta i costruttori asincroni, il modello consigliato consiste nell'usare i metodi asincroni dopo avere risolto in modo sincrono il servizio.</span><span class="sxs-lookup"><span data-stu-id="13e4d-331">C# does not support asynchronous constructors, therefore the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="13e4d-332">Evitare di archiviare i dati e la configurazione direttamente nel contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="13e4d-332">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="13e4d-333">Ad esempio, il carrello acquisti di un utente non dovrebbe in genere essere aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="13e4d-333">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="13e4d-334">La configurazione deve usare il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="13e4d-334">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="13e4d-335">Analogamente, evitare gli oggetti "contenitori di dati" che hanno la sola funzione di consentire l'accesso ad altri oggetti.</span><span class="sxs-lookup"><span data-stu-id="13e4d-335">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="13e4d-336">È preferibile richiedere l'elemento effettivo tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="13e4d-336">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="13e4d-337">Evitare l'accesso statico ai servizi (ad esempio, la tipizzazione statica [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) da usare in altre posizioni).</span><span class="sxs-lookup"><span data-stu-id="13e4d-337">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) for use elsewhere).</span></span>

* <span data-ttu-id="13e4d-338">Evitare di usare il *modello di localizzatore del servizio*.</span><span class="sxs-lookup"><span data-stu-id="13e4d-338">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="13e4d-339">Ad esempio, non richiamare <xref:System.IServiceProvider.GetService*> per ottenere un'istanza del servizio quando è invece possibile usare l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="13e4d-339">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead.</span></span> <span data-ttu-id="13e4d-340">Un'altra variazione del localizzatore del servizio da evitare è inserire una factory che risolve le dipendenze in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="13e4d-340">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="13e4d-341">Queste procedure combinano le strategie di [inversione del controllo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="13e4d-341">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="13e4d-342">Evitare l'accesso statico a `HttpContext` (ad esempio [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)).</span><span class="sxs-lookup"><span data-stu-id="13e4d-342">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)).</span></span>

<span data-ttu-id="13e4d-343">È tuttavia possibile che in alcuni casi queste raccomandazioni debbano essere ignorate.</span><span class="sxs-lookup"><span data-stu-id="13e4d-343">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="13e4d-344">Le eccezioni sono rare e principalmente si tratta di casi speciali all'interno del framework stesso.</span><span class="sxs-lookup"><span data-stu-id="13e4d-344">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="13e4d-345">L'inserimento di dipendenze è un'*alternativa* ai modelli di accesso agli oggetti statici/globali.</span><span class="sxs-lookup"><span data-stu-id="13e4d-345">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="13e4d-346">Se l'inserimento di dipendenze viene usato con l'accesso agli oggetti statico i vantaggi non saranno evidenti.</span><span class="sxs-lookup"><span data-stu-id="13e4d-346">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="13e4d-347">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="13e4d-347">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="13e4d-348">Scrittura di codice pulito in ASP.NET Core con inserimento delle dipendenze (MSDN)</span><span class="sxs-lookup"><span data-stu-id="13e4d-348">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* <span data-ttu-id="13e4d-349">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) (Principio delle dipendenze esplicite)</span><span class="sxs-lookup"><span data-stu-id="13e4d-349">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* <span data-ttu-id="13e4d-350">[Inversion of Control Containers and the Dependency Injection Pattern](https://www.martinfowler.com/articles/injection.html) (Martin Fowler) (Contenitori di inversione del controllo e modello di inserimento delle dipendenze)</span><span class="sxs-lookup"><span data-stu-id="13e4d-350">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* <span data-ttu-id="13e4d-351">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (Come registrare un servizio con più interfacce in ASP.NET Core DI)</span><span class="sxs-lookup"><span data-stu-id="13e4d-351">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>
