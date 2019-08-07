---
title: Indirizzi IP attendibili del client per ASP.NET Core
author: damienbod
description: Informazioni su come scrivere filtri middleware o azione per convalidare gli indirizzi IP remoti rispetto a un elenco di indirizzi IP approvati.
ms.author: tdykstra
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: ca05989efabea3a71c6912e98055a6746e0f5966
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223926"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="ee36f-103">Indirizzi IP attendibili del client per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee36f-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="ee36f-104">Di [Damien Bowden](https://twitter.com/damien_bod) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="ee36f-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="ee36f-105">Questo articolo illustra tre modi per implementare un elenco di indirizzi IP attendibili (noto anche come whitelist) in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ee36f-105">This article shows three ways to implement an IP safelist (also known as a whitelist) in an ASP.NET Core app.</span></span> <span data-ttu-id="ee36f-106">È possibile usare:</span><span class="sxs-lookup"><span data-stu-id="ee36f-106">You can use:</span></span>

* <span data-ttu-id="ee36f-107">Middleware per controllare l'indirizzo IP remoto di ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="ee36f-107">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="ee36f-108">Filtri azione per controllare l'indirizzo IP remoto delle richieste per controller o metodi di azione specifici.</span><span class="sxs-lookup"><span data-stu-id="ee36f-108">Action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="ee36f-109">Razor Pages filtri per controllare l'indirizzo IP remoto delle richieste per Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="ee36f-109">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="ee36f-110">In ogni caso, una stringa contenente gli indirizzi IP client approvati viene archiviata in un'impostazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="ee36f-110">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="ee36f-111">Il middleware o il filtro analizza la stringa in un elenco e controlla se l'IP remoto è nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="ee36f-111">The middleware or filter parses the string into a list and checks if the remote IP is in the list.</span></span> <span data-ttu-id="ee36f-112">In caso contrario, viene restituito un codice di stato HTTP 403 Forbidden.</span><span class="sxs-lookup"><span data-stu-id="ee36f-112">If not, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="ee36f-113">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ee36f-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-safelist"></a><span data-ttu-id="ee36f-114">Elenchi di sicurezza</span><span class="sxs-lookup"><span data-stu-id="ee36f-114">The safelist</span></span>

<span data-ttu-id="ee36f-115">L'elenco è configurato nel file *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="ee36f-115">The list is configured in the *appsettings.json* file.</span></span> <span data-ttu-id="ee36f-116">Si tratta di un elenco delimitato da punti e virgola e può contenere indirizzi IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="ee36f-116">It's a semicolon-delimited list and can contain IPv4 and IPv6 addresses.</span></span>

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a><span data-ttu-id="ee36f-117">Middleware</span><span class="sxs-lookup"><span data-stu-id="ee36f-117">Middleware</span></span>

<span data-ttu-id="ee36f-118">Il `Configure` metodo aggiunge il middleware e vi passa la stringa di attendibilità in un parametro del costruttore.</span><span class="sxs-lookup"><span data-stu-id="ee36f-118">The `Configure` method adds the middleware and passes the safelist string to it in a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="ee36f-119">Il middleware analizza la stringa in una matrice e cerca l'indirizzo IP remoto nella matrice.</span><span class="sxs-lookup"><span data-stu-id="ee36f-119">The middleware parses the string into an array and looks for the remote IP address in the array.</span></span> <span data-ttu-id="ee36f-120">Se l'indirizzo IP remoto non viene trovato, il middleware restituisce HTTP 401 non consentito.</span><span class="sxs-lookup"><span data-stu-id="ee36f-120">If the remote IP address is not found, the middleware returns HTTP 401 Forbidden.</span></span> <span data-ttu-id="ee36f-121">Questo processo di convalida viene ignorato per le richieste HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="ee36f-121">This validation process is bypassed for HTTP Get requests.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="ee36f-122">Filtro azioni</span><span class="sxs-lookup"><span data-stu-id="ee36f-122">Action filter</span></span>

<span data-ttu-id="ee36f-123">Se si desidera un tipo di attendibilità solo per controller o metodi di azione specifici, utilizzare un filtro azioni.</span><span class="sxs-lookup"><span data-stu-id="ee36f-123">If you want a safelist only for specific controllers or action methods, use an action filter.</span></span> <span data-ttu-id="ee36f-124">Di seguito è riportato un esempio:</span><span class="sxs-lookup"><span data-stu-id="ee36f-124">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

<span data-ttu-id="ee36f-125">Il filtro azioni viene aggiunto al contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="ee36f-125">The action filter is added to the services container.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="ee36f-126">Il filtro può quindi essere utilizzato su un controller o un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="ee36f-126">The filter can then be used on a controller or action method.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

<span data-ttu-id="ee36f-127">Nell'app di esempio, il filtro viene applicato al `Get` metodo.</span><span class="sxs-lookup"><span data-stu-id="ee36f-127">In the sample app, the filter is applied to the `Get` method.</span></span> <span data-ttu-id="ee36f-128">Quindi, quando si testa l'app inviando `Get` una richiesta API, l'attributo convalida l'indirizzo IP del client.</span><span class="sxs-lookup"><span data-stu-id="ee36f-128">So when you test the app by sending a `Get` API request, the attribute is validating the client IP address.</span></span> <span data-ttu-id="ee36f-129">Quando si esegue il test chiamando l'API con qualsiasi altro metodo HTTP, il middleware convalida l'IP del client.</span><span class="sxs-lookup"><span data-stu-id="ee36f-129">When you test by calling the API with any other HTTP method, the middleware is validating the client IP.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="ee36f-130">Filtro Razor Pages</span><span class="sxs-lookup"><span data-stu-id="ee36f-130">Razor Pages filter</span></span> 

<span data-ttu-id="ee36f-131">Per un'app Razor Pages, usare un filtro Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="ee36f-131">If you want a safelist for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="ee36f-132">Di seguito è riportato un esempio:</span><span class="sxs-lookup"><span data-stu-id="ee36f-132">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

<span data-ttu-id="ee36f-133">Questo filtro viene abilitato aggiungendolo alla raccolta di filtri MVC.</span><span class="sxs-lookup"><span data-stu-id="ee36f-133">This filter is enabled by adding it to the MVC Filters collection.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

<span data-ttu-id="ee36f-134">Quando si esegue l'app e si richiede una pagina Razor, il filtro Razor Pages convalida l'IP del client.</span><span class="sxs-lookup"><span data-stu-id="ee36f-134">When you run the app and request a Razor page, the Razor Pages filter is validating the client IP.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ee36f-135">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="ee36f-135">Next steps</span></span>

<span data-ttu-id="ee36f-136">[Altre informazioni su ASP.NET Core middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="ee36f-136">[Learn more about ASP.NET Core Middleware](xref:fundamentals/middleware/index).</span></span>
