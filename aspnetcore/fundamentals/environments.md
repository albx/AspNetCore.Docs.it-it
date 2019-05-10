---
title: Usare più ambienti in ASP.NET Core
author: rick-anderson
description: Informazioni su come controllare il comportamento di app ASP.NET Core in più ambienti.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/10/2019
uid: fundamentals/environments
ms.openlocfilehash: a0e6d62f352a886a9bc051813a21d94c1605a1ce
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087039"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="68b9f-103">Usare più ambienti in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68b9f-103">Use multiple environments in ASP.NET Core</span></span>

<span data-ttu-id="68b9f-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="68b9f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="68b9f-105">ASP.NET Core configura il comportamento dell'app in base all'ambiente di runtime e tramite una variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="68b9f-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="68b9f-106">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68b9f-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="68b9f-107">Ambienti</span><span class="sxs-lookup"><span data-stu-id="68b9f-107">Environments</span></span>

<span data-ttu-id="68b9f-108">ASP.NET Core legge la variabile di ambiente `ASPNETCORE_ENVIRONMENT` all'avvio dell'app e ne archivia il valore in [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname).</span><span class="sxs-lookup"><span data-stu-id="68b9f-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname).</span></span> <span data-ttu-id="68b9f-109">La variabile `ASPNETCORE_ENVIRONMENT` può essere impostata su qualsiasi valore, ma il framework supporta [tre valori](/dotnet/api/microsoft.aspnetcore.hosting.environmentname): [Development](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) e [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production).</span><span class="sxs-lookup"><span data-stu-id="68b9f-109">You can set `ASPNETCORE_ENVIRONMENT` to any value, but [three values](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) are supported by the framework: [Development](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging), and [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production).</span></span> <span data-ttu-id="68b9f-110">Se la variabile `ASPNETCORE_ENVIRONMENT` non viene impostata, il valore predefinito è `Production`.</span><span class="sxs-lookup"><span data-stu-id="68b9f-110">If `ASPNETCORE_ENVIRONMENT` isn't set, it defaults to `Production`.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="68b9f-111">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="68b9f-111">The preceding code:</span></span>

