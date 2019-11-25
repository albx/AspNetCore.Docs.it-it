---
page_type: sample
description: Informazioni su come aggiungere Swashbuckle al progetto dell'API Web ASP.NET Core per integrare l'interfaccia utente di Swagger.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: d48288de90626ada83f5da1759f0057f0be46f19
ms.sourcegitcommit: f91d322f790123d41ec3271fa084ae20ed9f89a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155142"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="9c348-102">Introduzione a Swashbuckle e ad ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9c348-102">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="9c348-103">Quando si usa un'API Web, la comprensione dei vari metodi che la compongono può risultare complessa per gli sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="9c348-103">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="9c348-104">[Swagger](https://swagger.io/), detto anche [OpenAPI](https://www.openapis.org/), risolve il problema della generazione di pagine della Guida e della documentazione utili per le API Web.</span><span class="sxs-lookup"><span data-stu-id="9c348-104">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="9c348-105">Offre vantaggi quali la documentazione interattiva, la generazione di SDK client e l'individuabilità delle API.</span><span class="sxs-lookup"><span data-stu-id="9c348-105">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="9c348-106">In questo esempio viene mostrata l'implementazione .NET di [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="9c348-106">In this sample, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) the .NET implementation is shown.</span></span>

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="9c348-107">Aggiungere e configurare il middleware di Swagger</span><span class="sxs-lookup"><span data-stu-id="9c348-107">Add and configure Swagger middleware</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });
}
```

<span data-ttu-id="9c348-108">Nel metodo `Startup.Configure` abilitare il middleware per la gestione del documento JSON generato e l'interfaccia utente di Swagger:</span><span class="sxs-lookup"><span data-stu-id="9c348-108">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1"); 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="9c348-109">La chiamata del metodo `UseSwaggerUI` precedente abilita il [middleware dei file statici](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="9c348-109">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span></span> <span data-ttu-id="9c348-110">Se si usa .NET Framework o .NET Core 1.x, aggiungere il pacchetto NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) al progetto.</span><span class="sxs-lookup"><span data-stu-id="9c348-110">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="9c348-111">Avviare l'app e passare a `http://localhost:<port>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="9c348-111">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="9c348-112">Il documento generato che descrive gli endpoint viene illustrato in [Specifica Swagger (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="9c348-112">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="9c348-113">L'interfaccia utente di Swagger è disponibile in `http://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="9c348-113">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="9c348-114">Esplorare l'API tramite l'interfaccia utente di Swagger e incorporarla in altri programmi.</span><span class="sxs-lookup"><span data-stu-id="9c348-114">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="9c348-115">Per gestire l'interfaccia utente di Swagger nella radice dell'app (`http://localhost:<port>/`), impostare la proprietà `RoutePrefix` su una stringa vuota:</span><span class="sxs-lookup"><span data-stu-id="9c348-115">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

<span data-ttu-id="9c348-116">Se si usano le directory con ISS o il proxy inverso, impostare l'endpoint Swagger su un percorso relativo tramite il prefisso `./`.</span><span class="sxs-lookup"><span data-stu-id="9c348-116">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="9c348-117">Ad esempio `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="9c348-117">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="9c348-118">L'uso di `/swagger/v1/swagger.json` indica all'app di cercare il file JSON nella vera radice dell'URL (con il prefisso della route, se usato).</span><span class="sxs-lookup"><span data-stu-id="9c348-118">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="9c348-119">Ad esempio, usare `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` invece di `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="9c348-119">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="9c348-120">Personalizzare ed estendere</span><span class="sxs-lookup"><span data-stu-id="9c348-120">Customize and extend</span></span>

<span data-ttu-id="9c348-121">Swagger offre opzioni per documentare il modello a oggetti e personalizzare l'interfaccia utente in modo che corrisponda al tema.</span><span class="sxs-lookup"><span data-stu-id="9c348-121">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="9c348-122">Nella classe `Startup` aggiungere gli spazi dei nomi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9c348-122">In the `Startup` class, add the following namespaces:</span></span>
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="9c348-123">Informazioni e descrizione API</span><span class="sxs-lookup"><span data-stu-id="9c348-123">API info and description</span></span>

<span data-ttu-id="9c348-124">L'azione di configurazione passata al metodo `AddSwaggerGen` aggiunge informazioni come ad esempio autore, licenza e descrizione:</span><span class="sxs-lookup"><span data-stu-id="9c348-124">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

```csharp
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

