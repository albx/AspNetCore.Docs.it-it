---
title: Helper tag di ancoraggio in ASP.NET Core
author: pkellner
description: Informazioni sugli attributi dell'helper tag di ancoraggio ASP.NET Core e sul ruolo di ogni attributo per l'estensione del comportamento del tag di ancoraggio HTML.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 6bfbad39115c7823b5677d3c52ca64cfb0683037
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664003"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="5e016-103">Helper tag di ancoraggio in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e016-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="5e016-104">Di [Peter Kellner](https://peterkellner.net) e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="5e016-104">By [Peter Kellner](https://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="5e016-105">L'[helper tag](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) di ancoraggio migliora il tag di ancoraggio HTML standard (`<a ... ></a>`) con l'aggiunta di nuovi attributi.</span><span class="sxs-lookup"><span data-stu-id="5e016-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="5e016-106">Per convenzione, i nomi di attributo hanno il prefisso `asp-`.</span><span class="sxs-lookup"><span data-stu-id="5e016-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="5e016-107">Il valore dell'attributo `href` dell'elemento di ancoraggio visualizzato dipende dai valori degli attributi `asp-`.</span><span class="sxs-lookup"><span data-stu-id="5e016-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="5e016-108">Per una panoramica degli helper tag, vedere <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="5e016-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="5e016-109">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5e016-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5e016-110">*SpeakerController* viene usato negli esempi in tutto il documento:</span><span class="sxs-lookup"><span data-stu-id="5e016-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="5e016-111">Attributi dell'helper tag di ancoraggio</span><span class="sxs-lookup"><span data-stu-id="5e016-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="5e016-112">asp-controller</span><span class="sxs-lookup"><span data-stu-id="5e016-112">asp-controller</span></span>

<span data-ttu-id="5e016-113">L'attributo [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) assegna il controller usato per generare l'URL.</span><span class="sxs-lookup"><span data-stu-id="5e016-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="5e016-114">Il markup seguente elenca tutti gli altoparlanti:</span><span class="sxs-lookup"><span data-stu-id="5e016-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="5e016-115">Codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="5e016-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="5e016-116">Se si specifica l'attributo `asp-controller` e l'attributo `asp-action` viene omesso, il valore `asp-action` predefinito è l'azione del controller associata alla visualizzazione di esecuzione corrente.</span><span class="sxs-lookup"><span data-stu-id="5e016-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="5e016-117">Se `asp-action` viene omesso dal markup precedente e viene usato l'helper tag di ancoraggio nella visualizzazione *Index* di *HomeController* ( */Home*), il codice HTML generato è:</span><span class="sxs-lookup"><span data-stu-id="5e016-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController*'s *Index* view (*/Home*), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a><span data-ttu-id="5e016-118">asp-action</span><span class="sxs-lookup"><span data-stu-id="5e016-118">asp-action</span></span>

<span data-ttu-id="5e016-119">Il valore dell'attributo [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) rappresenta il nome dell'azione del controller incluso nell'attributo `href` generato.</span><span class="sxs-lookup"><span data-stu-id="5e016-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="5e016-120">Il markup seguente imposta il valore dell'attributo `href` generato sulla pagina delle valutazioni degli altoparlanti:</span><span class="sxs-lookup"><span data-stu-id="5e016-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="5e016-121">Codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="5e016-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="5e016-122">Se non viene specificato alcun attributo `asp-controller` viene usato il controller predefinito per chiamare la visualizzazione che esegue la visualizzazione corrente.</span><span class="sxs-lookup"><span data-stu-id="5e016-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="5e016-123">Se il valore dell'attributo `asp-action` è `Index` non viene accodata alcuna azione all'URL, con conseguente chiamata dell'azione `Index` predefinita.</span><span class="sxs-lookup"><span data-stu-id="5e016-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="5e016-124">L'azione specificata (o usata per impostazione predefinita) deve esistere nel controller a cui si fa riferimento in `asp-controller`.</span><span class="sxs-lookup"><span data-stu-id="5e016-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="5e016-125">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="5e016-125">asp-route-{value}</span></span>

