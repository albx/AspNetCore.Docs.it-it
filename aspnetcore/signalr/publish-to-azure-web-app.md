---
title: Pubblicare un'app SignalR di ASP.NET Core nel servizio app Azure
author: bradygaster
description: Informazioni su come pubblicare un ASP.NET Core SignalR app per app Azure servizio.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: d03a007ca883b3d0391b848e3e92c90469ee640a
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963924"
---
# <a name="publish-an-aspnet-core-opno-locsignalr-app-to-azure-app-service"></a><span data-ttu-id="967c2-103">Pubblicare un'app SignalR di ASP.NET Core nel servizio app Azure</span><span class="sxs-lookup"><span data-stu-id="967c2-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="967c2-104">Di [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="967c2-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="967c2-105">Il [servizio app Azure](/azure/app-service/app-service-web-overview) è un servizio della piattaforma [Microsoft Cloud Computing](https://azure.microsoft.com/) per l'hosting di app web, tra cui ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="967c2-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="967c2-106">Questo articolo si riferisce alla pubblicazione di un'app SignalR di ASP.NET Core da Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="967c2-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="967c2-107">Per altre informazioni, vedere [servizioSignalR per Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="967c2-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="967c2-108">Pubblicare l'app</span><span class="sxs-lookup"><span data-stu-id="967c2-108">Publish the app</span></span>

