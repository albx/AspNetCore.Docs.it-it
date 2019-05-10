---
title: Helper tag Partial in ASP.NET Core
author: scottaddie
description: Informazioni sull'helper tag Partial di ASP.NET Core e sul ruolo dei singoli attributi dell'helper nel rendering di una visualizzazione parziale.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 757c407bbae9c33c5b05a1edc01a37c2ced8dd36
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890286"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="f142b-103">Helper tag Partial in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f142b-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="f142b-104">Di [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="f142b-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="f142b-105">Per una panoramica degli helper tag, vedere <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="f142b-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="f142b-106">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f142b-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="f142b-107">Panoramica</span><span class="sxs-lookup"><span data-stu-id="f142b-107">Overview</span></span>

<span data-ttu-id="f142b-108">L'helper tag Partial viene usato per il rendering di una [visualizzazione parziale](xref:mvc/views/partial) in Razor Pages e nelle app MVC.</span><span class="sxs-lookup"><span data-stu-id="f142b-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in Razor Pages and MVC apps.</span></span> <span data-ttu-id="f142b-109">Tenere presente che:</span><span class="sxs-lookup"><span data-stu-id="f142b-109">Consider that it:</span></span>

* <span data-ttu-id="f142b-110">Richiede ASP.NET Core 2.1 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="f142b-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="f142b-111">Rappresenta un'alternativa alla [sintassi helper HTML](xref:mvc/views/partial#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="f142b-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="f142b-112">Esegue il rendering della visualizzazione parziale in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="f142b-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="f142b-113">Le opzioni helper HTML per il rendering di una visualizzazione parziale includono:</span><span class="sxs-lookup"><span data-stu-id="f142b-113">The HTML Helper options for rendering a partial view include:</span></span>

