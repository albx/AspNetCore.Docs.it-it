---
title: Aggiungere la convalida a una pagina Razor ASP.NET Core
author: rick-anderson
description: Informazioni su come aggiungere la convalida a una pagina Razor in ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: d6d45dc7154bf415c3b098299d066b6fb37cf64d
ms.sourcegitcommit: 16502797ea749e2690feaa5e652a65b89c007c89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68483280"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="f44b2-103">Aggiungere la convalida a una pagina Razor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f44b2-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="f44b2-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f44b2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f44b2-105">In questa sezione la logica di convalida viene aggiunta al modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f44b2-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="f44b2-106">Le regole di convalida vengono applicate ogni volta che un utente crea o modifica un film.</span><span class="sxs-lookup"><span data-stu-id="f44b2-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="f44b2-107">Convalida</span><span class="sxs-lookup"><span data-stu-id="f44b2-107">Validation</span></span>

<span data-ttu-id="f44b2-108">Un concetto di base dello sviluppo del software si chiama [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself", Non ripeterti).</span><span class="sxs-lookup"><span data-stu-id="f44b2-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="f44b2-109">Le pagine Razor promuovono lo sviluppo in cui la funzionalità è stata specificata una volta e questa modifica è riflessa sull'intera app.</span><span class="sxs-lookup"><span data-stu-id="f44b2-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="f44b2-110">DRY contribuisce a:</span><span class="sxs-lookup"><span data-stu-id="f44b2-110">DRY can help:</span></span>

* <span data-ttu-id="f44b2-111">Ridurre la quantità di codice in un'app.</span><span class="sxs-lookup"><span data-stu-id="f44b2-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="f44b2-112">Rendere il codice meno soggetto ad errori e più facile da testare e gestire.</span><span class="sxs-lookup"><span data-stu-id="f44b2-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="f44b2-113">Il supporto della convalida fornito dalle pagine di Razor e da Entity Framework è un buon esempio del principio di DRY.</span><span class="sxs-lookup"><span data-stu-id="f44b2-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="f44b2-114">Le regole di convalida vengono specificate in modo dichiarativo in un'unica posizione (nella classe del modello) e le regole vengono applicate ovunque nell'app.</span><span class="sxs-lookup"><span data-stu-id="f44b2-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="f44b2-115">Errore di convalida dell'interfaccia utente nelle pagine Razor</span><span class="sxs-lookup"><span data-stu-id="f44b2-115">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="f44b2-116">Eseguire l'app e passare a Pages/Movies.</span><span class="sxs-lookup"><span data-stu-id="f44b2-116">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="f44b2-117">Selezionare il collegamento **Crea nuovo**.</span><span class="sxs-lookup"><span data-stu-id="f44b2-117">Select the **Create New** link.</span></span> <span data-ttu-id="f44b2-118">Completare il modulo con alcuni valori non validi.</span><span class="sxs-lookup"><span data-stu-id="f44b2-118">Complete the form with some invalid values.</span></span> <span data-ttu-id="f44b2-119">Quando la convalida del lato client jQuery rileva l'errore, viene visualizzato un messaggio di errore.</span><span class="sxs-lookup"><span data-stu-id="f44b2-119">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Il modulo di vista del film con più errori di convalida del lato client jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

<span data-ttu-id="f44b2-121">Si noti come il modulo ha eseguito automaticamente il rendering di un messaggio di errore di convalida in ogni campo che contiene un valore non valido.</span><span class="sxs-lookup"><span data-stu-id="f44b2-121">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="f44b2-122">Gli errori vengono applicati sia sul lato client (uso di JavaScript e jQuery) sia sul lato server (quando un utente ha JavaScript disabilitato).</span><span class="sxs-lookup"><span data-stu-id="f44b2-122">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="f44b2-123">Un vantaggio significativo è che non c'era **nessuna** modifica del codice necessaria nelle pagine Create o Edit.</span><span class="sxs-lookup"><span data-stu-id="f44b2-123">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="f44b2-124">Una volta che le DataAnnotations sono state applicate al modello, è stata abilitata la convalida dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="f44b2-124">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="f44b2-125">Le pagine Razor create in questa esercitazione hanno selezionato automaticamente le regole di convalida (usando gli attributi di convalida delle proprietà della classe `Movie` del modello).</span><span class="sxs-lookup"><span data-stu-id="f44b2-125">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="f44b2-126">Testa la convalida tramite la pagina Edit: viene applicata la stessa convalida.</span><span class="sxs-lookup"><span data-stu-id="f44b2-126">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="f44b2-127">I dati del modulo non vengono registrati nel server fino a quando non sono presenti errori di convalida nel lato client.</span><span class="sxs-lookup"><span data-stu-id="f44b2-127">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="f44b2-128">Verificare che i dati del modulo non siano stati registrati da uno o più degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="f44b2-128">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="f44b2-129">Inserire un punto di interruzione nel metodo `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="f44b2-129">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="f44b2-130">Inviare il modulo (selezionare **Crea** o **Salva**).</span><span class="sxs-lookup"><span data-stu-id="f44b2-130">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="f44b2-131">Il punto di interruzione non viene mai raggiunto.</span><span class="sxs-lookup"><span data-stu-id="f44b2-131">The break point is never hit.</span></span>
* <span data-ttu-id="f44b2-132">Usare lo [Strumento Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="f44b2-132">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="f44b2-133">Usare gli strumenti di sviluppo del browser per monitorare il traffico di rete.</span><span class="sxs-lookup"><span data-stu-id="f44b2-133">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="f44b2-134">Convalida sul lato server</span><span class="sxs-lookup"><span data-stu-id="f44b2-134">Server-side validation</span></span>

<span data-ttu-id="f44b2-135">Quando JavaScript è disabilitato nel browser, l'invio del modulo con errori verrà inoltrato al server.</span><span class="sxs-lookup"><span data-stu-id="f44b2-135">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="f44b2-136">Facoltativo, convalida sul lato server del test:</span><span class="sxs-lookup"><span data-stu-id="f44b2-136">Optional, test server-side validation:</span></span>

* <span data-ttu-id="f44b2-137">Disabilitare JavaScript nel browser.</span><span class="sxs-lookup"><span data-stu-id="f44b2-137">Disable JavaScript in the browser.</span></span> <span data-ttu-id="f44b2-138">È possibile disabilitare JavaScript usando gli strumenti di sviluppo del browser.</span><span class="sxs-lookup"><span data-stu-id="f44b2-138">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="f44b2-139">Se non è possibile disabilitare JavaScript nel browser, provare un altro browser.</span><span class="sxs-lookup"><span data-stu-id="f44b2-139">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="f44b2-140">Impostare un punto di interruzione nel metodo `OnPostAsync` della pagina Crea o Modifica.</span><span class="sxs-lookup"><span data-stu-id="f44b2-140">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="f44b2-141">Inviare un modulo con dati non validi.</span><span class="sxs-lookup"><span data-stu-id="f44b2-141">Submit a form with invalid data.</span></span>
* <span data-ttu-id="f44b2-142">Verificare che lo stato del modello non sia valido:</span><span class="sxs-lookup"><span data-stu-id="f44b2-142">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

<span data-ttu-id="f44b2-143">Il codice seguente mostra una parte della pagina *Create.cshtml* di cui è stato eseguito lo scaffolding in precedenza nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="f44b2-143">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="f44b2-144">Viene usato dalle pagine Create e Edit per visualizzare il modulo iniziale e per visualizzare nuovamente il modulo in caso di errore.</span><span class="sxs-lookup"><span data-stu-id="f44b2-144">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="f44b2-145">L'[helper tag di input](xref:mvc/views/working-with-forms) usa gli attributi [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produce gli attributi HTML necessari per la convalida jQuery sul lato client.</span><span class="sxs-lookup"><span data-stu-id="f44b2-145">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="f44b2-146">L'[helper tag di convalida](xref:mvc/views/working-with-forms#the-validation-tag-helpers) visualizza gli errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="f44b2-146">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="f44b2-147">Per altre informazioni, vedere [Convalida](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="f44b2-147">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="f44b2-148">Le pagine Create e Edit non dispongono di nessuna regola di convalida.</span><span class="sxs-lookup"><span data-stu-id="f44b2-148">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="f44b2-149">Le regole di convalida e le stringhe di errore vengono specificate solo nella classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f44b2-149">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="f44b2-150">Queste regole di convalida vengono applicate automaticamente alle pagine Razor che modificano il modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f44b2-150">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="f44b2-151">Quando la logica di convalida deve cambiare, avviene solo nel modello.</span><span class="sxs-lookup"><span data-stu-id="f44b2-151">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="f44b2-152">La convalida viene applicata in modo coerente in tutta l'applicazione (la logica di convalida è definita in un'unica posizione).</span><span class="sxs-lookup"><span data-stu-id="f44b2-152">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="f44b2-153">La convalida in un'unica posizione consente di mantenere il codice pulito e rende più semplice la gestione e l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="f44b2-153">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="f44b2-154">Utilizzo degli attributi DataType</span><span class="sxs-lookup"><span data-stu-id="f44b2-154">Using DataType Attributes</span></span>

<span data-ttu-id="f44b2-155">Esaminare la classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f44b2-155">Examine the `Movie` class.</span></span> <span data-ttu-id="f44b2-156">Lo spazio dei nomi `System.ComponentModel.DataAnnotations` fornisce gli attributi di formattazione oltre al set predefinito di attributi di convalida.</span><span class="sxs-lookup"><span data-stu-id="f44b2-156">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="f44b2-157">L'attributo `DataType` viene applicato alle proprietà `ReleaseDate` e `Price`.</span><span class="sxs-lookup"><span data-stu-id="f44b2-157">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="f44b2-158">Gli attributi `DataType` forniscono solo gli hint per far sì che il motore di vista formatti i dati (e fornisca gli attributi, ad esempio `<a>` per gli URL e `<a href="mailto:EmailAddress.com">` per la posta elettronica).</span><span class="sxs-lookup"><span data-stu-id="f44b2-158">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="f44b2-159">Usare l'attributo `RegularExpression` per convalidare il formato dei dati.</span><span class="sxs-lookup"><span data-stu-id="f44b2-159">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="f44b2-160">L'attributo `DataType` viene usato per specificare un tipo di dati che è più specifico del tipo intrinseco del database.</span><span class="sxs-lookup"><span data-stu-id="f44b2-160">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="f44b2-161">Gli attributi `DataType` non sono gli attributi di convalida.</span><span class="sxs-lookup"><span data-stu-id="f44b2-161">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="f44b2-162">Nell'applicazione di esempio, viene visualizzata solo la data, senza l'ora.</span><span class="sxs-lookup"><span data-stu-id="f44b2-162">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="f44b2-163">L'enumerazione `DataType` fornisce per molti tipi di dati, ad esempio Date, Time, PhoneNumber, Currency, EmailAddress e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="f44b2-163">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="f44b2-164">L'attributo `DataType` può anche consentire all'applicazione di fornire automaticamente le funzionalità specifiche del tipo.</span><span class="sxs-lookup"><span data-stu-id="f44b2-164">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="f44b2-165">Ad esempio, è possibile creare un collegamento `mailto:` per `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="f44b2-165">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="f44b2-166">È possibile fornire un selettore di dati per `DataType.Date` nei browser che supportano HTML5.</span><span class="sxs-lookup"><span data-stu-id="f44b2-166">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="f44b2-167">Gli attributi `DataType` generano attributi HTML5 `data-` (pronunciati *data dash*) che poi i browser HTML5 consumano.</span><span class="sxs-lookup"><span data-stu-id="f44b2-167">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="f44b2-168">Gli attributi `DataType` **non** forniscono alcuna convalida.</span><span class="sxs-lookup"><span data-stu-id="f44b2-168">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="f44b2-169">`DataType.Date` non specifica il formato della data visualizzata.</span><span class="sxs-lookup"><span data-stu-id="f44b2-169">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="f44b2-170">Per impostazione predefinita, il campo dei dati viene visualizzato in base ai formati predefiniti per il valore `CultureInfo` del server.</span><span class="sxs-lookup"><span data-stu-id="f44b2-170">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="f44b2-171">L'annotazione dei dati `[Column(TypeName = "decimal(18, 2)")]` è necessaria per consentire a Entity Framework Core di eseguire correttamente il mapping di `Price` nella valuta del database.</span><span class="sxs-lookup"><span data-stu-id="f44b2-171">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="f44b2-172">Per altre informazioni, vedere [Tipi di dati](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="f44b2-172">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="f44b2-173">L'attributo `DisplayFormat` viene usato per specificare in modo esplicito il formato della data:</span><span class="sxs-lookup"><span data-stu-id="f44b2-173">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="f44b2-174">L'impostazione `ApplyFormatInEditMode` specifica che deve essere applicata la formattazione quando il valore viene visualizzato per la modifica.</span><span class="sxs-lookup"><span data-stu-id="f44b2-174">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="f44b2-175">Questo comportamento potrebbe non essere desiderato per alcuni campi.</span><span class="sxs-lookup"><span data-stu-id="f44b2-175">You might not want that behavior for some fields.</span></span> <span data-ttu-id="f44b2-176">Ad esempio, in valori di valuta, è probabile che non si desideri il simbolo di valuta in modalità di modifica dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="f44b2-176">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="f44b2-177">L'attributo `DisplayFormat` può essere usato da solo, ma in genere è consigliabile usare l'attributo `DataType`.</span><span class="sxs-lookup"><span data-stu-id="f44b2-177">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="f44b2-178">L'attributo `DataType` fornisce la semantica dei dati anziché la modalità di esecuzione del rendering in una schermata e fornisce i seguenti vantaggi che non si ottengono con DisplayFormat:</span><span class="sxs-lookup"><span data-stu-id="f44b2-178">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="f44b2-179">Il browser può abilitare le funzionalità HTML5, ad esempio visualizzare un controllo di calendario, il simbolo della valuta appropriato per le impostazioni locali, i collegamenti alla posta elettronica e così via.</span><span class="sxs-lookup"><span data-stu-id="f44b2-179">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="f44b2-180">Per impostazione predefinita, il browser eseguirà il rendering dei dati usando il formato corretto in base alle impostazioni locali del sistema.</span><span class="sxs-lookup"><span data-stu-id="f44b2-180">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="f44b2-181">L'attributo `DataType` può abilitare il framework di ASP.NET Core a scegliere il modello di campo corretto per il rendering dei dati.</span><span class="sxs-lookup"><span data-stu-id="f44b2-181">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="f44b2-182">Il `DisplayFormat` se usato da solo usa il modello di stringa.</span><span class="sxs-lookup"><span data-stu-id="f44b2-182">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="f44b2-183">Nota: la convalida jQuery non funziona con l'attributo `Range` e con `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="f44b2-183">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="f44b2-184">Ad esempio, il codice seguente visualizzerà sempre un errore di convalida sul lato client, anche quando la data è compreso nell'intervallo specificato:</span><span class="sxs-lookup"><span data-stu-id="f44b2-184">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="f44b2-185">In genere non è consigliabile compilare date reali nei modelli, quindi l'uso dell'attributo `Range` e di `DateTime` è sconsigliato.</span><span class="sxs-lookup"><span data-stu-id="f44b2-185">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="f44b2-186">Il codice seguente illustra la combinazione di attributi in una sola riga:</span><span class="sxs-lookup"><span data-stu-id="f44b2-186">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="f44b2-187">[Introduzione a Razor Pages ed Entity Framework Core](xref:data/ef-rp/intro) include operazioni avanzate di Entity Framework Core con Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f44b2-187">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="f44b2-188">Applicare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="f44b2-188">Apply migrations</span></span>

<span data-ttu-id="f44b2-189">Le annotazioni DataAnnotations applicate alla classe modificano lo schema.</span><span class="sxs-lookup"><span data-stu-id="f44b2-189">The DataAnnotations applied to the class change the schema.</span></span> <span data-ttu-id="f44b2-190">Ad esempio, le annotazioni DataAnnotations applicate al campo `Title`:</span><span class="sxs-lookup"><span data-stu-id="f44b2-190">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="f44b2-191">Limitano i caratteri a 60.</span><span class="sxs-lookup"><span data-stu-id="f44b2-191">Limits the characters to 60.</span></span>
* <span data-ttu-id="f44b2-192">Non consentono un valore `null`.</span><span class="sxs-lookup"><span data-stu-id="f44b2-192">Doesn't allow a `null` value.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f44b2-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f44b2-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f44b2-194">La tabella `Movie` ha attualmente lo schema seguente:</span><span class="sxs-lookup"><span data-stu-id="f44b2-194">The `Movie` table currently has the following schema:</span></span>

``` sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="f44b2-195">Le modifiche dello schema precedenti non determinano la generazione di un'eccezione da parte di EF.</span><span class="sxs-lookup"><span data-stu-id="f44b2-195">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="f44b2-196">Tuttavia, creare una migrazione in modo che lo schema sia coerente con il modello.</span><span class="sxs-lookup"><span data-stu-id="f44b2-196">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="f44b2-197">Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="f44b2-197">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="f44b2-198">Nella Console di Gestione pacchetti immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="f44b2-198">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="f44b2-199">`Update-Database` esegue i metodi `Up` della classe `New_DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="f44b2-199">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="f44b2-200">Esaminare il metodo `Up`:</span><span class="sxs-lookup"><span data-stu-id="f44b2-200">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="f44b2-201">La tabella `Movie` aggiornata presenta lo schema seguente:</span><span class="sxs-lookup"><span data-stu-id="f44b2-201">The updated `Movie` table has the following schema:</span></span>

``` sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f44b2-202">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="f44b2-202">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f44b2-203">Non sono necessarie migrazioni per SQLite.</span><span class="sxs-lookup"><span data-stu-id="f44b2-203">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="f44b2-204">Pubblicare in Azure</span><span class="sxs-lookup"><span data-stu-id="f44b2-204">Publish to Azure</span></span>

<span data-ttu-id="f44b2-205">Per informazioni sulla distribuzione in Azure, vedere [Esercitazione: Creare un'app ASP.NET Core in Azure con il database SQL](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span><span class="sxs-lookup"><span data-stu-id="f44b2-205">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="f44b2-206">Grazie aver completato questa introduzione alle pagine Razor.</span><span class="sxs-lookup"><span data-stu-id="f44b2-206">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="f44b2-207">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) (Introduzione a Razor Pages ed Entity Framework Core) è un complemento ideale per questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="f44b2-207">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f44b2-208">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f44b2-208">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="f44b2-209">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="f44b2-209">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="f44b2-210">Precedente: Aggiunta di un nuovo campo</span><span class="sxs-lookup"><span data-stu-id="f44b2-210">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
