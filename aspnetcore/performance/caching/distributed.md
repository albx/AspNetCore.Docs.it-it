---
title: Memorizzazione nella cache in ASP.NET Core distribuita
author: guardrex
description: Informazioni su come usare un'istanza di cache distribuita di ASP.NET Core per migliorare le prestazioni delle app e la scalabilità, soprattutto in un ambiente di farm di server o cloud.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: performance/caching/distributed
ms.openlocfilehash: c3774c26116a4cb70386d0060f2244d224fec8e1
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750994"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="b57b5-103">Memorizzazione nella cache in ASP.NET Core distribuita</span><span class="sxs-lookup"><span data-stu-id="b57b5-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="b57b5-104">Dal [Luke Latham](https://github.com/guardrex) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b57b5-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b57b5-105">Una cache distribuita è una cache condivisa da più server di app, in genere gestito come un servizio esterno per i server di app che vi accedono.</span><span class="sxs-lookup"><span data-stu-id="b57b5-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="b57b5-106">Una cache distribuita può migliorare le prestazioni e scalabilità di un'app ASP.NET Core, soprattutto quando l'app è ospitata da un servizio cloud o una server farm.</span><span class="sxs-lookup"><span data-stu-id="b57b5-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="b57b5-107">Una cache distribuita presenta diversi vantaggi rispetto altri scenari di memorizzazione nella cache in cui sono archiviati dati memorizzati nella cache nei server delle singole app.</span><span class="sxs-lookup"><span data-stu-id="b57b5-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="b57b5-108">Quando i dati memorizzati nella cache sono distribuiti, i dati:</span><span class="sxs-lookup"><span data-stu-id="b57b5-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="b57b5-109">Viene *coerente* (coerente) in tutte le richieste a più server.</span><span class="sxs-lookup"><span data-stu-id="b57b5-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="b57b5-110">Può essere ripristinato dopo il riavvio del server e le distribuzioni di app.</span><span class="sxs-lookup"><span data-stu-id="b57b5-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="b57b5-111">Non utilizza memoria locale.</span><span class="sxs-lookup"><span data-stu-id="b57b5-111">Doesn't use local memory.</span></span>

<span data-ttu-id="b57b5-112">Configurazione di cache distribuita è specifico dell'implementazione.</span><span class="sxs-lookup"><span data-stu-id="b57b5-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="b57b5-113">Questo articolo descrive come configurare SQL Server e le cache distribuite Redis.</span><span class="sxs-lookup"><span data-stu-id="b57b5-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="b57b5-114">Le implementazioni di terze parti sono anche disponibili, ad esempio [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache su GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="b57b5-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="b57b5-115">Indipendentemente dalla scelta dell'implementazione è selezionata, l'app interagisce con la cache usando il <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia.</span><span class="sxs-lookup"><span data-stu-id="b57b5-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="b57b5-116">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b57b5-116">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b57b5-117">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="b57b5-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b57b5-118">Per usare un Server SQL distribuito cache, riferimento il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) o aggiungere un riferimento al pacchetto le [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="b57b5-118">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="b57b5-119">Per usare un Redis cache, riferimento distribuita il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) e aggiungere un riferimento al pacchetto le [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="b57b5-119">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="b57b5-120">Il pacchetto di Redis non è incluso nel `Microsoft.AspNetCore.App` creare un pacchetto, pertanto è necessario fare riferimento a pacchetto Redis separatamente nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="b57b5-120">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b57b5-121">Per usare un Server SQL distribuito cache, riferimento il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) o aggiungere un riferimento al pacchetto le [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="b57b5-121">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="b57b5-122">Per usare un Redis cache, riferimento distribuita il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) e aggiungere un riferimento al pacchetto le [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="b57b5-122">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="b57b5-123">Il pacchetto di Redis non è incluso nel `Microsoft.AspNetCore.App` creare un pacchetto, pertanto è necessario fare riferimento a pacchetto Redis separatamente nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="b57b5-123">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="b57b5-124">Interfaccia IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="b57b5-124">IDistributedCache interface</span></span>

<span data-ttu-id="b57b5-125">Il <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia fornisce i metodi seguenti per modificare gli elementi nell'implementazione di cache distribuita:</span><span class="sxs-lookup"><span data-stu-id="b57b5-125">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="b57b5-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accetta una chiave di stringa e recupera un elemento memorizzato nella cache come un `byte[]` matrice se trovato nella cache.</span><span class="sxs-lookup"><span data-stu-id="b57b5-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="b57b5-127"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Aggiunge un elemento (come `byte[]` matrice) alla cache usando una chiave di stringa.</span><span class="sxs-lookup"><span data-stu-id="b57b5-127"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="b57b5-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Viene aggiornato un elemento nella cache in base alla chiave, reimpostare il timeout di scadenza scorrevole (se presente).</span><span class="sxs-lookup"><span data-stu-id="b57b5-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="b57b5-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Rimuove un elemento della cache basato sulla relativa chiave di stringa.</span><span class="sxs-lookup"><span data-stu-id="b57b5-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="b57b5-130">Stabilire i servizi di memorizzazione nella cache distribuiti</span><span class="sxs-lookup"><span data-stu-id="b57b5-130">Establish distributed caching services</span></span>

<span data-ttu-id="b57b5-131">Registrare un'implementazione di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b57b5-131">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b57b5-132">Implementazioni forniti dal Framework, descritte in questo argomento includono:</span><span class="sxs-lookup"><span data-stu-id="b57b5-132">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="b57b5-133">Cache distribuita in memoria</span><span class="sxs-lookup"><span data-stu-id="b57b5-133">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="b57b5-134">Cache distribuita di SQL Server</span><span class="sxs-lookup"><span data-stu-id="b57b5-134">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="b57b5-135">Cache distribuita di Redis</span><span class="sxs-lookup"><span data-stu-id="b57b5-135">Distributed Redis cache</span></span>](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="b57b5-136">Cache distribuita in memoria</span><span class="sxs-lookup"><span data-stu-id="b57b5-136">Distributed Memory Cache</span></span>

<span data-ttu-id="b57b5-137">La Cache di memoria distribuita (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) è un'implementazione fornita dal framework di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> che archivia gli elementi in memoria.</span><span class="sxs-lookup"><span data-stu-id="b57b5-137">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="b57b5-138">La Cache di memoria distribuita non è un'istanza di cache distribuita effettivo.</span><span class="sxs-lookup"><span data-stu-id="b57b5-138">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="b57b5-139">Gli elementi memorizzati nella cache vengono archiviati per l'istanza dell'app nel server in cui viene eseguita l'app.</span><span class="sxs-lookup"><span data-stu-id="b57b5-139">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="b57b5-140">La Cache distribuita di memoria è un'implementazione utile:</span><span class="sxs-lookup"><span data-stu-id="b57b5-140">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="b57b5-141">Negli scenari di test e sviluppo.</span><span class="sxs-lookup"><span data-stu-id="b57b5-141">In development and testing scenarios.</span></span>
* <span data-ttu-id="b57b5-142">Quando si usa un singolo server in termini di consumo di memoria e di produzione non è un problema.</span><span class="sxs-lookup"><span data-stu-id="b57b5-142">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="b57b5-143">Implementazione della Cache in memoria distribuita riassunti memorizzati nella cache di archiviazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="b57b5-143">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="b57b5-144">Consente di implementare che un vero e proprio distribuite soluzioni di memorizzazione nella cache nel futuro se più nodi o la tolleranza di errore diventano necessari.</span><span class="sxs-lookup"><span data-stu-id="b57b5-144">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="b57b5-145">L'app di esempio vengono utilizzate le Cache in memoria distribuita quando l'app viene eseguita nell'ambiente di sviluppo in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b57b5-145">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="b57b5-146">Cache del Server SQL distribuito</span><span class="sxs-lookup"><span data-stu-id="b57b5-146">Distributed SQL Server Cache</span></span>

<span data-ttu-id="b57b5-147">L'implementazione di Cache distribuita di Server SQL (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) consente alla cache distribuita di utilizzare un database di SQL Server come archivio di backup.</span><span class="sxs-lookup"><span data-stu-id="b57b5-147">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="b57b5-148">Per creare una tabella di elemento memorizzato nella cache di SQL Server in un'istanza di SQL Server, è possibile usare il `sql-cache` dello strumento.</span><span class="sxs-lookup"><span data-stu-id="b57b5-148">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="b57b5-149">Lo strumento crea una tabella con il nome e allo schema specificato.</span><span class="sxs-lookup"><span data-stu-id="b57b5-149">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="b57b5-150">Creare una tabella in SQL Server eseguendo il `sql-cache create` comando.</span><span class="sxs-lookup"><span data-stu-id="b57b5-150">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="b57b5-151">Specificare l'istanza di SQL Server (`Data Source`), database (`Initial Catalog`), lo schema (ad esempio, `dbo`) e il nome di tabella (ad esempio, `TestCache`):</span><span class="sxs-lookup"><span data-stu-id="b57b5-151">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="b57b5-152">Un messaggio viene registrato per indicare che lo strumento ha esito negativo:</span><span class="sxs-lookup"><span data-stu-id="b57b5-152">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="b57b5-153">La tabella creata dal `sql-cache` strumento presenta lo schema seguente:</span><span class="sxs-lookup"><span data-stu-id="b57b5-153">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabella della Cache di SQL Server](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="b57b5-155">Un'app deve modificare i valori della cache usando un'istanza di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, non un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span><span class="sxs-lookup"><span data-stu-id="b57b5-155">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="b57b5-156">L'app di esempio implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in un ambiente non di sviluppo in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b57b5-156">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="b57b5-157">Oggetto <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (e, facoltativamente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> e <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) vengono in genere archiviate all'esterno di controllo del codice sorgente (ad esempio, archiviati per il [Secret Manager](xref:security/app-secrets) o nella *appSettings. JSON* / *appsettings. {ENVIRONMENT}. JSON* file).</span><span class="sxs-lookup"><span data-stu-id="b57b5-157">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="b57b5-158">La stringa di connessione può contenere le credenziali che devono essere mantenute all'esterno di sistemi di controllo di origine.</span><span class="sxs-lookup"><span data-stu-id="b57b5-158">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="b57b5-159">Cache distribuite Redis</span><span class="sxs-lookup"><span data-stu-id="b57b5-159">Distributed Redis Cache</span></span>

<span data-ttu-id="b57b5-160">[Redis](https://redis.io/) è un archivio dati in memoria open source, che viene spesso usato come una cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="b57b5-160">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="b57b5-161">È possibile usare Redis in locale, ed è possibile configurare un [Cache Redis di Azure](https://azure.microsoft.com/services/cache/) per un'app ASP.NET Core ospitate in Azure.</span><span class="sxs-lookup"><span data-stu-id="b57b5-161">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b57b5-162">Consente di configurare un'app per l'implementazione della cache mediante un <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> istanza (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in un ambiente non di sviluppo in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b57b5-162">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b57b5-163">Consente di configurare un'app per l'implementazione della cache mediante un <xref:Microsoft.Extensions.Caching.Redis.RedisCache> istanza (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span><span class="sxs-lookup"><span data-stu-id="b57b5-163">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

<span data-ttu-id="b57b5-164">Per installare Redis in locale:</span><span class="sxs-lookup"><span data-stu-id="b57b5-164">To install Redis on your local machine:</span></span>

* <span data-ttu-id="b57b5-165">Installare il [pacchetto Chocolatey Redis](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="b57b5-165">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
* <span data-ttu-id="b57b5-166">Eseguire `redis-server` da un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="b57b5-166">Run `redis-server` from a command prompt.</span></span>

## <a name="use-the-distributed-cache"></a><span data-ttu-id="b57b5-167">Usare la cache distribuita</span><span class="sxs-lookup"><span data-stu-id="b57b5-167">Use the distributed cache</span></span>

<span data-ttu-id="b57b5-168">Usare la <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> l'interfaccia, richiedere un'istanza di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> da qualsiasi altro costruttore nell'app.</span><span class="sxs-lookup"><span data-stu-id="b57b5-168">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="b57b5-169">L'istanza viene fornita dalla [inserimento delle dipendenze (dipendenze)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b57b5-169">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="b57b5-170">All'avvio dell'app, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> viene inserito nelle `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b57b5-170">When the app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="b57b5-171">L'ora corrente viene memorizzato nella cache usando <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (per altre informazioni, vedere [Host Web: Interfaccia IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="b57b5-171">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="b57b5-172">L'app di esempio inserisce <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> nella `IndexModel` da utilizzare per la pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="b57b5-172">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="b57b5-173">Ogni volta che viene caricata la pagina di indice, la cache è selezionata per la volta memorizzati nella cache in `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="b57b5-173">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="b57b5-174">Se l'ora memorizzati nella cache non è ancora scaduto, viene visualizzata l'ora.</span><span class="sxs-lookup"><span data-stu-id="b57b5-174">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="b57b5-175">Se sono trascorsi 20 secondi dall'ultima volta l'ora memorizzati nella cache è stato eseguito (l'ultima è stata caricata in questa pagina), la pagina viene visualizzata *tempo scaduto memorizzato nella cache*.</span><span class="sxs-lookup"><span data-stu-id="b57b5-175">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="b57b5-176">Aggiornare immediatamente il tempo memorizzato nella cache all'ora corrente selezionando il **reimpostare memorizzato nella cache ora** pulsante.</span><span class="sxs-lookup"><span data-stu-id="b57b5-176">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="b57b5-177">Le attivazioni di pulsanti di `OnPostResetCachedTime` metodo del gestore.</span><span class="sxs-lookup"><span data-stu-id="b57b5-177">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="b57b5-178">Non è necessario usare una durata Singleton o con ambito per <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> istanze (almeno per le implementazioni predefinite).</span><span class="sxs-lookup"><span data-stu-id="b57b5-178">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="b57b5-179">È anche possibile creare un <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ogni volta che si potrebbe essere necessario uno invece di usare l'inserimento delle dipendenze, ma la creazione di un'istanza nel codice possono rendere più difficile da testare il codice dell'istanza e viola il [principio delle dipendenze esplicite](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="b57b5-179">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="b57b5-180">Suggerimenti</span><span class="sxs-lookup"><span data-stu-id="b57b5-180">Recommendations</span></span>

<span data-ttu-id="b57b5-181">Quando si decide quale implementazione di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> è ideale per l'app, tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="b57b5-181">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="b57b5-182">Infrastruttura esistente</span><span class="sxs-lookup"><span data-stu-id="b57b5-182">Existing infrastructure</span></span>
* <span data-ttu-id="b57b5-183">Requisiti relativi alle prestazioni</span><span class="sxs-lookup"><span data-stu-id="b57b5-183">Performance requirements</span></span>
* <span data-ttu-id="b57b5-184">Costi</span><span class="sxs-lookup"><span data-stu-id="b57b5-184">Cost</span></span>
* <span data-ttu-id="b57b5-185">Esperienza del team</span><span class="sxs-lookup"><span data-stu-id="b57b5-185">Team experience</span></span>

<span data-ttu-id="b57b5-186">Soluzioni di memorizzazione nella cache di solito si basano sull'archiviazione in memoria per fornire il recupero veloce dei dati memorizzati nella cache, ma la memoria è una risorsa limitata e costosi da espandere.</span><span class="sxs-lookup"><span data-stu-id="b57b5-186">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="b57b5-187">Unico archivio dati comunemente utilizzati in una cache.</span><span class="sxs-lookup"><span data-stu-id="b57b5-187">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="b57b5-188">In generale, una cache Redis offre una maggiore velocità effettiva e latenza più bassa rispetto a una cache di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b57b5-188">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="b57b5-189">Tuttavia, il benchmarking è in genere obbligatorie per determinare le caratteristiche delle prestazioni delle strategie di memorizzazione nella cache.</span><span class="sxs-lookup"><span data-stu-id="b57b5-189">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="b57b5-190">Quando SQL Server viene usato come archivio di backup di cache distribuita, utilizzare dello stesso database per la cache e l'archiviazione di dati normale dell'app e il recupero può influire negativamente sulle prestazioni di entrambi.</span><span class="sxs-lookup"><span data-stu-id="b57b5-190">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="b57b5-191">È consigliabile usare un'istanza di SQL Server dedicata per la cache distribuita di archivio di backup.</span><span class="sxs-lookup"><span data-stu-id="b57b5-191">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b57b5-192">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b57b5-192">Additional resources</span></span>

* [<span data-ttu-id="b57b5-193">Redis Cache in Azure</span><span class="sxs-lookup"><span data-stu-id="b57b5-193">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="b57b5-194">Database SQL in Azure</span><span class="sxs-lookup"><span data-stu-id="b57b5-194">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="b57b5-195">[ASP.NET Core Provider IDistributedCache per NCache nella Web farm](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache su GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="b57b5-195">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