* [<span data-ttu-id="f142b-114">@await Html.PartialAsync</span><span class="sxs-lookup"><span data-stu-id="f142b-114">@await Html.PartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [<span data-ttu-id="f142b-115">@await Html.RenderPartialAsync</span><span class="sxs-lookup"><span data-stu-id="f142b-115">@await Html.RenderPartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [@Html.Partial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [@Html.RenderPartial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="f142b-116">Il modello *Product* è usato negli esempi di questo documento:</span><span class="sxs-lookup"><span data-stu-id="f142b-116">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="f142b-117">Segue un inventario degli attributi dell'helper tag Partial.</span><span class="sxs-lookup"><span data-stu-id="f142b-117">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="f142b-118">name</span><span class="sxs-lookup"><span data-stu-id="f142b-118">name</span></span>

<span data-ttu-id="f142b-119">L'attributo `name` è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="f142b-119">The `name` attribute is required.</span></span> <span data-ttu-id="f142b-120">Indica il nome o il percorso della visualizzazione parziale di cui eseguire il rendering.</span><span class="sxs-lookup"><span data-stu-id="f142b-120">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="f142b-121">Quando viene offerto un nome della visualizzazione parziale, viene avviato il processo di [individuazione delle visualizzazioni](xref:mvc/views/overview#view-discovery).</span><span class="sxs-lookup"><span data-stu-id="f142b-121">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="f142b-122">Tale processo viene ignorato quando viene offerto un percorso esplicito.</span><span class="sxs-lookup"><span data-stu-id="f142b-122">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="f142b-123">Per tutti i valori `name` accettabili, vedere [Individuazione delle visualizzazioni parziali](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="f142b-123">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="f142b-124">Il markup seguente usa un percorso esplicito, che indica che *_ProductPartial.cshtml* deve essere caricato dalla cartella *Shared*.</span><span class="sxs-lookup"><span data-stu-id="f142b-124">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="f142b-125">Mediante l'attributo [for](#for) un modello viene passato alla visualizzazione parziale per l'associazione.</span><span class="sxs-lookup"><span data-stu-id="f142b-125">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="f142b-126">for</span><span class="sxs-lookup"><span data-stu-id="f142b-126">for</span></span>

<span data-ttu-id="f142b-127">L'attributo `for` assegna una [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) da valutare rispetto al modello corrente.</span><span class="sxs-lookup"><span data-stu-id="f142b-127">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="f142b-128">Un elemento `ModelExpression` deduce la sintassi `@Model.`.</span><span class="sxs-lookup"><span data-stu-id="f142b-128">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="f142b-129">Ad esempio `for="Product"` può essere usato invece di `for="@Model.Product"`.</span><span class="sxs-lookup"><span data-stu-id="f142b-129">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="f142b-130">Questo comportamento di inferenza predefinito può essere ignorato se si usa il simbolo `@` per definire un'espressione inline.</span><span class="sxs-lookup"><span data-stu-id="f142b-130">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="f142b-131">Il markup seguente carica *_ProductPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f142b-131">The following markup loads *_ProductPartial.cshtml*:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="f142b-132">La visualizzazione parziale è correlata alla proprietà `Product` del modello di pagina associato:</span><span class="sxs-lookup"><span data-stu-id="f142b-132">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="f142b-133">modello</span><span class="sxs-lookup"><span data-stu-id="f142b-133">model</span></span>

<span data-ttu-id="f142b-134">L'attributo `model` assegna un'istanza del modello da passare alla visualizzazione parziale.</span><span class="sxs-lookup"><span data-stu-id="f142b-134">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="f142b-135">L'attributo `model` non può essere usato con l'attributo [for](#for).</span><span class="sxs-lookup"><span data-stu-id="f142b-135">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="f142b-136">Nel markup seguente viene creata un'istanza di un nuovo oggetto `Product` che viene quindi passata all'attributo `model` per il binding:</span><span class="sxs-lookup"><span data-stu-id="f142b-136">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="f142b-137">view-data</span><span class="sxs-lookup"><span data-stu-id="f142b-137">view-data</span></span>

<span data-ttu-id="f142b-138">L'attributo `view-data` assegna un [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) da passare alla visualizzazione parziale.</span><span class="sxs-lookup"><span data-stu-id="f142b-138">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="f142b-139">Il markup seguente rende l'intera raccolta ViewData accessibile alla visualizzazione parziale:</span><span class="sxs-lookup"><span data-stu-id="f142b-139">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="f142b-140">Nel codice precedente, il valore della chiave `IsNumberReadOnly` è impostato su `true` e aggiunto alla raccolta ViewData.</span><span class="sxs-lookup"><span data-stu-id="f142b-140">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="f142b-141">Di conseguenza l'elemento `ViewData["IsNumberReadOnly"]` viene reso accessibile all'interno della visualizzazione parziale seguente:</span><span class="sxs-lookup"><span data-stu-id="f142b-141">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="f142b-142">In questo esempio il valore di `ViewData["IsNumberReadOnly"]` determina se il campo *Number* viene visualizzato come campo di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="f142b-142">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="f142b-143">Eseguire la migrazione da un helper HTML</span><span class="sxs-lookup"><span data-stu-id="f142b-143">Migrate from an HTML Helper</span></span>

<span data-ttu-id="f142b-144">Osservare l'esempio di helper HTML asincrono seguente.</span><span class="sxs-lookup"><span data-stu-id="f142b-144">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="f142b-145">Viene eseguita l'iterazione e visualizzata una raccolta di prodotti.</span><span class="sxs-lookup"><span data-stu-id="f142b-145">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="f142b-146">Per il primo parametro del metodo `PartialAsync`, viene caricata la visualizzazione parziale *_ProductPartial.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f142b-146">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="f142b-147">Un'istanza del modello `Product` viene passata alla visualizzazione parziale per l'associazione.</span><span class="sxs-lookup"><span data-stu-id="f142b-147">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="f142b-148">L'helper tag Partial seguente consente di ottenere lo stesso comportamento asincrono dell'helper HTML `PartialAsync`.</span><span class="sxs-lookup"><span data-stu-id="f142b-148">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="f142b-149">All'attributo `model` viene assegnata un'istanza del modello `Product` per l'associazione alla visualizzazione parziale.</span><span class="sxs-lookup"><span data-stu-id="f142b-149">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="f142b-150">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f142b-150">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