* <span data-ttu-id="68b9f-112">Chiama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` è impostato su `Development`.</span><span class="sxs-lookup"><span data-stu-id="68b9f-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="68b9f-113">Chiama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando il valore di `ASPNETCORE_ENVIRONMENT` è uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="68b9f-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="68b9f-114">L'[helper per tag di ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa il valore di `IHostingEnvironment.EnvironmentName` per includere o escludere il markup nell'elemento:</span><span class="sxs-lookup"><span data-stu-id="68b9f-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="68b9f-115">In Windows e macOS le variabili di ambiente e i relativi valori non applicano la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="68b9f-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="68b9f-116">In Linux le variabili di ambiente e i relativi valori **applicano la distinzione tra maiuscole e minuscole** per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="68b9f-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="68b9f-117">Sviluppo</span><span class="sxs-lookup"><span data-stu-id="68b9f-117">Development</span></span>

<span data-ttu-id="68b9f-118">L'ambiente di sviluppo può abilitare funzionalità che non devono essere esposte nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="68b9f-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="68b9f-119">Ad esempio i modelli ASP.NET Core abilitano la [pagina delle eccezioni per gli sviluppatori](xref:fundamentals/error-handling#developer-exception-page) nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="68b9f-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="68b9f-120">L'ambiente di sviluppo computer locale può essere impostato nel file *Properties\launchSettings.json* del progetto.</span><span class="sxs-lookup"><span data-stu-id="68b9f-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="68b9f-121">I valori di ambiente in *launchSettings.json* sostituiscono i valori impostati nell'ambiente di sistema.</span><span class="sxs-lookup"><span data-stu-id="68b9f-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="68b9f-122">Il codice JSON seguente visualizza tre profili di un file *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="68b9f-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="68b9f-123">La proprietà `applicationUrl` in *launchSettings.json* può specificare un elenco di URL di server.</span><span class="sxs-lookup"><span data-stu-id="68b9f-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="68b9f-124">Usare un punto e virgola tra gli URL dell'elenco:</span><span class="sxs-lookup"><span data-stu-id="68b9f-124">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="68b9f-125">Quando l'app viene avviata con [dotnet run](/dotnet/core/tools/dotnet-run) viene usato il primo profilo con `"commandName": "Project"`.</span><span class="sxs-lookup"><span data-stu-id="68b9f-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="68b9f-126">Il valore di `commandName` specifica il server Web da avviare.</span><span class="sxs-lookup"><span data-stu-id="68b9f-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="68b9f-127">`commandName` può avere uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="68b9f-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="68b9f-128">`Project` (che avvia Kestrel)</span><span class="sxs-lookup"><span data-stu-id="68b9f-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="68b9f-129">Quando un'app viene avviata con [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="68b9f-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="68b9f-130">*launchSettings.json*, se disponibile, viene letto.</span><span class="sxs-lookup"><span data-stu-id="68b9f-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="68b9f-131">Le impostazioni `environmentVariables` in *launchSettings.json* sostituiscono le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="68b9f-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="68b9f-132">Viene visualizzato l'ambiente host.</span><span class="sxs-lookup"><span data-stu-id="68b9f-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="68b9f-133">L'output seguente visualizza un'app avviata con [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="68b9f-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="68b9f-134">La scheda **Debug** nelle proprietà di progetto di Visual Studio visualizza una GUI per la modifica del file *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="68b9f-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Proprietà progetto - Impostazione delle variabili di ambiente](environments/_static/project-properties-debug.png)

<span data-ttu-id="68b9f-136">È possibile che le modifiche apportate ai profili di progetto abbiano effetto solo dopo il riavvio del server Web.</span><span class="sxs-lookup"><span data-stu-id="68b9f-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="68b9f-137">Perché Kestrel rilevi le modifiche apportate al suo ambiente, deve essere riavviato.</span><span class="sxs-lookup"><span data-stu-id="68b9f-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="68b9f-138">Evitare di archiviare segreti in *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="68b9f-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="68b9f-139">Per l'archiviazione di segreti per lo sviluppo locale, usare lo [strumento Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="68b9f-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="68b9f-140">Quando si usa [Visual Studio Code](https://code.visualstudio.com/), le variabili di ambiente possono essere impostate nel file *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="68b9f-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="68b9f-141">Nell'esempio seguente l'ambiente viene impostato su `Development`:</span><span class="sxs-lookup"><span data-stu-id="68b9f-141">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="68b9f-142">Un file *.vscode/launch.json* del progetto non viene letto quando si avvia l'app con `dotnet run` come accade per il file *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="68b9f-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="68b9f-143">Quando si avvia un'app in un ambiente di sviluppo che non ha un file *launchSettings.json*, impostare una variabile di ambiente o un argomento della riga di comando sul comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="68b9f-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="68b9f-144">Produzione</span><span class="sxs-lookup"><span data-stu-id="68b9f-144">Production</span></span>

<span data-ttu-id="68b9f-145">È necessario che l'ambiente di produzione sia configurato per ottimizzare la sicurezza, le prestazioni e l'affidabilità dell'app.</span><span class="sxs-lookup"><span data-stu-id="68b9f-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="68b9f-146">Alcune impostazioni comuni che differiscono dallo sviluppo includono:</span><span class="sxs-lookup"><span data-stu-id="68b9f-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="68b9f-147">Memorizzazione nella cache.</span><span class="sxs-lookup"><span data-stu-id="68b9f-147">Caching.</span></span>
* <span data-ttu-id="68b9f-148">Risorse lato client in bundle, minimizzate e potenzialmente offerte da una rete CDN.</span><span class="sxs-lookup"><span data-stu-id="68b9f-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="68b9f-149">Pagine di errore di diagnostica disabilitate.</span><span class="sxs-lookup"><span data-stu-id="68b9f-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="68b9f-150">Pagine di errore descrittive abilitate.</span><span class="sxs-lookup"><span data-stu-id="68b9f-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="68b9f-151">Registrazione e monitoraggio della produzione abilitati.</span><span class="sxs-lookup"><span data-stu-id="68b9f-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="68b9f-152">Ad esempio [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="68b9f-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="68b9f-153">Impostare l'ambiente</span><span class="sxs-lookup"><span data-stu-id="68b9f-153">Set the environment</span></span>

<span data-ttu-id="68b9f-154">Spesso risulta utile impostare un ambiente specifico per i test.</span><span class="sxs-lookup"><span data-stu-id="68b9f-154">It's often useful to set a specific environment for testing.</span></span> <span data-ttu-id="68b9f-155">Se l'ambiente non viene impostato, il valore predefinito è `Production`, che disabilita la maggior parte delle funzionalità di debug.</span><span class="sxs-lookup"><span data-stu-id="68b9f-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="68b9f-156">Il metodo per l'impostazione dell'ambiente dipende dal sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="68b9f-156">The method for setting the environment depends on the operating system.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="68b9f-157">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="68b9f-157">Azure App Service</span></span>

<span data-ttu-id="68b9f-158">Per impostare l'ambiente in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/), attenersi alla procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="68b9f-158">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="68b9f-159">Selezionare l'app dal pannello **Servizi app**.</span><span class="sxs-lookup"><span data-stu-id="68b9f-159">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="68b9f-160">Nel gruppo **IMPOSTAZIONI** selezionare il pannello **Impostazioni dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="68b9f-160">In the **SETTINGS** group, select the **Application settings** blade.</span></span>
1. <span data-ttu-id="68b9f-161">Nell'area **Impostazioni dell'applicazione** selezionare **Aggiungi nuova impostazione**.</span><span class="sxs-lookup"><span data-stu-id="68b9f-161">In the **Application settings** area, select **Add new setting**.</span></span>
1. <span data-ttu-id="68b9f-162">In **Immettere un nome** specificare `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="68b9f-162">For **Enter a name**, provide `ASPNETCORE_ENVIRONMENT`.</span></span> <span data-ttu-id="68b9f-163">In **Immettere un valore** specificare l'ambiente (ad esempio, `Staging`).</span><span class="sxs-lookup"><span data-stu-id="68b9f-163">For **Enter a value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="68b9f-164">Selezionare la casella di controllo **Impostazione slot** per lasciare invariata l'impostazione dell'ambiente sullo slot corrente quando gli slot di distribuzione vengono scambiati.</span><span class="sxs-lookup"><span data-stu-id="68b9f-164">Select the **Slot Setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="68b9f-165">Per altre informazioni, vedere [Documentazione di Azure: Impostazioni incluse nello scambio](/azure/app-service/web-sites-staged-publishing).</span><span class="sxs-lookup"><span data-stu-id="68b9f-165">For more information, see [Azure Documentation: Which settings are swapped?](/azure/app-service/web-sites-staged-publishing).</span></span>
1. <span data-ttu-id="68b9f-166">Selezionare **Salva** nella parte superiore del pannello.</span><span class="sxs-lookup"><span data-stu-id="68b9f-166">Select **Save** at the top of the blade.</span></span>

