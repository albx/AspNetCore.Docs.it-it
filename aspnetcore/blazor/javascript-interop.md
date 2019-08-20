---
title: Interoperabilità JavaScript di ASP.NET Core Blazer
author: guardrex
description: Informazioni su come richiamare funzioni JavaScript da metodi .NET e .NET da JavaScript in app blazer.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/javascript-interop
ms.openlocfilehash: ffd25fe0288159681f7fc052fc09e1f6fc425404
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030308"
---
# <a name="aspnet-core-blazor-javascript-interop"></a><span data-ttu-id="33e92-103">Interoperabilità JavaScript di ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="33e92-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="33e92-104">Di [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="33e92-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="33e92-105">Un'app blazer può richiamare funzioni JavaScript da metodi .NET e .NET dal codice JavaScript.</span><span class="sxs-lookup"><span data-stu-id="33e92-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="33e92-106">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="33e92-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="33e92-107">Richiamare funzioni JavaScript da metodi .NET</span><span class="sxs-lookup"><span data-stu-id="33e92-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="33e92-108">In alcuni casi è necessario che il codice .NET chiami una funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="33e92-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="33e92-109">Ad esempio, una chiamata JavaScript può esporre le funzionalità o le funzionalità del browser da una libreria JavaScript all'app.</span><span class="sxs-lookup"><span data-stu-id="33e92-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="33e92-110">Per chiamare JavaScript da .NET, usare l' `IJSRuntime` astrazione.</span><span class="sxs-lookup"><span data-stu-id="33e92-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="33e92-111">Il `InvokeAsync<T>` metodo accetta un identificatore per la funzione JavaScript che si vuole richiamare insieme a un numero qualsiasi di argomenti serializzabili in JSON.</span><span class="sxs-lookup"><span data-stu-id="33e92-111">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="33e92-112">L'identificatore della funzione è relativo all'ambito globale (`window`).</span><span class="sxs-lookup"><span data-stu-id="33e92-112">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="33e92-113">Se si desidera chiamare `window.someScope.someFunction`, l'identificatore è. `someScope.someFunction`</span><span class="sxs-lookup"><span data-stu-id="33e92-113">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="33e92-114">Non è necessario registrare la funzione prima che venga chiamata.</span><span class="sxs-lookup"><span data-stu-id="33e92-114">There's no need to register the function before it's called.</span></span> <span data-ttu-id="33e92-115">Il tipo `T` restituito deve anche essere serializzabile in JSON.</span><span class="sxs-lookup"><span data-stu-id="33e92-115">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="33e92-116">Per le app sul lato server:</span><span class="sxs-lookup"><span data-stu-id="33e92-116">For server-side apps:</span></span>

