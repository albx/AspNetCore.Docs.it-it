---
title: Caching distribuito in ASP.NET Core
author: guardrex
description: Informazioni su come usare una cache distribuita ASP.NET Core per migliorare le prestazioni e la scalabilità delle app, soprattutto in un ambiente cloud o server farm.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: performance/caching/distributed
ms.openlocfilehash: 8417463038bcdc0f77852bec3c3bb8a618153009
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773844"
---
# <a name="distributed-caching-in-aspnet-core"></a>Caching distribuito in ASP.NET Core

Di [Luke Latham](https://github.com/guardrex) e [Steve Smith](https://ardalis.com/)

Una cache distribuita è una cache condivisa da più server app, in genere gestita come servizio esterno ai server app che vi accedono. Una cache distribuita può migliorare le prestazioni e la scalabilità di un'app ASP.NET Core, soprattutto quando l'app è ospitata da un servizio cloud o da un server farm.

Una cache distribuita presenta diversi vantaggi rispetto ad altri scenari di memorizzazione nella cache in cui i dati memorizzati nella cache vengono archiviati nei singoli server dell'app.

Quando vengono distribuiti i dati memorizzati nella cache, i dati:

* È *coerente* (coerente) tra le richieste a più server.
* Sopravvive ai riavvii del server e alle distribuzioni di app.
* Non usa la memoria locale.

La configurazione della cache distribuita è specifica dell'implementazione. Questo articolo descrive come configurare le cache distribuite SQL Server e Redis. Sono disponibili anche implementazioni di terze parti, ad esempio [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache su GitHub](https://github.com/Alachisoft/NCache)). Indipendentemente dall'implementazione selezionata, l'app interagisce con la cache usando l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prerequisiti

::: moniker range=">= aspnetcore-3.0"

Per utilizzare una SQL Server cache distribuita, aggiungere un riferimento al pacchetto [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Per usare una cache distribuita Redis, aggiungere un riferimento al pacchetto [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Per usare una cache distribuita SQL Server, fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) o aggiungere un riferimento al pacchetto [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Per usare una cache distribuita Redis, fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e aggiungere un riferimento al pacchetto [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) . Il pacchetto Redis non è incluso nel `Microsoft.AspNetCore.App` pacchetto, quindi è necessario fare riferimento al pacchetto Redis separatamente nel file di progetto.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Per usare una cache distribuita SQL Server, fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) o aggiungere un riferimento al pacchetto [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .

Per usare una cache distribuita Redis, fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e aggiungere un riferimento al pacchetto [Microsoft. Extensions. Caching. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) . Il pacchetto Redis non è incluso nel `Microsoft.AspNetCore.App` pacchetto, quindi è necessario fare riferimento al pacchetto Redis separatamente nel file di progetto.

::: moniker-end

## <a name="idistributedcache-interface"></a>Interfaccia IDistributedCache

L' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia fornisce i metodi seguenti per modificare gli elementi nell'implementazione della cache distribuita:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> `byte[]` Accetta una chiave di stringa e recupera un elemento memorizzato nella cache come matrice se viene trovato nella cache. &ndash;
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>`byte[]` , <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> Aggiunge&ndash; un elemento (come matrice) alla cache usando una chiave di stringa.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> ,&ndash; Aggiorna un elemento nella cache in base alla relativa chiave, reimpostando il timeout di scadenza variabile (se presente).
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> ,&ndash; Rimuove un elemento della cache in base alla relativa chiave di stringa.

## <a name="establish-distributed-caching-services"></a>Stabilire servizi di memorizzazione nella cache distribuiti

Registrare un'implementazione di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`. Le implementazioni fornite dal Framework descritte in questo argomento includono:

* [Cache di memoria distribuita](#distributed-memory-cache)
* [Cache SQL Server distribuita](#distributed-sql-server-cache)
* [Cache Redis distribuita](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a>Cache di memoria distribuita

La cache di memoria distribuita (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) è un'implementazione fornita dal framework di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> che archivia gli elementi in memoria. La cache di memoria distribuita non è una cache distribuita effettiva. Gli elementi memorizzati nella cache vengono archiviati dall'istanza dell'app nel server in cui è in esecuzione l'app.

La cache di memoria distribuita è un'implementazione utile:

* Negli scenari di sviluppo e test.
* Quando un singolo server viene utilizzato nell'ambiente di produzione e l'utilizzo della memoria non costituisce un problema. L'implementazione della cache di memoria distribuita astrae l'archiviazione dei dati memorizzati nella cache. Consente di implementare in futuro una soluzione di memorizzazione nella cache distribuita se più nodi o tolleranza di errore diventano necessari.

L'app di esempio usa la cache di memoria distribuita quando l'app viene eseguita nell'ambiente di sviluppo `Startup.ConfigureServices`in:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

### <a name="distributed-sql-server-cache"></a>Cache SQL Server distribuita

L'implementazione della cache SQL Server distribuita (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) consente alla cache distribuita di usare un database SQL Server come archivio di backup. Per creare una tabella SQL Server elemento memorizzato nella cache in un'istanza di SQL Server, è `sql-cache` possibile utilizzare lo strumento. Lo strumento crea una tabella con il nome e lo schema specificati.

Creare una tabella in SQL Server eseguendo il `sql-cache create` comando. Fornire l'istanza di SQL Server`Data Source`(), il`Initial Catalog`database (), lo schema ( `dbo`ad esempio,) e il nome della tabella `TestCache`(ad esempio,):

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Viene registrato un messaggio per indicare che lo strumento è stato completato correttamente:

```console
Table and index were created successfully.
```

La tabella creata dallo `sql-cache` strumento presenta lo schema seguente:

![Tabella cache SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Un'app deve modificare i valori della cache usando un' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>istanza di, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>non un oggetto.

L'app di esempio <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> implementa in un ambiente non di sviluppo `Startup.ConfigureServices`in:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

> [!NOTE]
> Un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (e, facoltativamente <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> , <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>e) vengono in genere archiviati all'esterno del controllo del codice sorgente (ad esempio, archiviati dal [gestore dei segreti](xref:security/app-secrets) o in *appSettings. JSON*/*appSettings. { ENVIRONMENT} file JSON* ). La stringa di connessione può contenere credenziali che devono essere mantenute fuori dai sistemi di controllo del codice sorgente.

### <a name="distributed-redis-cache"></a>Cache Redis distribuita

[Redis](https://redis.io/) è un archivio dati in memoria open source, che viene spesso usato come cache distribuita. È possibile usare Redis localmente ed è possibile configurare una [cache Redis di Azure](https://azure.microsoft.com/services/cache/) per un'app ASP.NET Core ospitata in Azure.

::: moniker range=">= aspnetcore-3.0"

Un'app configura l'implementazione della cache usando un' <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> istanza (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in un ambiente non di sviluppo in `Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Un'app configura l'implementazione della cache usando un' <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> istanza (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in un ambiente non di sviluppo in `Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Un'app configura l'implementazione della cache usando un' <xref:Microsoft.Extensions.Caching.Redis.RedisCache> istanza di<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>():

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

Per installare Redis nel computer locale:

* Installare il [pacchetto di cioccolaty Redis](https://chocolatey.org/packages/redis-64/).
* Eseguire `redis-server` da un prompt dei comandi.

## <a name="use-the-distributed-cache"></a>Usare la cache distribuita

Per usare l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia, richiedere un'istanza di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> da qualsiasi costruttore nell'app. L'istanza viene fornita da [inserimento delle dipendenze (di)](xref:fundamentals/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

Quando l'app di esempio viene <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> avviata, viene `Startup.Configure`inserita in. L'ora corrente viene memorizzata <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> nella cache usando (per altre [informazioni, vedere host generico: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Quando l'app di esempio viene <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> avviata, viene `Startup.Configure`inserita in. L'ora corrente viene memorizzata <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> nella cache usando (per altre [informazioni, vedere host Web: Interfaccia](xref:fundamentals/host/web-host#iapplicationlifetime-interface)IApplicationLifetime:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

L'app di esempio inserisce <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> nell'oggetto `IndexModel` per l'uso da parte della pagina di indice.

Ogni volta che viene caricata la pagina di indice, viene verificata la presenza dell' `OnGetAsync`ora memorizzata nella cache in. Se il tempo di memorizzazione nella cache non è scaduto, viene visualizzata l'ora. Se sono trascorsi 20 secondi dall'ultima volta in cui è stato eseguito l'accesso all'ora memorizzata nella cache (l'ultima volta che questa pagina è stata caricata), la pagina Visualizza l' *ora della cache scaduta*.

Aggiornare immediatamente l'ora memorizzata nella cache all'ora corrente selezionando il pulsante **Reimposta tempo memorizzato nella cache** . Il pulsante attiva il `OnPostResetCachedTime` metodo del gestore.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

> [!NOTE]
> Non è necessario usare un singleton o una durata con ambito per <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> le istanze (almeno per le implementazioni predefinite).
>
> È anche possibile creare un' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> istanza di ogni volta che è necessario invece di usare, ma la creazione di un'istanza nel codice può rendere il codice più difficile da testare e violare il [principio delle dipendenze esplicite](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

## <a name="recommendations"></a>Consigli

Quando si decide quale implementazione <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> di è migliore per l'app, tenere presente quanto segue:

* Infrastruttura esistente
* Requisiti relativi alle prestazioni
* Costi
* Esperienza del team

Le soluzioni per la memorizzazione nella cache si basano in genere sull'archiviazione in memoria per consentire un rapido recupero dei dati memorizzati nella cache, ma la memoria è una risorsa limitata ed è dispendiosa per espanderla. Archiviare solo i dati utilizzati di frequente in una cache.

In genere, una cache Redis offre una velocità effettiva più elevata e una latenza inferiore rispetto a una cache SQL Server. Tuttavia, il benchmarking è in genere necessario per determinare le caratteristiche di prestazioni delle strategie di memorizzazione nella cache.

Quando SQL Server viene usato come archivio di backup della cache distribuita, l'uso dello stesso database per la cache e l'archiviazione e il recupero dei dati ordinari dell'app possono influire negativamente sulle prestazioni di entrambi. Si consiglia di usare un'istanza di SQL Server dedicata per l'archivio di backup della cache distribuita.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Cache Redis in Azure](/azure/azure-cache-for-redis/)
* [Database SQL in Azure](/azure/sql-database/)
* [ASP.NET Core provider IDistributedCache per NCache in Web farm](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache su GitHub](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
