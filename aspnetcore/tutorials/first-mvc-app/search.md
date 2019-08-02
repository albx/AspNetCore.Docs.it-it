---
title: Aggiungere la funzionalità di ricerca a un'app ASP.NET Core MVC
author: rick-anderson
description: Illustra come aggiungere la funzionalità di ricerca a un'app ASP.NET Core MVC di base
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: ed6c7a095143670b7d06e43db3a428dec9bf97ad
ms.sourcegitcommit: 3204bc89ae6354b61ee0a9b2770ebe5214b7790c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707835"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="1a4f3-103">Aggiungere la funzionalità di ricerca a un'app ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="1a4f3-103">Add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="1a4f3-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1a4f3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1a4f3-105">In questa sezione si aggiunge la funzionalità di ricerca al metodo di azione `Index` che consente di cercare film in base al *genere* o al *nome*.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="1a4f3-106">Aggiornare il metodo `Index` all'interno di *Controllers/MoviesController.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-106">Update the `Index` method found inside *Controllers/MoviesController.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="1a4f3-107">La prima riga del metodo di azione `Index` crea una query [LINQ](/dotnet/standard/using-linq) per selezionare i film:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="1a4f3-108">La query viene *solo* definita in questo punto, ma **non** viene eseguita nel database.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="1a4f3-109">Se il parametro `searchString` contiene una stringa, la query dei film viene modificata per filtrare in base al valore della stringa di ricerca:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="1a4f3-110">Il codice `s => s.Title.Contains()` precedente è un'[espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="1a4f3-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="1a4f3-111">Le espressioni lambda vengono usate nelle query [LINQ](/dotnet/standard/using-linq) basate sul metodo come argomenti dei metodi di operatori di query standard, quali il metodo [Where](/dotnet/api/system.linq.enumerable.where) o `Contains` (usato nel codice precedente).</span><span class="sxs-lookup"><span data-stu-id="1a4f3-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="1a4f3-112">Le query LINQ non vengono eseguite al momento della definizione o della modifica chiamando un metodo quale `Where`, `Contains` o `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="1a4f3-113">L'esecuzione della query viene invece posticipata.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="1a4f3-114">Per esecuzione posticipata si intende che la valutazione di un'espressione viene ritardata finché non viene eseguita l'iterazione del valore o non viene chiamato il metodo `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="1a4f3-115">Per altre informazioni sull'esecuzione posticipata di query, vedere [Esecuzione di query](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="1a4f3-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="1a4f3-116">Nota: il metodo [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) viene eseguito nel database, non nel codice C# illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="1a4f3-117">La distinzione tra maiuscole/minuscole nella query dipende dal database e dalle regole di confronto.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="1a4f3-118">In SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) esegue il mapping a [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), che fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="1a4f3-119">In SQLite, con le regole di confronto predefinite si fa distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="1a4f3-120">Passare a `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="1a4f3-121">Accodare una stringa di query, ad esempio `?searchString=Ghost`, all'URL.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="1a4f3-122">Vengono visualizzati i film filtrati.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-122">The filtered movies are displayed.</span></span>

