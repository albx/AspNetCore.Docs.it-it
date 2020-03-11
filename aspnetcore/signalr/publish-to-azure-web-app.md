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
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661357"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a>Pubblicare un'app SignalR ASP.NET Core nel servizio app Azure

Di [Brady Gaster](https://twitter.com/bradygaster)

Il [servizio app Azure](/azure/app-service/app-service-web-overview) è un servizio della piattaforma [Microsoft Cloud Computing](https://azure.microsoft.com/) per l'hosting di app web, tra cui ASP.NET Core.

> [!NOTE]
> Questo articolo si riferisce alla pubblicazione di un'app SignalR ASP.NET Core da Visual Studio. Per altre informazioni, vedere [servizio SignalR per Azure](https://azure.microsoft.com/services/signalr-service).

## <a name="publish-the-app"></a>Pubblicare l'app

Questo articolo illustra la pubblicazione con gli strumenti di Visual Studio. Visual Studio Code utenti possono usare i comandi dell'interfaccia della riga di comando di [Azure](/cli/azure) per pubblicare app in Azure. Per altre informazioni, vedere [pubblicare un'app ASP.NET Core in Azure con gli strumenti da riga di comando](/azure/app-service/app-service-web-get-started-dotnet).

1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica**.

1. Verificare che il **servizio app** e **Crea nuovo** siano selezionati nella finestra di dialogo **selezionare una destinazione di pubblicazione** .

1. Selezionare **Crea profilo** dall'elenco a discesa pulsante **pubblica** .

   Immettere le informazioni descritte nella tabella seguente nella finestra di dialogo **Crea servizio app** e selezionare **Crea**.

   | Elemento               | Descrizione |
   | ------------------ | ----------- |
   | **Nome**           | Nome univoco dell'app. |
   | **Sottoscrizione**   | Sottoscrizione di Azure utilizzata dall'app. |
   | **Gruppo di risorse** | Gruppo di risorse correlate a cui appartiene l'app. |
   | **Piano di hosting**   | Piano tariffario per l'app Web. |

1. Selezionare il **servizio SignalR di Azure** nell'elenco a discesa **dipendenze** > **Aggiungi** :

   ![area dipendenze che mostra la selezione del servizio SignalR di Azure nell'elenco a discesa Aggiungi](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. Nella finestra di dialogo **servizio SignalR di Azure** selezionare **Crea una nuova istanza del servizio SignalR di Azure**.

1. Specificare un **nome**, un **gruppo di risorse**e una **località**. Tornare alla finestra di dialogo **servizio SignalR di Azure** e selezionare **Aggiungi**.

Visual Studio completa le attività seguenti:

* Consente di creare un profilo di pubblicazione contenente le impostazioni di pubblicazione.
* Crea un' *app Web di Azure* con i dettagli forniti.
* Pubblica l'app.
* Avvia un browser che carica l'app Web.

Il formato dell'URL dell'app è `{APP SERVICE NAME}.azurewebsites.net`. Ad esempio, un'app denominata `SignalRChatApp` dispone di un URL di `https://signalrchatapp.azurewebsites.net`.

Se si verifica un errore di *Gateway HTTP 502,2-Bad* quando si distribuisce un'app destinata a una versione di anteprima di .NET Core, vedere [distribuire ASP.NET Core versione di anteprima al servizio app Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) per risolverlo.

## <a name="configure-the-app-in-azure-app-service"></a>Configurare l'app nel servizio app Azure

> [!NOTE]
> *Questa sezione si applica solo alle app che non usano il servizio SignalR di Azure.*
>
> Se l'app usa il servizio Azure SignalR, il servizio app non richiede la configurazione dell'affinità di Application Request Routing (ARR) e dei socket Web descritti in questa sezione. I client connettono i socket Web al servizio SignalR di Azure, non direttamente all'app.

Per le app ospitate senza il servizio SignalR di Azure, abilitare:

* [Affinità arr](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) per indirizzare le richieste da un utente alla stessa istanza del servizio app. L'impostazione predefinita è **on**.
* [Web socket](xref:fundamentals/websockets) per consentire il funzionamento del trasporto di Web Socket. L'impostazione predefinita è **off**.

1. Nella portale di Azure passare all'app Web in **Servizi app**.
1. Aprire **configurazione** > **Impostazioni generali**.
1. Impostare **Web socket** **su on**.
1. Verificare che **affinità arr** sia impostato **su on**.

## <a name="app-service-plan-limits"></a>Limiti del piano di servizio app

I socket Web e gli altri trasporti sono limitati in base al piano di servizio app selezionato. Per altre informazioni, vedere le sezioni limitazioni dei *servizi cloud di Azure* e limiti del *servizio app* dell'articolo [sottoscrizione di Azure e limiti, quote e vincoli del servizio](/azure/azure-subscription-service-limits#app-service-limits) .

## <a name="additional-resources"></a>Risorse aggiuntive

* [Che cos'è il servizio SignalR di Azure?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Pubblicare un'app ASP.NET Core in Azure con gli strumenti da riga di comando](/azure/app-service/app-service-web-get-started-dotnet)
* [Ospitare e distribuire ASP.NET Core app di anteprima in Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
