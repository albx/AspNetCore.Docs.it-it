---
title: Piattaforme supportate da ASP.NET Core SignalR
author: bradygaster
description: Informazioni sulle piattaforme supportate per ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2019
uid: signalr/supported-platforms
ms.openlocfilehash: 1be7a307710e6e522c0088fd1ca01da11a13eda1
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426978"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="1f3df-103">Piattaforme supportate da ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="1f3df-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="1f3df-104">Requisiti di sistema del server</span><span class="sxs-lookup"><span data-stu-id="1f3df-104">Server system requirements</span></span>

<span data-ttu-id="1f3df-105">SignalR per ASP.NET Core supporta qualsiasi piattaforma server supportata da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f3df-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="1f3df-106">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="1f3df-106">JavaScript client</span></span>

<span data-ttu-id="1f3df-107">Il [client JavaScript](https://www.npmjs.com/package/@aspnet/signalr) viene eseguito in NodeJS 8 e versioni successive e nei browser seguenti:</span><span class="sxs-lookup"><span data-stu-id="1f3df-107">The [JavaScript client](https://www.npmjs.com/package/@aspnet/signalr) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="1f3df-108">Browser</span><span class="sxs-lookup"><span data-stu-id="1f3df-108">Browser</span></span>                         | <span data-ttu-id="1f3df-109">Versione</span><span class="sxs-lookup"><span data-stu-id="1f3df-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="1f3df-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="1f3df-110">Microsoft Edge</span></span>                  | <span data-ttu-id="1f3df-111">&dagger; corrente</span><span class="sxs-lookup"><span data-stu-id="1f3df-111">Current&dagger;</span></span> |
| <span data-ttu-id="1f3df-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="1f3df-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="1f3df-113">&dagger; corrente</span><span class="sxs-lookup"><span data-stu-id="1f3df-113">Current&dagger;</span></span> |
| <span data-ttu-id="1f3df-114">Google Chrome; include Android</span><span class="sxs-lookup"><span data-stu-id="1f3df-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="1f3df-115">&dagger; corrente</span><span class="sxs-lookup"><span data-stu-id="1f3df-115">Current&dagger;</span></span> |
| <span data-ttu-id="1f3df-116">Safari include iOS</span><span class="sxs-lookup"><span data-stu-id="1f3df-116">Safari; includes iOS</span></span>            | <span data-ttu-id="1f3df-117">&dagger; corrente</span><span class="sxs-lookup"><span data-stu-id="1f3df-117">Current&dagger;</span></span> |
| <span data-ttu-id="1f3df-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="1f3df-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="1f3df-119">11</span><span class="sxs-lookup"><span data-stu-id="1f3df-119">11</span></span>              |

<span data-ttu-id="1f3df-120">&dagger;*corrente* si riferisce alla versione più recente del browser.</span><span class="sxs-lookup"><span data-stu-id="1f3df-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="1f3df-121">Client .NET</span><span class="sxs-lookup"><span data-stu-id="1f3df-121">.NET client</span></span>

<span data-ttu-id="1f3df-122">Il [client .NET](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) viene eseguito su qualsiasi piattaforma supportata da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f3df-122">The [.NET client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="1f3df-123">Ad esempio, [gli sviluppatori Novell possono usare SignalR](https://github.com/aspnet/Announcements/issues/305) per la creazione di app Android usando Novell. Android 8.4.0.1 e versioni successive e app iOS con Novell. iOS 11.14.0.4 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="1f3df-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="1f3df-124">Se il server esegue IIS, il trasporto WebSocket richiede IIS 8,0 o versioni successive in Windows Server 2012 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="1f3df-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="1f3df-125">Gli altri trasporti sono supportati in tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="1f3df-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="1f3df-126">Client Java</span><span class="sxs-lookup"><span data-stu-id="1f3df-126">Java client</span></span>

<span data-ttu-id="1f3df-127">Il [client Java](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) supporta Java 8 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="1f3df-127">The [Java client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="1f3df-128">Client non supportati</span><span class="sxs-lookup"><span data-stu-id="1f3df-128">Unsupported clients</span></span>

<span data-ttu-id="1f3df-129">I client seguenti sono disponibili, ma sono sperimentali o non ufficiali.</span><span class="sxs-lookup"><span data-stu-id="1f3df-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="1f3df-130">Non sono attualmente supportati e potrebbero non essere mai.</span><span class="sxs-lookup"><span data-stu-id="1f3df-130">They aren't currently supported and may never be.</span></span>

* [<span data-ttu-id="1f3df-131">C++client</span><span class="sxs-lookup"><span data-stu-id="1f3df-131">C++ client</span></span>](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [<span data-ttu-id="1f3df-132">Client Swift</span><span class="sxs-lookup"><span data-stu-id="1f3df-132">Swift client</span></span>](https://github.com/moozzyk/SignalR-Client-Swift)
