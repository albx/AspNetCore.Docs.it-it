---
title: Helper tag nei moduli in ASP.NET Core
author: rick-anderson
description: Descrive gli helper tag predefiniti usati con i moduli.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/working-with-forms
ms.openlocfilehash: 380d47d33706b3197dba3b9f7e3e1f186e27115f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890816"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="8305e-103">Helper tag nei moduli in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8305e-103">Tag Helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="8305e-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette) e [Jerrie Pelser](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="8305e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="8305e-105">Questo documento illustra l'uso di helper tag Form e gli elementi HTML comunemente usati all'interno di questi.</span><span class="sxs-lookup"><span data-stu-id="8305e-105">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="8305e-106">L'elemento [Form](https://www.w3.org/TR/html401/interact/forms.html) del linguaggio HTML rappresenta il meccanismo principale usato dalle app Web per eseguire il postback di dati nel server.</span><span class="sxs-lookup"><span data-stu-id="8305e-106">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="8305e-107">La maggior parte di questo documento descrive gli [helper tag](tag-helpers/intro.md) e spiega come questi consentono di creare moduli HTML solidi in modo produttivo.</span><span class="sxs-lookup"><span data-stu-id="8305e-107">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="8305e-108">Prima di leggere questo documento, è consigliabile leggere [Introduzione agli helper tag](tag-helpers/intro.md).</span><span class="sxs-lookup"><span data-stu-id="8305e-108">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="8305e-109">In molti casi gli helper HTML offrono un approccio alternativo a un helper tag specifico, ma è importante tenere presente che gli helper tag non sostituiscono gli helper HTML e che non esiste un helper tag per ogni helper HTML.</span><span class="sxs-lookup"><span data-stu-id="8305e-109">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="8305e-110">Se un'alternativa sotto forma di helper HTML esiste, viene citata.</span><span class="sxs-lookup"><span data-stu-id="8305e-110">When an HTML Helper alternative exists, it's mentioned.</span></span>

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="8305e-111">Helper tag Form</span><span class="sxs-lookup"><span data-stu-id="8305e-111">The Form Tag Helper</span></span>

<span data-ttu-id="8305e-112">L'helper tag [Form](https://www.w3.org/TR/html401/interact/forms.html):</span><span class="sxs-lookup"><span data-stu-id="8305e-112">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="8305e-113">Genera il valore dell'attributo `action` di [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) HTML per un'azione del controller MVC o una route denominata</span><span class="sxs-lookup"><span data-stu-id="8305e-113">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="8305e-114">Genera un [token di verifica della richiesta](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) nascosto per impedire richieste intersito false, in caso di uso con l'attributo `[ValidateAntiForgeryToken]` nel metodo azione HTTP Post</span><span class="sxs-lookup"><span data-stu-id="8305e-114">Generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="8305e-115">Fornisce l'attributo `asp-route-<Parameter Name>`, dove `<Parameter Name>` viene aggiunto ai valori della route.</span><span class="sxs-lookup"><span data-stu-id="8305e-115">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="8305e-116">I parametri `routeValues` in `Html.BeginForm` e `Html.BeginRouteForm` forniscono una funzionalità simile.</span><span class="sxs-lookup"><span data-stu-id="8305e-116">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="8305e-117">Ha come helper HTML alternativi `Html.BeginForm` e `Html.BeginRouteForm`</span><span class="sxs-lookup"><span data-stu-id="8305e-117">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="8305e-118">Esempio:</span><span class="sxs-lookup"><span data-stu-id="8305e-118">Sample:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="8305e-119">L'helper tag Form precedente genera il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-119">The Form Tag Helper above generates the following HTML:</span></span>

```HTML
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

<span data-ttu-id="8305e-120">Il runtime MVC genera il valore dell'attributo `action` dagli attributi `asp-controller` e `asp-action` dell'helper tag Form.</span><span class="sxs-lookup"><span data-stu-id="8305e-120">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="8305e-121">L'helper tag Form genera anche un [token di verifica della richiesta](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) nascosto per impedire richieste intersito false, in caso di uso con l'attributo `[ValidateAntiForgeryToken]` nel metodo azione HTTP Post.</span><span class="sxs-lookup"><span data-stu-id="8305e-121">The Form Tag Helper also generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="8305e-122">La protezione di un Form HTML puro da richieste intersito false è difficile, e l'helper tag Form offre questo servizio.</span><span class="sxs-lookup"><span data-stu-id="8305e-122">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="8305e-123">Uso di una route denominata</span><span class="sxs-lookup"><span data-stu-id="8305e-123">Using a named route</span></span>

<span data-ttu-id="8305e-124">L'attributo `asp-route` degli helper tag può anche generare un markup per l'attributo `action` HTML.</span><span class="sxs-lookup"><span data-stu-id="8305e-124">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="8305e-125">Un'app con una [route](../../fundamentals/routing.md) denominata `register` può usare il markup seguente per la pagina di registrazione:</span><span class="sxs-lookup"><span data-stu-id="8305e-125">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="8305e-126">Molte delle visualizzazioni nella cartella *Views/Account* (generata quando si crea una nuova app Web con *account utente singoli*) contengono l'attributo [asp-route-returnurl](xref:mvc/views/working-with-forms):</span><span class="sxs-lookup"><span data-stu-id="8305e-126">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts*) contain the [asp-route-returnurl](xref:mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="8305e-127">Con i modelli predefiniti, `returnUrl` viene popolato automaticamente solo quando si tenta di accedere a una risorsa autorizzata senza aver effettuato l'autenticazione o l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="8305e-127">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="8305e-128">Se l'utente tenta un accesso non autorizzato, il middleware di sicurezza lo reindirizza alla pagina di accesso con `returnUrl` impostato.</span><span class="sxs-lookup"><span data-stu-id="8305e-128">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-form-action-tag-helper"></a><span data-ttu-id="8305e-129">Helper tag per l'azione modulo</span><span class="sxs-lookup"><span data-stu-id="8305e-129">The Form Action Tag Helper</span></span>

<span data-ttu-id="8305e-130">L'helper tag per l'azione modulo genera l'attributo `formaction` per l'elemento `<button ...>` o il tag `<input type="image" ...>` generato.</span><span class="sxs-lookup"><span data-stu-id="8305e-130">The Form Action Tag Helper generates the `formaction` attribute on the generated `<button ...>` or `<input type="image" ...>` tag.</span></span> <span data-ttu-id="8305e-131">L'attributo `formaction` controlla se un modulo invia i dati.</span><span class="sxs-lookup"><span data-stu-id="8305e-131">The `formaction` attribute controls where a form submits its data.</span></span> <span data-ttu-id="8305e-132">Viene associato agli elementi [\<input>](https://www.w3.org/wiki/HTML/Elements/input) di tipo `image` e agli elementi [\<button>](https://www.w3.org/wiki/HTML/Elements/button).</span><span class="sxs-lookup"><span data-stu-id="8305e-132">It binds to [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elements of type `image` and [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elements.</span></span> <span data-ttu-id="8305e-133">L'helper tag per l'azione modulo consente l'utilizzo di vari attributi `asp-` di [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) per controllare quale collegamento `formaction` viene generato per l'elemento corrispondente.</span><span class="sxs-lookup"><span data-stu-id="8305e-133">The Form Action Tag Helper enables the usage of several [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` attributes to control what `formaction` link is generated for the corresponding element.</span></span>

