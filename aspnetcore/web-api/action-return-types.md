---
title: Tipi restituiti per le azioni del controller nell'API Web di ASP.NET Core
author: scottaddie
description: Informazioni sull'uso dei vari tipi restituiti dal metodo per le azioni del controller nell'API Web di ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/04/2019
uid: web-api/action-return-types
ms.openlocfilehash: 180d76c2c2e53dbf64b8fcc5cdc6d2b6f4dab6eb
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887116"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>Tipi restituiti per le azioni del controller nell'API Web di ASP.NET Core

Di [Scott Addie](https://github.com/scottaddie)

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

ASP.NET Core offre le seguenti opzioni per i tipi restituiti per le azioni del controller nell'API Web:

::: moniker range=">= aspnetcore-2.1"

* [Tipo specifico](#specific-type)
* [IActionResult](#iactionresult-type)
* [ActionResult\<T>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [Tipo specifico](#specific-type)
* [IActionResult](#iactionresult-type)

::: moniker-end

Questo documento spiega quando è più appropriato utilizzare ogni tipo restituito.

## <a name="specific-type"></a>Tipo specifico

L'azione più semplice restituisce un tipo di dati primitivo o complesso, ad esempio, `string` o un tipo di oggetto personalizzato. Si consideri l'azione seguente, che restituisce una raccolta di oggetti `Product` personalizzati:

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

In assenza di condizioni note da cui proteggersi durante l'esecuzione dell'azione, la restituzione di un tipo specifico potrebbe essere sufficiente. L'azione precedente non accetta parametri, quindi non è necessario eseguire la convalida dei vincoli dei parametri.

Quando per un'azione devono essere prese in considerazione condizioni note, vengono introdotti più percorsi di ritorno. In tal caso, è comune combinare un tipo restituito [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) al tipo restituito primitivo o complesso. Ai fini di questo tipo di azione, è necessario usare [IActionResult](#iactionresult-type) oppure [ActionResult\<T >](#actionresultt-type).

## <a name="iactionresult-type"></a>Tipo IActionResult

Il tipo restituito [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) è appropriato quando un'azione supporta più tipi restituiti [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult). I tipi `ActionResult` rappresentano vari codici di stato HTTP. Alcuni tipi restituiti comuni che rientrano in questa categoria sono [BadRequestResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestresult) (400), [NotFoundResult](/dotnet/api/microsoft.aspnetcore.mvc.notfoundresult) (404) e [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200).

Dal momento che nell'azione sono presenti più percorsi e tipi restituiti, è necessario usare spesso l'attributo [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute.-ctor). Questo attributo genera dettagli più descrittivi sulla risposta per le pagine della Guida di API generate da strumenti quali [Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger). `[ProducesResponseType]` indica i tipi noti e i codici di stato HTTP che l'azione deve restituire.

### <a name="synchronous-action"></a>Azione sincrona

Si consideri la seguente azione sincrona che prevede due possibili tipi restituiti:

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

Nell'azione precedente, viene restituito un codice di stato 404 quando il prodotto rappresentato da `id` non esiste nell'archivio dati sottostante. Il metodo helper [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) viene richiamato come collegamento a `return new NotFoundResult();`. Se il prodotto esiste, viene restituito un oggetto `Product` che rappresenta il payload con il codice di stato 200. Il metodo helper [Ok](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.ok) viene richiamato come forma abbreviata di `return new OkObjectResult(product);`.

### <a name="asynchronous-action"></a>Azione asincrona

Si consideri la seguente azione asincrona che prevede due possibili tipi restituiti:

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

Nel codice precedente:

* Un codice di stato 400 ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) viene restituito dal runtime di ASP.NET Core quando la descrizione del prodotto contiene "XYZ Widget".
* Un codice di stato 201 viene generato dal metodo [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) quando viene creato un prodotto. In questo percorso del codice viene restituito l'oggetto `Product`.

Ad esempio, il modello seguente indica che le richieste devono includere le proprietà `Name` e `Description`. Pertanto, se nella richiesta non vengono forniti `Name` e `Description`, la convalida del modello avrà esito negativo.

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

Se viene applicato l'attributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) in ASP.NET Core 2.1 o versioni successive, gli errori di convalida del modello generano un codice di stato 400. Per altre informazioni, vedere [Risposte HTTP 400 automatiche](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>Tipo ActionResult\<T>

ASP.NET Core 2.1 introduce il tipo restituito [ActionResult\<T >](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1) per le azioni del controller API Web. Consente di restituire un tipo che deriva da [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) o di restituire un [tipo specifico](#specific-type). `ActionResult<T>` offre i vantaggi seguenti rispetto al [tipo IActionResult](#iactionresult-type):

* La proprietà `Type` dell'attributo [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute) può essere esclusa. Ad esempio, `[ProducesResponseType(200, Type = typeof(Product))]` viene semplificato in `[ProducesResponseType(200)]`. Il tipo restituito previsto dell'azione viene invece dedotto da `T` in `ActionResult<T>`.
* [Operatori di cast impliciti](/dotnet/csharp/language-reference/keywords/implicit) supportano la conversione di `T` e `ActionResult` in `ActionResult<T>`. `T` viene convertito in [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult), il che significa che `return new ObjectResult(T);` viene semplificato in `return T;`.

C# non supporta operatori di cast impliciti sulle interfacce. Di conseguenza, per la conversione dell'interfaccia in un tipo concreto è necessario usare `ActionResult<T>`. Ad esempio, usare `IEnumerable` nell'esempio seguente non funziona:

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get()
{
    return _repository.GetProducts();
}
```

Una delle opzioni disponibili per correggere il codice precedente consiste nel restituire `_repository.GetProducts().ToList();`.

La maggior parte delle azioni presenta un tipo restituito specifico. Durante l'esecuzione di un'azione possono verificarsi condizioni impreviste, nel qual caso il tipo specifico non viene restituito. Ad esempio, il parametro di input di un'azione potrebbe non superare la convalida del modello. In tal caso, è consueto che venga restituito il tipo `ActionResult` appropriato anziché il tipo specifico.

### <a name="synchronous-action"></a>Azione sincrona

Si consideri un'azione sincrona che prevede due possibili tipi restituiti:

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

Nel codice precedente, viene restituito un codice di stato 404 quando il prodotto non esiste nel database. Se il prodotto esiste, viene restituito l'oggetto `Product` corrispondente. Prima di ASP.NET Core 2.1, la riga `return product;` sarebbe stata `return Ok(product);`.

> [!TIP]
> A partire da ASP.NET Core 2.1, è attiva l'inferenza per l'origine di associazione del parametro di azione quando una classe controller è decorata con l'attributo`[ApiController]`. Un nome di parametro corrispondente a un nome del modello di route viene associato automaticamente usando i dati della route della richiesta. Di conseguenza, il parametro `id` dell'azione precedente non è annotato in modo esplicito con l'attributo [[FromRoute]](/dotnet/api/microsoft.aspnetcore.mvc.fromrouteattribute).

### <a name="asynchronous-action"></a>Azione asincrona

Si consideri un'azione asincrona che prevede due possibili tipi restituiti:

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

Nel codice precedente:

* Un codice di stato 400 ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) viene restituito dal runtime di ASP.NET Core quando:
  * È stato applicato l'attributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) e la convalida del modello ha esito negativo.
  * La descrizione del prodotto contiene "XYZ Widget".
* Un codice di stato 201 viene generato dal metodo [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) quando viene creato un prodotto. In questo percorso del codice viene restituito l'oggetto `Product`.

> [!TIP]
> A partire da ASP.NET Core 2.1, è attiva l'inferenza per l'origine di associazione del parametro di azione quando una classe controller è decorata con l'attributo`[ApiController]`. I parametri di tipo complesso vengono associati automaticamente tramite il corpo della richiesta. Di conseguenza, il parametro `product` dell'azione precedente non è annotato in modo esplicito con l'attributo [[FromBody]](/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute).

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
