---
title: Ospitare e distribuire Blazor
author: guardrex
description: Informazioni su come ospitare e distribuire app Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/23/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 5def0356d13975211dd234f6a6a9f5a993d003b7
ms.sourcegitcommit: e1623d8279b27ff83d8ad67a1e7ef439259decdf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2019
ms.locfileid: "66223173"
---
# <a name="host-and-deploy-blazor"></a><span data-ttu-id="3c25d-103">Ospitare e distribuire Blazor</span><span class="sxs-lookup"><span data-stu-id="3c25d-103">Host and deploy Blazor</span></span>

<span data-ttu-id="3c25d-104">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3c25d-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="3c25d-105">Pubblicare l'app</span><span class="sxs-lookup"><span data-stu-id="3c25d-105">Publish the app</span></span>

<span data-ttu-id="3c25d-106">Le app vengono pubblicate per la distribuzione nella configurazione per il rilascio.</span><span class="sxs-lookup"><span data-stu-id="3c25d-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3c25d-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c25d-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3c25d-108">Selezionare **Compila** > **Pubblica {APPLICAZIONE}** sulla barra di spostamento.</span><span class="sxs-lookup"><span data-stu-id="3c25d-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="3c25d-109">Selezionare la *destinazione di pubblicazione*.</span><span class="sxs-lookup"><span data-stu-id="3c25d-109">Select the *publish target*.</span></span> <span data-ttu-id="3c25d-110">Per pubblicare in locale, selezionare **Cartella**.</span><span class="sxs-lookup"><span data-stu-id="3c25d-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="3c25d-111">Accettare il percorso predefinito nel campo **Scegliere una cartella** oppure specificarne uno diverso.</span><span class="sxs-lookup"><span data-stu-id="3c25d-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="3c25d-112">Selezionare il pulsante **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="3c25d-112">Select the **Publish** button.</span></span>


# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="3c25d-113">Visual Studio Code/Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="3c25d-113">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="3c25d-114">Usare il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) per pubblicare l'app con una configurazione per il rilascio:</span><span class="sxs-lookup"><span data-stu-id="3c25d-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```console
dotnet publish -c Release
```

---

<span data-ttu-id="3c25d-115">La pubblicazione dell'app attiva un [ripristino](/dotnet/core/tools/dotnet-restore) delle dipendenze del progetto e [compila](/dotnet/core/tools/dotnet-build) il progetto prima di creare gli asset per la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="3c25d-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="3c25d-116">Come parte del processo di compilazione, vengono rimossi gli assembly e i metodi non usati per ridurre le dimensioni del download e i tempi di caricamento dell'app.</span><span class="sxs-lookup"><span data-stu-id="3c25d-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="3c25d-117">Un'app lato client Blazor viene pubblicata nella cartella */bin/Release/{FRAMEWORK DI DESTINAZIONE}/dist*.</span><span class="sxs-lookup"><span data-stu-id="3c25d-117">A Blazor client-side app is published to the */bin/Release/{TARGET FRAMEWORK}/dist* folder.</span></span> <span data-ttu-id="3c25d-118">Un'app lato server Blazor viene pubblicata nella cartella */bin/Release/{FRAMEWORK DI DESTINAZIONE}/publish*.</span><span class="sxs-lookup"><span data-stu-id="3c25d-118">A Blazor server-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="3c25d-119">Gli asset nella cartella vengono distribuiti nel server Web.</span><span class="sxs-lookup"><span data-stu-id="3c25d-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="3c25d-120">La distribuzione potrebbe essere un processo manuale o automatizzato a seconda degli strumenti di sviluppo in uso.</span><span class="sxs-lookup"><span data-stu-id="3c25d-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="3c25d-121">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="3c25d-121">Deployment</span></span>

<span data-ttu-id="3c25d-122">Per indicazioni sulla distribuzione, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="3c25d-122">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>

## <a name="blazor-serverless-hosting-with-azure-storage"></a><span data-ttu-id="3c25d-123">Hosting serverless in Blazor con Archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="3c25d-123">Blazor serverless hosting with Azure Storage</span></span>

<span data-ttu-id="3c25d-124">Le app Blazor sul lato client possono essere rese disponibili da [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) come contenuto statico direttamente da un contenitore di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="3c25d-124">Blazor client-side apps can be served from [Azure Storage](https://azure.microsoft.com/services/storage/) as static content directly from a storage container.</span></span>

<span data-ttu-id="3c25d-125">Per altre informazioni, vedere [Ospitare e distribuire Blazor sul lato client (distribuzione autonoma): Archiviazione di Azure](xref:host-and-deploy/blazor/client-side#azure-storage).</span><span class="sxs-lookup"><span data-stu-id="3c25d-125">For more information, see [Host and deploy Blazor client-side (Standalone deployment): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage).</span></span>
