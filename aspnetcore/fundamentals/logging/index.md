---
title: Registrazione in .NET Core e ASP.NET Core
author: tdykstra
description: Informazioni su come usare il framework di registrazione fornito dal pacchetto NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: fundamentals/logging/index
ms.openlocfilehash: bb38ebca3c7b9bb4c28a52c0dad80be9669e1b40
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71924882"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Registrazione in .NET Core e ASP.NET Core

Di [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)

.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti. Questo articolo illustra come usare l'API di registrazione con i provider predefiniti.

::: moniker range=">= aspnetcore-3.0"

La maggior parte degli esempi di codice illustrati in questo articolo proviene da app ASP.NET Core. Le parti relative alla registrazione di questi frammenti di codice si applicano alle app .NET Core che usano l'[host generico](xref:fundamentals/host/generic-host). Per informazioni su come usare l'host generico nelle app console non Web, vedere [Servizi ospitati](xref:fundamentals/host/hosted-services).

Il codice di registrazione per le app senza host generico differisce per il modo in cui vengono [aggiunti i provider](#add-providers) e [creati i logger](#create-logs). Gli esempi di codice non host sono illustrati nelle sezioni dell'articolo in cui sono riportate queste procedure.

::: moniker-end

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Aggiungere provider

Un provider di registrazione visualizza o archivia i log. Il provider Console, ad esempio, visualizza i log nella console, mentre il provider Azure Application Insights li archivia in Azure Application Insights. I log possono essere inviati a più destinazioni aggiungendo più provider.

::: moniker range=">= aspnetcore-3.0"

Per aggiungere un provider in un'app che usa l'host generico, chiamare il metodo di estensione `Add{provider name}` del provider in *Program.cs*:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

In un'app console non host chiamare il metodo di estensione `Add{provider name}` del provider durante la creazione di un oggetto `LoggerFactory`:

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory` e `AddConsole` richiedono un'istruzione `using` per `Microsoft.Extensions.Logging`.

I modelli di progetto ASP.NET Core predefiniti chiamano <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, che aggiunge i provider di registrazione seguenti:

* Console
* Debug
* EventSource
* EventLog (solo quando è in esecuzione su Windows)

È possibile sostituire i provider predefiniti con quelli di propria scelta. Chiamare <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e aggiungere i provider desiderati.

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

Per aggiungere un provider, chiamare il metodo di estensione `Add{provider name}` del provider in *Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Il codice precedente richiede riferimenti a `Microsoft.Extensions.Logging` e `Microsoft.Extensions.Configuration`.

Il modello di progetto predefinito chiama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, che aggiunge i provider di registrazione seguenti:

* Console
* Debug
* EventSource (a partire da ASP.NET Core 2.2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Se si usa `CreateDefaultBuilder`, è possibile sostituire i provider predefiniti con quelli di propria scelta. Chiamare <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e aggiungere i provider desiderati.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

Altre informazioni sui [provider di registrazione predefiniti](#built-in-logging-providers) e sui [provider di registrazione di terze parti](#third-party-logging-providers) vengono fornite più avanti in questo articolo.

## <a name="create-logs"></a>Creare log

Per creare i log, usare un oggetto <xref:Microsoft.Extensions.Logging.ILogger%601>. In un'app Web o in un servizio ospitato, ottenere un oggetto `ILogger` da un inserimento delle dipendenze. Nelle app console non host, usare `LoggerFactory` per creare un oggetto `ILogger`.

L'esempio seguente di ASP.NET Core crea un logger con `TodoApiSample.Pages.AboutModel` come categoria. La *categoria* del log è una stringa associata a ogni log. L'istanza di `ILogger<T>` fornita dall'inserimento delle dipendenze crea log con nome completo di tipo `T` come categoria. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

L'esempio seguente di app console non host crea un logger con `LoggingConsoleApp.Program` come categoria.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

Negli esempi seguenti di ASP.NET Core e app console, il logger viene usato per creare log con `Information` come livello. Il *livello* del log indica la gravità dell'evento registrato. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

[Livelli](#log-level) e [categorie](#log-category) vengono illustrati in modo dettagliato più avanti in questo articolo. 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a>Creare log nella classe Program

Per scrivere log nella classe `Program` di un'app ASP.NET Core, ottenere un'istanza di `ILogger` dall'inserimento delle dipendenze dopo aver creato l'host:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

### <a name="create-logs-in-the-startup-class"></a>Creare log nella classe Startup

Per scrivere log nel metodo `Startup.Configure` di un'app ASP.NET Core, includere un parametro `ILogger` nella firma del metodo:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

La scrittura di log prima del completamento della configurazione del contenitore di inserimento delle dipendenze nel metodo `Startup.ConfigureServices` non è supportata:

* L'inserimento del logger nel costruttore `Startup` non è supportato.
* L'inserimento del logger nella firma del metodo `Startup.ConfigureServices` non è supportato

Questa restrizione è dovuta al fatto che la registrazione dipende dall'inserimento delle dipendenze e dalla configurazione, che a sua volta dipende dall'inserimento delle dipendenze. Il contenitore di inserimento delle dipendenze non viene configurato finché il metodo `ConfigureServices` non è completato.

L'inserimento di un logger nel costruttore `Startup` è possibile nelle versioni precedenti di ASP.NET Core perché viene creato un contenitore di inserimento delle dipendenze separato per l'host Web. Per informazioni sul motivo per cui viene creato un solo contenitore per l'host generico, vedere l'[annuncio relativo alle modifiche di rilievo](https://github.com/aspnet/Announcements/issues/353).

Se è necessario configurare un servizio che dipende da `ILogger<T>`, è comunque possibile eseguire questa operazione usando l'inserimento nel costruttore o specificando un metodo factory. L'approccio con il metodo factory è consigliato solo se non sono disponibili altre opzioni. Si supponga, ad esempio, di dover specificare una proprietà con un servizio ottenuto dall'inserimento delle dipendenze:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

Il precedente codice evidenziato è un oggetto `Func` che viene eseguito la prima volta che il contenitore di inserimento delle dipendenze deve creare un'istanza di `MyService`. È possibile accedere a uno dei servizi registrati in questo modo.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a>Creare log nella classe Startup

Per scrivere log nella classe `Startup`, includere un parametro `ILogger` nella firma del costruttore:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Creare log nella classe Program

Per scrivere log nella classe `Program`, ottenere un'istanza di `ILogger` dall'inserimento delle dipendenze:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a>Evitare l'uso di metodi logger asincroni

La registrazione deve essere così rapida da non giustificare l'impatto sulle prestazioni del codice asincrono. Se l'archivio dati di registrazione è lento, non scrivere direttamente al suo interno. Scrivere invece i messaggi di log prima in un archivio veloce e quindi spostarli nell'archivio lento in un secondo momento. Ad esempio, se la registrazione viene eseguita in SQL Server, è preferibile non farlo direttamente in un metodo `Log`, poiché i metodi `Log` sono sincroni. Al contrario, aggiungere i messaggi di log in modo sincrono a una coda in memoria e usare un ruolo di lavoro in background per eseguire il pull dei messaggi dalla coda per eseguire le operazioni asincrone di push dei dati in SQL Server.

## <a name="configuration"></a>Configurazione

La configurazione dei provider di registrazione viene fornita da uno o più provider di configurazione:

* Formati di file (INI, JSON e XML).
* Argomenti della riga di comando.
* Variabili di ambiente.
* Oggetti .NET in memoria.
* Archiviazione con [Secret Manager](xref:security/app-secrets) senza crittografia.
* Un archivio utente non crittografato, come [Azure Key Vault](xref:security/key-vault-configuration).
* Provider personalizzati (installati o creati).

Ad esempio, la configurazione di registrazione viene comunemente fornita dalla sezione `Logging` del file di impostazioni dell'app. L'esempio seguente mostra il contenuto di un file *appsettings.Development.json* tipico:

::: moniker range=">= aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

La proprietà `Logging` può avere le proprietà `LogLevel` e quella del provider di log (nell'esempio, Console).

La proprietà `LogLevel` in `Logging` specifica il [livello](#log-level) minimo per la registrazione per determinate categorie. Nell'esempio, le categorie `System` e `Microsoft` eseguono la registrazione al livello `Information`, mentre tutte le altre la eseguono al livello `Debug`.

Altre proprietà in `Logging` specificano i provider di registrazione. L'esempio si riferisce al provider Console. Se un provider supporta gli [ambiti di log](#log-scopes), `IncludeScopes` indica se tali ambiti sono abilitati. Una proprietà del provider (ad esempio `Console` nell'esempio) può specificare anche una proprietà `LogLevel`. `LogLevel` in un provider specifica i livelli di registrazione per tale provider.

Se i livelli sono specificati in `Logging.{providername}.LogLevel`, eseguono l'override di eventuali valori impostati in `Logging.LogLevel`.

::: moniker-end

Per informazioni sull'implementazione dei provider di configurazione, vedere <xref:fundamentals/configuration/index>.

## <a name="sample-logging-output"></a>Esempio di output di registrazione

Con il codice di esempio illustrato nella sezione precedente i log vengono visualizzati nella console quando l'app viene eseguita dalla riga di comando. Ecco un esempio di output della console:

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

::: moniker-end

I log precedenti sono stati generati inviando una richiesta HTTP Get all'app di esempio all'indirizzo `http://localhost:5000/api/todo/0`.

Ecco un esempio degli stessi log visualizzati nella finestra Debug quando si esegue l'app di esempio in Visual Studio:

::: moniker range=">= aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

I log creati dalle chiamate a `ILogger` illustrate nella sezione precedente iniziano con "TodoApiSample". I log che iniziano con le categorie "Microsoft" provengono dal codice del framework ASP.NET Core. ASP.NET Core e il codice dell'applicazione usano la stessa API di registrazione e gli stessi provider.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

I log creati dalle chiamate a `ILogger` illustrate nella sezione precedente iniziano con "TodoApi". I log che iniziano con le categorie "Microsoft" provengono dal codice del framework ASP.NET Core. ASP.NET Core e il codice dell'applicazione usano la stessa API di registrazione e gli stessi provider.

::: moniker-end

Il resto di questo articolo spiega alcuni dettagli e le opzioni per la registrazione.

## <a name="nuget-packages"></a>Pacchetti NuGet

Le interfacce `ILogger` e `ILoggerFactory` si trovano in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e le loro implementazioni predefinite si trovano in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Categoria dei log

Quando viene creato un oggetto `ILogger`, viene specificata la relativa *categoria*. La categoria è inclusa in ogni messaggio di log creato da tale istanza di `ILogger`. La categoria può essere qualsiasi stringa, ma per convenzione si usa il nome della classe, ad esempio "TodoApi.Controllers.TodoController".

Usare `ILogger<T>` per ottenere un'istanza di `ILogger` che usa il nome completo di tipo `T` come categoria:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

Per specificare in modo esplicito la categoria, chiamare `ILoggerFactory.CreateLogger`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

L'uso di `ILogger<T>` equivale a chiamare `CreateLogger` con il nome completo di tipo `T`.

## <a name="log-level"></a>Livello di registrazione

Ogni log specifica un valore <xref:Microsoft.Extensions.Logging.LogLevel>. Il livello di registrazione indica la gravità o l'importanza. È ad esempio possibile scrivere un log `Information` quando un metodo termina normalmente e un log `Warning` quando un metodo restituisce un codice di stato *404 Non trovato*.

Il codice seguente crea i log `Information` e `Warning`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

Nel codice precedente il primo parametro è l'[ID dell'evento di log](#log-event-id). Il secondo parametro è un modello di messaggio con segnaposto per i valori degli argomenti forniti dai parametri dei metodi rimanenti. I parametri dei metodi sono descritti nella [sezione relativa al modello di messaggio](#log-message-template) più avanti in questo articolo.

I metodi di registrazione che includono il livello nel nome del metodo (ad esempio `LogInformation` e `LogWarning`) sono [metodi di estensione per ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Questi metodi chiamano un metodo `Log` che accetta un parametro `LogLevel`. È possibile chiamare il metodo `Log` direttamente anziché chiamare uno di questi metodi di estensione, ma la sintassi è relativamente complessa. Per altre informazioni, vedere <xref:Microsoft.Extensions.Logging.ILogger> e il [codice sorgente delle estensioni del logger](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core definisce i livelli di registrazione seguenti, ordinati dal meno grave al più grave.

* Trace = 0

  Per informazioni in genere utili solo per il debug. Questi messaggi possono contenere dati sensibili dell'applicazione e pertanto non devono essere abilitati in un ambiente di produzione. *Disattivato per impostazione predefinita.*

* Debug = 1

  Per informazioni che possono essere utili per lo sviluppo e il debug. Esempio: `Entering method Configure with flag set to true.` Abilitare i livelli di registrazione `Debug` nell'ambiente di produzione solo in fase di risoluzione dei problemi, per non fare aumentare eccessivamente il volume dei log.

* Information = 2

  Per tenere traccia del flusso generale dell'app. Queste registrazioni hanno in genere un valore a lungo termine. Esempio: `Request received for path /api/todo`

* Warning = 3

  Per gli eventi imprevisti o anomali nel flusso dell'app. Tali eventi potrebbero includere errori o altre condizioni che non provocano l'arresto dell'app, ma che potrebbe essere necessario analizzare. Le eccezioni gestite sono una situazione comune in cui usare il livello di registrazione `Warning`. Esempio: `FileNotFoundException for file quotes.txt.`

* Error = 4

  Per errori ed eccezioni che non possono essere gestiti. Questi messaggi indicano un errore nell'operazione o nell'attività in corso (ad esempio la richiesta HTTP corrente), non un errore a livello di app. Messaggio di registrazione di esempio:`Cannot insert record due to duplicate key violation.`

* Critical = 5

  Per gli errori che richiedono attenzione immediata. Esempi: scenari di perdita di dati, spazio su disco insufficiente.

Usare il livello di registrazione per controllare la quantità di output di log scritto in un supporto di archiviazione specifico o in una finestra. Esempio:

* Nell'ambiente di produzione, inviare i messaggi con livello da `Trace` a `Information` a un archivio dati per grandi volumi. Inviare i messaggi con livello da `Warning` a `Critical` a un archivio dati di valore.
* Durante lo sviluppo, inviare i messaggi con livello da `Warning` a `Critical` alla console e aggiungere il livello da `Trace` a `Information` quando si svolgono attività di risoluzione dei problemi.

La sezione [Filtro dei log](#log-filtering) più avanti in questo articolo descrive come controllare quali livelli di registrazione gestisce un provider.

ASP.NET Core scrive log per gli eventi del framework. Gli esempi di log illustrati in precedenza in questo articolo escludono i log al di sotto del livello `Information`, quindi non vengono creati log di livello `Debug` o `Trace`. Ecco un esempio di log della console prodotti eseguendo l'app di esempio configurata in modo da mostrare i log di livello `Debug`:

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

::: moniker-end

## <a name="log-event-id"></a>ID evento di registrazione

Ogni log può specificare un *ID evento*. A tale scopo, l'app di esempio usa una classe `LoggingEvents` definita a livello locale:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

Un ID evento associa un set di eventi. Ad esempio, tutti i log correlati alla visualizzazione di un elenco di elementi in una pagina potrebbero essere indicati con 1001.

Il provider di log può archiviare l'ID evento in un campo ID o nel messaggio di registrazione oppure non archiviarlo. Il provider Debug non visualizza gli ID evento. Il provider Console visualizza gli ID evento tra parentesi quadre dopo la categoria:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Modello di messaggio di registrazione

Ogni log specifica un modello di messaggio. Il modello di messaggio può contenere segnaposto per i quali vengono forniti argomenti. Usare nomi per i segnaposto, non numeri.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

L'ordine dei segnaposto, non il loro nome, determina i parametri da usare per fornire i valori corrispondenti. Nel codice seguente, si noti che i nomi dei parametri non sono in sequenza nel modello di messaggio:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Questo codice crea un messaggio di log con i valori dei parametri in sequenza:

```text
Parameter values: parm1, parm2
```

Il framework di registrazione funziona in questo modo per consentire ai provider di registrazione di implementare la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Al sistema di registrazione vengono passati gli argomenti e non solo il modello di messaggio formattato. Queste informazioni consentono ai provider di registrazione di archiviare i valori dei parametri come campi. Si supponga, ad esempio, che il metodo logger esegua chiamate simili alla seguente:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Se si inviano i log all'archiviazione tabelle di Azure, ogni entità tabella di Azure può avere le proprietà `ID` e `RequestTime`, il che semplifica le query sui dati dei log. Una query può trovare tutti i log entro un determinato intervallo `RequestTime` senza che sia necessario analizzare il messaggio di testo per determinare l'intervallo di tempo.

## <a name="logging-exceptions"></a>Registrazione delle eccezioni

I metodi logger dispongono di overload che consentono di passare un'eccezione, come nell'esempio seguente:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

Provider diversi gestiscono le informazioni sulle eccezioni in modi diversi. Ecco un esempio di output del provider Debug dal codice sopra riportato.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtro dei log

È possibile specificare un livello di registrazione minimo per un provider e una categoria specifici oppure per tutti i provider o tutte le categorie. Tutti i log sotto il livello minimo non sono passati al provider, quindi non vengono visualizzati o archiviati.

Per eliminare tutti i log, specificare `LogLevel.None` come livello di registrazione minimo. Il valore intero di `LogLevel.None` è 6, maggiore di `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Creare regole di filtro nella configurazione

Il codice del modello di progetto chiama `CreateDefaultBuilder` per configurare la registrazione per i provider Console e Debug. Il metodo `CreateDefaultBuilder` imposta inoltre la registrazione per la ricerca della configurazione in una sezione `Logging`, come illustrato [in precedenza in questo articolo](#configuration).

I dati di configurazione specificano i livelli di registrazione minimi in base al provider e alla categoria, come nell'esempio seguente:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

Questo codice JSON crea sei regole di filtro, una per il provider Debug, quattro per il provider Console e una per tutti i provider. Quando viene creato un oggetto `ILogger`, viene scelta una singola regola per ogni provider.

### <a name="filter-rules-in-code"></a>Regole di filtro nel codice

L'esempio seguente illustra come registrare le regole di filtro nel codice:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

Il secondo `AddFilter` specifica il provider Debug tramite il nome del tipo. Il primo `AddFilter` si applica a tutti i provider in quanto non consente di specificare un tipo di provider.

### <a name="how-filtering-rules-are-applied"></a>Applicazione delle regole di filtro

I dati di configurazione e il codice `AddFilter` illustrato negli esempi precedenti creano le regole indicate nella tabella seguente. I primi sei provengono dall'esempio di configurazione e gli ultimi due dall'esempio di codice.

| NUMBER | Provider      | Categorie che iniziano con...          | Livello di registrazione minimo |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Debug         | Tutte le categorie                          | Informazioni       |
| 2      | Console       | Microsoft.AspNetCore.Mvc.Razor.Internal | Avviso           |
| 3      | Console       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Debug             |
| 4      | Console       | Microsoft.AspNetCore.Mvc.Razor          | Errore             |
| 5      | Console       | Tutte le categorie                          | Informazioni       |
| 6      | Tutti i provider | Tutte le categorie                          | Debug             |
| 7      | Tutti i provider | System                                  | Debug             |
| 8      | Debug         | Microsoft                               | Trace             |

Quando viene creato un oggetto `ILogger`, l'oggetto `ILoggerFactory` seleziona una singola regola per ogni provider da applicare al logger. Tutti i messaggi scritti da un'istanza di `ILogger` vengono filtrati in base alle regole selezionate. Tra le regole disponibili viene selezionata la regola più specifica possibile per ogni coppia di categoria e provider.

L'algoritmo seguente viene usato per ogni provider quando viene creato un `ILogger` per una determinata categoria:

* Selezionare tutte le regole corrispondenti al provider o al relativo alias. Se non viene trovata alcuna corrispondenza, selezionare tutte le regole con un provider vuoto.
* Dal risultato del passaggio precedente, selezionare le regole con il prefisso di categoria corrispondente più lungo. Se non viene trovata alcuna corrispondenza, selezionare tutte le regole che non specificano una categoria.
* Se sono selezionate più regole, scegliere l'**ultima**.
* Se non è selezionata alcuna regola, usare `MinimumLevel`.

Con l'elenco di regole precedente, si supponga di creare un oggetto `ILogger` per la categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":

* Per il provider Debug si applicano le regole 1, 6 e 8. La regola 8 è più specifica, così sarà quella selezionata.
* Per il provider Console si applicano le regole 3, 4, 5 e 6. La regola 3 è la più specifica.

L'istanza di `ILogger` risultante invia i log di livello `Trace` o superiore al provider Debug. I log di livello `Debug` o superiore vengono inviati al provider Console.

### <a name="provider-aliases"></a>Alias dei provider

Ogni provider definisce un *alias* che può essere utilizzato nella configurazione al posto del nome completo di tipo.  Per i provider predefiniti, usare gli alias seguenti:

* Console
* Debug
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Livello minimo predefinito

Esiste un'impostazione del livello minimo che diventa effettiva solo se non si applica alcuna regola di configurazione o codice per un provider e una categoria specifici. L'esempio seguente illustra come impostare il livello minimo:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

Se non si imposta in modo esplicito il livello minimo, il valore predefinito è `Information`, che indica che i log `Trace` e `Debug` vengono ignorati.

### <a name="filter-functions"></a>Funzioni di filtro

Una funzione di filtro viene richiamata per tutti i provider e le categorie a cui non sono assegnate regole tramite la configurazione o il codice. Il codice della funzione ha accesso al tipo di provider, alla categoria e al livello di registrazione. Esempio:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a>Livelli e categorie di sistema

Di seguito sono elencate alcune categorie usate da ASP.NET Core ed Entity Framework Core, con note sui log previsti per ognuna:

| Category                            | Note |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Diagnostica generale di ASP.NET Core. |
| Microsoft.AspNetCore.DataProtection | Chiavi considerate, trovate e usate. |
| Microsoft.AspNetCore.HostFiltering  | Host consentiti. |
| Microsoft.AspNetCore.Hosting        | Tempo impiegato per il completamento delle richieste HTTP e ora di inizio delle richieste. Assembly di avvio dell'hosting caricati. |
| Microsoft.AspNetCore.Mvc            | Diagnostica di MVC e Razor. Associazione di modelli, esecuzione di filtri, compilazione di viste, selezione di azioni. |
| Microsoft.AspNetCore.Routing        | Informazioni sulla corrispondenza di route. |
| Microsoft.AspNetCore.Server         | Risposte di avvio, arresto e keep-alive per la connessione. Informazioni sui certificati HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | File forniti. |
| Microsoft.EntityFrameworkCore       | Diagnostica generale di Entity Framework Core. Attività e configurazione del database, rilevamento delle modifiche, migrazioni. |

## <a name="log-scopes"></a>Ambiti dei log

 Un *ambito* può raggruppare un set di operazioni logiche. Questo raggruppamento può essere usato per collegare gli stessi dati a ogni log creato come parte di un set. Ad esempio, ogni log creato come parte dell'elaborazione di una transazione può includere l'ID transazione.

Un ambito è un tipo `IDisposable` restituito dal metodo <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e viene mantenuto fino all'eliminazione. Un ambito si usa mediante il wrapping delle chiamate del logger in un blocco `using`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

Il codice seguente abilita gli ambiti per il provider Console:

*Program.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> La configurazione dell'opzione del logger della console `IncludeScopes` è necessaria per abilitare la registrazione basata sull'ambito.
>
> Per informazioni sulla configurazione, vedere la sezione [Configurazione](#configuration).

Ogni messaggio di registrazione include le informazioni sull'ambito:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Provider di registrazione predefiniti

ASP.NET Core include i provider seguenti:

* [Console](#console-provider)
* [Debug](#debug-provider)
* [EventSource](#eventsource-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Per informazioni su StdOut e la registrazione debug con il modulo ASP.NET Core, vedere <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console-provider"></a>Provider Console

Il pacchetto del provider [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) invia l'output della registrazione alla console. 

```csharp
logging.AddConsole();
```

Per visualizzare l'output di registrazione del provider Console, aprire un prompt dei comandi nella cartella del progetto ed eseguire il comando seguente:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Provider Debug

Il pacchetto del provider [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) scrive l'output della registrazione usando la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chiamate del metodo `Debug.WriteLine`).

In Linux, questo provider scrive i log in */var/log/message*.

```csharp
logging.AddDebug();
```

### <a name="eventsource-provider"></a>Provider EventSource

Per le app destinate a ASP.NET Core 1.1.0 o versione successiva, il pacchetto di provider [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) può implementare la traccia degli eventi. In Windows, usa [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803). Il provider è multipiattaforma, ma non sono ancora disponibili gli strumenti di raccolta e visualizzazione degli eventi per Linux o macOS.

```csharp
logging.AddEventSourceLogger();
```

Un buon metodo per raccogliere e visualizzare i log consiste nell'usare l'[utilità PerfView](https://github.com/Microsoft/perfview). Sono disponibili altri strumenti per la visualizzazione dei log ETW, ma PerfView fornisce un'esperienza ottimale per l'uso con gli eventi ETW generati da ASP.NET Core.

Per configurare PerfView per la raccolta degli eventi registrati da questo provider, aggiungere la stringa `*Microsoft-Extensions-Logging` nell'elenco **Provider aggiuntivi** (non dimenticare l'asterisco all'inizio della stringa).

![Provider aggiuntivi di PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Provider EventLog di Windows

Il pacchetto di provider [Microsoft.Extensions.Logging.AventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) invia l'output del log al registro eventi di Windows.

```csharp
logging.AddEventLog();
```

Gli [overload di AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) consentono di passare <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.

### <a name="tracesource-provider"></a>Provider TraceSource

Il pacchetto di provider [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa le librerie e i provider <xref:System.Diagnostics.TraceSource>.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

Gli [overload di AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) consentono di passare un cambio di origine e un listener di traccia.

Per usare questo provider, un'app deve essere in esecuzione in .NET Framework (invece che in .NET Core). Il provider può instradare i messaggi a diversi [listener](/dotnet/framework/debug-trace-profile/trace-listeners), come <xref:System.Diagnostics.TextWriterTraceListener>, usato nell'app di esempio.

### <a name="azure-app-service-provider"></a>Provider del Servizio app di Azure

Il pacchetto di provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) scrive i log in file di testo nel file system di un'app del Servizio app di Azure e nell'[archivio di BLOB](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in un account di archiviazione di Azure.

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

Il pacchetto del provider non è incluso nel framework condiviso. Per usare il provider, aggiungere il relativo pacchetto al progetto.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Il pacchetto del provider non è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Quando la destinazione è .NET Framework o il riferimento è il metapacchetto `Microsoft.AspNetCore.App`, aggiungere il pacchetto del provider al progetto. 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Per configurare le impostazioni del provider, usare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, come illustrato nell'esempio seguente:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Per configurare le impostazioni del provider, usare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, come illustrato nell'esempio seguente:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Un overload di <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> consente di passare <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. L'oggetto impostazioni può eseguire l'override delle impostazioni predefinite, ad esempio il modello di output di registrazione, il nome di BLOB e il limite di dimensioni di file. Il *modello di output* è un modello di messaggio che viene applicato a tutti i log in aggiunta a quanto specificato quando si chiama un metodo `ILogger`.

::: moniker-end

Quando si distribuisce un'app del servizio app, l'applicazione applica le impostazioni della sezione [Log del servizio app](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) della pagina **Servizio app** del portale di Azure. Quando le impostazioni seguenti vengono aggiornate, le modifiche hanno effetto immediatamente senza richiedere un riavvio o la ridistribuzione dell'app.

* **Registrazione applicazioni (file system)**
* **Registrazione applicazione (BLOB)**

Il percorso predefinito per i file di log è nella cartella *D:\\home\\LogFiles\\Application* e il nome di file predefinito è *diagnostics-aaaammgg.txt*. Il limite predefinito per le dimensioni del file è 10 MB e il numero massimo predefinito di file conservati è 2. Il nome BLOB predefinito è *{nome-app}{timestamp}/aaaa/mm/gg/hh/{guid}-applicationLog.txt*.

Il provider funziona solo quando il progetto viene eseguito nell'ambiente di Azure. Non ha alcun effetto quando il progetto viene eseguito localmente, in quanto non scrive nulla in file locali o in archivi di sviluppo locali per i BLOB.

#### <a name="azure-log-streaming"></a>Flusso di registrazione di Azure

Il flusso di registrazione di Azure consente di visualizzare l'attività di registrazione in tempo reale da:

* Server applicazioni
* Server Web
* Traccia delle richieste non riuscite

Per configurare il flusso di registrazione di Azure:

* Passare alla pagina **Log del servizio app** dalla pagina del portale dell'app.
* Impostare **Registrazione applicazioni (file system)** su **Sì**.
* Scegliere il livello di registrazione in **Livello**.

Passare alla pagina **Flusso di registrazione** per visualizzare i messaggi dell'app. I messaggi vengono registrati dall'app tramite l'interfaccia `ILogger`.

### <a name="azure-application-insights-trace-logging"></a>Registrazione di traccia di Azure Application Insights

Il pacchetto di provider [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) scrive log in Azure Application Insights. Application Insights è un servizio che monitora un'app Web e fornisce gli strumenti per l'esecuzione di query sui dati di telemetria e la loro analisi. Se si usa questo provider, è possibile eseguire query sui log e analizzarli usando gli strumenti di Application Insights.

Il provider di registrazione è incluso come dipendenza di [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), ovvero il pacchetto che fornisce tutti i dati di telemetria disponibili per ASP.NET Core. Se si usa questo pacchetto, non è necessario installare il pacchetto di provider.

Non usare il pacchetto [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web), che è per ASP.NET 4.x.

Per altre informazioni, vedere le seguenti risorse:

* [Panoramica di Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights per applicazioni ASP.NET Core](/azure/azure-monitor/app/asp-net-core): iniziare da qui se si vuole implementare l'intervallo completo dei dati di telemetria di Application Insights insieme alla registrazione.
* [ApplicationInsightsLoggerProvider per log ILogger .NET Core](/azure/azure-monitor/app/ilogger): iniziare da qui se si vuole implementare il provider di registrazione senza gli altri dati di telemetria di Application Insights.
* [Adattatori di registrazione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Installare, configurare e inizializzare Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): esercitazione interattiva nel sito Microsoft Learn.

## <a name="third-party-logging-providers"></a>Provider di registrazione di terze parti

Framework di registrazione di terze parti che usano ASP.NET Core:

* [elmah.io](https://elmah.io/) ([repository GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repository GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([repository GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([repository GitHub](https://github.com/catalingavan/KissLog-net))
* [Loggr](https://loggr.net/) ([repository GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([repository GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([repository GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([repository GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repository Github](https://github.com/googleapis/google-cloud-dotnet))

Alcuni framework di terze parti possono eseguire la [registrazione semantica, nota anche come registrazione strutturata](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

L'uso di un framework di terze parti è simile a quello di uno dei provider predefiniti:

1. Aggiungere un pacchetto NuGet al progetto.
1. Chiamare un `ILoggerFactory` metodo di estensione fornito dal framework di registrazione.

Per altre informazioni, vedere la documentazione di ogni provider. I provider di registrazione di terze parti non sono supportati da Microsoft.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/logging/loggermessage>
