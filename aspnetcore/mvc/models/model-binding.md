---
title: Associazione di modelli in ASP.NET Core
author: tdykstra
description: Informazioni su come funziona l'associazione di modelli in ASP.NET Core e su come personalizzarne il comportamento.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: tdykstra
ms.date: 05/31/2019
uid: mvc/models/model-binding
ms.openlocfilehash: 10a9f8327bf16d11ec1e04ac3888d701f1ab1778
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724534"
---
# <a name="model-binding-in-aspnet-core"></a>Associazione di modelli in ASP.NET Core

Questo articolo illustra cos'è l'associazione di modelli, come funziona e come personalizzarne il comportamento.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([procedura per il download](xref:index#how-to-download-a-sample)).

## <a name="what-is-model-binding"></a>Che cos'è l'associazione di modelli

I controller e le pagine Razor operano sui dati provenienti dalle richieste HTTP. Ad esempio, i dati di route possono fornire una chiave del record e i campi modulo inviati possono fornire valori per le proprietà del modello. La scrittura di codice per recuperare tutti i valori e convertirli da stringhe in tipi .NET sarebbe noiosa e soggetta a errori. L'associazione di modelli consente di automatizzare questo processo. Il sistema di associazione di modelli:

* Recupera i dati da diverse origini, ad esempio i dati di route, i campi modulo e le stringhe di query.
* Fornisce i dati ai controller e alle pagine Razor in parametri di metodo e proprietà pubbliche.
* Converte dati stringa in tipi .NET.
* Aggiorna le proprietà dei tipi complessi.

## <a name="example"></a>Esempio

Si supponga di avere il metodo di azione seguente:

[!code-csharp[](model-binding/samples/2.x/Controllers/PetsController.cs?name=snippet_DogsOnly)]

E l'app riceve una richiesta con questo URL:

```
http://contoso.com/api/pets/2?DogsOnly=true
```

L'associazione di modelli esegue i passaggi seguenti dopo la selezione del metodo di azione da parte del sistema di routing:

* Trova il primo parametro di `GetByID`, un intero denominato `id`.
* Esamina le origini disponibili nella richiesta HTTP e trova `id` = "2" nei dati di route.
* Converte la stringa "2" nell'intero 2.
* Trova il parametro successivo di `GetByID`, un valore booleano denominato `dogsOnly`.
* Esamina le origini e trova "DogsOnly=true" nella stringa di query. Per la corrispondenza dei nomi non viene applicata la distinzione tra maiuscole e minuscole.
* Converte la stringa "true" nel valore booleano `true`.

Il framework chiama quindi il metodo `GetById`, passando 2 per il parametro `id` e `true` per il parametro `dogsOnly`.

Nell'esempio precedente le destinazioni dell'associazione di modelli sono parametri di metodo che sono tipi semplici. Le destinazioni possono essere anche le proprietà di un tipo complesso. Dopo l'associazione di ogni proprietà, viene eseguita la [convalida dei modelli](xref:mvc/models/validation) per la proprietà. Il record dei dati associati al modello e di eventuali errori di associazione o convalida viene archiviato in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oppure [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState). Per scoprire se questo processo ha esito positivo, l'app controlla il flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).

## <a name="targets"></a>Destinazioni

L'associazione di modelli cerca di trovare i valori per i tipi di destinazioni seguenti:

* Parametri del metodo di azione del controller a cui viene indirizzata una richiesta.
* Parametri del metodo del gestore di Razor Pages a cui viene indirizzata una richiesta. 
* Proprietà pubbliche di un controller o una classe `PageModel`, se specificate dagli attributi.

### <a name="bindproperty-attribute"></a>Attributo [BindProperty]

Può essere applicato a una proprietà pubblica di un controller o una classe `PageModel` per fare in modo che l'associazione di modelli usi tale proprietà come destinazione:

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=7-8)]

### <a name="bindpropertiesattribute"></a>Attributo [BindProperties]

Disponibile in ASP.NET Core 2.1 e versioni successive.  Può essere applicato a un controller o una classe `PageModel` per indicare all'associazione del modello di usare tutte le proprietà pubbliche della classe come destinazione:

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>Associazione di modelli per le richieste HTTP GET