![Vista Index](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="1a4f3-124">Se si modifica la firma del metodo `Index` in modo che contenga un parametro denominato `id`, il parametro `id` corrisponderà al segnaposto `{id}` facoltativo per le route predefinite impostate in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="1a4f3-125">Modificare il parametro in `id` e tutte le occorrenze di `searchString` cambiano in `id`.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="1a4f3-126">Il metodo `Index` precedente:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="1a4f3-127">Il metodo `Index` aggiornato con il parametro `id`:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="1a4f3-128">È ora possibile passare il titolo della ricerca come dati di route (un segmento di URL), anziché come valore della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![Vista Index con la parola ghost aggiunta all'URL e un elenco restituito di due film: Ghostbusters e Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="1a4f3-130">Tuttavia, non è possibile supporre che gli utenti modifichino l'URL ogni volta che desiderano cercare un film.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="1a4f3-131">A questo punto si aggiungeranno elementi dell'interfaccia utente per filtrare i film.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="1a4f3-132">Se è stata modificata la firma del metodo `Index` per testare come passare il parametro `ID` associato alla route, impostarlo di nuovo in modo che accetti un parametro denominato `searchString`:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="1a4f3-133">Aprire il file *Views/Movies/Index.cshtml* e aggiungere il markup `<form>` evidenziato di seguito:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="1a4f3-134">Il tag `<form>` HTML usa l'[helper tag del modulo](xref:mvc/views/working-with-forms) e quindi quando si invia il modulo, la stringa di filtro viene registrata nell'azione `Index` del controller di film.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="1a4f3-135">Salvare le modifiche e quindi testare il filtro.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-135">Save your changes and then test the filter.</span></span>

![Vista Index con la parola ghost digitata nella casella di testo del filtro del titolo](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="1a4f3-137">Non è presente alcun overload del metodo `[HttpPost]` `Index` come previsto.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="1a4f3-138">Non è necessario perché il metodo non modifica lo stato dell'app, ma filtra solo i dati.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="1a4f3-139">È possibile aggiungere il metodo `[HttpPost] Index` seguente.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="1a4f3-140">Il parametro `notUsed` viene usato per creare un overload per il metodo `Index`.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="1a4f3-141">Questo aspetto verrà trattato in una fase successiva dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="1a4f3-142">Se si aggiunge questo metodo, l'invoker di azione trova la corrispondenza con il metodo `[HttpPost] Index` e il metodo `[HttpPost] Index` viene eseguito come illustrato nell'immagine seguente.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![Finestra del browser con la risposta dell'applicazione From HttpPost Index: filter on ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="1a4f3-144">Tuttavia, anche se si aggiunge questa versione `[HttpPost]` del metodo `Index`, esiste una limitazione sul modo sulla relativa implementazione.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="1a4f3-145">Si supponga che si desideri usare come segnalibro una ricerca specifica o inviare un collegamento agli amici su cui possono fare clic per visualizzare lo stesso elenco filtrato di film.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="1a4f3-146">Si noti che l'URL per la richiesta HTTP POST è lo stesso URL per la richiesta GET (localhost:xxxxx/Movies/Index); le informazioni sulla ricerca non sono disponibili nell'URL.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:xxxxx/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="1a4f3-147">Le informazioni sulla stringa di ricerca vengono inviate al server come un [valore del campo modulo](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span><span class="sxs-lookup"><span data-stu-id="1a4f3-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="1a4f3-148">È possibile eseguire una verifica con gli strumenti di sviluppo del browser o l'eccellente [strumento Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="1a4f3-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](https://www.telerik.com/fiddler).</span></span> <span data-ttu-id="1a4f3-149">L'immagine seguente mostra gli strumenti di sviluppo del browser Chrome:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-149">The image below shows the Chrome browser Developer tools:</span></span>

![Scheda di rete degli strumenti di sviluppo in Microsoft Edge che mostra il corpo di una richiesta con un valore searchString di ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="1a4f3-151">È possibile esaminare il parametro di ricerca e il token [XSRF](xref:security/anti-request-forgery) nel corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="1a4f3-152">Si noti, come indicato nell'esercitazione precedente, che l'[helper tag del modulo](xref:mvc/views/working-with-forms) genera un token antifalsificazione [XSRF](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="1a4f3-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="1a4f3-153">Poiché non si stanno modificando i dati, non è necessario convalidare il token nel metodo del controller.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="1a4f3-154">Poiché il parametro di ricerca si trova nel corpo della richiesta e non nell'URL, non è possibile acquisire queste informazioni sulla ricerca da usare come segnalibro o condividerle con altri utenti.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="1a4f3-155">Per risolvere il problema, specificare che la richiesta deve essere `HTTP GET` nel file *Views/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-155">Fix this by specifying the request should be `HTTP GET` found in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="1a4f3-156">A questo punto quando si invia una ricerca, l'URL contiene la stringa di query della ricerca.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="1a4f3-157">La ricerca passerà anche al metodo di azione `HttpGet Index`, anche se si dispone di un metodo `HttpPost Index`.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![Finestra del browser che mostra searchString=ghost nell'URL e i film restituiti, Ghostbusters e Ghostbusters 2, contengono la parola ghost](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="1a4f3-159">Il markup seguente mostra la modifica al tag `form`:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-159">The following markup shows the change to the `form` tag:</span></span>

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="add-search-by-genre"></a><span data-ttu-id="1a4f3-160">Aggiungere la funzionalità di ricerca in base al genere</span><span class="sxs-lookup"><span data-stu-id="1a4f3-160">Add Search by genre</span></span>

<span data-ttu-id="1a4f3-161">Aggiungere la classe `MovieGenreViewModel` seguente alla cartella *Models*:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="1a4f3-162">Il modello di vista movie-genre conterrà:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-162">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="1a4f3-163">Un elenco di film.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-163">A list of movies.</span></span>
* <span data-ttu-id="1a4f3-164">`SelectList` contiene l'elenco dei generi.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="1a4f3-165">Consente all'utente di selezionare un genere dall'elenco.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-165">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="1a4f3-166">`MovieGenre` che contiene il genere selezionato.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-166">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="1a4f3-167">`SearchString`, che contiene il testo immesso dagli utenti nella casella di testo di ricerca.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="1a4f3-168">Sostituire il metodo `Index` in `MoviesController.cs` con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="1a4f3-169">Il codice seguente è una query `LINQ` che recupera tutti i generi dal database.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="1a4f3-170">L'elenco `SelectList` di generi viene creato presentando generi distinti per evitare che l'elenco di selezione includa generi duplicati.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="1a4f3-171">Quando l'utente cerca l'elemento, viene mantenuto il valore di ricerca nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="1a4f3-172">Aggiungere la funzionalità di ricerca in base al genere alla vista Index</span><span class="sxs-lookup"><span data-stu-id="1a4f3-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="1a4f3-173">Aggiornare `Index.cshtml` in *Views/Movies/* come segue:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-173">Update `Index.cshtml` found in *Views/Movies/* as follows:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

<span data-ttu-id="1a4f3-174">Esaminare l'espressione lambda usata nell'helper HTML seguente:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="1a4f3-175">Nel codice precedente, l'helper HTML `DisplayNameFor` controlla la proprietà `Title` a cui si fa riferimento nell'espressione lambda per determinare il nome visualizzato.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="1a4f3-176">Poiché l'espressione lambda viene controllata e anziché essere valutata, non viene generata una violazione di accesso quando `model`, `model.Movies` o `model.Movies[0]` sono `null` o vuoti.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="1a4f3-177">Quando invece l'espressione lambda viene valutata, ad esempio `@Html.DisplayFor(modelItem => item.Title)`, vengono valutati i valori delle proprietà del modello.</span><span class="sxs-lookup"><span data-stu-id="1a4f3-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="1a4f3-178">Eseguire il test dell'app effettuando una ricerca per genere, titolo del film ed entrambi:</span><span class="sxs-lookup"><span data-stu-id="1a4f3-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![Finestra del browser con i risultati di https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="1a4f3-180">[Precedente](controller-methods-views.md)
> [Successivo](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="1a4f3-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>