<span data-ttu-id="8305e-134">Attributi di [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) supportati per controllare il valore di `formaction`:</span><span class="sxs-lookup"><span data-stu-id="8305e-134">Supported [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) attributes to control the value of `formaction`:</span></span>

|<span data-ttu-id="8305e-135">Attributo</span><span class="sxs-lookup"><span data-stu-id="8305e-135">Attribute</span></span>|<span data-ttu-id="8305e-136">Description</span><span class="sxs-lookup"><span data-stu-id="8305e-136">Description</span></span>|
|---|---|
|[<span data-ttu-id="8305e-137">asp-controller</span><span class="sxs-lookup"><span data-stu-id="8305e-137">asp-controller</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|<span data-ttu-id="8305e-138">Nome del controller.</span><span class="sxs-lookup"><span data-stu-id="8305e-138">The name of the controller.</span></span>|
|[<span data-ttu-id="8305e-139">asp-action</span><span class="sxs-lookup"><span data-stu-id="8305e-139">asp-action</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|<span data-ttu-id="8305e-140">Nome del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8305e-140">The name of the action method.</span></span>|
|[<span data-ttu-id="8305e-141">asp-area</span><span class="sxs-lookup"><span data-stu-id="8305e-141">asp-area</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|<span data-ttu-id="8305e-142">Nome dell'area.</span><span class="sxs-lookup"><span data-stu-id="8305e-142">The name of the area.</span></span>|
|[<span data-ttu-id="8305e-143">asp-page</span><span class="sxs-lookup"><span data-stu-id="8305e-143">asp-page</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|<span data-ttu-id="8305e-144">Nome della pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="8305e-144">The name of the Razor page.</span></span>|
|[<span data-ttu-id="8305e-145">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="8305e-145">asp-page-handler</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|<span data-ttu-id="8305e-146">Nome del gestore di pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="8305e-146">The name of the Razor page handler.</span></span>|
|[<span data-ttu-id="8305e-147">asp-route</span><span class="sxs-lookup"><span data-stu-id="8305e-147">asp-route</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|<span data-ttu-id="8305e-148">Nome della route.</span><span class="sxs-lookup"><span data-stu-id="8305e-148">The name of the route.</span></span>|
|[<span data-ttu-id="8305e-149">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="8305e-149">asp-route-{value}</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|<span data-ttu-id="8305e-150">Singolo valore di route URL.</span><span class="sxs-lookup"><span data-stu-id="8305e-150">A single URL route value.</span></span> <span data-ttu-id="8305e-151">Ad esempio `asp-route-id="1234"`.</span><span class="sxs-lookup"><span data-stu-id="8305e-151">For example, `asp-route-id="1234"`.</span></span>|
|[<span data-ttu-id="8305e-152">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="8305e-152">asp-all-route-data</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|<span data-ttu-id="8305e-153">Tutti i valori di route.</span><span class="sxs-lookup"><span data-stu-id="8305e-153">All route values.</span></span>|
|[<span data-ttu-id="8305e-154">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="8305e-154">asp-fragment</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|<span data-ttu-id="8305e-155">Frammento di URL.</span><span class="sxs-lookup"><span data-stu-id="8305e-155">The URL fragment.</span></span>|

### <a name="submit-to-controller-example"></a><span data-ttu-id="8305e-156">Esempio di invio al controller</span><span class="sxs-lookup"><span data-stu-id="8305e-156">Submit to controller example</span></span>

<span data-ttu-id="8305e-157">Il markup seguente invia il modulo all'azione `Index` di `HomeController` quando viene selezionato l'input o un pulsante:</span><span class="sxs-lookup"><span data-stu-id="8305e-157">The following markup submits the form to the `Index` action of `HomeController` when the input or button are selected:</span></span>

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

<span data-ttu-id="8305e-158">Il markup precedente genera il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-158">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a><span data-ttu-id="8305e-159">Esempio di invio a una pagina</span><span class="sxs-lookup"><span data-stu-id="8305e-159">Submit to page example</span></span>

<span data-ttu-id="8305e-160">Il markup seguente invia il modulo alla pagina Razor `About`:</span><span class="sxs-lookup"><span data-stu-id="8305e-160">The following markup submits the form to the `About` Razor Page:</span></span>

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

<span data-ttu-id="8305e-161">Il markup precedente genera il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-161">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a><span data-ttu-id="8305e-162">Esempio di invio a una route</span><span class="sxs-lookup"><span data-stu-id="8305e-162">Submit to route example</span></span>

<span data-ttu-id="8305e-163">Prendere in considerazione l'endpoint `/Home/Test`:</span><span class="sxs-lookup"><span data-stu-id="8305e-163">Consider the `/Home/Test` endpoint:</span></span>

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

<span data-ttu-id="8305e-164">Il markup seguente invia il modulo all'endpoint `/Home/Test`.</span><span class="sxs-lookup"><span data-stu-id="8305e-164">The following markup submits the form to the `/Home/Test` endpoint.</span></span>

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

<span data-ttu-id="8305e-165">Il markup precedente genera il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-165">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a><span data-ttu-id="8305e-166">Helper tag Input</span><span class="sxs-lookup"><span data-stu-id="8305e-166">The Input Tag Helper</span></span>

<span data-ttu-id="8305e-167">L'helper tag Input associa un elemento HTML [ \<input>](https://www.w3.org/wiki/HTML/Elements/input) a un'espressione di modello nella visualizzazione Razor.</span><span class="sxs-lookup"><span data-stu-id="8305e-167">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="8305e-168">Sintassi:</span><span class="sxs-lookup"><span data-stu-id="8305e-168">Syntax:</span></span>

```HTML
<input asp-for="<Expression Name>">
```

<span data-ttu-id="8305e-169">L'helper tag Input:</span><span class="sxs-lookup"><span data-stu-id="8305e-169">The Input Tag Helper:</span></span>

* <span data-ttu-id="8305e-170">Genera gli attributi HTML `id` e `name` per il nome dell'espressione specificata nell'attributo `asp-for`.</span><span class="sxs-lookup"><span data-stu-id="8305e-170">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="8305e-171">`asp-for="Property1.Property2"` è equivalente a `m => m.Property1.Property2`.</span><span class="sxs-lookup"><span data-stu-id="8305e-171">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="8305e-172">Il nome dell'espressione viene usato come valore dell'attributo `asp-for`.</span><span class="sxs-lookup"><span data-stu-id="8305e-172">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="8305e-173">Per altre informazioni, vedere la sezione [Nomi delle espressioni](#expression-names).</span><span class="sxs-lookup"><span data-stu-id="8305e-173">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="8305e-174">Imposta il valore dell'attributo HTML `type` in base agli attributi relativi al tipo di modello e all'[annotazione dei dati](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) applicati alla proprietà del modello</span><span class="sxs-lookup"><span data-stu-id="8305e-174">Sets the HTML `type` attribute value based on the model type and  [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="8305e-175">Non sovrascrive il valore dell'attributo HTML `type` se è già stato specificato</span><span class="sxs-lookup"><span data-stu-id="8305e-175">Won't overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="8305e-176">Genera attributi di convalida [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) da attributi di [annotazione dei dati](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) applicati alla proprietà del modello</span><span class="sxs-lookup"><span data-stu-id="8305e-176">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="8305e-177">Sovrappone una funzionalità di helper HTML con `Html.TextBoxFor` e `Html.EditorFor`.</span><span class="sxs-lookup"><span data-stu-id="8305e-177">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="8305e-178">Per i dettagli, vedere la sezione **Alternative helper HTML per l'helper tag Input**.</span><span class="sxs-lookup"><span data-stu-id="8305e-178">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="8305e-179">Consente una tipizzazione forte.</span><span class="sxs-lookup"><span data-stu-id="8305e-179">Provides strong typing.</span></span> <span data-ttu-id="8305e-180">Se il nome di proprietà viene modificato e non si aggiorna l'helper tag, si ottiene un errore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-180">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

```HTML
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

<span data-ttu-id="8305e-181">L'helper tag `Input` imposta l'attributo HTML `type` in base al tipo .NET.</span><span class="sxs-lookup"><span data-stu-id="8305e-181">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="8305e-182">La tabella seguente elenca alcuni tipi .NET comuni e il tipo HTML generato. Non sono elencati tutti i tipi .NET.</span><span class="sxs-lookup"><span data-stu-id="8305e-182">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="8305e-183">Tipo .NET</span><span class="sxs-lookup"><span data-stu-id="8305e-183">.NET type</span></span>|<span data-ttu-id="8305e-184">Tipo Input</span><span class="sxs-lookup"><span data-stu-id="8305e-184">Input Type</span></span>|
|---|---|
|<span data-ttu-id="8305e-185">Bool</span><span class="sxs-lookup"><span data-stu-id="8305e-185">Bool</span></span>|<span data-ttu-id="8305e-186">type="checkbox"</span><span class="sxs-lookup"><span data-stu-id="8305e-186">type="checkbox"</span></span>|
|<span data-ttu-id="8305e-187">Stringa</span><span class="sxs-lookup"><span data-stu-id="8305e-187">String</span></span>|<span data-ttu-id="8305e-188">type="text"</span><span class="sxs-lookup"><span data-stu-id="8305e-188">type="text"</span></span>|
|<span data-ttu-id="8305e-189">DateTime</span><span class="sxs-lookup"><span data-stu-id="8305e-189">DateTime</span></span>|<span data-ttu-id="8305e-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span><span class="sxs-lookup"><span data-stu-id="8305e-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span></span>|
|<span data-ttu-id="8305e-191">Byte</span><span class="sxs-lookup"><span data-stu-id="8305e-191">Byte</span></span>|<span data-ttu-id="8305e-192">type="number"</span><span class="sxs-lookup"><span data-stu-id="8305e-192">type="number"</span></span>|
|<span data-ttu-id="8305e-193">Int</span><span class="sxs-lookup"><span data-stu-id="8305e-193">Int</span></span>|<span data-ttu-id="8305e-194">type="number"</span><span class="sxs-lookup"><span data-stu-id="8305e-194">type="number"</span></span>|
|<span data-ttu-id="8305e-195">Single, Double</span><span class="sxs-lookup"><span data-stu-id="8305e-195">Single, Double</span></span>|<span data-ttu-id="8305e-196">type="number"</span><span class="sxs-lookup"><span data-stu-id="8305e-196">type="number"</span></span>|

<span data-ttu-id="8305e-197">La tabella seguente illustra alcuni attributi di [annotazioni dei dati](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) comuni di cui l'helper tag Input esegue il mapping a tipi di input specifici (non tutti gli attributi di convalida sono elencati):</span><span class="sxs-lookup"><span data-stu-id="8305e-197">The following table shows some common [data annotations](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>

|<span data-ttu-id="8305e-198">Attributo</span><span class="sxs-lookup"><span data-stu-id="8305e-198">Attribute</span></span>|<span data-ttu-id="8305e-199">Tipo Input</span><span class="sxs-lookup"><span data-stu-id="8305e-199">Input Type</span></span>|
|---|---|
|<span data-ttu-id="8305e-200">[EmailAddress]</span><span class="sxs-lookup"><span data-stu-id="8305e-200">[EmailAddress]</span></span>|<span data-ttu-id="8305e-201">type="email"</span><span class="sxs-lookup"><span data-stu-id="8305e-201">type="email"</span></span>|
|<span data-ttu-id="8305e-202">[Url]</span><span class="sxs-lookup"><span data-stu-id="8305e-202">[Url]</span></span>|<span data-ttu-id="8305e-203">type="url"</span><span class="sxs-lookup"><span data-stu-id="8305e-203">type="url"</span></span>|
|<span data-ttu-id="8305e-204">[HiddenInput]</span><span class="sxs-lookup"><span data-stu-id="8305e-204">[HiddenInput]</span></span>|<span data-ttu-id="8305e-205">type="hidden"</span><span class="sxs-lookup"><span data-stu-id="8305e-205">type="hidden"</span></span>|
|<span data-ttu-id="8305e-206">[Phone]</span><span class="sxs-lookup"><span data-stu-id="8305e-206">[Phone]</span></span>|<span data-ttu-id="8305e-207">type="tel"</span><span class="sxs-lookup"><span data-stu-id="8305e-207">type="tel"</span></span>|
|<span data-ttu-id="8305e-208">[DataType(DataType.Password)]</span><span class="sxs-lookup"><span data-stu-id="8305e-208">[DataType(DataType.Password)]</span></span>|<span data-ttu-id="8305e-209">type="password"</span><span class="sxs-lookup"><span data-stu-id="8305e-209">type="password"</span></span>|
|<span data-ttu-id="8305e-210">[DataType(DataType.Date)]</span><span class="sxs-lookup"><span data-stu-id="8305e-210">[DataType(DataType.Date)]</span></span>|<span data-ttu-id="8305e-211">type="date"</span><span class="sxs-lookup"><span data-stu-id="8305e-211">type="date"</span></span>|
|<span data-ttu-id="8305e-212">[DataType(DataType.Time)]</span><span class="sxs-lookup"><span data-stu-id="8305e-212">[DataType(DataType.Time)]</span></span>|<span data-ttu-id="8305e-213">type="time"</span><span class="sxs-lookup"><span data-stu-id="8305e-213">type="time"</span></span>|

<span data-ttu-id="8305e-214">Esempio:</span><span class="sxs-lookup"><span data-stu-id="8305e-214">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="8305e-215">Il codice precedente genera il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-215">The code above generates the following HTML:</span></span>

```HTML
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

<span data-ttu-id="8305e-216">Le annotazioni dei dati applicate alle proprietà `Email` e `Password` generano metadati per il modello.</span><span class="sxs-lookup"><span data-stu-id="8305e-216">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="8305e-217">L'helper tag Input usa i metadati del modello e genere attributi [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` (vedere [Convalida del modello](../models/validation.md)).</span><span class="sxs-lookup"><span data-stu-id="8305e-217">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="8305e-218">Questi attributi descrivono i validator da collegare ai campi di input.</span><span class="sxs-lookup"><span data-stu-id="8305e-218">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="8305e-219">Ciò consente una convalida HTML5 e [jQuery](https://jquery.com/) discreta.</span><span class="sxs-lookup"><span data-stu-id="8305e-219">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="8305e-220">Gli attributi discreti hanno il formato `data-val-rule="Error Message"`, in cui rule è il nome della regola di convalida (ad esempio `data-val-required`, `data-val-email`, `data-val-maxlength` e così via). Se l'attributo specifica un messaggio di errore, quest'ultimo costituisce il valore dell'attributo `data-val-rule`.</span><span class="sxs-lookup"><span data-stu-id="8305e-220">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it's displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="8305e-221">Esistono anche attributi di `data-val-ruleName-argumentName="argumentValue"` di Form che offrono dettagli aggiuntivi sulla regola, ad esempio, `data-val-maxlength-max="1024"` .</span><span class="sxs-lookup"><span data-stu-id="8305e-221">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="8305e-222">Alternative helper HTML per l'helper tag Input</span><span class="sxs-lookup"><span data-stu-id="8305e-222">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="8305e-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` e `Html.EditorFor` hanno funzionalità che si sovrappongono a quelle dell'helper tag Input.</span><span class="sxs-lookup"><span data-stu-id="8305e-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="8305e-224">L'helper tag Input imposta automaticamente l'attributo `type`, mentre `Html.TextBox` e `Html.TextBoxFor` non lo fanno.</span><span class="sxs-lookup"><span data-stu-id="8305e-224">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` won't.</span></span> <span data-ttu-id="8305e-225">`Html.Editor` e `Html.EditorFor` gestiscono raccolte, oggetti complessi e modelli, mentre l'helper tag Input non lo fa.</span><span class="sxs-lookup"><span data-stu-id="8305e-225">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper doesn't.</span></span> <span data-ttu-id="8305e-226">L'helper tag Input, `Html.EditorFor` e `Html.TextBoxFor` sono fortemente tipizzati (usano espressioni lambda), mentre `Html.TextBox` e `Html.Editor` non lo sono (usano nomi di espressioni).</span><span class="sxs-lookup"><span data-stu-id="8305e-226">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="8305e-227">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="8305e-227">HtmlAttributes</span></span>

<span data-ttu-id="8305e-228">Quando eseguono i modelli predefiniti, `@Html.Editor()` e `@Html.EditorFor()` usano una voce `ViewDataDictionary` speciale denominata `htmlAttributes`.</span><span class="sxs-lookup"><span data-stu-id="8305e-228">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="8305e-229">È possibile aumentare questo comportamento usando i parametri `additionalViewData`.</span><span class="sxs-lookup"><span data-stu-id="8305e-229">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="8305e-230">La chiave "htmlAttributes" non fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="8305e-230">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="8305e-231">La chiave "htmlAttributes" viene gestita in modo analogo all'oggetto `htmlAttributes` passato agli helper di input come `@Html.TextBox()`.</span><span class="sxs-lookup"><span data-stu-id="8305e-231">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```HTML
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="8305e-232">Nomi delle espressioni</span><span class="sxs-lookup"><span data-stu-id="8305e-232">Expression names</span></span>

<span data-ttu-id="8305e-233">Il valore dell'attributo `asp-for` è un `ModelExpression` e il lato destro di un'espressione lambda.</span><span class="sxs-lookup"><span data-stu-id="8305e-233">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="8305e-234">Pertanto, `asp-for="Property1"` diventa `m => m.Property1` nel codice generato. Per questo motivo il prefisso `Model` non è necessario.</span><span class="sxs-lookup"><span data-stu-id="8305e-234">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="8305e-235">È possibile usare il carattere "\@" per iniziare un'espressione inline, spostandolo prima di `m.`:</span><span class="sxs-lookup"><span data-stu-id="8305e-235">You can use the "\@" character to start an inline expression and move before the `m.`:</span></span>

```HTML
@{
       var joe = "Joe";
   }
   <input asp-for="@joe">
```

<span data-ttu-id="8305e-236">Genera quanto segue:</span><span class="sxs-lookup"><span data-stu-id="8305e-236">Generates the following:</span></span>

```HTML
<input type="text" id="joe" name="joe" value="Joe">
```

<span data-ttu-id="8305e-237">Con le proprietà delle raccolte, `asp-for="CollectionProperty[23].Member"` genera lo stesso nome di `asp-for="CollectionProperty[i].Member"` se il valore di `i` è `23`.</span><span class="sxs-lookup"><span data-stu-id="8305e-237">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

<span data-ttu-id="8305e-238">Quando ASP.NET Core MVC calcola il valore di `ModelExpression`, analizza diverse origini, tra cui `ModelState`.</span><span class="sxs-lookup"><span data-stu-id="8305e-238">When ASP.NET Core MVC calculates the value of `ModelExpression`, it inspects several sources, including `ModelState`.</span></span> <span data-ttu-id="8305e-239">Considerare `<input type="text" asp-for="@Name">`.</span><span class="sxs-lookup"><span data-stu-id="8305e-239">Consider `<input type="text" asp-for="@Name">`.</span></span> <span data-ttu-id="8305e-240">L'attributo `value` calcolato è il primo valore non Null da:</span><span class="sxs-lookup"><span data-stu-id="8305e-240">The calculated `value` attribute is the first non-null value from:</span></span>

* <span data-ttu-id="8305e-241">Voce `ModelState` con chiave "Name".</span><span class="sxs-lookup"><span data-stu-id="8305e-241">`ModelState` entry with key "Name".</span></span>
* <span data-ttu-id="8305e-242">Risultato dell'espressione `Model.Name`.</span><span class="sxs-lookup"><span data-stu-id="8305e-242">Result of the expression `Model.Name`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="8305e-243">Esplorazione delle proprietà figlio</span><span class="sxs-lookup"><span data-stu-id="8305e-243">Navigating child properties</span></span>

<span data-ttu-id="8305e-244">È anche possibile passare alle proprietà figlio tramite il percorso delle proprietà del modello di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="8305e-244">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="8305e-245">Si consideri una classe modello più complessa contenente una proprietà `Address` figlio.</span><span class="sxs-lookup"><span data-stu-id="8305e-245">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="8305e-246">Nella visualizzazione, viene eseguita l'associazione a `Address.AddressLine1`:</span><span class="sxs-lookup"><span data-stu-id="8305e-246">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="8305e-247">Per `Address.AddressLine1` viene generato il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-247">The following HTML is generated for `Address.AddressLine1`:</span></span>

```HTML
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="8305e-248">Nomi delle espressioni e raccolte</span><span class="sxs-lookup"><span data-stu-id="8305e-248">Expression names and Collections</span></span>

<span data-ttu-id="8305e-249">Esempio di modello contenente una matrice di `Colors`:</span><span class="sxs-lookup"><span data-stu-id="8305e-249">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="8305e-250">Metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="8305e-250">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
   {
       ViewData["Index"] = colorIndex;
       return View(GetPerson(id));
   }
```

<span data-ttu-id="8305e-251">Il codice Razor seguente illustra come accedere a un elemento `Color` specifico:</span><span class="sxs-lookup"><span data-stu-id="8305e-251">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="8305e-252">Modello *Views/Shared/EditorTemplates/String.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8305e-252">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="8305e-253">Esempio di uso di `List<T>`:</span><span class="sxs-lookup"><span data-stu-id="8305e-253">Sample using `List<T>`:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="8305e-254">Il codice Razor seguente illustra come eseguire l'iterazione in una raccolta:</span><span class="sxs-lookup"><span data-stu-id="8305e-254">The following Razor shows how to iterate over a collection:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="8305e-255">Modello *Views/Shared/EditorTemplates/ToDoItem.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8305e-255">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

<span data-ttu-id="8305e-256">`foreach` deve essere usato se possibile, quando il valore verrà usato in un contesto `asp-for` o in un contesto equivalente `Html.DisplayFor`.</span><span class="sxs-lookup"><span data-stu-id="8305e-256">`foreach` should be used if possible when the value is going to be used in an `asp-for` or `Html.DisplayFor` equivalent context.</span></span> <span data-ttu-id="8305e-257">In genere `for` è preferibile a `foreach` (se lo scenario lo consente) perché non è necessario allocare un enumeratore. Tuttavia, la valutazione di un indicizzatore in un'espressione LINQ può essere dispendiosa e deve essere ridotta al minimo.</span><span class="sxs-lookup"><span data-stu-id="8305e-257">In general, `for` is better than `foreach` (if the scenario allows it) because it doesn't need to allocate an enumerator; however, evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="8305e-258">Il codice di esempio commentato precedente illustra come sostituire l'espressione lambda con l'operatore `@` per accedere a ogni `ToDoItem` nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="8305e-258">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="8305e-259">Helper tag Textarea</span><span class="sxs-lookup"><span data-stu-id="8305e-259">The Textarea Tag Helper</span></span>

<span data-ttu-id="8305e-260">`Textarea Tag Helper` è simile all'helper Tag Input.</span><span class="sxs-lookup"><span data-stu-id="8305e-260">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="8305e-261">Genera gli attributi `id` e `name`, nonché gli attributi di convalida dei dati dal modello per un elemento [ \<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea).</span><span class="sxs-lookup"><span data-stu-id="8305e-261">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="8305e-262">Consente una tipizzazione forte.</span><span class="sxs-lookup"><span data-stu-id="8305e-262">Provides strong typing.</span></span>

* <span data-ttu-id="8305e-263">Helper HTML alternativo: `Html.TextAreaFor`</span><span class="sxs-lookup"><span data-stu-id="8305e-263">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="8305e-264">Esempio:</span><span class="sxs-lookup"><span data-stu-id="8305e-264">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="8305e-265">Viene generato il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-265">The following HTML is generated:</span></span>

```HTML
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a><span data-ttu-id="8305e-266">Helper tag Label</span><span class="sxs-lookup"><span data-stu-id="8305e-266">The Label Tag Helper</span></span>

* <span data-ttu-id="8305e-267">Genera la didascalia dell'etichetta e l'attributo `for` in un elemento [\<etichetta>](https://www.w3.org/wiki/HTML/Elements/label) per un nome di espressione</span><span class="sxs-lookup"><span data-stu-id="8305e-267">Generates the label caption and `for` attribute on a [\<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="8305e-268">Helper HTML alternativo: `Html.LabelFor`.</span><span class="sxs-lookup"><span data-stu-id="8305e-268">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="8305e-269">Rispetto a un elemento etichetta HTML puro, `Label Tag Helper` offre i vantaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8305e-269">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="8305e-270">Si ottiene automaticamente il valore descrittivo dell'etichetta dall'attributo `Display`.</span><span class="sxs-lookup"><span data-stu-id="8305e-270">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="8305e-271">Il nome visualizzato desiderato può cambiare nel tempo e la combinazione dell'attributo `Display` e dell'helper tag Label applica l'attributo `Display` ovunque venga usato.</span><span class="sxs-lookup"><span data-stu-id="8305e-271">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="8305e-272">Meno markup nel codice sorgente</span><span class="sxs-lookup"><span data-stu-id="8305e-272">Less markup in source code</span></span>

* <span data-ttu-id="8305e-273">Tipizzazione forte con la proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="8305e-273">Strong typing with the model property.</span></span>

<span data-ttu-id="8305e-274">Esempio:</span><span class="sxs-lookup"><span data-stu-id="8305e-274">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="8305e-275">Per l'elemento `<label>` viene generato il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-275">The following HTML is generated for the `<label>` element:</span></span>

```HTML
<label for="Email">Email Address</label>
```

<span data-ttu-id="8305e-276">L'helper tag Label genera il valore dell'attributo `for` di "Email", che è l'ID associato all'elemento `<input>`.</span><span class="sxs-lookup"><span data-stu-id="8305e-276">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="8305e-277">Gli helper tag generano elementi `id` e `for` coerenti. In questo modo possono essere associati correttamente.</span><span class="sxs-lookup"><span data-stu-id="8305e-277">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="8305e-278">La didascalia di questo esempio deriva dall'attributo `Display`.</span><span class="sxs-lookup"><span data-stu-id="8305e-278">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="8305e-279">Se il modello non contiene un attributo `Display`, la didascalia corrisponde al nome della proprietà dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="8305e-279">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="8305e-280">Helper tag di convalida</span><span class="sxs-lookup"><span data-stu-id="8305e-280">The Validation Tag Helpers</span></span>

<span data-ttu-id="8305e-281">Ci sono due helper tag di convalida.</span><span class="sxs-lookup"><span data-stu-id="8305e-281">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="8305e-282">`Validation Message Tag Helper`, che visualizza un messaggio di convalida per una singola proprietà del modello, e `Validation Summary Tag Helper`, che visualizza un riepilogo degli errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="8305e-282">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="8305e-283">`Input Tag Helper` aggiunge attributi di convalida sul lato client HTML5 agli elementi di input in base agli attributi di annotazione dei dati per le classi del modello.</span><span class="sxs-lookup"><span data-stu-id="8305e-283">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="8305e-284">La convalida viene eseguita anche per il server.</span><span class="sxs-lookup"><span data-stu-id="8305e-284">Validation is also performed on the server.</span></span> <span data-ttu-id="8305e-285">Quando si verifica un errore di convalida, l'helper tag di convalida visualizza questi messaggi di errore.</span><span class="sxs-lookup"><span data-stu-id="8305e-285">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="8305e-286">Helper tag Validation Message</span><span class="sxs-lookup"><span data-stu-id="8305e-286">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="8305e-287">Aggiunge l'attributo [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` all'elemento [span](https://developer.mozilla.org/docs/Web/HTML/Element/span), che collega i messaggi di errore di convalida nel campo di input della proprietà del modello specificato.</span><span class="sxs-lookup"><span data-stu-id="8305e-287">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="8305e-288">Quando si verifica un errore di convalida sul lato client, [jQuery](https://jquery.com/) visualizza il messaggio di errore nell'elemento `<span>`.</span><span class="sxs-lookup"><span data-stu-id="8305e-288">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="8305e-289">La convalida viene eseguita anche nel server.</span><span class="sxs-lookup"><span data-stu-id="8305e-289">Validation also takes place on the server.</span></span> <span data-ttu-id="8305e-290">Nei client JavaScript può essere disabilitato e una parte della convalida può essere eseguita solo sul lato server.</span><span class="sxs-lookup"><span data-stu-id="8305e-290">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="8305e-291">Helper HTML alternativo: `Html.ValidationMessageFor`</span><span class="sxs-lookup"><span data-stu-id="8305e-291">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="8305e-292">`Validation Message Tag Helper` viene usato con l'attributo `asp-validation-for` in un elemento HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span).</span><span class="sxs-lookup"><span data-stu-id="8305e-292">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```HTML
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="8305e-293">L'helper tag Validation Message genera il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-293">The Validation Message Tag Helper will generate the following HTML:</span></span>

```HTML
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="8305e-294">In genere si usa `Validation Message Tag Helper` dopo un helper tag `Input` per la stessa proprietà.</span><span class="sxs-lookup"><span data-stu-id="8305e-294">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="8305e-295">In questo modo è possibile visualizzare i messaggi di errore di convalida vicino all'input che ha causato l'errore.</span><span class="sxs-lookup"><span data-stu-id="8305e-295">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="8305e-296">È necessario avere una visualizzazione con i riferimenti agli script JavaScript e [jQuery](https://jquery.com/) corretti per la convalida lato client.</span><span class="sxs-lookup"><span data-stu-id="8305e-296">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="8305e-297">Per altre informazioni, vedere [Convalida del modello](../models/validation.md).</span><span class="sxs-lookup"><span data-stu-id="8305e-297">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="8305e-298">Se si verifica un errore di convalida sul lato server, ad esempio quando la convalida sul lato server è personalizzata o quando la convalida sul lato client è disabilitata, MVC inserisce il messaggio di errore nel corpo dell'elemento `<span>`.</span><span class="sxs-lookup"><span data-stu-id="8305e-298">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```HTML
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="8305e-299">Helper tag Validation Summary</span><span class="sxs-lookup"><span data-stu-id="8305e-299">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="8305e-300">Considera come destinazione gli elementi `<div>` con l'attributo `asp-validation-summary`</span><span class="sxs-lookup"><span data-stu-id="8305e-300">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="8305e-301">Helper HTML alternativo: `@Html.ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="8305e-301">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="8305e-302">`Validation Summary Tag Helper` viene usato per visualizzare un riepilogo dei messaggi di convalida.</span><span class="sxs-lookup"><span data-stu-id="8305e-302">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="8305e-303">Il valore dell'attributo `asp-validation-summary` può corrispondere a uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="8305e-303">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="8305e-304">asp-validation-summary</span><span class="sxs-lookup"><span data-stu-id="8305e-304">asp-validation-summary</span></span>|<span data-ttu-id="8305e-305">Messaggi di convalida visualizzati</span><span class="sxs-lookup"><span data-stu-id="8305e-305">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="8305e-306">ValidationSummary.All</span><span class="sxs-lookup"><span data-stu-id="8305e-306">ValidationSummary.All</span></span>|<span data-ttu-id="8305e-307">Livello di modello e proprietà</span><span class="sxs-lookup"><span data-stu-id="8305e-307">Property and model level</span></span>|
|<span data-ttu-id="8305e-308">ValidationSummary.ModelOnly</span><span class="sxs-lookup"><span data-stu-id="8305e-308">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="8305e-309">Modello</span><span class="sxs-lookup"><span data-stu-id="8305e-309">Model</span></span>|
|<span data-ttu-id="8305e-310">ValidationSummary.None</span><span class="sxs-lookup"><span data-stu-id="8305e-310">ValidationSummary.None</span></span>|<span data-ttu-id="8305e-311">nessuno</span><span class="sxs-lookup"><span data-stu-id="8305e-311">None</span></span>|

### <a name="sample"></a><span data-ttu-id="8305e-312">Esempio</span><span class="sxs-lookup"><span data-stu-id="8305e-312">Sample</span></span>

<span data-ttu-id="8305e-313">Nell'esempio seguente, il modello di dati viene decorato con attributi `DataAnnotation` e genera messaggi di errore di convalida per l'elemento `<input>`.</span><span class="sxs-lookup"><span data-stu-id="8305e-313">In the following example, the data model is decorated with `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="8305e-314">Quando si verifica un errore di convalida, l'helper tag di convalida visualizza il messaggio di errore:</span><span class="sxs-lookup"><span data-stu-id="8305e-314">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="8305e-315">Codice HTML generato (se il modello è valido):</span><span class="sxs-lookup"><span data-stu-id="8305e-315">The generated HTML (when the model is valid):</span></span>

```HTML
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a><span data-ttu-id="8305e-316">Helper tag Select</span><span class="sxs-lookup"><span data-stu-id="8305e-316">The Select Tag Helper</span></span>

* <span data-ttu-id="8305e-317">Genera l'elemento [select](https://www.w3.org/wiki/HTML/Elements/select) e gli elementi [option](https://www.w3.org/wiki/HTML/Elements/option) associati per le proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="8305e-317">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="8305e-318">Ha come helper HTML alternativi `Html.DropDownListFor` e `Html.ListBoxFor`</span><span class="sxs-lookup"><span data-stu-id="8305e-318">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="8305e-319">`Select Tag Helper` `asp-for` specifica il nome della proprietà di modello per l'elemento [select](https://www.w3.org/wiki/HTML/Elements/select) e `asp-items` specifica gli elementi [option](https://www.w3.org/wiki/HTML/Elements/option).</span><span class="sxs-lookup"><span data-stu-id="8305e-319">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="8305e-320">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8305e-320">For example:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="8305e-321">Esempio:</span><span class="sxs-lookup"><span data-stu-id="8305e-321">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="8305e-322">Il metodo `Index` inizializza `CountryViewModel`, imposta il paese selezionato e lo passa alla visualizzazione `Index`.</span><span class="sxs-lookup"><span data-stu-id="8305e-322">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

<span data-ttu-id="8305e-323">Il metodo `Index` di HTTP POST visualizza la selezione:</span><span class="sxs-lookup"><span data-stu-id="8305e-323">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="8305e-324">Visualizzazione `Index`:</span><span class="sxs-lookup"><span data-stu-id="8305e-324">The `Index` view:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="8305e-325">Che genera il codice HTML seguente (con "CA" selezionato):</span><span class="sxs-lookup"><span data-stu-id="8305e-325">Which generates the following HTML (with "CA" selected):</span></span>

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> <span data-ttu-id="8305e-326">Non è consigliabile usare `ViewBag` o `ViewData` con l'helper tag Select.</span><span class="sxs-lookup"><span data-stu-id="8305e-326">We don't recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="8305e-327">Un modello di visualizzazione è più solido e in genere presenta meno problemi quando si tratta di fornire metadati MVC.</span><span class="sxs-lookup"><span data-stu-id="8305e-327">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="8305e-328">Il valore dell'attributo `asp-for` rappresenta un caso speciale e non richiede un prefisso `Model`, che invece è richiesto dagli altri attributi di helper tag, ad esempio `asp-items`</span><span class="sxs-lookup"><span data-stu-id="8305e-328">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="8305e-329">Associazione di enum</span><span class="sxs-lookup"><span data-stu-id="8305e-329">Enum binding</span></span>

<span data-ttu-id="8305e-330">Spesso è comodo usare `<select>` con una proprietà `enum` e generare gli elementi `SelectListItem` dai valori `enum`.</span><span class="sxs-lookup"><span data-stu-id="8305e-330">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="8305e-331">Esempio:</span><span class="sxs-lookup"><span data-stu-id="8305e-331">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="8305e-332">Il metodo `GetEnumSelectList` genera un oggetto `SelectList` per un enum.</span><span class="sxs-lookup"><span data-stu-id="8305e-332">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="8305e-333">Per ottenere un'interfaccia utente più completa, è possibile decorare l'elenco di enumeratori con l'attributo `Display`:</span><span class="sxs-lookup"><span data-stu-id="8305e-333">You can decorate your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="8305e-334">Viene generato il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-334">The following HTML is generated:</span></span>

```HTML
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a><span data-ttu-id="8305e-335">Gruppo di opzioni</span><span class="sxs-lookup"><span data-stu-id="8305e-335">Option Group</span></span>

<span data-ttu-id="8305e-336">L'elemento HTML [ \<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) viene generato quando il modello di visualizzazione contiene uno o più oggetti `SelectListGroup`.</span><span class="sxs-lookup"><span data-stu-id="8305e-336">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="8305e-337">`CountryViewModelGroup` suddivide gli elementi `SelectListItem` nei gruppi "North America" ed "Europe":</span><span class="sxs-lookup"><span data-stu-id="8305e-337">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="8305e-338">I due gruppi sono illustrati di seguito:</span><span class="sxs-lookup"><span data-stu-id="8305e-338">The two groups are shown below:</span></span>

![esempio di gruppi di opzioni](working-with-forms/_static/grp.png)

<span data-ttu-id="8305e-340">Codice HTML generato:</span><span class="sxs-lookup"><span data-stu-id="8305e-340">The generated HTML:</span></span>

```HTML
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a><span data-ttu-id="8305e-341">Selezione multipla</span><span class="sxs-lookup"><span data-stu-id="8305e-341">Multiple select</span></span>

<span data-ttu-id="8305e-342">L'helper tag Select genera automaticamente l'attributo [multiple = "multiple"](http://w3c.github.io/html-reference/select.html) se la proprietà specificata nell'attributo `asp-for` è `IEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="8305e-342">The Select Tag Helper  will automatically generate the [multiple = "multiple"](http://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="8305e-343">Dato, ad esempio, il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-343">For example, given the following model:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="8305e-344">Con la visualizzazione seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-344">With the following view:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="8305e-345">Viene generato il codice HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="8305e-345">Generates the following HTML:</span></span>

```HTML
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a><span data-ttu-id="8305e-346">Nessuna selezione</span><span class="sxs-lookup"><span data-stu-id="8305e-346">No selection</span></span>

<span data-ttu-id="8305e-347">Se si usa l'opzione "not specified" in più pagine, è possibile creare un modello per evitare di ripetere lo stesso codice HTML:</span><span class="sxs-lookup"><span data-stu-id="8305e-347">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="8305e-348">Modello *Views/Shared/EditorTemplates/CountryViewModel.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8305e-348">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="8305e-349">L'aggiunta di elementi HTML [ \<option>](https://www.w3.org/wiki/HTML/Elements/option) non è limitata al caso relativo a *nessuna selezione*.</span><span class="sxs-lookup"><span data-stu-id="8305e-349">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements isn't limited to the *No selection* case.</span></span> <span data-ttu-id="8305e-350">La visualizzazione e il metodo di azione seguenti, ad esempio, generano codice HTML simile al codice precedente:</span><span class="sxs-lookup"><span data-stu-id="8305e-350">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

[!code-HTML[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="8305e-351">L'elemento `<option>` corretto viene selezionato (con l'attributo `selected="selected"`) a seconda del valore `Country` corrente.</span><span class="sxs-lookup"><span data-stu-id="8305e-351">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

```HTML
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a><span data-ttu-id="8305e-352">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8305e-352">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* [<span data-ttu-id="8305e-353">Elemento Form HTML</span><span class="sxs-lookup"><span data-stu-id="8305e-353">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)
* [<span data-ttu-id="8305e-354">Token di verifica della richiesta</span><span class="sxs-lookup"><span data-stu-id="8305e-354">Request Verification Token</span></span>](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [<span data-ttu-id="8305e-355">Interfaccia IAttributeAdapter</span><span class="sxs-lookup"><span data-stu-id="8305e-355">IAttributeAdapter Interface</span></span>](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [<span data-ttu-id="8305e-356">Frammenti di codice per questo documento</span><span class="sxs-lookup"><span data-stu-id="8305e-356">Code snippets for this document</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