<span data-ttu-id="5e016-126">L'attributo [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) abilita un prefisso di route con caratteri jolly.</span><span class="sxs-lookup"><span data-stu-id="5e016-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="5e016-127">Qualsiasi valore che sostituisce il segnaposto `{value}` viene interpretato come potenziale parametro di route.</span><span class="sxs-lookup"><span data-stu-id="5e016-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="5e016-128">Se non viene trovata una route predefinita, il prefisso di route viene accodato come parametro di richiesta e relativo valore all'attributo `href` generato.</span><span class="sxs-lookup"><span data-stu-id="5e016-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="5e016-129">In caso contrario viene sostituito nel modello di route.</span><span class="sxs-lookup"><span data-stu-id="5e016-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="5e016-130">Esaminare l'azione del controller seguente:</span><span class="sxs-lookup"><span data-stu-id="5e016-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="5e016-131">Con un modello di route predefinito definito in *Startup.Configure*:</span><span class="sxs-lookup"><span data-stu-id="5e016-131">With a default route template defined in *Startup.Configure*:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="5e016-132">La visualizzazione MVC usa il modello, fornito dall'azione, come segue:</span><span class="sxs-lookup"><span data-stu-id="5e016-132">The MVC view uses the model, provided by the action, as follows:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

<span data-ttu-id="5e016-133">È stata trovata una corrispondenza per il segnaposto `{id?}` della route predefinita.</span><span class="sxs-lookup"><span data-stu-id="5e016-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="5e016-134">Codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="5e016-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="5e016-135">Si supponga che il prefisso di route non faccia parte del modello di routing corrispondente, come nel caso della visualizzazione MVC seguente:</span><span class="sxs-lookup"><span data-stu-id="5e016-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

<span data-ttu-id="5e016-136">Il codice HTML seguente viene generato perché `speakerid` non è stato trovato nella route corrispondente:</span><span class="sxs-lookup"><span data-stu-id="5e016-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="5e016-137">Se `asp-controller` o `asp-action` non vengono specificati, viene eseguita la stessa elaborazione predefinita usata nell'attributo `asp-route`.</span><span class="sxs-lookup"><span data-stu-id="5e016-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="5e016-138">asp-route</span><span class="sxs-lookup"><span data-stu-id="5e016-138">asp-route</span></span>

<span data-ttu-id="5e016-139">L'attributo [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) viene usato per la creazione di un URL collegato direttamente a una route denominata.</span><span class="sxs-lookup"><span data-stu-id="5e016-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="5e016-140">Mediante gli [attributi di routing](xref:mvc/controllers/routing#attribute-routing) è possibile assegnare un nome a una route come mostrato in `SpeakerController` e usarla nell'azione `Evaluations` corrispondente:</span><span class="sxs-lookup"><span data-stu-id="5e016-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="5e016-141">Nel markup seguente l'attributo `asp-route` fa riferimento alla route denominata:</span><span class="sxs-lookup"><span data-stu-id="5e016-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="5e016-142">L'helper tag di ancoraggio genera una route direttamente per tale azione del controller usando l'URL */Speaker/Evaluations*.</span><span class="sxs-lookup"><span data-stu-id="5e016-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations*.</span></span> <span data-ttu-id="5e016-143">Codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="5e016-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="5e016-144">Se è specificato `asp-controller` o `asp-action` in aggiunta a `asp-route`, la route generata potrebbe non essere quella prevista.</span><span class="sxs-lookup"><span data-stu-id="5e016-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="5e016-145">Per evitare un conflitto di route, è consigliabile non usare `asp-route` con gli attributi `asp-controller` o `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="5e016-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

### <a name="asp-all-route-data"></a><span data-ttu-id="5e016-146">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="5e016-146">asp-all-route-data</span></span>

<span data-ttu-id="5e016-147">L'attributo [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) supporta la creazione di un dizionario di coppie chiave-valore.</span><span class="sxs-lookup"><span data-stu-id="5e016-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="5e016-148">La chiave è il nome del parametro e il valore è il valore del parametro.</span><span class="sxs-lookup"><span data-stu-id="5e016-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="5e016-149">Nell'esempio seguente viene inizializzato un dizionario che viene poi passato a una visualizzazione Razor.</span><span class="sxs-lookup"><span data-stu-id="5e016-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="5e016-150">In alternativa è possibile passare i dati con il modello.</span><span class="sxs-lookup"><span data-stu-id="5e016-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="5e016-151">Il codice precedente genera il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="5e016-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="5e016-152">Il dizionario `asp-all-route-data` viene reso flat per produrre una stringa di query corrispondente ai requisiti dell'azione `Evaluations` in overload:</span><span class="sxs-lookup"><span data-stu-id="5e016-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="5e016-153">In presenza di chiavi nel dizionario corrispondenti ai parametri di route, questi valori vengono sostituiti nella route come appropriato.</span><span class="sxs-lookup"><span data-stu-id="5e016-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="5e016-154">Gli altri valori non corrispondenti vengono generati come parametri di richiesta.</span><span class="sxs-lookup"><span data-stu-id="5e016-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="5e016-155">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="5e016-155">asp-fragment</span></span>

