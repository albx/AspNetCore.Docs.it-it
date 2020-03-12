---
title: Proteggere un'app ASP.NET Core Blazor webassembly autonomamente con gli account Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 6883af3486256e7c6905626d8da09e8ae0c4a896
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083825"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="69bb0-102">Proteggere un'app ASP.NET Core Blazor webassembly autonomamente con gli account Microsoft</span><span class="sxs-lookup"><span data-stu-id="69bb0-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="69bb0-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="69bb0-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="69bb0-104">Per creare un'app Blazor webassembly autonoma che usa gli [account Microsoft con Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="69bb0-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="69bb0-105">Creare un'applicazione Web e un tenant di AAD</span><span class="sxs-lookup"><span data-stu-id="69bb0-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="69bb0-106">Registrare un'app AAD nell'area **Azure Active Directory** > **registrazioni app** del portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="69bb0-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="69bb0-107">1 \.</span><span class="sxs-lookup"><span data-stu-id="69bb0-107">1\.</span></span> <span data-ttu-id="69bb0-108">Specificare un **nome** per l'app (ad esempio, **Blazor AAD client**).</span><span class="sxs-lookup"><span data-stu-id="69bb0-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="69bb0-109">2 \.</span><span class="sxs-lookup"><span data-stu-id="69bb0-109">2\.</span></span> <span data-ttu-id="69bb0-110">In **tipi di account supportati**selezionare **account in qualsiasi directory dell'organizzazione**.</span><span class="sxs-lookup"><span data-stu-id="69bb0-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="69bb0-111">3 \.</span><span class="sxs-lookup"><span data-stu-id="69bb0-111">3\.</span></span> <span data-ttu-id="69bb0-112">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare un uri di reindirizzamento di `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="69bb0-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="69bb0-113">4 \.</span><span class="sxs-lookup"><span data-stu-id="69bb0-113">4\.</span></span> <span data-ttu-id="69bb0-114">Disabilitare le **autorizzazioni** > la casella **di controllo Concedi a OpenID e offline_access le autorizzazioni** .</span><span class="sxs-lookup"><span data-stu-id="69bb0-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="69bb0-115">5 \.</span><span class="sxs-lookup"><span data-stu-id="69bb0-115">5\.</span></span> <span data-ttu-id="69bb0-116">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="69bb0-116">Select **Register**.</span></span>

   <span data-ttu-id="69bb0-117">In **Authentication** > **configurazioni della piattaforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="69bb0-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="69bb0-118">1 \.</span><span class="sxs-lookup"><span data-stu-id="69bb0-118">1\.</span></span> <span data-ttu-id="69bb0-119">Verificare che sia presente l' **URI di reindirizzamento** del `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="69bb0-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="69bb0-120">2 \.</span><span class="sxs-lookup"><span data-stu-id="69bb0-120">2\.</span></span> <span data-ttu-id="69bb0-121">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="69bb0-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="69bb0-122">3 \.</span><span class="sxs-lookup"><span data-stu-id="69bb0-122">3\.</span></span> <span data-ttu-id="69bb0-123">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="69bb0-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="69bb0-124">4 \.</span><span class="sxs-lookup"><span data-stu-id="69bb0-124">4\.</span></span> <span data-ttu-id="69bb0-125">Selezionare il pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="69bb0-125">Select the **Save** button.</span></span>

   <span data-ttu-id="69bb0-126">Registrare l'ID applicazione (ID client), ad esempio `11111111-1111-1111-1111-111111111111`.</span><span class="sxs-lookup"><span data-stu-id="69bb0-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="69bb0-127">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="69bb0-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="69bb0-128">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso, ad esempio `-o BlazorSample`.</span><span class="sxs-lookup"><span data-stu-id="69bb0-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="69bb0-129">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="69bb0-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="69bb0-130">Dopo aver creato l'app, dovrebbe essere possibile:</span><span class="sxs-lookup"><span data-stu-id="69bb0-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="69bb0-131">Accedere all'app usando un account Microsoft.</span><span class="sxs-lookup"><span data-stu-id="69bb0-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="69bb0-132">Richiedere i token di accesso per le API Microsoft usando lo stesso approccio usato per le app Blazor autonome purché l'app sia stata configurata correttamente.</span><span class="sxs-lookup"><span data-stu-id="69bb0-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="69bb0-133">Per altre informazioni, vedere [Guida introduttiva: configurare un'applicazione per esporre le API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="69bb0-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="69bb0-134">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="69bb0-134">Authentication package</span></span>

<span data-ttu-id="69bb0-135">Quando viene creata un'app per usare gli account aziendali o dell'Istituto di istruzione (`SingleOrg`), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="69bb0-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="69bb0-136">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="69bb0-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="69bb0-137">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="69bb0-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="69bb0-138">Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del pacchetto `Microsoft.AspNetCore.Blazor.Templates` illustrato nell'articolo <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="69bb0-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="69bb0-139">Il pacchetto di `Microsoft.Authentication.WebAssembly.Msal` aggiunge in modo transitivo il pacchetto di `Microsoft.AspNetCore.Components.WebAssembly.Authentication` all'app.</span><span class="sxs-lookup"><span data-stu-id="69bb0-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="69bb0-140">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="69bb0-140">Authentication service support</span></span>

<span data-ttu-id="69bb0-141">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il metodo di estensione `AddMsalAuthentication` fornito dal pacchetto di `Microsoft.Authentication.WebAssembly.Msal`.</span><span class="sxs-lookup"><span data-stu-id="69bb0-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="69bb0-142">Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il provider di identità (IP).</span><span class="sxs-lookup"><span data-stu-id="69bb0-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="69bb0-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="69bb0-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="69bb0-144">Il metodo `AddMsalAuthentication` accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="69bb0-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="69bb0-145">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione degli account Microsoft.</span><span class="sxs-lookup"><span data-stu-id="69bb0-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="index-page"></a><span data-ttu-id="69bb0-146">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="69bb0-146">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="69bb0-147">Componente app</span><span class="sxs-lookup"><span data-stu-id="69bb0-147">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="69bb0-148">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="69bb0-148">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="69bb0-149">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="69bb0-149">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="69bb0-150">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="69bb0-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="69bb0-151">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="69bb0-151">Additional resources</span></span>

* [<span data-ttu-id="69bb0-152">Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft</span><span class="sxs-lookup"><span data-stu-id="69bb0-152">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="69bb0-153">Guida introduttiva: configurare un'applicazione per esporre le API Web</span><span class="sxs-lookup"><span data-stu-id="69bb0-153">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