* <span data-ttu-id="33e92-117">Più richieste utente vengono elaborate dall'app sul lato server.</span><span class="sxs-lookup"><span data-stu-id="33e92-117">Multiple user requests are processed by the server-side app.</span></span> <span data-ttu-id="33e92-118">Non chiamare `JSRuntime.Current` in un componente per richiamare funzioni JavaScript.</span><span class="sxs-lookup"><span data-stu-id="33e92-118">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="33e92-119">Inserire l' `IJSRuntime` astrazione e usare l'oggetto inserito per eseguire chiamate di interoperabilità JavaScript.</span><span class="sxs-lookup"><span data-stu-id="33e92-119">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>
* <span data-ttu-id="33e92-120">Mentre un'app Blaze sta eseguendo il prerendering, non è possibile chiamare JavaScript perché non è stata stabilita una connessione con il browser.</span><span class="sxs-lookup"><span data-stu-id="33e92-120">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="33e92-121">Per altre informazioni, vedere la sezione [rilevare quando un'app blazer è prerendering](#detect-when-a-blazor-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="33e92-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="33e92-122">L'esempio seguente è basato su [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), un decodificatore basato su JavaScript sperimentale.</span><span class="sxs-lookup"><span data-stu-id="33e92-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="33e92-123">Nell'esempio viene illustrato come richiamare una funzione JavaScript da un C# metodo.</span><span class="sxs-lookup"><span data-stu-id="33e92-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="33e92-124">La funzione JavaScript accetta una matrice di byte da C# un metodo, decodifica la matrice e restituisce il testo al componente per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="33e92-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="33e92-125">All'interno `<head>` dell'elemento di *wwwroot/index.html* (lato client Blazer) o *pages/_Host. cshtml* (lato server fiammeggiar), fornire una funzione che usi `TextDecoder` per decodificare una matrice passata:</span><span class="sxs-lookup"><span data-stu-id="33e92-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side), provide a function that uses `TextDecoder` to decode a passed array:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

<span data-ttu-id="33e92-126">Il codice JavaScript, ad esempio il codice illustrato nell'esempio precedente, può anche essere caricato da un file JavaScript (con*estensione js*) con un riferimento al file script:</span><span class="sxs-lookup"><span data-stu-id="33e92-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="33e92-127">Il componente seguente:</span><span class="sxs-lookup"><span data-stu-id="33e92-127">The following component:</span></span>

* <span data-ttu-id="33e92-128">Richiama la `ConvertArray` funzione JavaScript usando `JsRuntime` quando viene selezionato un pulsante Component (**Convert Array**).</span><span class="sxs-lookup"><span data-stu-id="33e92-128">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="33e92-129">Dopo la chiamata della funzione JavaScript, la matrice passata viene convertita in una stringa.</span><span class="sxs-lookup"><span data-stu-id="33e92-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="33e92-130">La stringa viene restituita al componente per la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="33e92-130">The string is returned to the component for display.</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

<span data-ttu-id="33e92-131">Per usare l' `IJSRuntime` astrazione, adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="33e92-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="33e92-132">Inserire l' `IJSRuntime` astrazione nel componente Razor (*Razor*):</span><span class="sxs-lookup"><span data-stu-id="33e92-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* <span data-ttu-id="33e92-133">Inserire l' `IJSRuntime` astrazione in una classe ( *. cs*):</span><span class="sxs-lookup"><span data-stu-id="33e92-133">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* <span data-ttu-id="33e92-134">Per la generazione di contenuto dinamico con [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), `[Inject]` usare l'attributo:</span><span class="sxs-lookup"><span data-stu-id="33e92-134">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="33e92-135">Nell'app di esempio lato client che accompagna questo argomento sono disponibili due funzioni JavaScript per l'app sul lato client che interagiscono con il DOM per ricevere l'input dell'utente e visualizzare un messaggio di benvenuto:</span><span class="sxs-lookup"><span data-stu-id="33e92-135">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the client-side app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="33e92-136">`showPrompt`&ndash; Genera un messaggio di richiesta per accettare l'input dell'utente (il nome dell'utente) e restituisce il nome al chiamante.</span><span class="sxs-lookup"><span data-stu-id="33e92-136">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="33e92-137">`displayWelcome`Assegna un messaggio di benvenuto dal chiamante a un oggetto DOM con un `id` valore di `welcome`. &ndash;</span><span class="sxs-lookup"><span data-stu-id="33e92-137">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="33e92-138">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="33e92-138">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="33e92-139">Inserire il `<script>` Tag che fa riferimento al file JavaScript nel file *wwwroot/index.html* (lato client Blazer) o *pages/_Host. cshtml* (lato server Blaze).</span><span class="sxs-lookup"><span data-stu-id="33e92-139">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side).</span></span>

<span data-ttu-id="33e92-140">*wwwroot/index.html* (Lato client Blazer):</span><span class="sxs-lookup"><span data-stu-id="33e92-140">*wwwroot/index.html* (Blazor client-side):</span></span>

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="33e92-141">*Pages/_Host. cshtml* (Lato server fiammeggiante):</span><span class="sxs-lookup"><span data-stu-id="33e92-141">*Pages/_Host.cshtml* (Blazor server-side):</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

<span data-ttu-id="33e92-142">Non inserire un `<script>` tag in un file di componente perché `<script>` il tag non può essere aggiornato dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="33e92-142">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="33e92-143">I metodi .NET interoperano con le funzioni JavaScript nel file *exampleJsInterop. js* chiamando `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="33e92-143">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="33e92-144">L' `IJSRuntime` astrazione è asincrona per consentire scenari lato server.</span><span class="sxs-lookup"><span data-stu-id="33e92-144">The `IJSRuntime` abstraction is asynchronous to allow for server-side scenarios.</span></span> <span data-ttu-id="33e92-145">Se l'app esegue il lato client e si vuole richiamare una funzione JavaScript in modo sincrono, abbassato `Invoke<T>` a `IJSInProcessRuntime` e chiamare invece.</span><span class="sxs-lookup"><span data-stu-id="33e92-145">If the app runs client-side and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="33e92-146">È consigliabile che la maggior parte delle librerie di interoperabilità JavaScript usino le API asincrone per garantire che le librerie siano disponibili in tutti gli scenari, lato client o lato server.</span><span class="sxs-lookup"><span data-stu-id="33e92-146">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios, client-side or server-side.</span></span>

<span data-ttu-id="33e92-147">L'app di esempio include un componente per illustrare l'interoperabilità di JavaScript.</span><span class="sxs-lookup"><span data-stu-id="33e92-147">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="33e92-148">Il componente:</span><span class="sxs-lookup"><span data-stu-id="33e92-148">The component:</span></span>

* <span data-ttu-id="33e92-149">Riceve l'input dell'utente tramite un prompt di JavaScript.</span><span class="sxs-lookup"><span data-stu-id="33e92-149">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="33e92-150">Restituisce il testo al componente per l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="33e92-150">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="33e92-151">Chiama una seconda funzione JavaScript che interagisce con il DOM per visualizzare un messaggio di benvenuto.</span><span class="sxs-lookup"><span data-stu-id="33e92-151">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="33e92-152">*Pages/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="33e92-152">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="33e92-153">Quando `TriggerJsPrompt` viene eseguito selezionando il pulsante di **richiesta trigger JavaScript** del componente, viene `showPrompt` chiamata la funzione JavaScript fornita nel file *wwwroot/exampleJsInterop. js* .</span><span class="sxs-lookup"><span data-stu-id="33e92-153">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="33e92-154">La `showPrompt` funzione accetta l'input dell'utente (il nome dell'utente), che è codificato in HTML e restituito al componente.</span><span class="sxs-lookup"><span data-stu-id="33e92-154">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="33e92-155">Il componente archivia il nome dell'utente in una variabile locale, `name`.</span><span class="sxs-lookup"><span data-stu-id="33e92-155">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="33e92-156">La stringa archiviata `name` in viene incorporata in un messaggio di benvenuto, che viene passato a una `displayWelcome`funzione JavaScript,, che esegue il rendering del messaggio di benvenuto in un tag di intestazione.</span><span class="sxs-lookup"><span data-stu-id="33e92-156">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="33e92-157">Chiamare una funzione JavaScript void</span><span class="sxs-lookup"><span data-stu-id="33e92-157">Call a void JavaScript function</span></span>

<span data-ttu-id="33e92-158">Le funzioni JavaScript che restituiscono [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) vengono chiamate con `IJSRuntime.InvokeAsync<object>`, che restituisce `null`.</span><span class="sxs-lookup"><span data-stu-id="33e92-158">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeAsync<object>`, which returns `null`.</span></span>

## <a name="detect-when-a-blazor-app-is-prerendering"></a><span data-ttu-id="33e92-159">Rileva quando è in fase di prerendering un'app Blazer</span><span class="sxs-lookup"><span data-stu-id="33e92-159">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="33e92-160">Acquisisci riferimenti a elementi</span><span class="sxs-lookup"><span data-stu-id="33e92-160">Capture references to elements</span></span>

<span data-ttu-id="33e92-161">Alcuni scenari di interoperabilità [JavaScript](xref:blazor/javascript-interop) richiedono riferimenti agli elementi HTML.</span><span class="sxs-lookup"><span data-stu-id="33e92-161">Some [JavaScript interop](xref:blazor/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="33e92-162">Ad esempio, una libreria dell'interfaccia utente può richiedere un riferimento a un elemento per l'inizializzazione o potrebbe essere necessario chiamare API simili a quelle di un `focus` elemento `play`, ad esempio o.</span><span class="sxs-lookup"><span data-stu-id="33e92-162">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="33e92-163">Acquisire i riferimenti agli elementi HTML in un componente usando l'approccio seguente:</span><span class="sxs-lookup"><span data-stu-id="33e92-163">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="33e92-164">Aggiungere un `@ref` attributo all'elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="33e92-164">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="33e92-165">Definire un campo di tipo `ElementReference` il cui nome corrisponde al valore `@ref` dell'attributo.</span><span class="sxs-lookup"><span data-stu-id="33e92-165">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="33e92-166">Nell'esempio seguente viene illustrata l'acquisizione di `username` un riferimento all' `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="33e92-166">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> <span data-ttu-id="33e92-167">**Non** usare i riferimenti agli elementi acquisiti come modo per popolare o modificare il Dom quando il blazer interagisce con gli elementi a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="33e92-167">Do **not** use captured element references as a way of populating or manipulating the DOM when Blazor interacts with the elements referenced.</span></span> <span data-ttu-id="33e92-168">Questa operazione può interferire con il modello di rendering dichiarativo.</span><span class="sxs-lookup"><span data-stu-id="33e92-168">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="33e92-169">Per quanto riguarda il codice .NET, un `ElementReference` è un handle opaco.</span><span class="sxs-lookup"><span data-stu-id="33e92-169">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="33e92-170">L' *unica* cosa che è possibile fare `ElementReference` con è passarla al codice JavaScript tramite l'interoperabilità JavaScript.</span><span class="sxs-lookup"><span data-stu-id="33e92-170">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="33e92-171">Quando si esegue questa operazione, il codice sul lato JavaScript riceve `HTMLElement` un'istanza, che può essere usata con le API DOM normali.</span><span class="sxs-lookup"><span data-stu-id="33e92-171">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="33e92-172">Il codice seguente, ad esempio, definisce un metodo di estensione .NET che consente di impostare lo stato attivo su un elemento:</span><span class="sxs-lookup"><span data-stu-id="33e92-172">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="33e92-173">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="33e92-173">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="33e92-174">Usare `IJSRuntime.InvokeAsync<T>` e chiamare `exampleJsFunctions.focusElement` con un `ElementReference` oggetto per concentrare un elemento:</span><span class="sxs-lookup"><span data-stu-id="33e92-174">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementReference` to focus an element:</span></span>

```cshtml
@inject IJSRuntime JSRuntime

<input @ref="username" />
<button @onclick="SetFocus">Set focus on username</button>

@code {
    private ElementReference username;

    public async void SetFocus()
    {
        await JSRuntime.InvokeAsync<object>(
                "exampleJsFunctions.focusElement", username);
    }
}
```

<span data-ttu-id="33e92-175">Per usare un metodo di estensione per lo stato attivo di un elemento, creare un metodo di `IJSRuntime` estensione statico che riceva l'istanza:</span><span class="sxs-lookup"><span data-stu-id="33e92-175">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="33e92-176">Il metodo viene chiamato direttamente nell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="33e92-176">The method is called directly on the object.</span></span> <span data-ttu-id="33e92-177">Nell'esempio seguente si presuppone che il `Focus` metodo statico sia disponibile `JsInteropClasses` dallo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="33e92-177">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

```cshtml
@inject IJSRuntime JSRuntime
@using JsInteropClasses

<input @ref="username" />
<button @onclick="SetFocus">Set focus on username</button>

@code {
    private ElementReference username;

    public async Task SetFocus()
    {
        await username.Focus(JSRuntime);
    }
}
```

> [!IMPORTANT]
> <span data-ttu-id="33e92-178">La `username` variabile viene popolata solo dopo il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="33e92-178">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="33e92-179">Se un oggetto non `ElementReference` popolato viene passato al codice JavaScript, il codice JavaScript riceve un `null`valore di.</span><span class="sxs-lookup"><span data-stu-id="33e92-179">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="33e92-180">Per modificare i riferimenti agli elementi dopo che il componente ha terminato il rendering (per impostare lo stato attivo iniziale su `OnAfterRenderAsync` un `OnAfterRender` elemento), usare i metodi del ciclo di vita dei [componenti](xref:blazor/components#lifecycle-methods)o.</span><span class="sxs-lookup"><span data-stu-id="33e92-180">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).</span></span>

<!-- HOLD https://github.com/aspnet/AspNetCore.Docs/pull/13818
Capture a reference to an HTML element in a component by adding an `@ref` attribute to the HTML element. The following example shows capturing a reference to the `username` `<input>` element:

```cshtml
<input @ref="username" ... />
```

> [!NOTE]
> Do **not** use captured element references as a way of populating or manipulating the DOM when Blazor interacts with the elements referenced. Doing so may interfere with the declarative rendering model.

As far as .NET code is concerned, an `ElementReference` is an opaque handle. The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JavaScript interop. When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.

For example, the following code defines a .NET extension method that enables setting the focus on an element:

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementReference` to focus an element:

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,9-10)]

To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

The method is called directly on the object. The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,10)]

> [!IMPORTANT]
> The `username` variable is only populated after the component is rendered. If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`. To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).
-->

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="33e92-181">Richiamare metodi .NET da funzioni JavaScript</span><span class="sxs-lookup"><span data-stu-id="33e92-181">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="33e92-182">Chiamata al metodo .NET statico</span><span class="sxs-lookup"><span data-stu-id="33e92-182">Static .NET method call</span></span>

<span data-ttu-id="33e92-183">Per richiamare un metodo .NET statico da JavaScript, usare le `DotNet.invokeMethod` funzioni `DotNet.invokeMethodAsync` o.</span><span class="sxs-lookup"><span data-stu-id="33e92-183">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="33e92-184">Passare l'identificatore del metodo statico che si desidera chiamare, il nome dell'assembly che contiene la funzione e gli eventuali argomenti.</span><span class="sxs-lookup"><span data-stu-id="33e92-184">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="33e92-185">La versione asincrona è preferibile per supportare scenari lato server.</span><span class="sxs-lookup"><span data-stu-id="33e92-185">The asynchronous version is preferred to support server-side scenarios.</span></span> <span data-ttu-id="33e92-186">Per richiamare un metodo .NET da JavaScript, il metodo .NET deve essere pubblico, statico e avere l' `[JSInvokable]` attributo.</span><span class="sxs-lookup"><span data-stu-id="33e92-186">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="33e92-187">Per impostazione predefinita, l'identificatore del metodo è il nome del metodo, ma è possibile specificare un identificatore `JSInvokableAttribute` diverso tramite il costruttore.</span><span class="sxs-lookup"><span data-stu-id="33e92-187">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="33e92-188">La chiamata ai metodi generici aperti non è attualmente supportata.</span><span class="sxs-lookup"><span data-stu-id="33e92-188">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="33e92-189">L'app di esempio include C# un metodo per restituire una matrice `int`di s.</span><span class="sxs-lookup"><span data-stu-id="33e92-189">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="33e92-190">L' `JSInvokable` attributo viene applicato al metodo.</span><span class="sxs-lookup"><span data-stu-id="33e92-190">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="33e92-191">*Pages/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="33e92-191">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="33e92-192">JavaScript servito al client richiama il C# metodo .NET.</span><span class="sxs-lookup"><span data-stu-id="33e92-192">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="33e92-193">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="33e92-193">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="33e92-194">Quando si seleziona il pulsante **trigger .net static method ReturnArrayAsync** , esaminare l'output della console negli strumenti di sviluppo Web del browser.</span><span class="sxs-lookup"><span data-stu-id="33e92-194">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="33e92-195">L'output della console è:</span><span class="sxs-lookup"><span data-stu-id="33e92-195">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="33e92-196">Il quarto valore della matrice viene inserito nella matrice (`data.push(4);`) restituito da `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="33e92-196">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="33e92-197">Chiamata al metodo di istanza</span><span class="sxs-lookup"><span data-stu-id="33e92-197">Instance method call</span></span>

<span data-ttu-id="33e92-198">È anche possibile chiamare i metodi di istanza .NET da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="33e92-198">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="33e92-199">Per richiamare un metodo di istanza .NET da JavaScript:</span><span class="sxs-lookup"><span data-stu-id="33e92-199">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="33e92-200">Passare l'istanza .NET a JavaScript eseguendone il wrapping in `DotNetObjectRef` un'istanza di.</span><span class="sxs-lookup"><span data-stu-id="33e92-200">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectRef` instance.</span></span> <span data-ttu-id="33e92-201">L'istanza .NET viene passata per riferimento a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="33e92-201">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="33e92-202">Richiamare i metodi di istanza .NET sull'istanza usando `invokeMethod` le `invokeMethodAsync` funzioni o.</span><span class="sxs-lookup"><span data-stu-id="33e92-202">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="33e92-203">L'istanza di .NET può essere passata anche come argomento quando si richiamano altri metodi .NET da JavaScript.</span><span class="sxs-lookup"><span data-stu-id="33e92-203">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="33e92-204">L'app di esempio consente di registrare i messaggi nella console lato client.</span><span class="sxs-lookup"><span data-stu-id="33e92-204">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="33e92-205">Per gli esempi seguenti illustrati dall'app di esempio, esaminare l'output della console del browser negli strumenti di sviluppo del browser.</span><span class="sxs-lookup"><span data-stu-id="33e92-205">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="33e92-206">Quando il pulsante del **metodo di istanza .NET del trigger HelloHelper. sayHello** è selezionato, `ExampleJsInterop.CallHelloHelperSayHello` viene chiamato e `Blazor`passa un nome al metodo.</span><span class="sxs-lookup"><span data-stu-id="33e92-206">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="33e92-207">*Pages/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="33e92-207">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="33e92-208">`CallHelloHelperSayHello`richiama la funzione `sayHello` JavaScript con una nuova istanza di `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="33e92-208">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="33e92-209">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="33e92-209">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="33e92-210">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="33e92-210">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="33e92-211">Il nome viene passato al `HelloHelper`costruttore, che imposta la `HelloHelper.Name` proprietà.</span><span class="sxs-lookup"><span data-stu-id="33e92-211">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="33e92-212">Quando viene eseguita la `sayHello` funzione JavaScript, `HelloHelper.SayHello` restituisce il `Hello, {Name}!` messaggio, che viene scritto nella console dalla funzione JavaScript.</span><span class="sxs-lookup"><span data-stu-id="33e92-212">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="33e92-213">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="33e92-213">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="33e92-214">Output della console negli strumenti di sviluppo Web del browser:</span><span class="sxs-lookup"><span data-stu-id="33e92-214">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="33e92-215">Condividere il codice di interoperabilità in una libreria di classi</span><span class="sxs-lookup"><span data-stu-id="33e92-215">Share interop code in a class library</span></span>

<span data-ttu-id="33e92-216">Il codice di interoperabilità JavaScript può essere incluso in una libreria di classi, che consente di condividere il codice in un pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="33e92-216">JavaScript interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="33e92-217">La libreria di classi gestisce l'incorporamento delle risorse JavaScript nell'assembly compilato.</span><span class="sxs-lookup"><span data-stu-id="33e92-217">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="33e92-218">I file JavaScript vengono inseriti nella cartella *wwwroot*</span><span class="sxs-lookup"><span data-stu-id="33e92-218">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="33e92-219">Gli strumenti si occupano di incorporare le risorse quando la libreria viene compilata.</span><span class="sxs-lookup"><span data-stu-id="33e92-219">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="33e92-220">Nel file di progetto dell'app viene fatto riferimento al pacchetto NuGet compilato nello stesso modo in cui viene fatto riferimento a un pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="33e92-220">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="33e92-221">Dopo il ripristino del pacchetto, il codice dell'app può chiamare JavaScript come se fosse C#.</span><span class="sxs-lookup"><span data-stu-id="33e92-221">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="33e92-222">Per altre informazioni, vedere <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="33e92-222">For more information, see <xref:blazor/class-libraries>.</span></span>