<span data-ttu-id="68b9f-167">Servizio app di Azure riavvia automaticamente l'app quando un'impostazione dell'app (una variabile di ambiente) viene aggiunta, modificata o eliminata nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="68b9f-167">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="68b9f-168">WINDOWS</span><span class="sxs-lookup"><span data-stu-id="68b9f-168">Windows</span></span>

<span data-ttu-id="68b9f-169">Per impostare la variabile `ASPNETCORE_ENVIRONMENT` per la sessione corrente, se l'app viene avviata tramite [dotnet run](/dotnet/core/tools/dotnet-run), vengono usati i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="68b9f-169">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="68b9f-170">**Prompt dei comandi**</span><span class="sxs-lookup"><span data-stu-id="68b9f-170">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="68b9f-171">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="68b9f-171">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="68b9f-172">Questi comandi hanno effetto solo per la finestra corrente.</span><span class="sxs-lookup"><span data-stu-id="68b9f-172">These commands only take effect for the current window.</span></span> <span data-ttu-id="68b9f-173">Quando la finestra viene chiusa, l'impostazione `ASPNETCORE_ENVIRONMENT` viene ripristinata sull'impostazione predefinita o sul valore del computer.</span><span class="sxs-lookup"><span data-stu-id="68b9f-173">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="68b9f-174">Per impostare il valore a livello globale in Windows, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="68b9f-174">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="68b9f-175">Scegliere **Pannello di controllo** > **Sistema** > **Impostazioni di sistema avanzate** e aggiungere o modificare il valore `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="68b9f-175">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Proprietà di sistema avanzate](environments/_static/systemsetting_environment.png)

  ![Variabile di ambiente ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="68b9f-178">Aprire un prompt dei comandi di amministrazione e usare il comando `setx` o aprire un prompt dei comandi di PowerShell di amministrazione e usare `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="68b9f-178">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="68b9f-179">**Prompt dei comandi**</span><span class="sxs-lookup"><span data-stu-id="68b9f-179">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="68b9f-180">L'opzione `/M` indica di impostare la variabile di ambiente a livello del sistema.</span><span class="sxs-lookup"><span data-stu-id="68b9f-180">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="68b9f-181">Se non viene usata l'opzione `/M`, la variabile di ambiente viene impostata per l'account utente.</span><span class="sxs-lookup"><span data-stu-id="68b9f-181">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="68b9f-182">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="68b9f-182">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="68b9f-183">Il valore dell'opzione `Machine` indica di impostare la variabile di ambiente a livello del sistema.</span><span class="sxs-lookup"><span data-stu-id="68b9f-183">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="68b9f-184">Se non viene usata l'opzione `User`, la variabile di ambiente viene impostata per l'account utente.</span><span class="sxs-lookup"><span data-stu-id="68b9f-184">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="68b9f-185">Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` è impostata a livello globale, viene applicata per `dotnet run` in tutte le finestre di comando aperte dopo l'impostazione del valore.</span><span class="sxs-lookup"><span data-stu-id="68b9f-185">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="68b9f-186">**web.config**</span><span class="sxs-lookup"><span data-stu-id="68b9f-186">**web.config**</span></span>

<span data-ttu-id="68b9f-187">Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` con *web.config*, vedere la sezione *Impostazione delle variabili di ambiente* di <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="68b9f-187">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="68b9f-188">**File di progetto o profilo di pubblicazione**</span><span class="sxs-lookup"><span data-stu-id="68b9f-188">**Project file or publish profile**</span></span>

<span data-ttu-id="68b9f-189">**Per le distribuzioni di Windows IIS:** Includere la proprietà `<EnvironmentName>` nel profilo di pubblicazione (*.pubxml*) o nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="68b9f-189">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="68b9f-190">Questo approccio imposta l'ambiente in *web.config* quando viene pubblicato il progetto:</span><span class="sxs-lookup"><span data-stu-id="68b9f-190">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

::: moniker-end

<span data-ttu-id="68b9f-191">**Pool di applicazioni IIS singoli**</span><span class="sxs-lookup"><span data-stu-id="68b9f-191">**Per IIS Application Pool**</span></span>

<span data-ttu-id="68b9f-192">Per impostare la variabile di ambiente `ASPNETCORE_ENVIRONMENT` per un'app in esecuzione in un pool di applicazioni isolato (supportato in IIS 10.0 o versioni successive), vedere la sezione *AppCmd.exe* dell'argomento [Variabili di ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="68b9f-192">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="68b9f-193">Quando la variabile di ambiente `ASPNETCORE_ENVIRONMENT` viene impostata per un pool di app, il suo valore esegue l'override di un'impostazione a livello di sistema.</span><span class="sxs-lookup"><span data-stu-id="68b9f-193">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="68b9f-194">Durante l'hosting di un'app in IIS, quando si aggiunge o si modifica la variabile di ambiente `ASPNETCORE_ENVIRONMENT`, usare uno degli approcci seguenti per fare in modo che il nuovo valore venga selezionato dalle app:</span><span class="sxs-lookup"><span data-stu-id="68b9f-194">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="68b9f-195">Eseguire `net stop was /y` seguito da `net start w3svc` da un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="68b9f-195">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="68b9f-196">Riavviare il server.</span><span class="sxs-lookup"><span data-stu-id="68b9f-196">Restart the server.</span></span>

### <a name="macos"></a><span data-ttu-id="68b9f-197">macOS</span><span class="sxs-lookup"><span data-stu-id="68b9f-197">macOS</span></span>

<span data-ttu-id="68b9f-198">L'impostazione dell'ambiente corrente per macOS può essere eseguita in linea durante l'esecuzione dell'app:</span><span class="sxs-lookup"><span data-stu-id="68b9f-198">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="68b9f-199">In alternativa, impostare l'ambiente tramite `export` prima di eseguire l'app:</span><span class="sxs-lookup"><span data-stu-id="68b9f-199">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="68b9f-200">Le variabili di ambiente a livello computer sono impostate nel file con estensione *bashrc* o *bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="68b9f-200">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="68b9f-201">Modificare il file usando un qualsiasi editor di testo.</span><span class="sxs-lookup"><span data-stu-id="68b9f-201">Edit the file using any text editor.</span></span> <span data-ttu-id="68b9f-202">Aggiungere l'istruzione seguente:</span><span class="sxs-lookup"><span data-stu-id="68b9f-202">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

### <a name="linux"></a><span data-ttu-id="68b9f-203">Linux</span><span class="sxs-lookup"><span data-stu-id="68b9f-203">Linux</span></span>

<span data-ttu-id="68b9f-204">Per le distribuzioni di Linux, usare il comando `export` al prompt dei comandi per le impostazioni delle variabili basate sulla sessione e il file *bash_profile* per le impostazioni di ambiente a livello di computer.</span><span class="sxs-lookup"><span data-stu-id="68b9f-204">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="68b9f-205">Configurazione per ambiente</span><span class="sxs-lookup"><span data-stu-id="68b9f-205">Configuration by environment</span></span>

<span data-ttu-id="68b9f-206">Per caricare la configurazione dall'ambiente, sono consigliabili:</span><span class="sxs-lookup"><span data-stu-id="68b9f-206">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="68b9f-207">file *appSettings* (*appsettings.\< Ambiente>.json*).</span><span class="sxs-lookup"><span data-stu-id="68b9f-207">*appsettings* files (*appsettings.\<Environment>.json*).</span></span> <span data-ttu-id="68b9f-208">Vedere [Configurazione: Provider di configurazione dei file](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="68b9f-208">See [Configuration: File configuration provider](xref:fundamentals/configuration/index#file-configuration-provider).</span></span>
* <span data-ttu-id="68b9f-209">Variabili di ambiente (impostate in ogni sistema in cui è ospitata l'app).</span><span class="sxs-lookup"><span data-stu-id="68b9f-209">environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="68b9f-210">Vedere [Configurazione: Provider di configurazione dei file](xref:fundamentals/configuration/index#file-configuration-provider) e [Archiviazione sicura di segreti dell'app durante lo sviluppo: Variabili di ambiente](xref:security/app-secrets#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="68b9f-210">See [Configuration: File configuration provider](xref:fundamentals/configuration/index#file-configuration-provider) and [Safe storage of app secrets in development: Environment variables](xref:security/app-secrets#environment-variables).</span></span>
* <span data-ttu-id="68b9f-211">Secret Manager (solo nell'ambiente di sviluppo).</span><span class="sxs-lookup"><span data-stu-id="68b9f-211">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="68b9f-212">Vedere <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="68b9f-212">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="68b9f-213">Classe Startup e metodi basati sull'ambiente</span><span class="sxs-lookup"><span data-stu-id="68b9f-213">Environment-based Startup class and methods</span></span>

### <a name="startup-class-conventions"></a><span data-ttu-id="68b9f-214">Convenzioni delle classi di avvio</span><span class="sxs-lookup"><span data-stu-id="68b9f-214">Startup class conventions</span></span>

<span data-ttu-id="68b9f-215">Quando viene avviata un'app ASP.NET Core, la [classe Startup](xref:fundamentals/startup) avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="68b9f-215">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="68b9f-216">L'app può definire classi `Startup` separate per i diversi ambienti (ad esempio, `StartupDevelopment`) e la classe `Startup` appropriata viene selezionata durante il runtime.</span><span class="sxs-lookup"><span data-stu-id="68b9f-216">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`), and the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="68b9f-217">La classe il cui suffisso di nome corrisponde all'ambiente corrente ha la priorità.</span><span class="sxs-lookup"><span data-stu-id="68b9f-217">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="68b9f-218">Se non viene trovata una classe `Startup{EnvironmentName}` corrispondente, viene usata la classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="68b9f-218">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span>

<span data-ttu-id="68b9f-219">Per implementare le classi `Startup` basate sull'ambiente, creare una classe `Startup{EnvironmentName}` per ogni ambiente in uso e una classe `Startup` di fallback:</span><span class="sxs-lookup"><span data-stu-id="68b9f-219">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}
```

<span data-ttu-id="68b9f-220">Usare l'overload [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) che accetta un nome di assembly:</span><span class="sxs-lookup"><span data-stu-id="68b9f-220">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="68b9f-221">Convenzioni dei metodi di avvio</span><span class="sxs-lookup"><span data-stu-id="68b9f-221">Startup method conventions</span></span>

<span data-ttu-id="68b9f-222">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) supportano versioni specifiche per l'ambiente con i formati `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services`:</span><span class="sxs-lookup"><span data-stu-id="68b9f-222">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`:</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="68b9f-223">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="68b9f-223">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="68b9f-224">IHostingEnvironment.EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="68b9f-224">IHostingEnvironment.EnvironmentName</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname)
