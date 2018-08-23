---
title: DevOps con ASP.NET Core e Azure
author: CamSoper
description: Questa guida include informazioni complete sulla creazione di una pipeline DevOps per un'app ASP.NET Core ospitata in Azure.
ms.author: casoper
ms.date: 08/07/2018
uid: azure/devops/index
ms.openlocfilehash: f2eb2499a61ce639457f30f4d182dac6ff677a0f
ms.sourcegitcommit: 6d02e26748a09eabeb0e11740a398cc2bc90b098
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "41746055"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="e1f09-103">DevOps con ASP.NET Core e Azure</span><span class="sxs-lookup"><span data-stu-id="e1f09-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="e1f09-104">[![Immagine della copertina](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="e1f09-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="e1f09-105">Di [Cam Soper](https://twitter.com/camsoper) e [Scott Addie](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="e1f09-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="e1f09-106">Questa guida è disponibile come [e-book PDF scaricabile](https://aka.ms/devopsbook).</span><span class="sxs-lookup"><span data-stu-id="e1f09-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="e1f09-107">Introduzione</span><span class="sxs-lookup"><span data-stu-id="e1f09-107">Welcome</span></span> 

<span data-ttu-id="e1f09-108">Benvenuti alla guida del ciclo di sviluppo di Azure per .NET.</span><span class="sxs-lookup"><span data-stu-id="e1f09-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="e1f09-109">Questa guida presenta i concetti di base della creazione di un ciclo di sviluppo basato su Azure usando processi e strumenti di .NET.</span><span class="sxs-lookup"><span data-stu-id="e1f09-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="e1f09-110">Dopo aver completato questa guida, è possibile sfruttare i vantaggi di una toolchain DevOps completa.</span><span class="sxs-lookup"><span data-stu-id="e1f09-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="e1f09-111">A chi è destinata questa guida</span><span class="sxs-lookup"><span data-stu-id="e1f09-111">Who this guide is for</span></span>

<span data-ttu-id="e1f09-112">Questa guida è destinata a sviluppatori ASP.NET Core esperti (livello 200-300).</span><span class="sxs-lookup"><span data-stu-id="e1f09-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="e1f09-113">Non è necessario conoscere Azure, che viene illustrato in questa introduzione.</span><span class="sxs-lookup"><span data-stu-id="e1f09-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="e1f09-114">La guida può essere utile anche per i tecnici di DevOps che si occupano di operazioni più che di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="e1f09-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="e1f09-115">Questa guida è destinata agli sviluppatori per Windows.</span><span class="sxs-lookup"><span data-stu-id="e1f09-115">This guide targets Windows developers.</span></span> <span data-ttu-id="e1f09-116">Tuttavia .NET Core dispone di supporto completo per Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="e1f09-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="e1f09-117">Per adattare la guida a Linux/macOS, vedere i callout che indicano le differenze per Linux/macOS.</span><span class="sxs-lookup"><span data-stu-id="e1f09-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="e1f09-118">Argomenti non trattati dalla guida</span><span class="sxs-lookup"><span data-stu-id="e1f09-118">What this guide doesn't cover</span></span>

<span data-ttu-id="e1f09-119">Questa guida è incentrata su un'esperienza di distribuzione continua end-to-end per gli sviluppatori .NET.</span><span class="sxs-lookup"><span data-stu-id="e1f09-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="e1f09-120">Non è una guida completa per tutti gli aspetti di Azure e non illustra nel dettaglio le API .NET per i servizi di Azure.</span><span class="sxs-lookup"><span data-stu-id="e1f09-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="e1f09-121">Il contenuto riguarda l'integrazione continua, la distribuzione, il monitoraggio e il debug.</span><span class="sxs-lookup"><span data-stu-id="e1f09-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="e1f09-122">Verso la fine della guida vengono inclusi suggerimenti per le fasi successive.</span><span class="sxs-lookup"><span data-stu-id="e1f09-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="e1f09-123">I suggerimenti includono servizi della piattaforma Azure utili per gli sviluppatori ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e1f09-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="e1f09-124">Contenuto della Guida</span><span class="sxs-lookup"><span data-stu-id="e1f09-124">What's in this guide</span></span>

### <a name="tools-and-downloadsxrefazuredevopstools-and-downloads"></a>[<span data-ttu-id="e1f09-125">Strumenti e download</span><span class="sxs-lookup"><span data-stu-id="e1f09-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="e1f09-126">Informazioni su come ottenere gli strumenti usati nella guida.</span><span class="sxs-lookup"><span data-stu-id="e1f09-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-servicexrefazuredevopsdeploy-to-app-service"></a>[<span data-ttu-id="e1f09-127">Distribuire nel servizio app</span><span class="sxs-lookup"><span data-stu-id="e1f09-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="e1f09-128">Informazioni sui diversi metodi per distribuire un'app ASP.NET Core nel servizio App di Azure.</span><span class="sxs-lookup"><span data-stu-id="e1f09-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deploymentxrefazuredevopscicd"></a>[<span data-ttu-id="e1f09-129">Integrazione e distribuzione continua</span><span class="sxs-lookup"><span data-stu-id="e1f09-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="e1f09-130">Compilazione di una soluzione end-to-end di integrazione e distribuzione continua per l'app ASP.NET Core con GitHub, VSTS e Azure.</span><span class="sxs-lookup"><span data-stu-id="e1f09-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, VSTS, and Azure.</span></span>

### <a name="monitor-and-debugxrefazuredevopsmonitor"></a>[<span data-ttu-id="e1f09-131">Monitoraggio e debug</span><span class="sxs-lookup"><span data-stu-id="e1f09-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="e1f09-132">Informazioni sull'uso degli strumenti di Azure per eseguire il monitoraggio, risolvere i problemi e ottimizzare l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="e1f09-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-stepsxrefazuredevopsnext-steps"></a>[<span data-ttu-id="e1f09-133">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="e1f09-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="e1f09-134">Altri percorsi di apprendimento per gli sviluppatori ASP.NET Core che si avvicinano ad Azure.</span><span class="sxs-lookup"><span data-stu-id="e1f09-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="e1f09-135">Altre letture introduttive</span><span class="sxs-lookup"><span data-stu-id="e1f09-135">Additional introductory reading</span></span>

<span data-ttu-id="e1f09-136">Se si tratta della prima esperienza con il cloud computing, questi articoli illustrano i concetti di base.</span><span class="sxs-lookup"><span data-stu-id="e1f09-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="e1f09-137">Che cos'è il cloud computing?</span><span class="sxs-lookup"><span data-stu-id="e1f09-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="e1f09-138">Esempi di Cloud Computing</span><span class="sxs-lookup"><span data-stu-id="e1f09-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="e1f09-139">Che cos'è IaaS?</span><span class="sxs-lookup"><span data-stu-id="e1f09-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="e1f09-140">Che cos'è PaaS?</span><span class="sxs-lookup"><span data-stu-id="e1f09-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)