<span data-ttu-id="5e016-156">L'attributo [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) definisce un frammento di URL da accodare all'URL.</span><span class="sxs-lookup"><span data-stu-id="5e016-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="5e016-157">L'helper tag di ancoraggio aggiunge il carattere hash (#).</span><span class="sxs-lookup"><span data-stu-id="5e016-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="5e016-158">Considerare il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="5e016-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="5e016-159">Codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="5e016-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="5e016-160">I tag hash sono utili per la compilazione di app sul lato client.</span><span class="sxs-lookup"><span data-stu-id="5e016-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="5e016-161">Ad esempio possono essere usati per semplificare l'uso di contrassegni e la ricerca in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5e016-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="5e016-162">asp-area</span><span class="sxs-lookup"><span data-stu-id="5e016-162">asp-area</span></span>

<span data-ttu-id="5e016-163">L'attributo [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) imposta il nome dell'area usato per impostare la route appropriata.</span><span class="sxs-lookup"><span data-stu-id="5e016-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="5e016-164">Gli esempi seguenti illustrano come l'attributo `asp-area` determina la modifica del mapping delle route.</span><span class="sxs-lookup"><span data-stu-id="5e016-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

#### <a name="usage-in-razor-pages"></a><span data-ttu-id="5e016-165">Utilizzo in Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5e016-165">Usage in Razor Pages</span></span>

<span data-ttu-id="5e016-166">Le aree Razor Pages sono supportate in ASP.NET Core 2.1 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="5e016-166">Razor Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="5e016-167">Considerare la gerarchia di directory seguente:</span><span class="sxs-lookup"><span data-stu-id="5e016-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="5e016-168">**{Nome progetto}**</span><span class="sxs-lookup"><span data-stu-id="5e016-168">**{Project name}**</span></span>
  * <span data-ttu-id="5e016-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="5e016-169">**wwwroot**</span></span>
  * <span data-ttu-id="5e016-170">**Aree**</span><span class="sxs-lookup"><span data-stu-id="5e016-170">**Areas**</span></span>
    * <span data-ttu-id="5e016-171">**Sessioni**</span><span class="sxs-lookup"><span data-stu-id="5e016-171">**Sessions**</span></span>
      * <span data-ttu-id="5e016-172">**Pagine**</span><span class="sxs-lookup"><span data-stu-id="5e016-172">**Pages**</span></span>
        * <span data-ttu-id="5e016-173">*\_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e016-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="5e016-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e016-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="5e016-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5e016-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="5e016-176">**Pagine**</span><span class="sxs-lookup"><span data-stu-id="5e016-176">**Pages**</span></span>

<span data-ttu-id="5e016-177">Il markup per fare riferimento alla pagina Razor *Index* dell'area *Sessioni* è:</span><span class="sxs-lookup"><span data-stu-id="5e016-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="5e016-178">Codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="5e016-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="5e016-179">Per supportare le aree in un'app Razor Pages, eseguire una delle operazioni seguenti in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5e016-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="5e016-180">Impostare la [versione di compatibilità](xref:mvc/compatibility-version) su 2.1 o successiva.</span><span class="sxs-lookup"><span data-stu-id="5e016-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="5e016-181">Impostare la proprietà [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) su `true`:</span><span class="sxs-lookup"><span data-stu-id="5e016-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a><span data-ttu-id="5e016-182">Utilizzo in MVC</span><span class="sxs-lookup"><span data-stu-id="5e016-182">Usage in MVC</span></span>

<span data-ttu-id="5e016-183">Considerare la gerarchia di directory seguente:</span><span class="sxs-lookup"><span data-stu-id="5e016-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="5e016-184">**{Nome progetto}**</span><span class="sxs-lookup"><span data-stu-id="5e016-184">**{Project name}**</span></span>
  * <span data-ttu-id="5e016-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="5e016-185">**wwwroot**</span></span>
  * <span data-ttu-id="5e016-186">**Aree**</span><span class="sxs-lookup"><span data-stu-id="5e016-186">**Areas**</span></span>
    * <span data-ttu-id="5e016-187">**Blog**</span><span class="sxs-lookup"><span data-stu-id="5e016-187">**Blogs**</span></span>
      * <span data-ttu-id="5e016-188">**Controller**</span><span class="sxs-lookup"><span data-stu-id="5e016-188">**Controllers**</span></span>
        * <span data-ttu-id="5e016-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="5e016-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="5e016-190">**Visualizzazioni**</span><span class="sxs-lookup"><span data-stu-id="5e016-190">**Views**</span></span>
        * <span data-ttu-id="5e016-191">**Home**</span><span class="sxs-lookup"><span data-stu-id="5e016-191">**Home**</span></span>
          * <span data-ttu-id="5e016-192">*AboutBlog.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e016-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="5e016-193">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e016-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="5e016-194">*\_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e016-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="5e016-195">**Controller**</span><span class="sxs-lookup"><span data-stu-id="5e016-195">**Controllers**</span></span>

