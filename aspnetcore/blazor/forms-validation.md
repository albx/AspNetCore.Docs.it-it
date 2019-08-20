---
title: Moduli e convalida di ASP.NET Core Blazer
author: guardrex
description: Informazioni su come usare i moduli e gli scenari di convalida del campo in blazer.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/forms-validation
ms.openlocfilehash: 0b2e38cdbd974a28960b917fb6b5ce370f8c4659
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030336"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="21cc3-103">Moduli e convalida di ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="21cc3-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="21cc3-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="21cc3-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="21cc3-105">I moduli e la convalida sono supportati in blazer usando le annotazioni [dei dati](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="21cc3-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="21cc3-106">È probabile che i moduli e gli scenari di convalida cambino a ogni versione di anteprima.</span><span class="sxs-lookup"><span data-stu-id="21cc3-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="21cc3-107">Il tipo `ExampleModel` seguente definisce la logica di convalida usando le annotazioni dei dati:</span><span class="sxs-lookup"><span data-stu-id="21cc3-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="21cc3-108">Un modulo viene definito usando il `EditForm` componente.</span><span class="sxs-lookup"><span data-stu-id="21cc3-108">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="21cc3-109">Il modulo seguente illustra elementi tipici, componenti e codice Razor:</span><span class="sxs-lookup"><span data-stu-id="21cc3-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="@exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

* <span data-ttu-id="21cc3-110">Il modulo convalida l'input dell'utente nel `name` campo usando la convalida definita `ExampleModel` nel tipo.</span><span class="sxs-lookup"><span data-stu-id="21cc3-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="21cc3-111">Il modello viene creato nel `@code` blocco del componente e viene mantenuto in un campo privato (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="21cc3-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="21cc3-112">Il campo viene assegnato all' `Model` attributo `<EditForm>` dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="21cc3-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="21cc3-113">Il `DataAnnotationsValidator` componente connette il supporto di convalida usando le annotazioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="21cc3-113">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="21cc3-114">Il `ValidationSummary` componente riepiloga i messaggi di convalida.</span><span class="sxs-lookup"><span data-stu-id="21cc3-114">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="21cc3-115">`HandleValidSubmit`viene attivato quando il form viene inviato correttamente (supera la convalida).</span><span class="sxs-lookup"><span data-stu-id="21cc3-115">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="21cc3-116">È disponibile un set di componenti di input predefiniti per la ricezione e la convalida dell'input dell'utente.</span><span class="sxs-lookup"><span data-stu-id="21cc3-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="21cc3-117">Gli input vengono convalidati quando vengono modificati e quando viene inviato un modulo.</span><span class="sxs-lookup"><span data-stu-id="21cc3-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="21cc3-118">I componenti di input disponibili sono illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="21cc3-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="21cc3-119">Componente di input</span><span class="sxs-lookup"><span data-stu-id="21cc3-119">Input component</span></span> | <span data-ttu-id="21cc3-120">Con rendering come&hellip;</span><span class="sxs-lookup"><span data-stu-id="21cc3-120">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="21cc3-121">Tutti i componenti di input, tra `EditForm`cui, supportano attributi arbitrari.</span><span class="sxs-lookup"><span data-stu-id="21cc3-121">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="21cc3-122">Qualsiasi attributo che non corrisponde a un parametro component viene aggiunto all'elemento HTML sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="21cc3-122">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="21cc3-123">I componenti di input forniscono il comportamento predefinito per la convalida in base alla modifica e alla modifica della classe CSS in modo da riflettere lo stato del campo.</span><span class="sxs-lookup"><span data-stu-id="21cc3-123">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="21cc3-124">Alcuni componenti includono una logica di analisi utile.</span><span class="sxs-lookup"><span data-stu-id="21cc3-124">Some components include useful parsing logic.</span></span> <span data-ttu-id="21cc3-125">`InputDate` E`InputNumber` , ad esempio, gestire i valori non analizzabili con la registrazione normale come errori di convalida.</span><span class="sxs-lookup"><span data-stu-id="21cc3-125">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="21cc3-126">I tipi che possono accettare valori null supportano anche il supporto di valori null del campo di destinazione `int?`, ad esempio.</span><span class="sxs-lookup"><span data-stu-id="21cc3-126">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="21cc3-127">Il tipo `Starship` seguente definisce la logica di convalida usando un set di proprietà e annotazioni di dati `ExampleModel`più ampio rispetto a quello precedente:</span><span class="sxs-lookup"><span data-stu-id="21cc3-127">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="21cc3-128">Nell'esempio `Description` precedente è facoltativo perché non sono presenti annotazioni di dati.</span><span class="sxs-lookup"><span data-stu-id="21cc3-128">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="21cc3-129">Il form seguente convalida l'input dell'utente utilizzando la convalida definita nel `Starship` modello:</span><span class="sxs-lookup"><span data-stu-id="21cc3-129">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```cshtml
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" @bind-Value="starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea id="description" @bind-Value="starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" @bind-Value="starship.Classification">
            <option value="">Select classification ...</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
            <option value="Defense">Defense</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            @bind-Value="starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" @bind-Value="starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate id="productionDate" @bind-Value="starship.ProductionDate" />
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="21cc3-130">`EditForm` crea un oggetto `EditContext` come [valore di propagazione](xref:blazor/components#cascading-values-and-parameters) che tiene traccia dei metadati relativi al processo di modifica, inclusi i campi modificati e i messaggi di convalida correnti.</span><span class="sxs-lookup"><span data-stu-id="21cc3-130">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="21cc3-131">Fornisce inoltre eventi pratici per invii validi e non validi (`OnValidSubmit`, `OnInvalidSubmit`). `EditForm`</span><span class="sxs-lookup"><span data-stu-id="21cc3-131">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="21cc3-132">In alternativa, usare `OnSubmit` per attivare la convalida e verificare i valori dei campi con il codice di convalida personalizzato.</span><span class="sxs-lookup"><span data-stu-id="21cc3-132">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="21cc3-133">Il `DataAnnotationsValidator` componente connette il supporto di convalida usando le annotazioni dei dati `EditContext`a a cascata.</span><span class="sxs-lookup"><span data-stu-id="21cc3-133">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="21cc3-134">L'abilitazione del supporto per la convalida usando le annotazioni dei dati richiede attualmente questo gesto esplicito, ma si sta valutando come il comportamento predefinito che sarà quindi possibile eseguire l'override.</span><span class="sxs-lookup"><span data-stu-id="21cc3-134">Enabling support for validation using data annotations currently requires this explicit gesture, but we're considering making this the default behavior that you can then override.</span></span> <span data-ttu-id="21cc3-135">Per usare un sistema di convalida diverso rispetto alle annotazioni dei `DataAnnotationsValidator` dati, sostituire con un'implementazione personalizzata.</span><span class="sxs-lookup"><span data-stu-id="21cc3-135">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="21cc3-136">L'implementazione del ASP.NET Core è disponibile per l'ispezione nell'origine riferimento: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="21cc3-136">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="21cc3-137">*L'implementazione del ASP.NET Core è soggetta a aggiornamenti rapidi durante il periodo di versione di anteprima.*</span><span class="sxs-lookup"><span data-stu-id="21cc3-137">*The ASP.NET Core implementation is subject to rapid updates during the preview release period.*</span></span>

<span data-ttu-id="21cc3-138">Il `ValidationSummary` componente riepiloga tutti i messaggi di convalida, che è simile all' [Helper tag di riepilogo della convalida](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="21cc3-138">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="21cc3-139">Il `ValidationMessage` componente Visualizza i messaggi di convalida per un campo specifico, che è simile all' [Helper tag del messaggio di convalida](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="21cc3-139">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="21cc3-140">Specificare il campo per la convalida con `For` l'attributo e un'espressione lambda che denomina la proprietà del modello:</span><span class="sxs-lookup"><span data-stu-id="21cc3-140">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="21cc3-141">I `ValidationMessage` componenti `ValidationSummary` e supportano attributi arbitrari.</span><span class="sxs-lookup"><span data-stu-id="21cc3-141">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="21cc3-142">Qualsiasi attributo che non corrisponde a un parametro component viene aggiunto all'elemento `<div>` generato `<ul>` o.</span><span class="sxs-lookup"><span data-stu-id="21cc3-142">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>