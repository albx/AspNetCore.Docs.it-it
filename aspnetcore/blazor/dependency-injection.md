---
title: Inserimento delle dipendenze di ASP.NET Core Blazer
author: guardrex
description: Scopri in che modo le app Blazer possono inserire i servizi nei componenti.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/dependency-injection
ms.openlocfilehash: a9330caa81eec0910206312283b3424c70cd1289
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68948391"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="d6057-103">Inserimento delle dipendenze di ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="d6057-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="d6057-104">Di [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="d6057-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="d6057-105">Blazer supporta l' [inserimento delle dipendenze (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d6057-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d6057-106">Le app possono usare i servizi predefiniti inserendoli in componenti.</span><span class="sxs-lookup"><span data-stu-id="d6057-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="d6057-107">Le app possono anche definire e registrare servizi personalizzati e renderli disponibili nell'app tramite DI.</span><span class="sxs-lookup"><span data-stu-id="d6057-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="d6057-108">DI è una tecnica per accedere ai servizi configurati in una posizione centrale.</span><span class="sxs-lookup"><span data-stu-id="d6057-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="d6057-109">Questa operazione può essere utile nelle app blazer per:</span><span class="sxs-lookup"><span data-stu-id="d6057-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="d6057-110">Condividere una singola istanza di una classe di servizio in molti componenti, noti come un servizio *singleton* .</span><span class="sxs-lookup"><span data-stu-id="d6057-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="d6057-111">Separare i componenti da classi di servizi concrete usando astrazioni di riferimento.</span><span class="sxs-lookup"><span data-stu-id="d6057-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="d6057-112">Si consideri ad esempio `IDataAccess` un'interfaccia per l'accesso ai dati nell'app.</span><span class="sxs-lookup"><span data-stu-id="d6057-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="d6057-113">L'interfaccia viene implementata da una `DataAccess` classe concreta e registrata come servizio nel contenitore del servizio dell'app.</span><span class="sxs-lookup"><span data-stu-id="d6057-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="d6057-114">Quando un componente usa il per ricevere un' `IDataAccess` implementazione di, il componente non è associato al tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="d6057-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="d6057-115">L'implementazione può essere scambiata, ad esempio per un'implementazione fittizia negli unit test.</span><span class="sxs-lookup"><span data-stu-id="d6057-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="d6057-116">Servizi predefiniti</span><span class="sxs-lookup"><span data-stu-id="d6057-116">Default services</span></span>

<span data-ttu-id="d6057-117">I servizi predefiniti vengono aggiunti automaticamente alla raccolta di servizi dell'app.</span><span class="sxs-lookup"><span data-stu-id="d6057-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="d6057-118">Service</span><span class="sxs-lookup"><span data-stu-id="d6057-118">Service</span></span> | <span data-ttu-id="d6057-119">Durata</span><span class="sxs-lookup"><span data-stu-id="d6057-119">Lifetime</span></span> | <span data-ttu-id="d6057-120">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d6057-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="d6057-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6057-121">Singleton</span></span> | <span data-ttu-id="d6057-122">Fornisce metodi per l'invio di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata da un URI.</span><span class="sxs-lookup"><span data-stu-id="d6057-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span> <span data-ttu-id="d6057-123">Si noti che questa istanza `HttpClient` di usa il browser per gestire il traffico HTTP in background.</span><span class="sxs-lookup"><span data-stu-id="d6057-123">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="d6057-124">[HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) viene impostato automaticamente sul prefisso URI di base dell'app.</span><span class="sxs-lookup"><span data-stu-id="d6057-124">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="d6057-125">Per altre informazioni, vedere <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="d6057-125">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="d6057-126">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6057-126">Singleton</span></span> | <span data-ttu-id="d6057-127">Rappresenta un'istanza di un runtime JavaScript in cui vengono inviate le chiamate a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d6057-127">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="d6057-128">Per altre informazioni, vedere <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="d6057-128">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `IUriHelper` | <span data-ttu-id="d6057-129">Singleton</span><span class="sxs-lookup"><span data-stu-id="d6057-129">Singleton</span></span> | <span data-ttu-id="d6057-130">Contiene gli helper per lavorare con gli URI e lo stato di navigazione.</span><span class="sxs-lookup"><span data-stu-id="d6057-130">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="d6057-131">Per ulteriori informazioni, vedere [URI e Helper dello stato di navigazione](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="d6057-131">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="d6057-132">Un provider di servizi personalizzato non fornisce automaticamente i servizi predefiniti elencati nella tabella.</span><span class="sxs-lookup"><span data-stu-id="d6057-132">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="d6057-133">Se si usa un provider di servizi personalizzato e si richiede uno dei servizi indicati nella tabella, aggiungere i servizi necessari al nuovo provider di servizi.</span><span class="sxs-lookup"><span data-stu-id="d6057-133">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="d6057-134">Aggiungere servizi a un'app</span><span class="sxs-lookup"><span data-stu-id="d6057-134">Add services to an app</span></span>

<span data-ttu-id="d6057-135">Dopo aver creato una nuova app, esaminare `Startup.ConfigureServices` il metodo:</span><span class="sxs-lookup"><span data-stu-id="d6057-135">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="d6057-136">Al `ConfigureServices` metodo viene passato un <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>oggetto, che è un elenco di oggetti del descrittore del servizio (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="d6057-136">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="d6057-137">I servizi vengono aggiunti fornendo descrittori del servizio alla raccolta di servizi.</span><span class="sxs-lookup"><span data-stu-id="d6057-137">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="d6057-138">Nell'esempio seguente viene illustrato il concetto con `IDataAccess` l'interfaccia e la relativa `DataAccess`implementazione concreta:</span><span class="sxs-lookup"><span data-stu-id="d6057-138">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="d6057-139">I servizi possono essere configurati con le durate mostrate nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="d6057-139">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="d6057-140">Durata</span><span class="sxs-lookup"><span data-stu-id="d6057-140">Lifetime</span></span> | <span data-ttu-id="d6057-141">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d6057-141">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="d6057-142">Il lato client di Blazer attualmente non dispone di un concetto di ambiti di.</span><span class="sxs-lookup"><span data-stu-id="d6057-142">Blazor client-side doesn't currently have a concept of DI scopes.</span></span> <span data-ttu-id="d6057-143">`Scoped`-i servizi registrati si `Singleton` comportano come servizi.</span><span class="sxs-lookup"><span data-stu-id="d6057-143">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="d6057-144">Tuttavia, il modello di hosting lato server supporta il `Scoped` ciclo di vita.</span><span class="sxs-lookup"><span data-stu-id="d6057-144">However, the server-side hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="d6057-145">In un componente Razor, la registrazione di un servizio con ambito ha come ambito la connessione.</span><span class="sxs-lookup"><span data-stu-id="d6057-145">In a Razor component, a scoped service registration is scoped to the connection.</span></span> <span data-ttu-id="d6057-146">Per questo motivo, è preferibile usare i servizi con ambito per i servizi che devono avere come ambito l'utente corrente, anche se l'obiettivo corrente è eseguire sul lato client nel browser.</span><span class="sxs-lookup"><span data-stu-id="d6057-146">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="d6057-147">La creazione di una *singola istanza* del servizio.</span><span class="sxs-lookup"><span data-stu-id="d6057-147">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="d6057-148">Tutti i componenti che richiedono `Singleton` un servizio ricevono un'istanza dello stesso servizio.</span><span class="sxs-lookup"><span data-stu-id="d6057-148">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="d6057-149">Ogni volta che un componente ottiene un'istanza di `Transient` un servizio dal contenitore del servizio, riceve una *nuova istanza* del servizio.</span><span class="sxs-lookup"><span data-stu-id="d6057-149">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="d6057-150">Il sistema DI è basato sul sistema DI ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6057-150">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="d6057-151">Per altre informazioni, vedere <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d6057-151">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="d6057-152">Richiedere un servizio in un componente</span><span class="sxs-lookup"><span data-stu-id="d6057-152">Request a service in a component</span></span>

<span data-ttu-id="d6057-153">Una volta aggiunti i servizi alla raccolta di servizi, inserire i servizi nei componenti usando la [ \@direttiva Inject](xref:mvc/views/razor#inject) Razor.</span><span class="sxs-lookup"><span data-stu-id="d6057-153">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="d6057-154">`@inject`dispone di due parametri:</span><span class="sxs-lookup"><span data-stu-id="d6057-154">`@inject` has two parameters:</span></span>

* <span data-ttu-id="d6057-155">Digitare &ndash; il tipo di servizio da inserire.</span><span class="sxs-lookup"><span data-stu-id="d6057-155">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="d6057-156">Proprietà &ndash; nome della proprietà che riceve il servizio app inserito.</span><span class="sxs-lookup"><span data-stu-id="d6057-156">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="d6057-157">La proprietà non richiede la creazione manuale.</span><span class="sxs-lookup"><span data-stu-id="d6057-157">The property doesn't require manual creation.</span></span> <span data-ttu-id="d6057-158">Il compilatore crea la proprietà.</span><span class="sxs-lookup"><span data-stu-id="d6057-158">The compiler creates the property.</span></span>

<span data-ttu-id="d6057-159">Per altre informazioni, vedere <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d6057-159">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="d6057-160">Usare più `@inject` istruzioni per inserire servizi diversi.</span><span class="sxs-lookup"><span data-stu-id="d6057-160">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="d6057-161">Nell'esempio riportato di seguito viene illustrato come usare `@inject`.</span><span class="sxs-lookup"><span data-stu-id="d6057-161">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="d6057-162">Il servizio che `Services.IDataAccess` implementa viene inserito nella proprietà `DataRepository`del componente.</span><span class="sxs-lookup"><span data-stu-id="d6057-162">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="d6057-163">Si noti come il codice usa solo l' `IDataAccess` astrazione:</span><span class="sxs-lookup"><span data-stu-id="d6057-163">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="d6057-164">Internamente, la proprietà generata`DataRepository`() è decorata `InjectAttribute` con l'attributo.</span><span class="sxs-lookup"><span data-stu-id="d6057-164">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="d6057-165">In genere, questo attributo non viene utilizzato direttamente.</span><span class="sxs-lookup"><span data-stu-id="d6057-165">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="d6057-166">Se è necessaria una classe base per i componenti e le proprietà inserite sono necessarie anche per la classe base, aggiungere `InjectAttribute`manualmente:</span><span class="sxs-lookup"><span data-stu-id="d6057-166">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="d6057-167">Nei componenti derivati dalla classe di base `@inject` , la direttiva non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="d6057-167">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="d6057-168">La `InjectAttribute` classe della classe base è sufficiente:</span><span class="sxs-lookup"><span data-stu-id="d6057-168">The `InjectAttribute` of the base class is sufficient:</span></span>

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="d6057-169">Usare l'inserimento DI dipendenze nei servizi</span><span class="sxs-lookup"><span data-stu-id="d6057-169">Use DI in services</span></span>

<span data-ttu-id="d6057-170">Servizi complessi potrebbe richiedere servizi aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="d6057-170">Complex services might require additional services.</span></span> <span data-ttu-id="d6057-171">Nell'esempio precedente, `DataAccess` potrebbe richiedere il `HttpClient` servizio predefinito.</span><span class="sxs-lookup"><span data-stu-id="d6057-171">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="d6057-172">`@inject`(o) `InjectAttribute`non è disponibile per l'uso nei servizi.</span><span class="sxs-lookup"><span data-stu-id="d6057-172">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="d6057-173">È necessario usare invece l' *inserimento del costruttore* .</span><span class="sxs-lookup"><span data-stu-id="d6057-173">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="d6057-174">I servizi necessari vengono aggiunti aggiungendo parametri al costruttore del servizio.</span><span class="sxs-lookup"><span data-stu-id="d6057-174">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="d6057-175">Quando si crea il servizio, vengono riconosciuti i servizi richiesti nel costruttore e forniti DI conseguenza.</span><span class="sxs-lookup"><span data-stu-id="d6057-175">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="d6057-176">Prerequisiti per l'inserimento del costruttore:</span><span class="sxs-lookup"><span data-stu-id="d6057-176">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="d6057-177">È necessario che esista un costruttore i cui argomenti possono essere tutti soddisfatti da DI.</span><span class="sxs-lookup"><span data-stu-id="d6057-177">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="d6057-178">Sono consentiti parametri aggiuntivi non analizzati da DI se specificano i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="d6057-178">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="d6057-179">Il costruttore applicabile deve essere *pubblico*.</span><span class="sxs-lookup"><span data-stu-id="d6057-179">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="d6057-180">È necessario che esista un costruttore applicabile.</span><span class="sxs-lookup"><span data-stu-id="d6057-180">One applicable constructor must exist.</span></span> <span data-ttu-id="d6057-181">In caso di ambiguità, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="d6057-181">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d6057-182">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d6057-182">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>