<span data-ttu-id="5e016-196">Se si imposta `asp-area` su "Blogs", la directory *Areas/Blogs* viene aggiunta come prefisso alle route dei controller e delle visualizzazioni associati per questo tag di ancoraggio.</span><span class="sxs-lookup"><span data-stu-id="5e016-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="5e016-197">Il markup per fare riferimento alla visualizzazione *AboutBlog* è:</span><span class="sxs-lookup"><span data-stu-id="5e016-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="5e016-198">Codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="5e016-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="5e016-199">Per supportare le aree in un'app MVC, il modello di route deve includere un riferimento all'area, se esistente.</span><span class="sxs-lookup"><span data-stu-id="5e016-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="5e016-200">Tale modello è rappresentato dal secondo parametro della chiamata del metodo `routes.MapRoute` in *Startup.Configure*:</span><span class="sxs-lookup"><span data-stu-id="5e016-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure*:</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="5e016-201">asp-protocol</span><span class="sxs-lookup"><span data-stu-id="5e016-201">asp-protocol</span></span>

<span data-ttu-id="5e016-202">L'attributo [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) consente di specificare un protocollo (ad esempio `https`) nell'URL.</span><span class="sxs-lookup"><span data-stu-id="5e016-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="5e016-203">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5e016-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="5e016-204">Codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="5e016-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="5e016-205">Il nome host nell'esempio è localhost.</span><span class="sxs-lookup"><span data-stu-id="5e016-205">The host name in the example is localhost.</span></span> <span data-ttu-id="5e016-206">L'helper tag di ancoraggio usa il dominio pubblico del sito Web per generare l'URL.</span><span class="sxs-lookup"><span data-stu-id="5e016-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="5e016-207">asp-host</span><span class="sxs-lookup"><span data-stu-id="5e016-207">asp-host</span></span>

<span data-ttu-id="5e016-208">L'attributo [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) consente di specificare un nome host nell'URL.</span><span class="sxs-lookup"><span data-stu-id="5e016-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="5e016-209">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5e016-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="5e016-210">Codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="5e016-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="5e016-211">asp-page</span><span class="sxs-lookup"><span data-stu-id="5e016-211">asp-page</span></span>

<span data-ttu-id="5e016-212">L'attributo [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) viene usato con le pagine Razor.</span><span class="sxs-lookup"><span data-stu-id="5e016-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="5e016-213">Usarlo per impostare il valore dell'attributo `href` per un tag di ancoraggio su una pagina specifica.</span><span class="sxs-lookup"><span data-stu-id="5e016-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="5e016-214">L'URL viene creato anteponendo una barra ("/") al nome della pagina.</span><span class="sxs-lookup"><span data-stu-id="5e016-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="5e016-215">L'esempio seguente fa riferimento alla pagina Razor Attendee:</span><span class="sxs-lookup"><span data-stu-id="5e016-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="5e016-216">Codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="5e016-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="5e016-217">L'attributo `asp-page` e gli attributi `asp-route`, `asp-controller` e `asp-action` si escludono a vicenda.</span><span class="sxs-lookup"><span data-stu-id="5e016-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="5e016-218">`asp-page` può tuttavia essere usato con `asp-route-{value}` per il controllo del routing, come dimostra il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="5e016-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="5e016-219">Codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="5e016-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="5e016-220">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="5e016-220">asp-page-handler</span></span>

<span data-ttu-id="5e016-221">L'attributo [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) viene usato con le pagine Razor.</span><span class="sxs-lookup"><span data-stu-id="5e016-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="5e016-222">È progettato per il collegamento di gestori di pagine specifici.</span><span class="sxs-lookup"><span data-stu-id="5e016-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="5e016-223">Si consideri il gestore di pagine seguente:</span><span class="sxs-lookup"><span data-stu-id="5e016-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="5e016-224">Il markup associato del modello di pagina si collega al gestore di pagina `OnGetProfile`.</span><span class="sxs-lookup"><span data-stu-id="5e016-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="5e016-225">Si noti che il prefisso `On<Verb>` del nome del metodo del gestore di pagina viene omesso nel valore dell'attributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="5e016-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="5e016-226">Quando il metodo è asincrono, viene omesso anche il suffisso `Async`.</span><span class="sxs-lookup"><span data-stu-id="5e016-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="5e016-227">Codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="5e016-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="5e016-228">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5e016-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