Per impostazione predefinita, le proprietà non vengono associate per le richieste HTTP GET. In genere, per una richiesta GET è sufficiente un parametro ID record. L'ID record viene usato per cercare l'elemento nel database. Pertanto, non è necessario associare una proprietà che contiene un'istanza del modello. Negli scenari in cui si vogliono associare le proprietà ai dati dalle richieste GET, impostare la proprietà `SupportsGet` su `true`:

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>Origini

Per impostazione predefinita, l'associazione di modelli ottiene i dati sotto forma di coppie chiave-valore dalle origini seguenti in una richiesta HTTP:

1. Campi modulo 
1. Il corpo della richiesta (per i controller [ con l'attributo [ApiController]](xref:web-api/index#binding-source-parameter-inference).)
1. Dati della route
1. Parametri della stringa di query
1. File caricati 

Per ogni parametro o proprietà di destinazione, le origini vengono analizzate nell'ordine indicato in questo elenco. Esistono tuttavia alcune eccezioni:

* I dati di route e i valori delle stringhe di query vengono usati solo per i tipi semplici.
* I file caricati vengono associati solo ai tipi di destinazione che implementano `IFormFile` o `IEnumerable<IFormFile>`.

Se il comportamento predefinito non fornisce i risultati corretti, è possibile usare uno degli attributi seguenti per specificare l'origine da usare per qualsiasi destinazione specificata. 

* [[FromQuery] ](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Ottiene i valori dalla stringa di query. 
* [[FromRoute] ](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Ottiene i valori dai dati di route.
* [[FromForm] ](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Ottiene i valori dai campi modulo inviati.
* [[FromBody] ](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Ottiene i valori dal corpo della richiesta.
* [[FromHeader] ](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Ottiene i valori dalle intestazioni HTTP.

Questi attributi:

* Vengono aggiunti singolarmente alle proprietà del modello (non alla classe del modello), come nell'esempio seguente:

  [!code-csharp[](model-binding/samples/2.x/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* Accettano facoltativamente un valore di nome di modello nel costruttore. Questa opzione è disponibile nel caso in cui il nome della proprietà non corrisponda al valore nella richiesta. Il valore nella richiesta, ad esempio, potrebbe essere un'intestazione con un segno meno nel nome, come nell'esempio seguente:

  [!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>Attributo [FromBody]

I dati del corpo della richiesta vengono analizzati tramite i formattatori di input specifici per il tipo di contenuto della richiesta. I formattatori di input sono descritti [più avanti in questo articolo](#input-formatters).

Non applicare `[FromBody]` a più di un parametro per ogni metodo di azione. Il runtime di ASP.NET Core delega la responsabilità della lettura del flusso di richiesta al formattatore di input. Dopo la lettura, il flusso di richiesta non è più disponibile per altre letture per l'associazione di altri parametri `[FromBody]`.

### <a name="additional-sources"></a>Origini aggiuntive

I dati di origine vengono forniti al sistema di associazione di modelli dai *provider di valori*. È possibile scrivere e registrare i provider di valori personalizzati che recuperano i dati per l'associazione di modelli da altre origini. Ad esempio, potrebbe essere necessario recuperare dati dai cookie o dallo stato della sessione. Per ottenere dati da una nuova origine:

* Creare una classe che implementi `IValueProvider`.
* Creare una classe che implementi `IValueProviderFactory`.
* Registrare la classe factory in `Startup.ConfigureServices`.

L'app di esempio include un esempio di [provider di valori](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) e [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) che ottiene i valori dai cookie. Ecco il codice di registrazione in `Startup.ConfigureServices`:

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=3)]

Il codice illustrato posiziona il provider di valori personalizzati dopo tutti i provider di valori predefiniti.  Per renderlo il primo nell'elenco, chiamare `Insert(0, new CookieValueProviderFactory())` invece di `Add`.

## <a name="no-source-for-a-model-property"></a>Nessuna origine per una proprietà del modello

Per impostazione predefinita, non viene creato un errore di stato del modello se non viene trovato alcun valore per una proprietà del modello. La proprietà viene impostata su Null o su un valore predefinito:

* I tipi semplici nullable vengono impostati su `null`.
* I tipi valore non nullable vengono impostati su `default(T)`. Ad esempio, un parametro `int id` viene impostato su 0.
* Per i tipi complessi, l'associazione di modelli crea un'istanza usando il costruttore predefinito, senza impostare proprietà.
* Le matrici vengono impostate su `Array.Empty<T>()`, ad eccezione delle matrici `byte[]` che vengono impostate su `null`.

Se lo stato del modello deve essere invalidato quando non viene trovato alcun valore nei campi modulo per una proprietà del modello, usare l'[attributo [BindRequired]](#bindrequired-attribute).

Si noti che questo comportamento `[BindRequired]` si applica all'associazione di modelli dai dati di moduli inviati e non ai dati JSON o XML nel corpo di una richiesta. I dati del corpo della richiesta vengono gestiti dai [formattatori di input](#input-formatters).

## <a name="type-conversion-errors"></a>Errori di conversione dei tipi

Se viene trovata un'origine ma non può essere convertita nel tipo di destinazione, lo stato del modello viene contrassegnato come non valido. Il parametro o la proprietà di destinazione viene impostato su Null o su un valore predefinito, come indicato nella sezione precedente.

In un controller API con l'attributo `[ApiController]`, uno stato del modello non valido genera una risposta HTTP 400 automatica.

In una pagina Razor visualizzare di nuovo la pagina con un messaggio di errore:

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

La convalida sul lato client consente di rilevare la maggior parte dei dati non validi che, in caso contrario, verrebbero inviati a un modulo di Razor Pages. Questa convalida rende difficile attivare il codice sopra evidenziato. L'app di esempio include un pulsante **Submit with Invalid Date** (Invia con data non valida) che inserisce dati non validi nel campo **Hire Date** (Data assunzione) e invia il modulo. Questo pulsante illustra il funzionamento del codice per visualizzare di nuovo la pagina quando si verificano errori di conversione dei dati.

Quando la pagina viene nuovamente visualizzata dal codice precedente, l'input non valido non viene visualizzato nel campo modulo. Questo avviene perché la proprietà del modello è stata impostata su Null o su un valore predefinito. L'input non valido viene visualizzato in un messaggio di errore. Ma se si vogliono visualizzare di nuovo i dati non validi nel campo modulo, è consigliabile impostare la proprietà del modello come stringa ed eseguire manualmente la conversione dei dati.

La stessa strategia è consigliata se si vuole evitare che gli errori di conversione del tipo causino errori di stato del modello. In tal caso, impostare la proprietà del modello come stringa.

## <a name="simple-types"></a>Tipi semplici

I tipi semplici in cui lo strumento di associazione di modelli può convertire le stringhe di origine includono i seguenti:

* [Boolean](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [DateTime](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Decimal](xref:System.ComponentModel.DecimalConverter)
* [Double](xref:System.ComponentModel.DoubleConverter)
* [Enum](xref:System.ComponentModel.EnumConverter)
* [Guid](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Single](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)
* [Uri](xref:System.UriTypeConverter)
* [Version](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>Tipi complessi

Un tipo pubblico deve avere un costruttore predefinito pubblico e proprietà scrivibili pubbliche da associare. Quando si verifica l'associazione di modelli, vengono create istanze della classe usando il costruttore predefinito pubblico. 

Per ogni proprietà del tipo complesso, l'associazione di modelli cerca il modello di nome *prefisso.nome_proprietà* nelle origini. Se non viene trovato, cerca semplicemente *nome_proprietà* senza il prefisso.

Per l'associazione a un parametro, il prefisso è il nome del parametro. Per l'associazione a una proprietà pubblica `PageModel`, il prefisso è il nome della proprietà pubblica. Alcuni attributi hanno una proprietà `Prefix` che consente di eseguire l'override dell'utilizzo predefinito del nome di un parametro o una proprietà.

Ad esempio, si supponga che il tipo complesso sia la classe `Instructor` seguente:

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>Prefisso = nome del parametro

Se il modello da associare è un parametro denominato `instructorToUpdate`:

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

L'associazione di modelli cerca prima di tutto la chiave `instructorToUpdate.ID` nelle origini. Se non viene trovata, cerca `ID` senza prefisso.

### <a name="prefix--property-name"></a>Prefisso = nome della proprietà

Se il modello da associare è una proprietà denominata `Instructor` del controller o della classe `PageModel`:

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

L'associazione di modelli cerca prima di tutto la chiave `Instructor.ID` nelle origini. Se non viene trovata, cerca `ID` senza prefisso.

### <a name="custom-prefix"></a>Prefisso personalizzato

Se il modello da associare è un parametro denominato `instructorToUpdate` e un attributo `Bind` specifica `Instructor` come prefisso:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

L'associazione di modelli cerca prima di tutto la chiave `Instructor.ID` nelle origini. Se non viene trovata, cerca `ID` senza prefisso.

### <a name="attributes-for-complex-type-targets"></a>Attributi per le destinazioni di tipo complesso

Sono disponibili vari attributi predefiniti per controllare l'associazione di modelli di tipi complessi:

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> Questi attributi influiscono sul modello di associazione quando i dati di modulo inviati sono l'origine dei valori. Non influiscono sui formattatori di input, che elaborano corpi di richieste JSON e XML inviati. I formattatori di input sono descritti [più avanti in questo articolo](#input-formatters).
>
> Vedere anche la discussione relativa all'attributo `[Required]` in [Convalida del modello](xref:mvc/models/validation#required-attribute).

### <a name="bindrequired-attribute"></a>Attributo [BindRequired]

Può essere applicato solo alle proprietà del modello e non ai parametri di metodo. Con questo attributo l'associazione di modelli aggiunge un errore di stato del modello se non è possibile eseguire l'associazione per una proprietà del modello. Di seguito è riportato un esempio:

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a>Attributo [BindNever]

Può essere applicato solo alle proprietà del modello e non ai parametri di metodo. Impedisce all'associazione di modelli di impostare una proprietà del modello. Di seguito è riportato un esempio:

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a>Attributo [Bind]

Può essere applicato a una classe o a un parametro di metodo. Specifica quali proprietà di un modello devono essere incluse nell'associazione di modelli.

Nell'esempio seguente vengono associate solo le proprietà specificate del modello `Instructor` quando viene chiamato qualsiasi gestore o metodo di azione:

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

Nell'esempio seguente vengono associate solo le proprietà specificate del modello `Instructor` quando viene chiamato il metodo `OnPost`:

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

L'attributo `[Bind]` può essere usato per evitare l'overposting negli scenari di *creazione*. Non funziona bene negli scenari di modifica perché le proprietà escluse vengono impostate su Null o su un valore predefinito anziché rimanere inalterate. Per difendersi dall'overposting, sono consigliati i modelli di visualizzazione anziché l'attributo `[Bind]`. Per altre informazioni, vedere [Nota sulla sicurezza relativa all'overposting](xref:data/ef-mvc/crud#security-note-about-overposting).

## <a name="collections"></a>Raccolte

Per le destinazioni che sono raccolte di tipi semplici, l'associazione di modelli cerca le corrispondenze per *nome_parametro* oppure *nome_proprietà*. Se non viene trovata alcuna corrispondenza, viene cercato uno dei formati supportati senza il prefisso. Ad esempio:

* Si supponga che il parametro da associare sia una matrice denominata `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* I dati di modulo o di stringhe di query possono essere in uno dei formati seguenti:
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* Il formato seguente è supportato solo nei dati di modulo:

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* Per tutti i formati di esempio precedenti, l'associazione di modelli passa una matrice di due elementi al parametro `selectedCourses`:

  * selectedCourses[0]=1050
  * selectedCourses[1]=2000

  Per i formati di dati che usano numeri con indice (... [0]... [1]...) è necessario assicurarsi che la numerazione sia progressiva a partire da zero. Se sono presenti eventuali interruzioni nella numerazione con indice, tutti gli elementi dopo l'interruzione vengono ignorati. Ad esempio, se gli indici sono 0 e 2 anziché 0 e 1, il secondo elemento viene ignorato.

## <a name="dictionaries"></a>Dizionari

Per le destinazioni `Dictionary`, l'associazione di modelli cerca le corrispondenze per *nome_parametro* oppure *nome_proprietà*. Se non viene trovata alcuna corrispondenza, viene cercato uno dei formati supportati senza il prefisso. Ad esempio:

* Si supponga che il parametro di destinazione sia un elemento `Dictionary<int, string>` denominato `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* I dati di modulo o di stringhe di query inviati possono essere simili a uno degli esempi seguenti:

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* Per tutti i formati di esempio precedenti, l'associazione di modelli passa un dizionario di due elementi al parametro `selectedCourses`:

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses["2000"]="Economics"

## <a name="special-data-types"></a>Tipi di dati speciali

Esistono alcuni tipi di dati speciali che l'associazione di modelli può gestire.

### <a name="iformfile-and-iformfilecollection"></a>IFormFile e IFormFileCollection

Un file caricato incluso nella richiesta HTTP.  È anche supportato `IEnumerable<IFormFile>` per più file.

### <a name="cancellationtoken"></a>CancellationToken

usato per annullare l'attività nei controller asincroni.

### <a name="formcollection"></a>FormCollection

Usato per recuperare tutti i valori dai dati di modulo inviati.

## <a name="input-formatters"></a>Formattatori di input

I dati nel corpo della richiesta possono essere in formato JSON, XML o in altri formati. Per analizzare questi dati, l'associazione di modelli usa un *formattatore di input* configurato in modo da gestire un particolare tipo di contenuto. Per impostazione predefinita, ASP.NET Core include i formattatori di input basati su JSON per la gestione dei dati JSON. È possibile aggiungere altri formattatori per altri tipi di contenuto.

ASP.NET Core consente di selezionare i formattatori di input in base all'attributo [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute). Se non è presente alcun attributo, viene usata l'[intestazione Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).

Per usare i formattatori di input XML predefiniti:

* Installare il pacchetto NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.

* In `Startup.ConfigureServices` chiamare <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> o <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.

  [!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* Applicare l'attributo `Consumes` alle classi controller o ai metodi di azione che devono aspettarsi XML nel corpo della richiesta.

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  Per altre informazioni, vedere [Introduzione alla serializzazione XML](https://docs.microsoft.com/en-us/dotnet/standard/serialization/introducing-xml-serialization).

## <a name="exclude-specified-types-from-model-binding"></a>Escludere i tipi specificati dall'associazione di modelli

Il comportamento dell'associazione di modelli e del sistema di convalida è determinato da [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata). È possibile personalizzare `ModelMetadata` mediante l'aggiunta di un provider di dettagli a [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders). Sono disponibili provider di dettagli predefiniti per la disabilitazione dell'associazione di modelli o la convalida per i tipi specificati.

Per disabilitare l'associazione di modelli per tutti i modelli di un tipo specificato, aggiungere un <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`. Ad esempio, per disabilitare l'associazione di modelli per tutti i modelli di tipo `System.Version`:

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

Per disabilitare la convalida per le proprietà di un tipo specificato, aggiungere un <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`. Ad esempio, per disabilitare la convalida per le proprietà di tipo `System.Guid`:

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a>Strumenti di associazione di modelli personalizzati

È possibile estendere l'associazione di modelli scrivendo uno strumento di associazione di modelli personalizzato e usando l'attributo `[ModelBinder]` per selezionarlo per una determinata destinazione. Altre informazioni sull'[associazione di modelli personalizzata](xref:mvc/advanced/custom-model-binding).

## <a name="manual-model-binding"></a>Associazione di modelli manuale

L'associazione di modelli può essere richiamata manualmente usando il metodo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>. Il metodo è definito in entrambe le classi `ControllerBase` e `PageModel`. Gli overload del metodo consentono di specificare il prefisso e il provider di valori da usare. Il metodo restituisce `false` se l'associazione di modelli non riesce. Di seguito è riportato un esempio:

[!code-csharp[](model-binding/samples/2.x/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a>Attributo [FromServices]

Il nome di questo attributo segue il modello degli attributi di associazione di modelli che specificano un'origine dati. Non si tratta però dell'associazione di dati da un provider di valori. Ottiene un'istanza di un tipo dal contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection). Lo scopo è fornire un'alternativa all'inserimento del costruttore per quando è necessario un servizio solo se viene chiamato un metodo specifico.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>