<span data-ttu-id="9c348-125">L'interfaccia utente di Swagger visualizza le informazioni sulla versione:</span><span class="sxs-lookup"><span data-stu-id="9c348-125">The Swagger UI displays the version's information:</span></span>

![Interfaccia utente di Swagger con informazioni sulla versione: descrizione, autore e altri collegamenti](sample_images/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="9c348-127">XML (commenti)</span><span class="sxs-lookup"><span data-stu-id="9c348-127">XML comments</span></span>

<span data-ttu-id="9c348-128">I commenti XML possono essere abilitati con gli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="9c348-128">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9c348-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c348-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9c348-130">Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Modifica <nome_progetto>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="9c348-130">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="9c348-131">Aggiungere manualmente le righe evidenziate al file con estensione *csproj*:</span><span class="sxs-lookup"><span data-stu-id="9c348-131">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9c348-132">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="9c348-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9c348-133">Dal *riquadro della soluzione* premere **controllo** e fare clic sul nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="9c348-133">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="9c348-134">Passare a **Strumenti** > **Modifica file**.</span><span class="sxs-lookup"><span data-stu-id="9c348-134">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="9c348-135">Aggiungere manualmente le righe evidenziate al file con estensione *csproj*:</span><span class="sxs-lookup"><span data-stu-id="9c348-135">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9c348-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9c348-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9c348-137">Aggiungere manualmente le righe evidenziate al file con estensione *csproj*:</span><span class="sxs-lookup"><span data-stu-id="9c348-137">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

<span data-ttu-id="9c348-138">Abilitando i commenti XML, viene eseguito il debug delle informazioni per membri e tipi pubblici non documentati.</span><span class="sxs-lookup"><span data-stu-id="9c348-138">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="9c348-139">I membri e tipi non documentati sono indicati nel messaggio di avviso.</span><span class="sxs-lookup"><span data-stu-id="9c348-139">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="9c348-140">Ad esempio, il messaggio seguente indica una violazione del codice di avviso 1591:</span><span class="sxs-lookup"><span data-stu-id="9c348-140">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="9c348-141">Per eliminare gli avvisi per l'intero progetto, definire un elenco delimitato da punto e virgola di codici di avviso da ignorare nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="9c348-141">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="9c348-142">L'aggiunta di codici di avviso a `$(NoWarn);` applica anche i [valori predefiniti di C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).</span><span class="sxs-lookup"><span data-stu-id="9c348-142">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

<span data-ttu-id="9c348-143">Per eliminare gli avvisi solo per membri specifici, racchiudere il codice in direttive del preprocessore [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning).</span><span class="sxs-lookup"><span data-stu-id="9c348-143">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="9c348-144">Questo approccio è utile per il codice che non deve essere esposto tramite la documentazione API. Nell'esempio seguente il codice di avviso CS1591 viene ignorato per l'intera classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="9c348-144">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="9c348-145">L'imposizione del codice di avviso viene ripristinata alla chiusura della definizione della classe.</span><span class="sxs-lookup"><span data-stu-id="9c348-145">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="9c348-146">Specificare più codici di avviso con un elenco delimitato da virgole.</span><span class="sxs-lookup"><span data-stu-id="9c348-146">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="9c348-147">Configurare Swagger per usare il file XML che viene generato con le istruzioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="9c348-147">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="9c348-148">Per Linux o sistemi operativi diversi da Windows, i percorsi e i nomi di file possono fare distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="9c348-148">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="9c348-149">Ad esempio, un file *ToDoApi.XML* è valido in Windows, ma non in CentOS.</span><span class="sxs-lookup"><span data-stu-id="9c348-149">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

```csharp
/// NOTE LAST 3 LINES IN THIS SNIPPET
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "ToDo API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = new Uri("https://example.com/terms"),
            Contact = new OpenApiContact
            {
                Name = "Shayne Boyer",
                Email = string.Empty,
                Url = new Uri("https://twitter.com/spboyer"),
            },
            License = new OpenApiLicense
            {
                Name = "Use under LICX",
                Url = new Uri("https://example.com/license"),
            }
        });

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
}
```

<span data-ttu-id="9c348-150">Nel codice precedente la [reflection](/dotnet/csharp/programming-guide/concepts/reflection) consente di compilare un nome file XML che corrisponde a quello del progetto API Web.</span><span class="sxs-lookup"><span data-stu-id="9c348-150">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="9c348-151">La proprietà [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) viene usata per costruire un percorso del file XML.</span><span class="sxs-lookup"><span data-stu-id="9c348-151">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="9c348-152">Alcune funzionalità di Swagger (ad esempio, schemi di parametri di input o i metodi HTTP e i codici di risposta dai rispettivi attributi) funzionano senza l'uso di un file di documentazione XML.</span><span class="sxs-lookup"><span data-stu-id="9c348-152">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="9c348-153">Per la maggior parte delle funzionalità, vale a dire i riepiloghi dei metodi e le descrizioni dei parametri e dei codici di risposta, l'uso di un file XML è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="9c348-153">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="9c348-154">L'aggiunta a un'azione di commenti con tripla barra migliora l'interfaccia utente di Swagger poiché viene aggiunta la descrizione all'intestazione della sezione.</span><span class="sxs-lookup"><span data-stu-id="9c348-154">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="9c348-155">Aggiungere un elemento [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) sopra l'azione `Delete`:</span><span class="sxs-lookup"><span data-stu-id="9c348-155">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
/// <param name="id"></param>        
[HttpDelete("{id}")]
public IActionResult Delete(long id)
{
    var todo = _context.TodoItems.Find(id);

    if (todo == null)
    {
        return NotFound();
    }

    _context.TodoItems.Remove(todo);
    _context.SaveChanges();

    return NoContent();
}
```
<span data-ttu-id="9c348-156">L'interfaccia utente di Swagger visualizza il testo interno dell'elemento `<summary>` del codice precedente:</span><span class="sxs-lookup"><span data-stu-id="9c348-156">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Interfaccia utente di Swagger che visualizza il commento XML "Deletes a specific TodoItem."](sample_images/triple-slash-comments.png)

<span data-ttu-id="9c348-159">L'interfaccia utente è determinata dallo schema JSON generato:</span><span class="sxs-lookup"><span data-stu-id="9c348-159">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```
<span data-ttu-id="9c348-160">Aggiungere un elemento [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) alla documentazione del metodo di azione `Create`.</span><span class="sxs-lookup"><span data-stu-id="9c348-160">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="9c348-161">In questo modo vengono integrate le informazioni specificate nell'elemento `<summary>` e l'interfaccia utente di Swagger risulta più affidabile.</span><span class="sxs-lookup"><span data-stu-id="9c348-161">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="9c348-162">Il contenuto dell'elemento `<remarks>` può essere costituito da testo, JSON o XML.</span><span class="sxs-lookup"><span data-stu-id="9c348-162">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
<span data-ttu-id="9c348-163">Si notino i miglioramenti dell'interfaccia utente con questi commenti aggiuntivi:</span><span class="sxs-lookup"><span data-stu-id="9c348-163">Notice the UI enhancements with these additional comments:</span></span>

![Interfaccia utente di Swagger con commenti aggiuntivi visualizzati](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="9c348-165">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="9c348-165">Data annotations</span></span>

<span data-ttu-id="9c348-166">Decorare il modello con gli attributi inclusi nello spazio dei nomi [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) per gestire i componenti dell'interfaccia utente di Swagger.</span><span class="sxs-lookup"><span data-stu-id="9c348-166">Decorate the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="9c348-167">Aggiungere l'attributo `[Required]` alla proprietà `Name` della classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="9c348-167">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace TodoApi.Models
{
    public class TodoItem
    {
        public long Id { get; set; }

        [Required]
        public string Name { get; set; }

        [DefaultValue(false)]
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="9c348-168">La presenza di questo attributo modifica il comportamento dell'interfaccia utente e lo schema JSON sottostante:</span><span class="sxs-lookup"><span data-stu-id="9c348-168">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="9c348-169">Aggiungere l'attributo `[Produces("application/json")]` al controller API.</span><span class="sxs-lookup"><span data-stu-id="9c348-169">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="9c348-170">Il suo scopo consiste nel dichiarare che le azioni del controller supportano un tipo di contenuto della risposta di *application/json*:</span><span class="sxs-lookup"><span data-stu-id="9c348-170">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
<span data-ttu-id="9c348-171">L'elenco a discesa **Response Content Type** (Tipo di contenuto risposta) include questo tipo di contenuto come selezione predefinita per le azioni GET del controller:</span><span class="sxs-lookup"><span data-stu-id="9c348-171">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Interfaccia utente di Swagger con tipo di contenuto di risposta predefinito](sample_images/json-response-content-type.png)

<span data-ttu-id="9c348-173">Con l'aumento dell'uso delle annotazioni dei dati nell'API Web, le pagine della Guida dell'API e dell'interfaccia utente diventano più descrittive e utili.</span><span class="sxs-lookup"><span data-stu-id="9c348-173">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="9c348-174">Descrivere i tipi di risposta</span><span class="sxs-lookup"><span data-stu-id="9c348-174">Describe response types</span></span>

<span data-ttu-id="9c348-175">La principale preoccupazione degli sviluppatori che utilizzano un'API Web è ciò che viene restituito, in particolare i tipi di risposta e i codici di errore, se diversi da quelli standard.</span><span class="sxs-lookup"><span data-stu-id="9c348-175">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="9c348-176">I tipi di risposta e i codici di errore vengono indicati nei commenti XML e nelle annotazioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="9c348-176">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="9c348-177">L'azione `Create` restituisce un codice di stato HTTP 201 in caso di esito positivo.</span><span class="sxs-lookup"><span data-stu-id="9c348-177">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="9c348-178">Quando il corpo della richiesta inviata è Null, viene restituito un codice di stato HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="9c348-178">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="9c348-179">Senza la documentazione appropriata nell'interfaccia utente di Swagger, il consumer non riconosce tali risultati previsti.</span><span class="sxs-lookup"><span data-stu-id="9c348-179">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="9c348-180">Risolvere il problema aggiungendo le righe evidenziate nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="9c348-180">Fix that problem by adding the highlighted lines in the following example:</span></span>

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

<span data-ttu-id="9c348-181">L'interfaccia utente di Swagger ora documenta chiaramente i codici di risposta HTTP previsti:</span><span class="sxs-lookup"><span data-stu-id="9c348-181">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Interfaccia utente di Swagger che visualizza la descrizione della classe risposta POST "Returns the newly created Todo item" e "400 - If the item is null" per il codice di stato e il motivo nei messaggi di risposta](sample_images/data-annotations-response-types.png)

<span data-ttu-id="9c348-183">In ASP.NET Core 2.2 o versioni successive, possono essere usate convenzioni in alternativa alla decorazione esplicita di azioni singole con `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="9c348-183">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="9c348-184">Per altre informazioni, vedere [Usare le convenzioni dell'API Web](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="9c348-184">For more information, see [Use web API conventions](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span></span>

<span data-ttu-id="9c348-185">Per informazioni sulla personalizzazione dell'interfaccia utente, vedere [personalizzare l'interfaccia utente](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span><span class="sxs-lookup"><span data-stu-id="9c348-185">For information on customizing the UI see: [Customize the UI](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span></span>