<span data-ttu-id="967c2-109">Questo articolo illustra la pubblicazione con gli strumenti di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="967c2-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="967c2-110">Visual Studio Code utenti possono usare i comandi dell'interfaccia della riga di comando di [Azure](/cli/azure) per pubblicare app in Azure.</span><span class="sxs-lookup"><span data-stu-id="967c2-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="967c2-111">Per altre informazioni, vedere [pubblicare un'app ASP.NET Core in Azure con gli strumenti da riga di comando](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="967c2-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="967c2-112">Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="967c2-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="967c2-113">Verificare che il **servizio app** e **Crea nuovo** siano selezionati nella finestra di dialogo **selezionare una destinazione di pubblicazione** .</span><span class="sxs-lookup"><span data-stu-id="967c2-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="967c2-114">Selezionare **Crea profilo** dall'elenco a discesa pulsante **pubblica** .</span><span class="sxs-lookup"><span data-stu-id="967c2-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="967c2-115">Immettere le informazioni descritte nella tabella seguente nella finestra di dialogo **Crea servizio app** e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="967c2-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="967c2-116">Elemento</span><span class="sxs-lookup"><span data-stu-id="967c2-116">Item</span></span>               | <span data-ttu-id="967c2-117">Descrizione</span><span class="sxs-lookup"><span data-stu-id="967c2-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="967c2-118">**Nome**</span><span class="sxs-lookup"><span data-stu-id="967c2-118">**Name**</span></span>           | <span data-ttu-id="967c2-119">Nome univoco dell'app.</span><span class="sxs-lookup"><span data-stu-id="967c2-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="967c2-120">**Sottoscrizione**</span><span class="sxs-lookup"><span data-stu-id="967c2-120">**Subscription**</span></span>   | <span data-ttu-id="967c2-121">Sottoscrizione di Azure utilizzata dall'app.</span><span class="sxs-lookup"><span data-stu-id="967c2-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="967c2-122">**Gruppo di risorse**</span><span class="sxs-lookup"><span data-stu-id="967c2-122">**Resource Group**</span></span> | <span data-ttu-id="967c2-123">Gruppo di risorse correlate a cui appartiene l'app.</span><span class="sxs-lookup"><span data-stu-id="967c2-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="967c2-124">**Piano di hosting**</span><span class="sxs-lookup"><span data-stu-id="967c2-124">**Hosting Plan**</span></span>   | <span data-ttu-id="967c2-125">Piano tariffario per l'app Web.</span><span class="sxs-lookup"><span data-stu-id="967c2-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="967c2-126">Selezionare il **servizio SignalR di Azure** nell'elenco a discesa **dipendenze** > **Aggiungi** :</span><span class="sxs-lookup"><span data-stu-id="967c2-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   ![Area dipendenze che mostra la selezione di Azure [! OP. Servizio NO-LOC (SignalR)] nell'elenco a discesa Aggiungi](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="967c2-128">Nella finestra di dialogo **servizio SignalR di Azure** selezionare **Crea una nuova istanza del servizio SignalR di Azure**.</span><span class="sxs-lookup"><span data-stu-id="967c2-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="967c2-129">Specificare un **nome**, un **gruppo di risorse**e una **località**.</span><span class="sxs-lookup"><span data-stu-id="967c2-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="967c2-130">Tornare alla finestra di dialogo **servizio SignalR di Azure** e selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="967c2-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="967c2-131">Visual Studio completa le attività seguenti:</span><span class="sxs-lookup"><span data-stu-id="967c2-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="967c2-132">Consente di creare un profilo di pubblicazione contenente le impostazioni di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="967c2-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="967c2-133">Crea un' *app Web di Azure* con i dettagli forniti.</span><span class="sxs-lookup"><span data-stu-id="967c2-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="967c2-134">Pubblica l'app.</span><span class="sxs-lookup"><span data-stu-id="967c2-134">Publishes the app.</span></span>
* <span data-ttu-id="967c2-135">Avvia un browser che carica l'app Web.</span><span class="sxs-lookup"><span data-stu-id="967c2-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="967c2-136">Il formato dell'URL dell'app è `{APP SERVICE NAME}.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="967c2-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="967c2-137">Ad esempio, un'app denominata `SignalRChatApp` dispone di un URL di `https://signalrchatapp.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="967c2-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="967c2-138">Se si verifica un errore di *Gateway HTTP 502,2-Bad* quando si distribuisce un'app destinata a una versione di anteprima di .NET Core, vedere [distribuire ASP.NET Core versione di anteprima al servizio app Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) per risolverlo.</span><span class="sxs-lookup"><span data-stu-id="967c2-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="967c2-139">Configurare l'app nel servizio app Azure</span><span class="sxs-lookup"><span data-stu-id="967c2-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="967c2-140">*Questa sezione si applica solo alle app che non usano il servizio SignalR di Azure.*</span><span class="sxs-lookup"><span data-stu-id="967c2-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="967c2-141">Se l'app usa il servizio Azure SignalR, il servizio app non richiede la configurazione dell'affinità di Application Request Routing (ARR) e dei socket Web descritti in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="967c2-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="967c2-142">I client connettono i socket Web al servizio SignalR di Azure, non direttamente all'app.</span><span class="sxs-lookup"><span data-stu-id="967c2-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="967c2-143">Per le app ospitate senza il servizio SignalR di Azure, abilitare:</span><span class="sxs-lookup"><span data-stu-id="967c2-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="967c2-144">[Affinità arr](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) per indirizzare le richieste da un utente alla stessa istanza del servizio app.</span><span class="sxs-lookup"><span data-stu-id="967c2-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="967c2-145">L'impostazione predefinita è **on**.</span><span class="sxs-lookup"><span data-stu-id="967c2-145">The default setting is **On**.</span></span>
* <span data-ttu-id="967c2-146">[Web socket](xref:fundamentals/websockets) per consentire il funzionamento del trasporto di Web Socket.</span><span class="sxs-lookup"><span data-stu-id="967c2-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="967c2-147">L'impostazione predefinita è **off**.</span><span class="sxs-lookup"><span data-stu-id="967c2-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="967c2-148">Nella portale di Azure passare all'app Web in **Servizi app**.</span><span class="sxs-lookup"><span data-stu-id="967c2-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="967c2-149">Aprire **configurazione** > **Impostazioni generali**.</span><span class="sxs-lookup"><span data-stu-id="967c2-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="967c2-150">Impostare **Web socket** **su on**.</span><span class="sxs-lookup"><span data-stu-id="967c2-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="967c2-151">Verificare che **affinità arr** sia impostato **su on**.</span><span class="sxs-lookup"><span data-stu-id="967c2-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="967c2-152">Limiti del piano di servizio app</span><span class="sxs-lookup"><span data-stu-id="967c2-152">App Service Plan limits</span></span>

<span data-ttu-id="967c2-153">I socket Web e gli altri trasporti sono limitati in base al piano di servizio app selezionato.</span><span class="sxs-lookup"><span data-stu-id="967c2-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="967c2-154">Per altre informazioni, vedere le sezioni limitazioni dei *servizi cloud di Azure* e limiti del *servizio app* dell'articolo [sottoscrizione di Azure e limiti, quote e vincoli del servizio](/azure/azure-subscription-service-limits#app-service-limits) .</span><span class="sxs-lookup"><span data-stu-id="967c2-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="967c2-155">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="967c2-155">Additional resources</span></span>

* <span data-ttu-id="967c2-156">[Che cos'è il servizio SignalR di Azure?](/azure/azure-signalr/signalr-overview)</span><span class="sxs-lookup"><span data-stu-id="967c2-156">[What is Azure SignalR Service?](/azure/azure-signalr/signalr-overview)</span></span>
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="967c2-157">Pubblicare un'app ASP.NET Core in Azure con gli strumenti da riga di comando</span><span class="sxs-lookup"><span data-stu-id="967c2-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="967c2-158">Ospitare e distribuire ASP.NET Core app di anteprima in Azure</span><span class="sxs-lookup"><span data-stu-id="967c2-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
