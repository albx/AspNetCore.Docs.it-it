---
title: Condividi controller, visualizzazioni, Razor Pages e altro con le parti dell'applicazione in ASP.NET Core
author: rick-anderson
description: Condividi controller, Visualizza, Razor Pages e altro con le parti dell'applicazione in ASP.NET Core
ms.author: riande
ms.date: 11/11/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: a102511478c40ae64aada919fee7072c3027ddcd
ms.sourcegitcommit: 4e3edff24ba6e43a103fee1b126c9826241bb37b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74958995"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a>Condividi controller, visualizzazioni, Razor Pages e altro ancora con le parti dell'applicazione

::: moniker range=">= aspnetcore-3.0"

Di [Rick Anderson](https://twitter.com/RickAndMSFT)

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([procedura per il download](xref:index#how-to-download-a-sample))

Una *parte dell'applicazione* è un'astrazione sulle risorse di un'app. Le parti dell'applicazione consentono ASP.NET Core di individuare controller, componenti di visualizzazione, helper tag, Razor Pages, origini di compilazione Razor e altro ancora. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> è una parte dell'applicazione. `AssemblyPart` incapsula un riferimento all'assembly ed espone i tipi e i riferimenti di compilazione.

I [provider di funzionalità](#fp) funzionano con le parti dell'applicazione per popolare le funzionalità di un'app ASP.NET Core. Il caso d'uso principale per le parti dell'applicazione consiste nel configurare un'app per individuare (o evitare il caricamento) ASP.NET Core funzionalità da un assembly. Ad esempio, potrebbe essere necessario condividere le funzionalità comuni tra più app. Con le parti dell'applicazione è possibile condividere un assembly (DLL) contenente controller, visualizzazioni, Razor Pages, origini di compilazione Razor, helper tag e altro ancora con più app. La condivisione di un assembly è preferibile alla duplicazione del codice in più progetti.

ASP.NET Core le app caricano le funzionalità da <xref:System.Web.WebPages.ApplicationPart>. La classe <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> rappresenta una parte dell'applicazione supportata da un assembly.

## <a name="load-aspnet-core-features"></a>Carica ASP.NET Core funzionalità

Usare le classi <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> e <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> per individuare e caricare funzionalità di ASP.NET Core (controller, componenti di visualizzazione e così via). Il <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tiene traccia delle parti dell'applicazione e dei provider di funzionalità disponibili. `ApplicationPartManager` è configurato in `Startup.ConfigureServices`:

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

Il codice seguente offre un approccio alternativo alla configurazione di `ApplicationPartManager` usando `AssemblyPart`:

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

I due esempi di codice precedenti caricano il `SharedController` da un assembly. Il `SharedController` non è presente nel progetto dell'app. Vedere il download dell'esempio di [soluzione WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) .

### <a name="include-views"></a>Includi visualizzazioni

Usare una [libreria di classi Razor](xref:razor-pages/ui-class) per includere le visualizzazioni nell'assembly.

### <a name="prevent-loading-resources"></a>Impedisci il caricamento delle risorse

Le parti dell'applicazione possono essere utilizzate per *evitare* il caricamento di risorse in un assembly o in un percorso specifico. Aggiungere o rimuovere membri della raccolta di <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> per nascondere o rendere disponibili risorse. L'ordine delle voci nella raccolta `ApplicationParts` non è importante. Configurare il `ApplicationPartManager` prima di usarlo per configurare i servizi nel contenitore. Ad esempio, configurare il `ApplicationPartManager` prima di richiamare `AddControllersAsServices`. Chiamare `Remove` sulla raccolta `ApplicationParts` per rimuovere una risorsa.

Il `ApplicationPartManager` include le parti per:

* Assembly dell'app e assembly dipendenti.
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

<a name="fp"></a>

## <a name="feature-providers"></a>Provider di funzionalità

I provider di funzionalità dell'applicazione esaminano le parti dell'applicazione e forniscono funzionalità per tali parti. Sono disponibili provider di funzionalità predefiniti per le funzionalità di ASP.NET Core seguenti:

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* `internal class` [RazorCompiledItemFeatureProvider](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)

I provider di funzionalità ereditano da <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, dove `T` è il tipo della funzionalità. I provider di funzionalità possono essere implementati per uno qualsiasi dei tipi di funzionalità elencati in precedenza. L'ordine dei provider di funzionalità nella `ApplicationPartManager.FeatureProviders` può influisca sul comportamento in fase di esecuzione. I provider aggiunti successivamente possono rispondere alle azioni eseguite dai provider aggiunti precedentemente.

### <a name="display-available-features"></a>Visualizzare funzionalità disponibili

Le funzionalità disponibili per un'app possono essere enumerate richiedendo un `ApplicationPartManager` tramite l' [inserimento di dipendenze](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

L' [esempio di download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa il codice precedente per visualizzare le funzionalità dell'app:

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Individuazione nelle parti dell'applicazione

Gli errori HTTP 404 non sono rari durante lo sviluppo con le parti dell'applicazione. Questi errori sono in genere causati da un requisito essenziale per il modo in cui vengono individuate le parti delle applicazioni. Se l'app restituisce un errore HTTP 404, verificare che siano stati soddisfatti i requisiti seguenti:

* È necessario impostare l'impostazione `applicationName` sull'assembly radice utilizzato per l'individuazione. L'assembly radice utilizzato per l'individuazione è in genere l'assembly del punto di ingresso.
* L'assembly radice deve avere un riferimento alle parti utilizzate per l'individuazione. Il riferimento può essere diretto o transitivo.
* L'assembly radice deve fare riferimento a Web SDK. Il Framework dispone di una logica che timbra gli attributi nell'assembly radice utilizzati per l'individuazione.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Di [Rick Anderson](https://twitter.com/RickAndMSFT)

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([procedura per il download](xref:index#how-to-download-a-sample))

Una *parte dell'applicazione* è un'astrazione sulle risorse di un'app. Le parti dell'applicazione consentono ASP.NET Core di individuare controller, componenti di visualizzazione, helper tag, Razor Pages, origini di compilazione Razor e altro ancora. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) è una parte dell'applicazione. `AssemblyPart` incapsula un riferimento all'assembly ed espone i tipi e i riferimenti di compilazione.

I *provider di funzionalità* funzionano con le parti dell'applicazione per popolare le funzionalità di un'app ASP.NET Core. Il caso d'uso principale per le parti dell'applicazione consiste nel configurare un'app per individuare (o evitare il caricamento) ASP.NET Core funzionalità da un assembly. Ad esempio, potrebbe essere necessario condividere le funzionalità comuni tra più app. Con le parti dell'applicazione è possibile condividere un assembly (DLL) contenente controller, visualizzazioni, Razor Pages, origini di compilazione Razor, helper tag e altro ancora con più app. La condivisione di un assembly è preferibile alla duplicazione del codice in più progetti.

ASP.NET Core le app caricano le funzionalità da <xref:System.Web.WebPages.ApplicationPart>. La classe <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> rappresenta una parte dell'applicazione supportata da un assembly.

## <a name="load-aspnet-core-features"></a>Carica ASP.NET Core funzionalità

Usare le classi `ApplicationPart` e `AssemblyPart` per individuare e caricare funzionalità di ASP.NET Core (controller, componenti di visualizzazione e così via). Il <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tiene traccia delle parti dell'applicazione e dei provider di funzionalità disponibili. `ApplicationPartManager` è configurato in `Startup.ConfigureServices`:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

Il codice seguente offre un approccio alternativo alla configurazione di `ApplicationPartManager` usando `AssemblyPart`:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

I due esempi di codice precedenti caricano il `SharedController` da un assembly. Il `SharedController` non è presente nel progetto dell'applicazione. Vedere il download dell'esempio di [soluzione WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .

### <a name="include-views"></a>Includi visualizzazioni

Usare una [libreria di classi Razor](xref:razor-pages/ui-class) per includere le visualizzazioni nell'assembly.

### <a name="prevent-loading-resources"></a>Impedisci il caricamento delle risorse

Le parti dell'applicazione possono essere utilizzate per *evitare* il caricamento di risorse in un assembly o in un percorso specifico. Aggiungere o rimuovere membri della raccolta di <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> per nascondere o rendere disponibili risorse. L'ordine delle voci nella raccolta `ApplicationParts` non è importante. Configurare il `ApplicationPartManager` prima di usarlo per configurare i servizi nel contenitore. Ad esempio, configurare il `ApplicationPartManager` prima di richiamare `AddControllersAsServices`. Chiamare `Remove` sulla raccolta `ApplicationParts` per rimuovere una risorsa.

Il codice seguente usa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> per rimuovere `MyDependentLibrary` dall'app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

Il `ApplicationPartManager` include le parti per:

* Assembly dell'app e assembly dipendenti.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Provider di funzionalità dell'applicazione

I provider di funzionalità dell'applicazione esaminano le parti dell'applicazione e forniscono funzionalità per tali parti. Sono disponibili provider di funzionalità predefiniti per le funzionalità di ASP.NET Core seguenti:

* [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Helper tag](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Componenti di visualizzazione](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

I provider di funzionalità ereditano da <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, dove `T` è il tipo della funzionalità. I provider di funzionalità possono essere implementati per uno qualsiasi dei tipi di funzionalità elencati in precedenza. L'ordine dei provider di funzionalità nella `ApplicationPartManager.FeatureProviders` può influisca sul comportamento in fase di esecuzione. I provider aggiunti successivamente possono rispondere alle azioni eseguite dai provider aggiunti precedentemente.

### <a name="display-available-features"></a>Visualizzare funzionalità disponibili

Le funzionalità disponibili per un'app possono essere enumerate richiedendo un `ApplicationPartManager` tramite l' [inserimento di dipendenze](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

L' [esempio di download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa il codice precedente per visualizzare le funzionalità dell'app:

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Individuazione nelle parti dell'applicazione

Gli errori HTTP 404 non sono rari durante lo sviluppo con le parti dell'applicazione. Questi errori sono in genere causati da un requisito essenziale per il modo in cui vengono individuate le parti delle applicazioni. Se l'app restituisce un errore HTTP 404, verificare che siano stati soddisfatti i requisiti seguenti:

* È necessario impostare l'impostazione `applicationName` sull'assembly radice utilizzato per l'individuazione. L'assembly radice utilizzato per l'individuazione è in genere l'assembly del punto di ingresso.
* L'assembly radice deve avere un riferimento alle parti utilizzate per l'individuazione. Il riferimento può essere diretto o transitivo.
* L'assembly radice deve fare riferimento a Web SDK.
  * Il framework ASP.NET Core dispone di una logica di compilazione personalizzata che timbra gli attributi nell'assembly radice utilizzati per l'individuazione.

::: moniker-end
