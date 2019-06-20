---
title: Pubblicare un'app ASP.NET Core in Azure con Visual Studio
author: rick-anderson
description: Informazioni su come pubblicare un'app ASP.NET Core in Servizio app di Azure con Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: c0251032d83349d4d3925d1ec594c9230ed6ad2d
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207651"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="3c9a2-103">Pubblicare un'app ASP.NET Core in Azure con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3c9a2-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="3c9a2-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3c9a2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="3c9a2-105">Se si lavora in macOS, vedere [Publish to Azure from Visual Studio for Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) (Pubblicare in Azure da Visual Studio per Mac).</span><span class="sxs-lookup"><span data-stu-id="3c9a2-105">See [Publish to Azure from Visual Studio for Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) if you are working on macOS.</span></span>

<span data-ttu-id="3c9a2-106">Per risolvere un problema di distribuzione del Servizio app di Azure, vedere <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-106">To troubleshoot an App Service deployment issue, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="set-up"></a><span data-ttu-id="3c9a2-107">Impostare</span><span class="sxs-lookup"><span data-stu-id="3c9a2-107">Set up</span></span>

* <span data-ttu-id="3c9a2-108">Aprire un [account Azure gratuito](https://azure.microsoft.com/free/dotnet/) se non è già disponibile un account.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="3c9a2-109">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="3c9a2-109">Create a web app</span></span>

<span data-ttu-id="3c9a2-110">Nella Pagina iniziale di Visual Studio selezionare **File > Nuovo > Progetto**</span><span class="sxs-lookup"><span data-stu-id="3c9a2-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![File (menu)](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="3c9a2-112">Completare la finestra di dialogo **Nuovo progetto**:</span><span class="sxs-lookup"><span data-stu-id="3c9a2-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="3c9a2-113">Nel riquadro a sinistra selezionare **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="3c9a2-114">Nel riquadro al centro selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3c9a2-115">Scegliere **OK**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-115">Select **OK**.</span></span>

![Finestra di dialogo Nuovo progetto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="3c9a2-117">Nella finestra di dialogo **Nuova applicazione Web ASP.NET Core**:</span><span class="sxs-lookup"><span data-stu-id="3c9a2-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="3c9a2-118">Selezionare **Applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-118">Select **Web Application**.</span></span>
* <span data-ttu-id="3c9a2-119">Selezionare **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-119">Select **Change Authentication**.</span></span>

![Finestra di dialogo Nuovo progetto](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="3c9a2-121">Viene visualizzata la finestra di dialogo **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="3c9a2-122">Selezionare **Account utente individuali**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="3c9a2-123">Selezionare **OK** per tornare a **Nuova applicazione Web ASP.NET Core** e selezionare nuovamente **OK**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Finestra per autenticazione Nuova applicazione Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="3c9a2-125">Visual Studio crea la soluzione.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="3c9a2-126">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="3c9a2-126">Run the app</span></span>

* <span data-ttu-id="3c9a2-127">Premere CTRL+F5 per eseguire il progetto.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="3c9a2-128">Eseguire il test dei collegamenti **About** (Informazioni su) e **Contact** (Contatto).</span><span class="sxs-lookup"><span data-stu-id="3c9a2-128">Test the **About** and **Contact** links.</span></span>

![Applicazione Web aperta in Microsoft Edge su localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="3c9a2-130">Registrare un utente</span><span class="sxs-lookup"><span data-stu-id="3c9a2-130">Register a user</span></span>

* <span data-ttu-id="3c9a2-131">Selezionare **Registra** e registrare un nuovo utente.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="3c9a2-132">È possibile usare un indirizzo di posta elettronica fittizio.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-132">You can use a fictitious email address.</span></span> <span data-ttu-id="3c9a2-133">Quando si esegue l'invio, nella pagina viene visualizzato l'errore seguente:</span><span class="sxs-lookup"><span data-stu-id="3c9a2-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="3c9a2-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."* (Errore interno del server: Operazione sul database non riuscita durante l'elaborazione della richiesta. Eccezione SQL: Impossibile aprire il file di database. Per risolvere il problema, applicare le migrazioni esistenti per il contesto di database dell'applicazione).</span><span class="sxs-lookup"><span data-stu-id="3c9a2-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="3c9a2-135">Selezionare **Apply Migrations** (Applica migrazioni) e, quando la pagina è stata caricata, eseguire l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Internal Server Error: A database operation failed while processing the request.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="3c9a2-139">L'app visualizza l'indirizzo di posta elettronica usato per registrare il nuovo utente e un collegamento **Disconnessione**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Applicazione Web aperta in Microsoft Edge](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="3c9a2-142">Distribuire l'app in Azure</span><span class="sxs-lookup"><span data-stu-id="3c9a2-142">Deploy the app to Azure</span></span>

<span data-ttu-id="3c9a2-143">In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e selezionare **Pubblica...**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Menu di scelta rapida con il collegamento per la pubblicazione evidenziato](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="3c9a2-145">Nella finestra di dialogo **Pubblica**:</span><span class="sxs-lookup"><span data-stu-id="3c9a2-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="3c9a2-146">Selezionare **Servizio app di Microsoft Azure**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="3c9a2-147">Selezionare l'icona a forma di ingranaggio e quindi selezionare **Crea profilo**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="3c9a2-148">Selezionare **Crea profilo**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-148">Select **Create Profile**.</span></span>

![Finestra di dialogo Pubblica](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="3c9a2-150">Creare risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="3c9a2-150">Create Azure resources</span></span>

<span data-ttu-id="3c9a2-151">Viene visualizzata la finestra di dialogo **Crea servizio app**:</span><span class="sxs-lookup"><span data-stu-id="3c9a2-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="3c9a2-152">Immettere la sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-152">Enter your subscription.</span></span>
* <span data-ttu-id="3c9a2-153">I campi di immissione **Nome dell'app**, **Gruppo di risorse** e **Piano di servizio app** vengono popolati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="3c9a2-154">È possibile mantenere questi nomi o modificarli.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-154">You can keep these names or change them.</span></span>

![Finestra di dialogo Servizio app](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="3c9a2-156">Selezionare la scheda **Servizi** per creare un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="3c9a2-157">Selezionare l'icona verde **+** per creare un nuovo database SQL</span><span class="sxs-lookup"><span data-stu-id="3c9a2-157">Select the green **+** icon to create a new SQL Database</span></span>

![Nuovo database SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="3c9a2-159">Selezionare **Nuovo** nella finestra di dialogo **Configura database SQL** per creare un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Nuovo database SQL e server](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="3c9a2-161">Viene visualizzata la finestra di dialogo **Configura SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="3c9a2-162">Immettere nome utente e password di amministratore e selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="3c9a2-163">È possibile mantenere il **Nome server** predefinito.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="3c9a2-164">La stringa "admin" non è consentita come nome utente di amministratore.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-164">"admin" isn't allowed as the administrator user name.</span></span>

![Finestra di dialogo Configura SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="3c9a2-166">Scegliere **OK**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-166">Select **OK**.</span></span>

<span data-ttu-id="3c9a2-167">Visual Studio torna alla finestra di dialogo **Crea servizio app**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="3c9a2-168">Selezionare **Crea** nella finestra di dialogo **Crea servizio app**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-168">Select **Create** on the **Create App Service** dialog.</span></span>

![Finestra di dialogo Configura database SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="3c9a2-170">Visual Studio crea l'app Web e SQL Server in Azure.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="3c9a2-171">Questo passaggio potrebbe richiedere alcuni minuti.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-171">This step can take a few minutes.</span></span> <span data-ttu-id="3c9a2-172">Per informazioni sulle risorse create, vedere [Risorse aggiuntive](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="3c9a2-172">For information on the resources created, see [Additional resources](#additional-resources).</span></span>

<span data-ttu-id="3c9a2-173">Al termine della distribuzione, selezionare **impostazioni**:</span><span class="sxs-lookup"><span data-stu-id="3c9a2-173">When deployment completes, select **Settings**:</span></span>

![Finestra di dialogo Configura SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="3c9a2-175">Nella pagina **Impostazioni** della finestra di dialogo **Pubblica**:</span><span class="sxs-lookup"><span data-stu-id="3c9a2-175">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="3c9a2-176">Espandere **Database** e selezionare **Usa questa stringa di connessione in fase di esecuzione**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="3c9a2-177">Espandere **Migrazioni Entity Framework** e selezionare **Applica questa migrazione in fase di pubblicazione**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="3c9a2-178">Selezionare **Salva**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-178">Select **Save**.</span></span> <span data-ttu-id="3c9a2-179">Visual Studio torna alla finestra di dialogo **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![Finestra di dialogo Pubblica: pannello Impostazioni](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="3c9a2-181">Fare clic su **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-181">Click **Publish**.</span></span> <span data-ttu-id="3c9a2-182">Visual Studio pubblica l'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-182">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="3c9a2-183">Al termine della distribuzione, l'app viene aperta in un browser.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-183">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="3c9a2-184">Testare l'app in Azure</span><span class="sxs-lookup"><span data-stu-id="3c9a2-184">Test your app in Azure</span></span>

* <span data-ttu-id="3c9a2-185">Eseguire il test dei collegamenti **About** (Informazioni su) e **Contact** (Contatto).</span><span class="sxs-lookup"><span data-stu-id="3c9a2-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="3c9a2-186">Registrare un nuovo utente</span><span class="sxs-lookup"><span data-stu-id="3c9a2-186">Register a new user</span></span>

![Applicazione Web aperta in Microsoft Edge in Servizio app di Azure](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="3c9a2-188">Aggiornare l'app</span><span class="sxs-lookup"><span data-stu-id="3c9a2-188">Update the app</span></span>

* <span data-ttu-id="3c9a2-189">Modificare la pagina Razor *Pages/About.cshtml* e modificarne il contenuto.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="3c9a2-190">Ad esempio, è possibile modificare il paragrafo specificando "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="3c9a2-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="3c9a2-191">Fare clic con il pulsante destro del mouse sul progetto e selezionare **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-191">Right-click on the project and select **Publish...** again.</span></span>

![Menu di scelta rapida con il collegamento per la pubblicazione evidenziato](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="3c9a2-193">Dopo la pubblicazione dell'app, verificare che le modifiche apportate siano disponibili in Azure.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![Verificare che l'attività sia stata completata](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="3c9a2-195">Eseguire la pulizia</span><span class="sxs-lookup"><span data-stu-id="3c9a2-195">Clean up</span></span>

<span data-ttu-id="3c9a2-196">Al termine del test dell'app accedere al [portale di Azure](https://portal.azure.com/) ed eliminare l'app.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="3c9a2-197">Selezionare **Gruppi di risorse** e in seguito il gruppo di risorse che è stato creato.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Portale di Azure: gruppi di risorse nel menu laterale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="3c9a2-199">Nella pagina **Gruppi di risorse** selezionare **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-199">In the **Resource groups** page, select **Delete**.</span></span>

![Portale di Azure: pagina Gruppi di risorse](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="3c9a2-201">Immettere il nome del gruppo di risorse e selezionare **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="3c9a2-202">A questo punto l'app e tutte le altre risorse create in questa esercitazione vengono eliminate da Azure.</span><span class="sxs-lookup"><span data-stu-id="3c9a2-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="3c9a2-203">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="3c9a2-203">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="3c9a2-204">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3c9a2-204">Additional resources</span></span>

* <span data-ttu-id="3c9a2-205">Per Visual Studio Code, vedere [Profili di pubblicazione](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="3c9a2-205">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="3c9a2-206">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="3c9a2-206">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="3c9a2-207">Gruppi di risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="3c9a2-207">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="3c9a2-208">Database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="3c9a2-208">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:host-and-deploy/azure-apps/troubleshoot>
