---
title: Routing ad azioni del controller in ASP.NET Core
author: rick-anderson
description: Informazioni su come ASP.NET Core MVC usa middleware di routing per verificare la corrispondenza degli URL delle richieste in ingresso ed eseguirne il mapping alle azioni.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/routing
ms.openlocfilehash: 1116cc699f749a137638b75095a7172ad0d4858a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663723"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="4161c-103">Routing ad azioni del controller in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4161c-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="4161c-104">Di [Ryan Nowak](https://github.com/rynowak) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4161c-104">By [Ryan Nowak](https://github.com/rynowak) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4161c-105">ASP.NET Core MVC usa [middleware](xref:fundamentals/middleware/index) di routing per verificare la corrispondenza degli URL delle richieste in ingresso ed eseguirne il mapping alle azioni.</span><span class="sxs-lookup"><span data-stu-id="4161c-105">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="4161c-106">Le route sono definite nel codice di avvio o negli attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-106">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="4161c-107">Le route descrivono il modo in cui i percorsi URL devono corrispondere alle azioni.</span><span class="sxs-lookup"><span data-stu-id="4161c-107">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="4161c-108">Vengono usate anche per generare gli URL (per i collegamenti) inviati nelle risposte.</span><span class="sxs-lookup"><span data-stu-id="4161c-108">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="4161c-109">Le azioni vengono indirizzate in modo convenzionale o con attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-109">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="4161c-110">Se una route viene inserita nel controller o nell'azione, viene indirizzata con attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-110">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="4161c-111">Per altre informazioni, vedere [Routing misto](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="4161c-111">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="4161c-112">Questo documento illustra le interazioni tra MVC e il routing e il modo in cui le tipiche app MVC usano le funzionalità di routing.</span><span class="sxs-lookup"><span data-stu-id="4161c-112">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="4161c-113">Vedere [Routing](xref:fundamentals/routing) per informazioni dettagliate sul routing avanzato.</span><span class="sxs-lookup"><span data-stu-id="4161c-113">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="4161c-114">Impostazione del middleware di routing</span><span class="sxs-lookup"><span data-stu-id="4161c-114">Setting up Routing Middleware</span></span>

<span data-ttu-id="4161c-115">Nel metodo *Configure* può apparire codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="4161c-115">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="4161c-116">Nella chiamata a `UseMvc`, si usa `MapRoute` per creare una singola route, a cui si farà riferimento come alla route predefinita (`default`).</span><span class="sxs-lookup"><span data-stu-id="4161c-116">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="4161c-117">La maggior parte delle app MVC userà una route con un modello simile alla route `default`.</span><span class="sxs-lookup"><span data-stu-id="4161c-117">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="4161c-118">Il modello di route `"{controller=Home}/{action=Index}/{id?}"` può corrispondere a un percorso URL come `/Products/Details/5` ed estrae i valori di route `{ controller = Products, action = Details, id = 5 }` suddividendo il percorso in token.</span><span class="sxs-lookup"><span data-stu-id="4161c-118">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="4161c-119">MVC tenterà di individuare un controller denominato `ProductsController` e di eseguire l'azione `Details`:</span><span class="sxs-lookup"><span data-stu-id="4161c-119">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="4161c-120">Si noti che in questo esempio l'associazione di modelli usa il valore di `id = 5` per impostare il parametro `id` su `5` quando si richiama l'azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-120">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="4161c-121">Per maggiori dettagli, vedere [Associazione di modelli](../models/model-binding.md).</span><span class="sxs-lookup"><span data-stu-id="4161c-121">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="4161c-122">Usando la route `default`:</span><span class="sxs-lookup"><span data-stu-id="4161c-122">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="4161c-123">Il modello di route:</span><span class="sxs-lookup"><span data-stu-id="4161c-123">The route template:</span></span>

* <span data-ttu-id="4161c-124">`{controller=Home}` definisce `Home` come oggetto `controller` predefinito</span><span class="sxs-lookup"><span data-stu-id="4161c-124">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="4161c-125">`{action=Index}` definisce `Index` come oggetto `action` predefinito</span><span class="sxs-lookup"><span data-stu-id="4161c-125">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="4161c-126">`{id?}` definisce `id` come facoltativo</span><span class="sxs-lookup"><span data-stu-id="4161c-126">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="4161c-127">I parametri di route predefiniti e facoltativi non devono necessariamente essere presenti nel percorso URL per trovare una corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="4161c-127">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="4161c-128">Vedere [Riferimento per i modelli di route](../../fundamentals/routing.md#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.</span><span class="sxs-lookup"><span data-stu-id="4161c-128">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="4161c-129">`"{controller=Home}/{action=Index}/{id?}"` può corrispondere al percorso URL `/` e restituirà i valori di route `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="4161c-129">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="4161c-130">I valori per `controller` e `action` usano i valori predefiniti, `id` non genera un valore perché non esiste un segmento corrispondente nel percorso URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-130">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="4161c-131">MVC usa questi valori di route per selezionare l'azione `HomeController` e `Index`:</span><span class="sxs-lookup"><span data-stu-id="4161c-131">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="4161c-132">Usando questa definizione del controller e il modello di route, l'azione `HomeController.Index` viene eseguita per tutti i percorsi URL seguenti:</span><span class="sxs-lookup"><span data-stu-id="4161c-132">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="4161c-133">Il metodo pratico `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="4161c-133">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="4161c-134">Può essere usato per sostituire:</span><span class="sxs-lookup"><span data-stu-id="4161c-134">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="4161c-135">`UseMvc` e `UseMvcWithDefaultRoute` aggiungono un'istanza di `RouterMiddleware` alla pipeline middleware.</span><span class="sxs-lookup"><span data-stu-id="4161c-135">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="4161c-136">MVC non interagisce direttamente con il middleware e usa il routing per gestire le richieste.</span><span class="sxs-lookup"><span data-stu-id="4161c-136">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="4161c-137">MVC è connesso alle route attraverso un'istanza di `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="4161c-137">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="4161c-138">Il codice all'interno di `UseMvc` è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="4161c-138">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="4161c-139">`UseMvc` non definisce direttamente le route, aggiunge un segnaposto alla raccolta di route per la route `attribute`.</span><span class="sxs-lookup"><span data-stu-id="4161c-139">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="4161c-140">L'overload `UseMvc(Action<IRouteBuilder>)` consente di aggiungere le proprie route e supporta anche il routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-140">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="4161c-141">`UseMvc` e tutte le relative varianti aggiungono un segnaposto per l'attributo Route: il routing degli attributi è sempre disponibile indipendentemente dalla modalità di configurazione `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="4161c-141">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="4161c-142">`UseMvcWithDefaultRoute` definisce una route predefinita e supporta il routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-142">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="4161c-143">La sezione [Routing con attributi](#attribute-routing-ref-label) include maggiori dettagli sul routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-143">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="4161c-144">Routing convenzionale</span><span class="sxs-lookup"><span data-stu-id="4161c-144">Conventional routing</span></span>

<span data-ttu-id="4161c-145">La route predefinita (`default`):</span><span class="sxs-lookup"><span data-stu-id="4161c-145">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="4161c-146">è un esempio di *routing convenzionale*.</span><span class="sxs-lookup"><span data-stu-id="4161c-146">is an example of a *conventional routing*.</span></span> <span data-ttu-id="4161c-147">Questo stile è detto *routing convenzionale* poiché stabilisce una *convenzione* per i percorsi URL:</span><span class="sxs-lookup"><span data-stu-id="4161c-147">We call this style *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="4161c-148">il primo segmento del percorso esegue il mapping al nome del controller</span><span class="sxs-lookup"><span data-stu-id="4161c-148">the first path segment maps to the controller name</span></span>

* <span data-ttu-id="4161c-149">il secondo esegue il mapping al nome dell'azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-149">the second maps to the action name.</span></span>

* <span data-ttu-id="4161c-150">il terzo segmento viene usato per un `id` facoltativo usato per eseguire il mapping a un'entità del modello</span><span class="sxs-lookup"><span data-stu-id="4161c-150">the third segment is used for an optional `id` used to map to a model entity</span></span>

<span data-ttu-id="4161c-151">Usando la route `default`, il percorso URL `/Products/List` esegue il mapping all'azione `ProductsController.List` e `/Blog/Article/17` lo esegue a `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="4161c-151">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="4161c-152">Questo mapping si basa **solo** sui nomi dell'azione e del controller e non su spazi dei nomi, percorsi dei file di origine o parametri del metodo.</span><span class="sxs-lookup"><span data-stu-id="4161c-152">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="4161c-153">L'uso del routing convenzionale con la route predefinita consente di compilare rapidamente l'applicazione senza dover creare un nuovo modello di URL per ogni azione che si definisce.</span><span class="sxs-lookup"><span data-stu-id="4161c-153">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="4161c-154">Per un'applicazione con azioni di stile CRUD, la coerenza degli URL tra i controller può semplificare il codice e rendere l'interfaccia utente più prevedibile.</span><span class="sxs-lookup"><span data-stu-id="4161c-154">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="4161c-155">L'oggetto `id` è definito come facoltativo nel modello di route, ovvero le azioni possono essere eseguite senza l'ID specificato come parte dell'URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-155">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="4161c-156">In genere, se si omette `id` dall'URL, il valore viene impostato su `0` dall'associazione del modello e di conseguenza non viene rilevata alcuna entità nel database corrispondente a `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="4161c-156">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="4161c-157">Il routing con attributi offre un controllo con granularità fine che consente di rendere l'ID obbligatorio per alcune azioni e non per altre.</span><span class="sxs-lookup"><span data-stu-id="4161c-157">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="4161c-158">Per convenzione la documentazione include i parametri facoltativi come `id` quando è probabile che appaiano nell'uso corretto.</span><span class="sxs-lookup"><span data-stu-id="4161c-158">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="4161c-159">Route multiple</span><span class="sxs-lookup"><span data-stu-id="4161c-159">Multiple routes</span></span>

<span data-ttu-id="4161c-160">È possibile aggiungere più route all'interno di `UseMvc` aggiungendo altre chiamate a `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="4161c-160">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="4161c-161">Questa operazione consente di definire più convenzioni o di aggiungere route convenzionali dedicate a un'azione specifica, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4161c-161">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="4161c-162">La route `blog` è una *route convenzionale dedicata*, vale a dire che usa il sistema di routing convenzionale, ma è dedicata a un'azione specifica.</span><span class="sxs-lookup"><span data-stu-id="4161c-162">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="4161c-163">Poiché `controller` e `action` non appaiono nel modello di route come parametri, possono avere solo i valori predefiniti e quindi questa route eseguirà sempre il mapping all'azione `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="4161c-163">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="4161c-164">Le route sono ordinate nella raccolta di route e verranno elaborate nell'ordine in cui vengono aggiunte.</span><span class="sxs-lookup"><span data-stu-id="4161c-164">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="4161c-165">Quindi in questo esempio la route `blog` viene tentata prima della route `default`.</span><span class="sxs-lookup"><span data-stu-id="4161c-165">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="4161c-166">Le *route convenzionali dedicate* usano spesso parametri di route catch-all come `{*article}` per acquisire la parte rimanente del percorso URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-166">*Dedicated conventional routes* often use catch-all route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="4161c-167">In questo modo una route può diventare troppo "greedy", ovvero corrispondere agli URL che dovrebbero corrispondere ad altre route.</span><span class="sxs-lookup"><span data-stu-id="4161c-167">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="4161c-168">Posizionare le route "greedy" più avanti nella tabella delle route per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="4161c-168">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="4161c-169">Fallback</span><span class="sxs-lookup"><span data-stu-id="4161c-169">Fallback</span></span>

<span data-ttu-id="4161c-170">Come parte dell'elaborazione della richiesta, MVC verifica che i valori di route possano essere usati per trovare un controller e un'azione nell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="4161c-170">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="4161c-171">Se i valori di route non corrispondano a un'azione, la route non viene considerata una corrispondenza e viene tentata la route successiva.</span><span class="sxs-lookup"><span data-stu-id="4161c-171">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="4161c-172">Questa operazione si chiama *fallback* e viene eseguita allo scopo di semplificare i casi in cui le route convenzionali si sovrappongono.</span><span class="sxs-lookup"><span data-stu-id="4161c-172">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="4161c-173">Rimozione dell'ambiguità delle azioni</span><span class="sxs-lookup"><span data-stu-id="4161c-173">Disambiguating actions</span></span>

<span data-ttu-id="4161c-174">Quando due azioni corrispondono in base al routing, MVC deve rimuovere l'ambiguità per scegliere il candidato migliore, altrimenti genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="4161c-174">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="4161c-175">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4161c-175">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="4161c-176">Questo controller definisce due azioni corrispondenti al percorso URL `/Products/Edit/17` e indirizzano i dati `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="4161c-176">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="4161c-177">Si tratta di un modello tipico per i controller MVC in cui `Edit(int)` visualizza un modulo per modificare un prodotto e `Edit(int, Product)` elabora il modulo inviato.</span><span class="sxs-lookup"><span data-stu-id="4161c-177">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="4161c-178">Per poter eseguire l'operazione, MVC deve scegliere `Edit(int, Product)` se la richiesta è un `POST` HTTP e `Edit(int)` quando il verbo HTTP è un altro elemento.</span><span class="sxs-lookup"><span data-stu-id="4161c-178">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="4161c-179">`HttpPostAttribute` (`[HttpPost]`) è un'implementazione di `IActionConstraint` che consente di selezionare l'azione solo quando il verbo HTTP è `POST`.</span><span class="sxs-lookup"><span data-stu-id="4161c-179">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="4161c-180">La presenza di un oggetto `IActionConstraint` fa sì che `Edit(int, Product)` sia una corrispondenza migliore di `Edit(int)`, quindi `Edit(int, Product)` viene tentata per prima.</span><span class="sxs-lookup"><span data-stu-id="4161c-180">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="4161c-181">È necessario scrivere implementazioni personalizzate di `IActionConstraint` solo in scenari specializzati, ma è importante comprendere il ruolo degli attributi, ad esempio `HttpPostAttribute`. Attributi simili vengono definiti per altri verbi HTTP.</span><span class="sxs-lookup"><span data-stu-id="4161c-181">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="4161c-182">Nel routing convenzionale è normale che le azioni usino lo stesso nome di azione quando fanno parte di un flusso di lavoro `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="4161c-182">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="4161c-183">La comodità offerta da questo modello sarà più evidente dopo aver esaminato la sezione relativa a [IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="4161c-183">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="4161c-184">Se vi sono più route corrispondenti e MVC non è in grado di trovare la route migliore, viene generata un'eccezione `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="4161c-184">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="4161c-185">Nomi di route</span><span class="sxs-lookup"><span data-stu-id="4161c-185">Route names</span></span>

<span data-ttu-id="4161c-186">Le stringhe `"blog"` e `"default"` degli esempi seguenti sono nomi di route:</span><span class="sxs-lookup"><span data-stu-id="4161c-186">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="4161c-187">I nomi di route consentono di assegnare a una route un nome logico, in modo che la route denominata possa essere usata per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-187">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="4161c-188">Questo semplifica notevolmente la creazione degli URL quando l'ordinamento delle route può rendere complessa la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-188">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="4161c-189">I nomi di route devono essere univoci a livello di applicazione.</span><span class="sxs-lookup"><span data-stu-id="4161c-189">Route names must be unique application-wide.</span></span>

<span data-ttu-id="4161c-190">I nomi di route non influiscono sulla corrispondenza degli URL o sulla gestione delle richieste, vengono usati solo per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-190">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="4161c-191">L'articolo sul [routing](xref:fundamentals/routing) contiene informazioni dettagliate sulla generazione di URL, inclusa la generazione negli helper specifici di MVC.</span><span class="sxs-lookup"><span data-stu-id="4161c-191">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="4161c-192">Routing con attributi</span><span class="sxs-lookup"><span data-stu-id="4161c-192">Attribute routing</span></span>

<span data-ttu-id="4161c-193">Il routing con attributi usa un set di attributi per eseguire il mapping delle azioni direttamente ai modelli di route.</span><span class="sxs-lookup"><span data-stu-id="4161c-193">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="4161c-194">Nell'esempio seguente viene usato l'oggetto `app.UseMvc();` nel metodo `Configure` e non viene passata alcuna route.</span><span class="sxs-lookup"><span data-stu-id="4161c-194">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="4161c-195">`HomeController` corrisponde a un set di URL simile a quello a cui corrisponderebbe la route predefinita `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="4161c-195">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="4161c-196">L'azione `HomeController.Index()` verrà eseguita per tutti i percorsi URL, `/`, `/Home` o `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="4161c-196">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="4161c-197">In questo esempio viene evidenziata un'importante differenza a livello di programmazione tra il routing con attributi e il routing convenzionale.</span><span class="sxs-lookup"><span data-stu-id="4161c-197">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="4161c-198">Il routing con attributi richiede più input per specificare una route, la route convenzionale predefinita gestisce le route in modo più conciso.</span><span class="sxs-lookup"><span data-stu-id="4161c-198">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="4161c-199">Tuttavia, il routing con attributi consente, e richiede, un controllo preciso dei modelli route da applicare a ogni azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-199">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="4161c-200">Con il routing con attributi i nomi del controller e delle azioni **non** hanno alcun ruolo nella selezione dell'azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-200">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="4161c-201">In questo esempio viene verificata la corrispondenza degli stessi URL dell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="4161c-201">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="4161c-202">I modelli di route precedenti non definiscono i parametri di route per `action`, `area` e `controller`.</span><span class="sxs-lookup"><span data-stu-id="4161c-202">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="4161c-203">In realtà, questi parametri di route non sono consentiti nelle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-203">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="4161c-204">Poiché il modello di route è già associato a un'azione, non avrebbe senso analizzare il nome dell'azione dall'URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-204">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="4161c-205">Routing con attributi Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="4161c-205">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="4161c-206">Il routing con attributi può usare anche gli attributi `Http[Verb]`, ad esempio `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="4161c-206">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="4161c-207">Tutti questi attributi possono accettare un modello di route.</span><span class="sxs-lookup"><span data-stu-id="4161c-207">All of these attributes can accept a route template.</span></span> <span data-ttu-id="4161c-208">Questo esempio illustra due azioni che corrispondono allo stesso modello di route:</span><span class="sxs-lookup"><span data-stu-id="4161c-208">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="4161c-209">Per un percorso URL come `/products` l'azione `ProductsApi.ListProducts` verrà eseguita quando il verbo HTTP è `GET` e `ProductsApi.CreateProduct` verrà eseguita quando il verbo HTTP è `POST`.</span><span class="sxs-lookup"><span data-stu-id="4161c-209">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="4161c-210">Nel routing con attributi l'URL viene prima confrontato con il set di modelli di route definiti dagli attributi della route.</span><span class="sxs-lookup"><span data-stu-id="4161c-210">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="4161c-211">Quando un modello di route corrisponde, vengono applicati i vincoli `IActionConstraint` per determinare quali azioni possono essere eseguite.</span><span class="sxs-lookup"><span data-stu-id="4161c-211">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="4161c-212">Quando si compila un'API REST, è raro che si voglia usare `[Route(...)]` su un metodo di azione perché l'azione accetterà tutti i metodi HTTP.</span><span class="sxs-lookup"><span data-stu-id="4161c-212">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="4161c-213">Si consiglia di usare l'oggetto `Http*Verb*Attributes`, più specifico, per essere precisi circa gli elementi supportati dall'API.</span><span class="sxs-lookup"><span data-stu-id="4161c-213">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="4161c-214">I client delle API REST devono sapere quali percorsi e verbi HTTP devono essere mappati a operazioni logiche specifiche.</span><span class="sxs-lookup"><span data-stu-id="4161c-214">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="4161c-215">Poiché una route con attributi si applica a un'azione specifica, è facile fare in modo che i parametri siano richiesti come parte della definizione del modello di route.</span><span class="sxs-lookup"><span data-stu-id="4161c-215">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="4161c-216">In questo esempio `id` è richiesto come parte del percorso URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-216">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="4161c-217">L'azione `ProductsApi.GetProduct(int)` verrà eseguita per un percorso URL come `/products/3`, ma non per un percorso URL come `/products`.</span><span class="sxs-lookup"><span data-stu-id="4161c-217">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="4161c-218">Vedere [Routing](../../fundamentals/routing.md) per una descrizione completa dei modelli di route e delle opzioni correlate.</span><span class="sxs-lookup"><span data-stu-id="4161c-218">See [Routing](../../fundamentals/routing.md) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="4161c-219">Nome di route</span><span class="sxs-lookup"><span data-stu-id="4161c-219">Route Name</span></span>

<span data-ttu-id="4161c-220">Nell’esempio di codice riportato di seguito viene definito un *nome di route* di `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="4161c-220">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="4161c-221">I nomi di route possono essere usati per generare un URL in base a un percorso specifico.</span><span class="sxs-lookup"><span data-stu-id="4161c-221">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="4161c-222">I nomi di route non influiscono sul comportamento del routing riguardo alla corrispondenza degli URL e vengono usati solo per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-222">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="4161c-223">I nomi di route devono essere univoci a livello di applicazione.</span><span class="sxs-lookup"><span data-stu-id="4161c-223">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="4161c-224">Confrontare questa opzione con la *route predefinita* convenzionale, che definisce il parametro `id` come facoltativo (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="4161c-224">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="4161c-225">La possibilità di specificare in modo preciso le API presenta dei vantaggi, ad esempio consente di inviare `/products` e `/products/5` ad azioni  differenti.</span><span class="sxs-lookup"><span data-stu-id="4161c-225">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="4161c-226">Combinazione di route</span><span class="sxs-lookup"><span data-stu-id="4161c-226">Combining routes</span></span>

<span data-ttu-id="4161c-227">Per rendere il routing con attributi meno ripetitivo, gli attributi di route del controller vengono combinati con gli attributi di route delle singole azioni.</span><span class="sxs-lookup"><span data-stu-id="4161c-227">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="4161c-228">I modelli di route definiti per il controller vengono anteposti ai modelli di route delle azioni.</span><span class="sxs-lookup"><span data-stu-id="4161c-228">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="4161c-229">Inserendo un attributo di route nel controller, **tutte** le azioni presenti nel controller useranno il routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-229">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="4161c-230">In questo esempio il percorso URL `/products` può corrispondere a `ProductsApi.ListProducts` e il percorso URL `/products/5` può corrispondere a `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="4161c-230">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="4161c-231">Entrambe le azioni corrispondono solo a `GET` HTTP perché sono contrassegnate con l'`HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="4161c-231">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="4161c-232">I modelli di route applicati a un'azione che iniziano con `/` o `~/` non vengono combinati con i modelli di route applicati al controller.</span><span class="sxs-lookup"><span data-stu-id="4161c-232">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="4161c-233">Questo esempio illustra la corrispondenza di un set di percorsi URL simile alla *route predefinita*.</span><span class="sxs-lookup"><span data-stu-id="4161c-233">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="4161c-234">Ordinamento delle route con attributi</span><span class="sxs-lookup"><span data-stu-id="4161c-234">Ordering attribute routes</span></span>

<span data-ttu-id="4161c-235">A differenza delle route convenzionali che vengono eseguite in un ordine definito, il routing con attributi crea una struttura ad albero e confronta tutte le route contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="4161c-235">In contrast to conventional routes which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="4161c-236">Si comporta come se le voci delle route seguissero un ordine ideale in cui le route più specifiche vengono probabilmente eseguite prima delle route più generali.</span><span class="sxs-lookup"><span data-stu-id="4161c-236">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="4161c-237">Ad esempio, una route come `blog/search/{topic}` è più specifica di una route come `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="4161c-237">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="4161c-238">In termini logici la route `blog/search/{topic}` viene eseguita per prima, per impostazione predefinita, perché questo è l'ordine più plausibile.</span><span class="sxs-lookup"><span data-stu-id="4161c-238">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="4161c-239">Usando il routing convenzionale, lo sviluppatore ordina le route in base alle proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="4161c-239">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="4161c-240">Nelle route con attributi si può configurare un ordine usando la proprietà `Order` di tutti gli attributi di route specificati dal framework.</span><span class="sxs-lookup"><span data-stu-id="4161c-240">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="4161c-241">Le route vengono elaborate in base a un ordinamento crescente della proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="4161c-241">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="4161c-242">L'ordine predefinito è `0`.</span><span class="sxs-lookup"><span data-stu-id="4161c-242">The default order is `0`.</span></span> <span data-ttu-id="4161c-243">L'impostazione di una route usando `Order = -1` viene eseguita prima delle route per cui non è impostato un ordine.</span><span class="sxs-lookup"><span data-stu-id="4161c-243">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="4161c-244">L'impostazione di una route usando `Order = 1` viene eseguito dopo l'ordinamento predefinito delle route.</span><span class="sxs-lookup"><span data-stu-id="4161c-244">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="4161c-245">Evitare la dipendenza da `Order`.</span><span class="sxs-lookup"><span data-stu-id="4161c-245">Avoid depending on `Order`.</span></span> <span data-ttu-id="4161c-246">Se lo spazio URL richiede i valori in un ordine esplicito per indirizzare correttamente i dati, si può probabilmente creare confusione anche per i client.</span><span class="sxs-lookup"><span data-stu-id="4161c-246">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="4161c-247">In genere il routing con attributi seleziona la route corretta con la corrispondenza di URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-247">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="4161c-248">Se l'ordine predefinito usato per la generazione di URL non funziona, usare il nome della route come override è in genere più semplice che applicare la proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="4161c-248">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="4161c-249">Il routing di Razor Pages e il routing del controller MVC condividono un'implementazione.</span><span class="sxs-lookup"><span data-stu-id="4161c-249">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="4161c-250">Per informazioni sull'ordine di routing negli argomenti di Razor Pages, vedere [Convenzioni di route e app per Razor Pages in ASP.NET Core: Ordine della route](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="4161c-250">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="4161c-251">Sostituzione dei token nei modelli di route ([controller], [action], [area])</span><span class="sxs-lookup"><span data-stu-id="4161c-251">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="4161c-252">Per praticità, le route con attributi supportano la *sostituzione dei token* eseguita racchiudendo i token tra parentesi quadre (`[`, `]`).</span><span class="sxs-lookup"><span data-stu-id="4161c-252">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="4161c-253">I token `[action]`, `[area]` e `[controller]` vengono sostituiti con i valori di nome dell'azione, nome dell'area e nome del controller dall'azione in cui è definita la route.</span><span class="sxs-lookup"><span data-stu-id="4161c-253">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="4161c-254">Nell'esempio seguente le azioni corrispondono ai percorsi URL come descritto nei commenti:</span><span class="sxs-lookup"><span data-stu-id="4161c-254">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="4161c-255">La sostituzione dei token avviene come ultimo passaggio della creazione delle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-255">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="4161c-256">L'esempio precedente si comporterà come il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="4161c-256">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="4161c-257">Le route con attributi possono anche essere combinate con l'ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="4161c-257">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="4161c-258">Ciò risulta particolarmente efficace se combinato con la sostituzione dei token.</span><span class="sxs-lookup"><span data-stu-id="4161c-258">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="4161c-259">La sostituzione dei token si applica anche ai nomi di route definiti dalle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-259">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="4161c-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]` genera un nome di route univoco per ogni azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="4161c-261">Per verificare la corrispondenza del delimitatore letterale della sostituzione di token `[` o `]`, eseguirne l'escape ripetendo il carattere (`[[` o `]]`).</span><span class="sxs-lookup"><span data-stu-id="4161c-261">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker range=">= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="4161c-262">Usare un trasformatore di parametri per personalizzare la sostituzione dei token</span><span class="sxs-lookup"><span data-stu-id="4161c-262">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="4161c-263">La sostituzione dei token può essere personalizzata usando un trasformatore di parametri.</span><span class="sxs-lookup"><span data-stu-id="4161c-263">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="4161c-264">Un trasformatore di parametri implementa `IOutboundParameterTransformer` e trasforma il valore dei parametri.</span><span class="sxs-lookup"><span data-stu-id="4161c-264">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="4161c-265">Ad esempio, un trasformatore di parametri `SlugifyParameterTransformer` personalizzato cambia il valore di route `SubscriptionManagement` in `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="4161c-265">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="4161c-266">`RouteTokenTransformerConvention` è una convenzione del modello di applicazione che:</span><span class="sxs-lookup"><span data-stu-id="4161c-266">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="4161c-267">Applica un trasformatore di parametri a tutte le route di attributi in un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="4161c-267">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="4161c-268">Personalizza i valori dei token delle route di attributi quando vengono sostituiti.</span><span class="sxs-lookup"><span data-stu-id="4161c-268">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="4161c-269">La convenzione `RouteTokenTransformerConvention` è registrata come un'opzione in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4161c-269">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="4161c-270">Route multiple</span><span class="sxs-lookup"><span data-stu-id="4161c-270">Multiple Routes</span></span>

<span data-ttu-id="4161c-271">Il routing con attributi supporta la definizione di più route che raggiungono la stessa azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-271">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="4161c-272">L'uso più comune è simulare il comportamento della *route convenzionale predefinita* come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="4161c-272">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="4161c-273">Inserire più attributi di route nel controller significa che verranno tutti combinati con tutti gli attributi di route nei metodi dell'azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-273">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="4161c-274">Quando più attributi di route (che implementano `IActionConstraint`) sono inseriti in un'azione, ogni vincolo dell'azione viene combinato con il modello di route dall'attributo che lo definisce.</span><span class="sxs-lookup"><span data-stu-id="4161c-274">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="4161c-275">Benché l'uso di più route per le azioni possa sembrare efficace, è preferibile mantenere lo spazio URL dell'applicazione semplice e ben definito.</span><span class="sxs-lookup"><span data-stu-id="4161c-275">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="4161c-276">Usare più route per le azioni solo se necessario, ad esempio per supportare i client esistenti.</span><span class="sxs-lookup"><span data-stu-id="4161c-276">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="4161c-277">Definizione di parametri facoltativi, valori predefiniti e vincoli della route con attributi</span><span class="sxs-lookup"><span data-stu-id="4161c-277">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="4161c-278">Le route con attributi supportano la stessa sintassi inline delle route convenzionali per specificare i parametri facoltativi, i valori predefiniti e i vincoli.</span><span class="sxs-lookup"><span data-stu-id="4161c-278">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="4161c-279">Vedere [Riferimento per i modelli di route](../../fundamentals/routing.md#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.</span><span class="sxs-lookup"><span data-stu-id="4161c-279">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="4161c-280">Attributi di route personalizzati che usano `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="4161c-280">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="4161c-281">Tutti gli attributi di route disponibili nel framework (`[Route(...)]`, `[HttpGet(...)]` e così via) implementano l'interfaccia `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="4161c-281">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="4161c-282">MVC cerca gli attributi per le classi del controller e i metodi delle azioni all'avvio dell'app e usa quelli che implementano `IRouteTemplateProvider` per compilare il set iniziale di route.</span><span class="sxs-lookup"><span data-stu-id="4161c-282">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="4161c-283">È possibile implementare `IRouteTemplateProvider` per definire i propri attributi di route.</span><span class="sxs-lookup"><span data-stu-id="4161c-283">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="4161c-284">Ogni `IRouteTemplateProvider` consente di definire una singola route con un modello di route, un ordinamento e un nome personalizzati:</span><span class="sxs-lookup"><span data-stu-id="4161c-284">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="4161c-285">L'attributo dell'esempio precedente imposta automaticamente `Template` su `"api/[controller]"` quando si applica `[MyApiController]`.</span><span class="sxs-lookup"><span data-stu-id="4161c-285">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="4161c-286">Uso del modello applicativo per personalizzare le route con attributi</span><span class="sxs-lookup"><span data-stu-id="4161c-286">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="4161c-287">Il *modello applicativo* è un modello a oggetti creato all'avvio con tutti i metadati usati da MVC per indirizzare ed eseguire le azioni.</span><span class="sxs-lookup"><span data-stu-id="4161c-287">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="4161c-288">Il *modello applicativo* include tutti i dati raccolti da attributi di route (attraverso `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="4161c-288">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="4161c-289">È possibile scrivere *convenzioni* per modificare il modello applicativo in fase di avvio per personalizzare il comportamento del routing.</span><span class="sxs-lookup"><span data-stu-id="4161c-289">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="4161c-290">Questa sezione illustra un semplice esempio di personalizzazione del routing con il modello applicativo.</span><span class="sxs-lookup"><span data-stu-id="4161c-290">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="4161c-291">Routing misto: routing con attributi e routing convenzionale</span><span class="sxs-lookup"><span data-stu-id="4161c-291">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="4161c-292">Le applicazioni MVC sono in grado di usare una combinazione di routing convenzionale e routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-292">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="4161c-293">In genere le route convenzionali vengono usate per i controller che gestiscono le pagine HTML per i browser e le route con attributi per i controller che gestiscono le API REST.</span><span class="sxs-lookup"><span data-stu-id="4161c-293">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="4161c-294">Le azioni vengono indirizzate in modo convenzionale o con attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-294">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="4161c-295">Se una route viene inserita nel controller o nell'azione, viene indirizzata con attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-295">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="4161c-296">Le azioni che definiscono le route con attributi non possono essere raggiunte usando le route convenzionali e viceversa.</span><span class="sxs-lookup"><span data-stu-id="4161c-296">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="4161c-297">**Qualsiasi** attributo di route del controller rende tutte le azioni presenti nel controller indirizzate con attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-297">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="4161c-298">Ciò che distingue i due tipi di sistema di routing è il processo applicato dopo che si è verificata la corrispondenza di un URL con un modello di route.</span><span class="sxs-lookup"><span data-stu-id="4161c-298">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="4161c-299">Nel routing convenzionale i valori di route della corrispondenza vengono usati per scegliere l'azione e il controller da una tabella di ricerca di tutte le azioni indirizzate in modo convenzionale.</span><span class="sxs-lookup"><span data-stu-id="4161c-299">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="4161c-300">Nel routing con attributi ogni modello è già associato a un'azione e non è necessaria alcuna ulteriore ricerca.</span><span class="sxs-lookup"><span data-stu-id="4161c-300">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="4161c-301">Segmenti complessi</span><span class="sxs-lookup"><span data-stu-id="4161c-301">Complex segments</span></span>

<span data-ttu-id="4161c-302">I segmenti complessi (ad esempio, `[Route("/dog{token}cat")]`), vengono elaborati individuando corrispondenze per i valori letterali da destra a sinistra in modalità non-greedy.</span><span class="sxs-lookup"><span data-stu-id="4161c-302">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="4161c-303">Vedere [il codice sorgente](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) per una descrizione.</span><span class="sxs-lookup"><span data-stu-id="4161c-303">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="4161c-304">Per altre informazioni, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="4161c-304">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="4161c-305">Generazione di URL</span><span class="sxs-lookup"><span data-stu-id="4161c-305">URL Generation</span></span>

<span data-ttu-id="4161c-306">Le applicazioni MVC sono in grado di usare le funzionalità di generazione di URL del routing per generare i collegamenti URL alle azioni.</span><span class="sxs-lookup"><span data-stu-id="4161c-306">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="4161c-307">La generazione di URL evita la codifica degli URL e consente di rendere il codice più affidabile e gestibile.</span><span class="sxs-lookup"><span data-stu-id="4161c-307">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="4161c-308">Questa sezione illustra le funzionalità di generazione di URL disponibili in MVC e tratta solo le nozioni di base sul funzionamento della generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-308">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="4161c-309">Vedere [Routing](../../fundamentals/routing.md) per una descrizione dettagliata della generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-309">See [Routing](../../fundamentals/routing.md) for a detailed description of URL generation.</span></span>

<span data-ttu-id="4161c-310">L'interfaccia `IUrlHelper` è l'elemento sottostante dell'infrastruttura tra MVC e il routing per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-310">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="4161c-311">È possibile trovare un'istanza di `IUrlHelper` disponibile usando la proprietà `Url` in controller, visualizzazioni e componenti di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="4161c-311">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="4161c-312">In questo esempio l'interfaccia `IUrlHelper` viene usata attraverso la proprietà `Controller.Url` per generare un URL a un'altra azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-312">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="4161c-313">Se l'applicazione usa la route convenzionale predefinita, il valore della variabile `url` sarà la stringa del percorso URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="4161c-313">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="4161c-314">Il percorso URL viene creato dal routing combinando i valori di route della richiesta corrente (valori di ambiente) con i valori passati a `Url.Action` e sostituendo tali valori nel modello di route:</span><span class="sxs-lookup"><span data-stu-id="4161c-314">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="4161c-315">Il valore di ogni parametro di route del modello di route viene sostituito attraverso la corrispondenza dei nomi con i valori e i valori di ambiente.</span><span class="sxs-lookup"><span data-stu-id="4161c-315">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="4161c-316">Per un parametro di route senza un valore si può usare un valore predefinito, se ne esiste uno, oppure ignorare il parametro se è facoltativo (come nel caso di `id` in questo esempio).</span><span class="sxs-lookup"><span data-stu-id="4161c-316">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="4161c-317">La generazione di URL avrà esito negativo se un parametro di route obbligatorio non ha un valore corrispondente.</span><span class="sxs-lookup"><span data-stu-id="4161c-317">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="4161c-318">Se la generazione di URL non riesce per una route, viene tentata la route successiva finché non vengono tentate tutte le route o viene trovata una corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="4161c-318">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="4161c-319">Nell'esempio di `Url.Action` precedente si presuppone che il routing sia convenzionale, ma la generazione di URL funziona in modo analogo con il routing con attributi, sebbene i concetti siano differenti.</span><span class="sxs-lookup"><span data-stu-id="4161c-319">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="4161c-320">Con il routing convenzionale i valori di route vengono usati per espandere un modello e i valori di route per `controller` e `action` in genere appaiono in quel modello. Questo procedimento funziona perché gli URL corrispondenti in base al routing rispettano una *convenzione*.</span><span class="sxs-lookup"><span data-stu-id="4161c-320">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="4161c-321">Nel routing con attributi i valori di route per `controller` e `action` non possono essere visualizzati nel modello ma vengono usati per individuare il modello da usare.</span><span class="sxs-lookup"><span data-stu-id="4161c-321">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="4161c-322">Questo esempio usa il routing con attributi:</span><span class="sxs-lookup"><span data-stu-id="4161c-322">This example uses attribute routing:</span></span>

[!code-csharp[](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="4161c-323">MVC compila una tabella di ricerca di tutte le azioni indirizzate con attributi e stabilisce la corrispondenza dei valori `controller` e `action` per selezionare il modello di route da usare per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-323">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="4161c-324">Nell'esempio precedente viene generato `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="4161c-324">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="4161c-325">Generazione di URL in base al nome dell'azione</span><span class="sxs-lookup"><span data-stu-id="4161c-325">Generating URLs by action name</span></span>

<span data-ttu-id="4161c-326">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="4161c-326">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="4161c-327">`Action`) e tutti gli overload correlati sono basati sull'idea che si voglia definire l'oggetto a cui ci si sta collegando specificando un nome di controller e un nome di azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-327">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="4161c-328">Quando si usa `Url.Action`, vengono specificati i valori di route correnti per `controller` e `action`: il valore di `controller` e `action` sono parte *dei valori di* *ambiente* **e** .</span><span class="sxs-lookup"><span data-stu-id="4161c-328">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="4161c-329">Il metodo `Url.Action`, usa sempre i valori correnti di `action` e `controller` e genererà un percorso URL che indirizza all'azione corrente.</span><span class="sxs-lookup"><span data-stu-id="4161c-329">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="4161c-330">Il routing tenta di usare i valori nei valori di ambiente per ottenere le informazioni non specificate durante la generazione di un URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-330">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="4161c-331">Usando una route come `{a}/{b}/{c}/{d}` e i valori di ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, il routing ha informazioni sufficienti per generare un URL senza valori aggiuntivi, poiché tutti i parametri di route hanno un valore.</span><span class="sxs-lookup"><span data-stu-id="4161c-331">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="4161c-332">Se è stato aggiunto il valore `{ d = Donovan }`, il valore `{ d = David }` viene ignorato e il percorso URL generato è `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="4161c-332">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="4161c-333">I percorsi URL sono gerarchici.</span><span class="sxs-lookup"><span data-stu-id="4161c-333">URL paths are hierarchical.</span></span> <span data-ttu-id="4161c-334">Nell'esempio precedente, se è stato aggiunto il valore `{ c = Cheryl }`, entrambi i valori `{ c = Carol, d = David }` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="4161c-334">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="4161c-335">In questo caso non è più disponibile un valore per `d` e la generazione dell'URL avrà esito negativo.</span><span class="sxs-lookup"><span data-stu-id="4161c-335">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="4161c-336">Sarà necessario specificare un valore per `c` e `d`.</span><span class="sxs-lookup"><span data-stu-id="4161c-336">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="4161c-337">In apparenza questo problema si può risolvere con la route predefinita (`{controller}/{action}/{id?}`), ma in realtà questo comportamento si verifica raramente perché `Url.Action` specifica sempre in modo esplicito un valore `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="4161c-337">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="4161c-338">Anche gli overload di `Url.Action` di maggiore durata accettano anche un oggetto *valori di route* aggiuntivo per specificare i valori per i parametri di route diversi da `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="4161c-338">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="4161c-339">In genere viene usato con `id` come `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="4161c-339">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="4161c-340">Per convenzione l'oggetto *valori di route* è in genere un oggetto di tipo anonimo, ma può anche essere un `IDictionary<>` o un *normale oggetto .NET*.</span><span class="sxs-lookup"><span data-stu-id="4161c-340">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="4161c-341">I valori di route aggiuntivi che non corrispondono a parametri di route vengono inseriti nella stringa di query.</span><span class="sxs-lookup"><span data-stu-id="4161c-341">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="4161c-342">Per creare un URL assoluto, usare un overload che accetta un `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="4161c-342">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="4161c-343">Generazione di URL in base alla route</span><span class="sxs-lookup"><span data-stu-id="4161c-343">Generating URLs by route</span></span>

<span data-ttu-id="4161c-344">Il codice precedente illustra come generare un URL passando il nome del controller e dell'azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-344">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="4161c-345">`IUrlHelper` specifica anche la famiglia di metodi `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="4161c-345">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="4161c-346">Questi metodi sono simili a `Url.Action`, ma non copiano i valori correnti di `action` e `controller` nei valori di route.</span><span class="sxs-lookup"><span data-stu-id="4161c-346">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="4161c-347">L'uso più comune consiste nello specificare un nome di route per usare un percorso specifico per generare l'URL, in genere *senza* specificare un nome di controller o azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-347">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="4161c-348">Generazione di URL in HTML</span><span class="sxs-lookup"><span data-stu-id="4161c-348">Generating URLs in HTML</span></span>

<span data-ttu-id="4161c-349">`IHtmlHelper` specifica i metodi `HtmlHelper``Html.BeginForm` e `Html.ActionLink` per generare rispettivamente gli elementi `<form>` e `<a>`.</span><span class="sxs-lookup"><span data-stu-id="4161c-349">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="4161c-350">Questi metodi usano il metodo `Url.Action` per generare un URL e accettano argomenti simili.</span><span class="sxs-lookup"><span data-stu-id="4161c-350">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="4161c-351">Gli oggetti `Url.RouteUrl` complementi di `HtmlHelper` sono `Html.BeginRouteForm` e `Html.RouteLink` e hanno una funzionalità simile.</span><span class="sxs-lookup"><span data-stu-id="4161c-351">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="4161c-352">Gli helper tag generano gli URL attraverso l'helper tag `form` e l'helper tag `<a>`.</span><span class="sxs-lookup"><span data-stu-id="4161c-352">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="4161c-353">Entrambi usano `IUrlHelper` per la propria implementazione.</span><span class="sxs-lookup"><span data-stu-id="4161c-353">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="4161c-354">Per altre informazioni, vedere [Uso dei moduli](../views/working-with-forms.md).</span><span class="sxs-lookup"><span data-stu-id="4161c-354">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="4161c-355">All'interno delle visualizzazioni `IUrlHelper` è reso disponibile dalla proprietà `Url` per tutte le generazioni di URL ad hoc che non rientrano nelle situazioni descritte in precedenza.</span><span class="sxs-lookup"><span data-stu-id="4161c-355">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="4161c-356">Generazione di URL nei risultati delle azioni</span><span class="sxs-lookup"><span data-stu-id="4161c-356">Generating URLS in Action Results</span></span>

<span data-ttu-id="4161c-357">Negli esempi precedenti è stato usato `IUrlHelper` in un controller, ma l'uso più comune in un controller è generare un URL come parte del risultato di un'azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-357">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="4161c-358">Le classi di base `ControllerBase` e `Controller` offrono metodi pratici per i risultati delle azioni che fanno riferimento a un'altra azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-358">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="4161c-359">Un uso tipico è eseguire il reindirizzamento dopo aver accettato l'input dell'utente.</span><span class="sxs-lookup"><span data-stu-id="4161c-359">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="4161c-360">I metodi factory dei risultati delle azioni seguono un modello simile ai metodi per `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="4161c-360">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="4161c-361">Caso speciale per le route convenzionali dedicate</span><span class="sxs-lookup"><span data-stu-id="4161c-361">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="4161c-362">Nel routing convenzionale è possibile usare un tipo speciale di definizione di route denominata *route convenzionale dedicata*.</span><span class="sxs-lookup"><span data-stu-id="4161c-362">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="4161c-363">Nell'esempio seguente la route denominata `blog` è una route convenzionale dedicata.</span><span class="sxs-lookup"><span data-stu-id="4161c-363">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="4161c-364">Usando queste definizioni di route, `Url.Action("Index", "Home")` genera il percorso URL `/` con la route `default` per un motivo ben preciso.</span><span class="sxs-lookup"><span data-stu-id="4161c-364">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="4161c-365">Si potrebbe pensare che i valori di route `{ controller = Home, action = Index }` siano sufficienti per generare un URL usando `blog` e che il risultato sia `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="4161c-365">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="4161c-366">Le route convenzionali dedicate si basano su un comportamento speciale dei valori predefiniti per i quali non esiste un parametro di route corrispondente che impedisce alla route di essere troppo "greedy" con la generazione degli URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-366">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="4161c-367">In questo caso i valori predefiniti sono `{ controller = Blog, action = Article }` e né `controller` né `action` vengono visualizzati come parametri di route.</span><span class="sxs-lookup"><span data-stu-id="4161c-367">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="4161c-368">Quando il routing esegue la generazione di URL, i valori specificati devono corrispondere ai valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="4161c-368">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="4161c-369">La generazione di URL che usa `blog` avrà esito negativo perché i valori `{ controller = Home, action = Index }` non corrispondono a `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="4161c-369">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="4161c-370">Il routing quindi esegue il fallback per provare `default`, che ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="4161c-370">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="4161c-371">Aree</span><span class="sxs-lookup"><span data-stu-id="4161c-371">Areas</span></span>

<span data-ttu-id="4161c-372">Le [aree](areas.md) sono una funzionalità di MVC che consente di organizzare le funzioni correlate in un gruppo come spazio dei nomi di routing separato (per le azioni del controller) e struttura di cartelle (per le visualizzazioni).</span><span class="sxs-lookup"><span data-stu-id="4161c-372">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="4161c-373">L'uso delle aree consente a un'applicazione di usare più controller con lo stesso nome, purché abbiano *aree* diverse.</span><span class="sxs-lookup"><span data-stu-id="4161c-373">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="4161c-374">Usando le aree si crea una gerarchia per il routing aggiungendo un altro parametro di route, `area` a `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="4161c-374">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="4161c-375">Questa sezione illustra il modo in cui il routing interagisce con le aree. Vedere [Aree](areas.md) per informazioni dettagliate sull'uso delle aree con le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="4161c-375">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="4161c-376">Nell'esempio seguente MVC viene configurato in modo da usare la route convenzionale predefinita e una *route di area* per un'area denominata `Blog`:</span><span class="sxs-lookup"><span data-stu-id="4161c-376">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="4161c-377">Nella corrispondenza di un percorso URL come `/Manage/Users/AddUser` la prima route produrrà i valori di route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="4161c-377">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="4161c-378">Il valore di route `area` è generato da un valore predefinito per `area`, infatti la route creata da `MapAreaRoute` equivale alla seguente:</span><span class="sxs-lookup"><span data-stu-id="4161c-378">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="4161c-379">`MapAreaRoute` crea una route che usa sia un valore predefinito che un vincolo per `area` usando il nome di area specificato, in questo caso `Blog`.</span><span class="sxs-lookup"><span data-stu-id="4161c-379">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="4161c-380">Il valore predefinito assicura che la route generi sempre `{ area = Blog, ... }`, il vincolo richiede il valore `{ area = Blog, ... }` per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-380">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="4161c-381">Il routing convenzionale dipende dall'ordinamento.</span><span class="sxs-lookup"><span data-stu-id="4161c-381">Conventional routing is order-dependent.</span></span> <span data-ttu-id="4161c-382">In generale, le route con aree devono essere posizionate prima delle altre nella tabella di route poiché sono più specifiche rispetto alle route senza un'area.</span><span class="sxs-lookup"><span data-stu-id="4161c-382">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="4161c-383">Se si usa l'esempio precedente, i valori di route corrispondono all'azione seguente:</span><span class="sxs-lookup"><span data-stu-id="4161c-383">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="4161c-384">`AreaAttribute` è ciò che denota un controller come parte di un'area, questo controller in particolare si trova nell'area `Blog`.</span><span class="sxs-lookup"><span data-stu-id="4161c-384">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="4161c-385">I controller senza un attributo `[Area]` non sono membri di alcuna area e **non** corrispondono quando il valore di route `area` viene specificato dal routing.</span><span class="sxs-lookup"><span data-stu-id="4161c-385">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="4161c-386">Nell'esempio seguente solo il primo controller indicato può corrispondere ai valori di route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="4161c-386">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="4161c-387">Lo spazio dei nomi di ogni controller viene indicato qui per motivi di completezza, per evitare conflitti di denominazione nei controller e la generazione di errori del compilatore.</span><span class="sxs-lookup"><span data-stu-id="4161c-387">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="4161c-388">Gli spazi dei nomi delle classi non hanno effetto sul routing di MVC.</span><span class="sxs-lookup"><span data-stu-id="4161c-388">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="4161c-389">I primi due controller sono membri di aree e corrispondono solo quando viene specificato il relativo nome di area dal valore di route `area`.</span><span class="sxs-lookup"><span data-stu-id="4161c-389">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="4161c-390">Il terzo controller non è un membro di un'area e può corrispondere solo quando non vengono specificati valori per `area` dal routing.</span><span class="sxs-lookup"><span data-stu-id="4161c-390">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="4161c-391">In termini di corrispondenza con *nessun valore*, l'assenza del valore `area` è come se il valore per `area` fosse Null o la stringa vuota.</span><span class="sxs-lookup"><span data-stu-id="4161c-391">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="4161c-392">Quando si esegue un'azione all'interno di un'area, il valore di route per `area` sarà disponibile come *valore di ambiente* per il routing da usare per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-392">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="4161c-393">Ciò significa che per impostazione predefinita le aree funzionano *temporaneamente* per la generazione di URL, come illustrato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="4161c-393">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="4161c-394">Informazioni su IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="4161c-394">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="4161c-395">Questa sezione offre un'analisi approfondita degli elementi interni del framework e del modo in cui MVC sceglie un'azione da eseguire.</span><span class="sxs-lookup"><span data-stu-id="4161c-395">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="4161c-396">Un'applicazione tipica non richiede un oggetto `IActionConstraint` personalizzato</span><span class="sxs-lookup"><span data-stu-id="4161c-396">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="4161c-397">Probabilmente l'utente ha già usato `IActionConstraint` anche se non ha familiarità con l'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="4161c-397">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="4161c-398">L'attributo `[HttpGet]` e gli attributi `[Http-VERB]` dello stesso tipo implementano `IActionConstraint` per limitare l'esecuzione di un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="4161c-398">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="4161c-399">Se si usa la route convenzionale predefinita, il percorso URL `/Products/Edit` produce i valori `{ controller = Products, action = Edit }`, che corrispondono a **entrambe** le azioni illustrate qui.</span><span class="sxs-lookup"><span data-stu-id="4161c-399">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="4161c-400">Nella terminologia di `IActionConstraint` si dice che entrambe le azioni sono considerate candidati poiché corrispondono entrambe ai dati della route.</span><span class="sxs-lookup"><span data-stu-id="4161c-400">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="4161c-401">Quando viene eseguito `HttpGetAttribute` indica che *Edit()* è una corrispondenza per *GET* e non è una corrispondenza per qualsiasi altro verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="4161c-401">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="4161c-402">L'azione `Edit(...)` non ha tutti i vincoli definiti e quindi corrisponde a qualsiasi verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="4161c-402">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="4161c-403">Presupponendo l'uso di un oggetto `POST`, corrisponde solo `Edit(...)`.</span><span class="sxs-lookup"><span data-stu-id="4161c-403">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="4161c-404">Per un oggetto `GET` possono invece corrispondere entrambe le azioni, tuttavia un'azione con `IActionConstraint` viene sempre considerata *migliore* rispetto a un'azione senza tale oggetto.</span><span class="sxs-lookup"><span data-stu-id="4161c-404">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="4161c-405">Quindi, dal momento che `Edit()` ha `[HttpGet]` è considerata più specifica e verrà selezionata se entrambe le azioni possono corrispondere.</span><span class="sxs-lookup"><span data-stu-id="4161c-405">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="4161c-406">Concettualmente, `IActionConstraint` è una forma di *overload*, ma anziché sostituire i metodi con lo stesso nome, supporta l'overload tra le azioni che corrispondono allo stesso URL.</span><span class="sxs-lookup"><span data-stu-id="4161c-406">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="4161c-407">Anche il routing con attributi usa `IActionConstraint` e può accadere che azioni di controller diversi vengano entrambe considerate candidati.</span><span class="sxs-lookup"><span data-stu-id="4161c-407">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="4161c-408">Implementazione di IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="4161c-408">Implementing IActionConstraint</span></span>

<span data-ttu-id="4161c-409">Il modo più semplice di implementare un oggetto `IActionConstraint` è creare una classe derivata da `System.Attribute` e inserirla nelle azioni e nel controller.</span><span class="sxs-lookup"><span data-stu-id="4161c-409">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="4161c-410">MVC rileverà automaticamente tutti gli oggetti `IActionConstraint` applicati come attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-410">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="4161c-411">È possibile usare il modello di applicazione per applicare i vincoli e questo è probabilmente l'approccio più flessibile, poiché consente di metaprogrammare le modalità di applicazione.</span><span class="sxs-lookup"><span data-stu-id="4161c-411">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="4161c-412">Nell'esempio seguente un vincolo sceglie un'azione in base a un *codice paese* dai dati di route.</span><span class="sxs-lookup"><span data-stu-id="4161c-412">In the following example a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="4161c-413">Vedere l'[esempio completo su GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="4161c-413">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="4161c-414">L'utente è responsabile dell'implementazione del metodo `Accept` e della scelta di un "ordine" per il vincolo da eseguire.</span><span class="sxs-lookup"><span data-stu-id="4161c-414">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="4161c-415">In questo caso il metodo `Accept` restituisce `true` per indicare che l'azione è una corrispondenza quando il valore di route `country` corrisponde.</span><span class="sxs-lookup"><span data-stu-id="4161c-415">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="4161c-416">Questo comportamento è diverso da quello di `RouteValueAttribute` poiché viene consentito il fallback a un'azione senza attributi.</span><span class="sxs-lookup"><span data-stu-id="4161c-416">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="4161c-417">L'esempio indica che se si definisce un'azione `en-US`, per il codice paese, ad esempio `fr-FR`, viene eseguito il fallback a un controller più generico a cui non è applicato `[CountrySpecific(...)]`.</span><span class="sxs-lookup"><span data-stu-id="4161c-417">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="4161c-418">La proprietà `Order` decide a quale *fase* appartiene il vincolo.</span><span class="sxs-lookup"><span data-stu-id="4161c-418">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="4161c-419">I vincoli relativi alle azioni vengono eseguiti in gruppi basati su `Order`.</span><span class="sxs-lookup"><span data-stu-id="4161c-419">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="4161c-420">Ad esempio, tutti gli attributi del metodo HTTP specificati dal framework usano lo stesso valore `Order` in modo da essere eseguiti nella stessa fase.</span><span class="sxs-lookup"><span data-stu-id="4161c-420">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="4161c-421">È possibile usare un numero illimitato di fasi per implementare i criteri necessari.</span><span class="sxs-lookup"><span data-stu-id="4161c-421">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="4161c-422">Quando si sceglie un valore per `Order` considerare se il vincolo dovrà essere applicato o meno prima dei metodi HTTP.</span><span class="sxs-lookup"><span data-stu-id="4161c-422">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="4161c-423">I numeri più bassi vengono eseguiti per primi.</span><span class="sxs-lookup"><span data-stu-id="4161c-423">Lower numbers run first.</span></span>
