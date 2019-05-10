---
title: Helper tag di cache distribuita in ASP.NET Core
author: pkellner
description: Informazioni su come usare l'helper tag di cache distribuita.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: a5b33451a763c297c6d7885855a321c43435abb4
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890456"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="a01d0-103">Helper tag di cache distribuita in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a01d0-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="a01d0-104">Di [Peter Kellner](http://peterkellner.net) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a01d0-104">By [Peter Kellner](http://peterkellner.net) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a01d0-105">L'helper tag di cache distribuita consente di migliorare notevolmente le prestazioni dell'app ASP.NET Core memorizzandone il contenuto in un'origine cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="a01d0-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="a01d0-106">Per una panoramica degli helper tag, vedere <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="a01d0-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="a01d0-107">L'helper tag di cache distribuita eredita dalla stessa classe di base da cui eredita l'helper tag di cache.</span><span class="sxs-lookup"><span data-stu-id="a01d0-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="a01d0-108">Tutti gli attributi dell'[helper tag di cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) sono disponibili per l'helper tag di cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="a01d0-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="a01d0-109">L'helper tag di cache distribuita usa l'[inserimento del costruttore](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span><span class="sxs-lookup"><span data-stu-id="a01d0-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="a01d0-110">L'interfaccia <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> viene passata nel costruttore dell'helper tag di cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="a01d0-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="a01d0-111">Se non viene creata alcuna implementazione concreta di `IDistributedCache` in `Startup.ConfigureServices` (*Startup.cs*), l'helper tag di cache distribuita usa lo stesso provider in memoria per l'archiviazione dei dati memorizzati nella cache dell'[helper tag di cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a01d0-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` (*Startup.cs*), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="a01d0-112">Attributi dell'helper per tag di cache distribuita</span><span class="sxs-lookup"><span data-stu-id="a01d0-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="a01d0-113">Attributi condivisi con l'helper tag di cache</span><span class="sxs-lookup"><span data-stu-id="a01d0-113">Attributes shared with the Cache Tag Helper</span></span>

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

<span data-ttu-id="a01d0-114">L'helper tag di cache distribuita eredita dalla stessa classe dell'helper tag di cache.</span><span class="sxs-lookup"><span data-stu-id="a01d0-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="a01d0-115">Per una descrizione di questi attributi, vedere [Helper tag di cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="a01d0-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="a01d0-116">name</span><span class="sxs-lookup"><span data-stu-id="a01d0-116">name</span></span>

| <span data-ttu-id="a01d0-117">Tipo di attributo</span><span class="sxs-lookup"><span data-stu-id="a01d0-117">Attribute Type</span></span> | <span data-ttu-id="a01d0-118">Esempio</span><span class="sxs-lookup"><span data-stu-id="a01d0-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="a01d0-119">Stringa</span><span class="sxs-lookup"><span data-stu-id="a01d0-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="a01d0-120">`name` è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="a01d0-120">`name` is required.</span></span> <span data-ttu-id="a01d0-121">L'attributo `name` viene usato come chiave per ogni istanza di cache archiviata.</span><span class="sxs-lookup"><span data-stu-id="a01d0-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="a01d0-122">A differenza dell'helper tag di cache, che assegna una chiave di cache a ogni istanza in base al nome della pagina Razor e alla posizione nella pagina Razor, nell'helper tag di cache distribuita la chiave è basata solo sull'attributo `name`.</span><span class="sxs-lookup"><span data-stu-id="a01d0-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="a01d0-123">Esempio:</span><span class="sxs-lookup"><span data-stu-id="a01d0-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="a01d0-124">Implementazioni IDistributedCache dell'helper tag di cache distribuita</span><span class="sxs-lookup"><span data-stu-id="a01d0-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="a01d0-125">Esistono due implementazioni di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> integrate in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a01d0-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="a01d0-126">Una è basata su SQL Server e l'altra su Redis.</span><span class="sxs-lookup"><span data-stu-id="a01d0-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="a01d0-127">I dettagli di queste implementazioni sono disponibili in <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="a01d0-127">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="a01d0-128">Entrambe le implementazioni implicano l'impostazione di un'istanza di `IDistributedCache` in `Startup`.</span><span class="sxs-lookup"><span data-stu-id="a01d0-128">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="a01d0-129">Nessun attributo di tag è associato specificamente all'uso di un'implementazione specifica di `IDistributedCache`.</span><span class="sxs-lookup"><span data-stu-id="a01d0-129">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a01d0-130">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a01d0-130">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
