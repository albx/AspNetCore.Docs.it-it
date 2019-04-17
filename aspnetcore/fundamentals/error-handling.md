---
title: Gestire gli errori in ASP.NET Core
author: tdykstra
description: Informazioni su come gestire gli errori nelle app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/07/2019
uid: fundamentals/error-handling
ms.openlocfilehash: cbb9462a3c6010e074dc391aa128ac2cbb901456
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468750"
---
# <a name="handle-errors-in-aspnet-core"></a>Gestire gli errori in ASP.NET Core

Di [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex) e [Steve Smith](https://ardalis.com/)

Questo articolo descrive gli approcci comuni per la gestione degli errori nelle app ASP.NET Core.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Come scaricare un esempio](xref:index#how-to-download-a-sample).) L'articolo include istruzioni su come impostare le direttive del preprocessore (`#if`, `#endif`, `#define`) nell'app di esempio per abilitare scenari diversi.

## <a name="developer-exception-page"></a>Pagina delle eccezioni per gli sviluppatori

In *Pagina delle eccezioni per gli sviluppatori* sono disponibili informazioni dettagliate sulle eccezioni delle richieste. La pagina è disponibile nel pacchetto [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) che si trova nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Aggiungere codice al metodo `Startup.Configure` per abilitare la pagina quando l'app è in esecuzione nell'[ambiente](xref:fundamentals/environments) di sviluppo:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Posizionare la chiamata a <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> prima di qualsiasi middleware in cui si vogliono rilevare le eccezioni.

> [!WARNING]
> Abilitare la pagina delle eccezioni per gli sviluppatori **solo quando l'app è in esecuzione nell'ambiente di sviluppo**. per non condividere pubblicamente le informazioni dettagliate sulle eccezioni quando l'app è in esecuzione nell'ambiente di produzione. Per altre informazioni sulla configurazione di ambienti, vedere <xref:fundamentals/environments>.

La pagina include le informazioni seguenti sull'eccezione e sulla richiesta:

* Analisi dello stack
* Parametri della stringa di query (se presenti)
* Cookie (se presenti)
* Intestazioni

Per visualizzare la pagina delle eccezioni per gli sviluppatori nell'[app di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), usare la direttiva del preprocessore `DevEnvironment` e selezionare **Trigger an exception** (Attiva un'eccezione) nella home page.

## <a name="exception-handler-page"></a>Pagina del gestore di eccezioni

Per configurare una pagina di gestione degli errori personalizzata per l'ambiente di produzione, usare il middleware di gestione delle eccezioni. Il middleware:

* Intercetta e registra le eccezioni.
* Esegue nuovamente la richiesta in una pipeline alternativa per la pagina o il controller indicati. La richiesta non viene eseguita nuovamente se la risposta è stata avviata.

Nell'esempio seguente <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> aggiunge il middleware di gestione delle eccezioni in ambienti non di sviluppo:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Il modello di app Razor Pages fornisce una pagina di errore (*.cshtml*) e una classe <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) nella cartella *Pages*. Per un'app MVC, il modello di progetto include un metodo di azione Error e una visualizzazione degli errori. Ecco il metodo di azione:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Non decorare il metodo dell'azione di gestione degli errori con attributi di metodo HTTP, ad esempio `HttpGet`. I verbi espliciti impediscono ad alcune richieste di raggiungere il metodo. Consentire l'accesso anonimo al metodo in modo che gli utenti non autenticati possano ricevere la visualizzazione degli errori.

### <a name="access-the-exception"></a>Accedere all'eccezione

Usare <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> per accedere all'eccezione e al percorso della richiesta originale in un controller o in una pagina del gestore degli errori:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> **Non** fornire informazioni sugli errori sensibili ai client. Fornire informazioni sugli errori rappresenta un rischio di sicurezza.

Per visualizzare la pagina di gestione delle eccezioni nell'[app di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), usare le direttive del preprocessore `ProdEnvironment` e `ErrorHandlerPage` e selezionare **Trigger an exception** (Attiva un'eccezione) nella home page.

## <a name="exception-handler-lambda"></a>Espressione lambda per il gestore di eccezioni

Un'alternativa a una [pagina di gestione delle eccezioni personalizzata](#exception-handler-page) consiste nel fornire un'espressione lambda a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>. L'uso di un'espressione lambda consente l'accesso all'errore prima di restituire la risposta.

