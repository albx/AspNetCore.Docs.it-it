---
title: Pubblicare un ASP.NET Core SignalR app servizio App di Azure
author: bradygaster
description: Informazioni su come pubblicare un'app ASP.NET Core SignalR in servizio App di Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/26/2019
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 87a9c93add373b24e3c473912cdbfcc00bbebf7e
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406107"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a><span data-ttu-id="9cbf2-103">Pubblicare un ASP.NET Core SignalR app servizio App di Azure</span><span class="sxs-lookup"><span data-stu-id="9cbf2-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="9cbf2-104">Da [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="9cbf2-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="9cbf2-105">[Servizio App di Azure](/azure/app-service/app-service-web-overview) è un [Microsoft il cloud computing](https://azure.microsoft.com/) servizio della piattaforma per l'hosting di App web, tra cui ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="9cbf2-106">Questo articolo si riferisce alla pubblicazione di un'app ASP.NET Core SignalR da Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="9cbf2-107">Per altre informazioni, vedere [servizio SignalR per Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="9cbf2-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="9cbf2-108">Pubblicare l'app</span><span class="sxs-lookup"><span data-stu-id="9cbf2-108">Publish the app</span></span>

<span data-ttu-id="9cbf2-109">Questo articolo illustra pubblicazione usando gli strumenti in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="9cbf2-110">Gli utenti di Visual Studio Code è possono usare [CLI Azure](/cli/azure) comandi per pubblicare le App in Azure.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="9cbf2-111">Per altre informazioni, vedere [pubblicare un'app ASP.NET Core in Azure con gli strumenti da riga di comando](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="9cbf2-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="9cbf2-112">Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="9cbf2-113">Verificare che **servizio App** e **Crea nuovo** selezionati nel **selezionare una destinazione di pubblicazione** finestra di dialogo.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="9cbf2-114">Selezionare **Crea profilo** dalle **Publish** pulsante elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="9cbf2-115">Immettere le informazioni descritte nella tabella riportata di seguito il **Crea servizio App** finestra di dialogo e selezionare **crea**.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="9cbf2-116">Elemento</span><span class="sxs-lookup"><span data-stu-id="9cbf2-116">Item</span></span>               | <span data-ttu-id="9cbf2-117">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9cbf2-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="9cbf2-118">**Name**</span><span class="sxs-lookup"><span data-stu-id="9cbf2-118">**Name**</span></span>           | <span data-ttu-id="9cbf2-119">Nome univoco dell'app.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="9cbf2-120">**Sottoscrizione**</span><span class="sxs-lookup"><span data-stu-id="9cbf2-120">**Subscription**</span></span>   | <span data-ttu-id="9cbf2-121">Sottoscrizione di Azure usati dall'app.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="9cbf2-122">**Gruppo di risorse**</span><span class="sxs-lookup"><span data-stu-id="9cbf2-122">**Resource Group**</span></span> | <span data-ttu-id="9cbf2-123">Gruppo di risorse correlate a cui appartiene l'app.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="9cbf2-124">**Piano di hosting**</span><span class="sxs-lookup"><span data-stu-id="9cbf2-124">**Hosting Plan**</span></span>   | <span data-ttu-id="9cbf2-125">Piano tariffario per l'app web.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="9cbf2-126">Selezionare il **servizio Azure SignalR** nel **dipendenze** > **Aggiungi** elenco a discesa:</span><span class="sxs-lookup"><span data-stu-id="9cbf2-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   ![La selezione del servizio Azure SignalR visualizzata nell'elenco a discesa Aggiungi area di dipendenze](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="9cbf2-128">Nel **servizio Azure SignalR** finestra di dialogo, seleziona **creare una nuova istanza di servizio Azure SignalR**.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="9cbf2-129">Fornire una **Name**, **gruppo di risorse**, e **percorso**.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="9cbf2-130">Tornare al **servizio Azure SignalR** finestra di dialogo e selezionare **Add**.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="9cbf2-131">Visual Studio completa le attività seguenti:</span><span class="sxs-lookup"><span data-stu-id="9cbf2-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="9cbf2-132">Crea un profilo di pubblicazione che contiene le impostazioni di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="9cbf2-133">Crea un' *App Web di Azure* con i dettagli forniti.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="9cbf2-134">Pubblica l'app.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-134">Publishes the app.</span></span>
* <span data-ttu-id="9cbf2-135">Avvia un browser, che carica l'app web.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="9cbf2-136">Il formato dell'URL dell'app è `{APP SERVICE NAME}.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="9cbf2-137">Ad esempio, un'app denominata `SignalRChatApp` dispone di un URL di `https://signalrchatapp.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="9cbf2-138">Se un HTTP *502.2 - Gateway non valido* errore si verifica quando si distribuisce un'app destinata a una versione di .NET Core preview, vedere [versione di anteprima di distribuzione di ASP.NET Core nel servizio App di Azure di](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="9cbf2-139">Configurare l'app nel servizio App di Azure</span><span class="sxs-lookup"><span data-stu-id="9cbf2-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="9cbf2-140">*In questa sezione si applica solo alle App non usa il servizio Azure SignalR.*</span><span class="sxs-lookup"><span data-stu-id="9cbf2-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="9cbf2-141">Se l'app Usa il servizio Azure SignalR, il servizio App non richiede la configurazione dell'affinità Application Request Routing (ARR) e Web socket descritte in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="9cbf2-142">I client si connettono i WebSocket per il servizio Azure SignalR, non direttamente all'app.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="9cbf2-143">Per le app ospitate senza il servizio Azure SignalR, abilitare:</span><span class="sxs-lookup"><span data-stu-id="9cbf2-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="9cbf2-144">[Affinità ARR](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) per indirizzare le richieste da un utente di tornare all'istanza del servizio App stesso.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="9cbf2-145">L'impostazione predefinita è **su**.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-145">The default setting is **On**.</span></span>
* <span data-ttu-id="9cbf2-146">[Web socket](xref:fundamentals/websockets) per consentire il trasporto WebSocket alla funzione.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="9cbf2-147">L'impostazione predefinita è **disattivata**.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="9cbf2-148">Nel portale di Azure, passare all'app web in **servizi App**.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="9cbf2-149">Aprire **Configuration** > **impostazioni generali**.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="9cbf2-150">Impostare **Web socket** al **su**.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="9cbf2-151">Verificare che **affinità ARR** è impostata su **su**.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="9cbf2-152">Piano di servizio App limita</span><span class="sxs-lookup"><span data-stu-id="9cbf2-152">App Service Plan limits</span></span>

<span data-ttu-id="9cbf2-153">Web socket e altri tipi di trasporto sono limitati basati sul piano di servizio App selezionato.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="9cbf2-154">Per altre informazioni, vedere la *servizi Cloud di Azure limita* e *limiti del servizio App* sezioni del [sottoscrizione di Azure e limiti, quote e vincoli](/azure/azure-subscription-service-limits#app-service-limits) articolo.</span><span class="sxs-lookup"><span data-stu-id="9cbf2-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9cbf2-155">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9cbf2-155">Additional resources</span></span>

* [<span data-ttu-id="9cbf2-156">Che cos'è servizio Azure SignalR?</span><span class="sxs-lookup"><span data-stu-id="9cbf2-156">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="9cbf2-157">Pubblicare un'app ASP.NET Core in Azure con gli strumenti da riga di comando</span><span class="sxs-lookup"><span data-stu-id="9cbf2-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="9cbf2-158">Ospitare e distribuire le App in anteprima di ASP.NET Core in Azure</span><span class="sxs-lookup"><span data-stu-id="9cbf2-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
