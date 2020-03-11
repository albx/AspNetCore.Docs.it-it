---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659719"
---
<span data-ttu-id="8b4f6-101">Sebbene un'app di Blazor server sia prerendering, alcune azioni, ad esempio la chiamata a JavaScript, non sono possibili perché non è stata stabilita una connessione con il browser.</span><span class="sxs-lookup"><span data-stu-id="8b4f6-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="8b4f6-102">I componenti potrebbero dover eseguire il rendering in modo diverso quando ne viene eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="8b4f6-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="8b4f6-103">Per ritardare le chiamate di interoperabilità JavaScript finché non viene stabilita la connessione con il browser, è possibile usare l'evento del ciclo di vita del [componente OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="8b4f6-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/lifecycle#after-component-render).</span></span> <span data-ttu-id="8b4f6-104">Questo evento viene chiamato solo dopo che viene eseguito il rendering completo dell'app e viene stabilita la connessione client.</span><span class="sxs-lookup"><span data-stu-id="8b4f6-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="8b4f6-105">Per il codice di esempio precedente, fornire una `setElementText` funzione JavaScript all'interno dell'elemento `<head>` di *wwwroot/index.html* (Blazor webassembly) o *pages/_Host. cshtml* (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="8b4f6-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="8b4f6-106">La funzione viene chiamata con `IJSRuntime.InvokeVoidAsync` e non restituisce un valore:</span><span class="sxs-lookup"><span data-stu-id="8b4f6-106">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="8b4f6-107">Nell'esempio precedente la Document Object Model (DOM) viene modificata direttamente a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="8b4f6-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="8b4f6-108">La modifica diretta del DOM con JavaScript non è consigliata nella maggior parte degli scenari perché JavaScript può interferire con il rilevamento delle modifiche Blazor.</span><span class="sxs-lookup"><span data-stu-id="8b4f6-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="8b4f6-109">Nel componente seguente viene illustrato come utilizzare l'interoperabilità JavaScript come parte della logica di inizializzazione di un componente in modo che sia compatibile con il prerendering.</span><span class="sxs-lookup"><span data-stu-id="8b4f6-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="8b4f6-110">Il componente indica che è possibile attivare un aggiornamento del rendering dall'interno `OnAfterRenderAsync`.</span><span class="sxs-lookup"><span data-stu-id="8b4f6-110">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="8b4f6-111">Lo sviluppatore deve evitare di creare un ciclo infinito in questo scenario.</span><span class="sxs-lookup"><span data-stu-id="8b4f6-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="8b4f6-112">Quando viene chiamato `JSRuntime.InvokeAsync`, `ElementRef` viene usato solo in `OnAfterRenderAsync` e non in nessun metodo del ciclo di vita precedente, perché non è presente alcun elemento JavaScript finché non viene eseguito il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="8b4f6-112">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="8b4f6-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) viene chiamato per eseguire nuovamente il rendering del componente con il nuovo stato ottenuto dalla chiamata di interoperabilità JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8b4f6-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="8b4f6-114">Il codice non crea un ciclo infinito perché `StateHasChanged` viene chiamato solo quando `infoFromJs` è `null`.</span><span class="sxs-lookup"><span data-stu-id="8b4f6-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="8b4f6-115">Per il codice di esempio precedente, fornire una `setElementText` funzione JavaScript all'interno dell'elemento `<head>` di *wwwroot/index.html* (Blazor webassembly) o *pages/_Host. cshtml* (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="8b4f6-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="8b4f6-116">La funzione viene chiamata con `IJSRuntime.InvokeAsync` e restituisce un valore:</span><span class="sxs-lookup"><span data-stu-id="8b4f6-116">The function is called with `IJSRuntime.InvokeAsync` and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="8b4f6-117">Nell'esempio precedente la Document Object Model (DOM) viene modificata direttamente a scopo dimostrativo.</span><span class="sxs-lookup"><span data-stu-id="8b4f6-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="8b4f6-118">La modifica diretta del DOM con JavaScript non è consigliata nella maggior parte degli scenari perché JavaScript può interferire con il rilevamento delle modifiche Blazor.</span><span class="sxs-lookup"><span data-stu-id="8b4f6-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