Di seguito è riportato un esempio dell'uso di un'espressione lambda per la gestione delle eccezioni:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

> [!WARNING]
> **Non** fornire informazioni sugli errori sensibili da <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> o <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> ai client. Fornire informazioni sugli errori rappresenta un rischio di sicurezza.

Per visualizzare il risultato dell'espressione lambda di gestione delle eccezioni nell'[app di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), usare le direttive del preprocessore `ProdEnvironment` e `ErrorHandlerLambda` e selezionare **Trigger an exception** (Attiva un'eccezione) nella home page.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Per impostazione predefinita, un'app ASP.NET Core non offre una tabella codici di stato per i codici di stato HTTP, ad esempio *404 - Non trovato*. L'app restituisce un codice di stato e un corpo della risposta vuoto. Per specificare le tabelle codici di stato, usare il middleware delle tabelle codici di stato.

Il middleware è disponibile nel pacchetto [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) che si trova nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Per abilitare i gestori di solo testo predefiniti per i codici di stato di errore comuni, chiamare <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> nel metodo `Startup.Configure`:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Chiamare `UseStatusCodePages` prima del middleware di gestione delle richieste (ad esempio, middleware dei file statici e middleware MVC).

Di seguito è riportato un esempio del testo visualizzato dai gestori predefiniti:

```
Status Code: 404; Not Found
```

Per visualizzare uno dei vari formati di tabella codici di stato nell'[app di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), usare una delle direttive del preprocessore che iniziano con `StatusCodePages` e selezionare **Trigger a 404** (Genera un 404) nella home page.

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages con stringa di formato

Per personalizzare il tipo di contenuto della risposta e il testo, usare l'overload di <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> che accetta un tipo di contenuto e una stringa di formato:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages con espressione lambda

Per specificare la gestione degli errori personalizzata e il codice per la scrittura delle risposte, usare l'overload di <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> che accetta un'espressione lambda:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirect"></a>UseStatusCodePagesWithRedirect

Metodo di estensione <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:

* Invia un codice di stato *302 - Trovato* al client.
* Reindirizza il client al percorso specificato nel modello di URL.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

Il modello di URL può includere un segnaposto `{0}` per il codice di stato, come illustrato nell'esempio. Se il modello di URL inizia con una tilde (~), la tilde viene sostituita con il valore `PathBase` dell'app. Se si punta a un endpoint all'interno dell'app, creare una visualizzazione MVC o una pagina Razor per l'endpoint. Per un esempio di pagine Razor, vedere [StatusCode.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/Pages/StatusCode.cshtml) nell'[app di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Questo metodo viene usato comunemente quando l'app:

* Deve reindirizzare il client a un endpoint diverso, in genere nei casi in cui un'altra app elabora l'errore. Per le app Web, la barra degli indirizzi del browser del client riflette l'endpoint reindirizzato.
* Non deve conservare e restituire il codice di stato originale con la risposta di reindirizzamento iniziale.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

Metodo di estensione <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:

* Restituisce il codice di stato originale al client.
* Genera il corpo della risposta eseguendo nuovamente la pipeline delle richieste con un percorso alternativo.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Se si punta a un endpoint all'interno dell'app, creare una visualizzazione MVC o una pagina Razor per l'endpoint. Per un esempio di pagine Razor, vedere [StatusCode.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/Pages/StatusCode.cshtml) nell'[app di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Questo metodo viene usato comunemente quando l'app deve:

* Elaborare la richiesta senza il reindirizzamento a un endpoint diverso. Per le app Web, la barra degli indirizzi del browser del client riflette l'endpoint richiesto in origine.
* Conservare e restituire il codice di stato originale con la risposta.

I modelli di URL e di stringa di query potrebbero includere un segnaposto (`{0}`) per il codice di stato. Il modello di URL deve iniziare con una barra (`/`). Quando si usa un segnaposto nel percorso, verificare che l'endpoint (pagina o controller) possa elaborare il segmento del percorso. Ad esempio, una pagina Razor per gli errori deve accettare il valore del segmento di percorso facoltativo con la direttiva `@page`:

```cshtml
@page "{code?}"
```

L'endpoint che elabora l'errore può ottenere l'URL originale che ha generato l'errore, come illustrato nell'esempio seguente:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Disabilitare le tabelle codici di stato

Le tabelle codici di stato possono essere disabilitate per richieste specifiche in un metodo del gestore Razor Pages o in un controller MVC. Per disabilitare le tabelle codici di stato, usare <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Codice di gestione delle eccezioni

Il codice in pagine di gestione delle eccezioni può generare eccezioni. È spesso consigliabile che le pagine di errore di produzione contengano esclusivamente contenuto statico.

### <a name="response-headers"></a>Intestazioni della risposta

Dopo l'invio delle intestazioni per una risposta:

* L'app non può modificare il codice di stato della risposta.
* Non è possibile eseguire pagine o gestori delle eccezioni. La risposta deve essere completata o la connessione deve essere interrotta.

## <a name="server-exception-handling"></a>Gestione delle eccezioni del server

Oltre alla logica di gestione delle eccezioni nell'app, l'[implementazione del server HTTP](xref:fundamentals/servers/index) può gestire alcune eccezioni. Se rileva un'eccezione prima dell'invio delle intestazioni di risposta, il server invia una risposta *500 - Errore interno del server* senza corpo. Se il server rileva un'eccezione dopo l'invio delle intestazioni di risposta, il server chiude la connessione. Le richieste che non sono gestite dall'app vengono gestite dal server. Qualsiasi eccezione che si verifica quando il server sta gestendo la richiesta viene gestita dalla gestione delle eccezioni del server. Eventuali pagine di errore personalizzate dell'app, middleware di gestione delle eccezioni e filtri non hanno effetto su questo comportamento.

## <a name="startup-exception-handling"></a>Gestione delle eccezioni durante l'avvio

Solo il livello di hosting può gestire le eccezioni che si verificano durante l'avvio dell'app. L'host può essere configurato per [acquisire gli errori di avvio](xref:fundamentals/host/web-host#capture-startup-errors) e [acquisire errori dettagliati](xref:fundamentals/host/web-host#detailed-errors).

Il livello di hosting può visualizzare una pagina di errore per un errore di avvio acquisito solo se l'errore si verifica dopo l'associazione indirizzo host/porta. Se si verifica un errore di associazione:

* Il livello di hosting registra un'eccezione critica.
* Si verifica un arresto anomalo del processo di dotnet.
* Non viene visualizzata alcuna pagina di errore quando il server HTTP è [Kestrel](xref:fundamentals/servers/kestrel).

Se durante l'esecuzione su [IIS](/iis) o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) il processo non può essere avviato, viene restituito un codice *502.5 Errore del processo* dal [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Per ulteriori informazioni, vedere <xref:host-and-deploy/iis/troubleshoot>. Per informazioni sulla risoluzione dei problemi di avvio con il servizio app di Azure, vedere <xref:host-and-deploy/azure-apps/troubleshoot>.

## <a name="database-error-page"></a>Pagina di errore di database

Il middleware per la [pagina degli errori del database](<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) acquisisce le eccezioni correlate al database che possono essere risolte con migrazioni di Entity Framework. Quando si verificano queste eccezioni, viene generata una risposta HTML con i dettagli delle azioni possibili per risolvere il problema. Questa pagina deve essere abilitata solo nell'ambiente di sviluppo. Abilitare la pagina aggiungendo codice `Startup.Configure`:

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

## <a name="exception-filters"></a>Filtri eccezioni

Nelle app MVC i filtri delle eccezioni possono essere configurati a livello globale o per ogni controller o azione singolarmente. Nelle app Razor Pages è possibile configurarli a livello globale o per ogni modello di pagina. Questi filtri gestiscono tutte le eccezioni non gestite che si verificano durante l'esecuzione di un'azione del controller o di un altro filtro Per ulteriori informazioni, vedere <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> I filtri delle eccezioni sono utili per intercettare le eccezioni che si verificano all'interno di azioni MVC, ma non sono flessibili come il middleware di gestione degli errori. È consigliabile usare il middleware. Usare i filtri solo quando è necessario eseguire la gestione degli errori in modo diverso in base all'azione MVC scelta.

## <a name="model-state-errors"></a>Errori di stato del modello

Per informazioni su come gestire gli errori dello stato del modello, vedere [Associazione di modelli](xref:mvc/models/model-binding) e [Convalida del modello](xref:mvc/models/validation).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
