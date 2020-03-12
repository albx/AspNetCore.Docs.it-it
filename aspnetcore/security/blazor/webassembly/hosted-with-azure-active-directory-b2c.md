---
title: Proteggere un'app ospitata ASP.NET Core Blazor webassembly con Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 232a4247f8bea23eec3dc35cba4659c88887124d
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083881"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="2b2e1-102">Proteggere un'app ospitata ASP.NET Core Blazor webassembly con Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="2b2e1-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="2b2e1-103">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2b2e1-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="2b2e1-104">Questo articolo descrive come creare un'app Blazor webassembly autonoma che usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="2b2e1-105">Registrare le app in AAD B2C e creare la soluzione</span><span class="sxs-lookup"><span data-stu-id="2b2e1-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="2b2e1-106">Creare un tenant</span><span class="sxs-lookup"><span data-stu-id="2b2e1-106">Create a tenant</span></span>

<span data-ttu-id="2b2e1-107">Seguire le istruzioni in [esercitazione: creare un tenant di Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) per creare un tenant di AAD B2C e registrare le informazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="2b2e1-108">AAD B2C istanza (ad esempio, `https://contoso.b2clogin.com/`, che include la barra finale)</span><span class="sxs-lookup"><span data-stu-id="2b2e1-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="2b2e1-109">AAD B2C dominio tenant, ad esempio `contoso.onmicrosoft.com`</span><span class="sxs-lookup"><span data-stu-id="2b2e1-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="2b2e1-110">Registrare un'app per le API server</span><span class="sxs-lookup"><span data-stu-id="2b2e1-110">Register a server API app</span></span>

<span data-ttu-id="2b2e1-111">Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) per registrare un'app AAD per l'app per le *API Server* nell'area **Azure Active Directory** > **registrazioni app** del portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="2b2e1-112">Selezionare **Nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-112">Select **New registration**.</span></span>
1. <span data-ttu-id="2b2e1-113">Specificare un **nome** per l'app (ad esempio, **Blazor AAD B2C server**).</span><span class="sxs-lookup"><span data-stu-id="2b2e1-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="2b2e1-114">Per i **tipi di account supportati**, selezionare **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="2b2e1-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="2b2e1-115">(multi-tenant) per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="2b2e1-116">L' *app* per le API del server non richiede un **URI di reindirizzamento** in questo scenario, quindi lasciare l'elenco a discesa impostato su **Web** e non immettere un URI di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="2b2e1-117">Verificare che siano abilitate le **autorizzazioni** > concedere l'autorizzazione **concent per l'amministratore a OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="2b2e1-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="2b2e1-118">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-118">Select **Register**.</span></span>

<span data-ttu-id="2b2e1-119">In **esporre un'API**:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="2b2e1-120">Selezionare **Aggiungi un ambito**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="2b2e1-121">Selezionare **Salva e continua**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="2b2e1-122">Specificare un **nome di ambito** , ad esempio `API.Access`.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="2b2e1-123">Fornire un **nome visualizzato** per il consenso dell'amministratore, ad esempio `Access API`.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="2b2e1-124">Fornire una **Descrizione del consenso dell'amministratore** , ad esempio `Allows the app to access server app API endpoints.`.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="2b2e1-125">Verificare che lo **stato** sia impostato su **abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="2b2e1-126">Selezionare **Aggiungi ambito**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-126">Select **Add scope**.</span></span>

<span data-ttu-id="2b2e1-127">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-127">Record the following information:</span></span>

* <span data-ttu-id="2b2e1-128">*App per le API server* ID applicazione (ID client) (ad esempio, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="2b2e1-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="2b2e1-129">ID directory (ID tenant) (ad esempio, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="2b2e1-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="2b2e1-130">*App per le API server* URI ID app (ad esempio `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, il portale di Azure potrebbe avere come valore predefinito l'ID client)</span><span class="sxs-lookup"><span data-stu-id="2b2e1-130">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="2b2e1-131">Ambito predefinito (ad esempio, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="2b2e1-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="2b2e1-132">Registrare un'app client</span><span class="sxs-lookup"><span data-stu-id="2b2e1-132">Register a client app</span></span>

<span data-ttu-id="2b2e1-133">Seguire le istruzioni riportate in [esercitazione: registrare un'applicazione in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) di nuovo per registrare un'app AAD per l' *app Client* nell'area **Azure Active Directory** > **registrazioni app** del portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-133">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="2b2e1-134">Selezionare **Nuova registrazione**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-134">Select **New registration**.</span></span>
1. <span data-ttu-id="2b2e1-135">Specificare un **nome** per l'app, ad esempio **Blazor AAD B2C client**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-135">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="2b2e1-136">Per i **tipi di account supportati**, selezionare **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="2b2e1-136">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="2b2e1-137">(multi-tenant) per questa esperienza.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-137">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="2b2e1-138">Lasciare l'elenco a discesa **URI di reindirizzamento** impostato su **Web**e specificare un uri di reindirizzamento di `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="2b2e1-139">Verificare che siano abilitate le **autorizzazioni** > concedere l'autorizzazione **concent per l'amministratore a OpenID e offline_access** .</span><span class="sxs-lookup"><span data-stu-id="2b2e1-139">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="2b2e1-140">Selezionare **Registra**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-140">Select **Register**.</span></span>

<span data-ttu-id="2b2e1-141">In **Authentication** > **configurazioni della piattaforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="2b2e1-142">Verificare che sia presente l' **URI di reindirizzamento** del `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="2b2e1-143">Per **concessione implicita**, selezionare le caselle di controllo per i token di **accesso** e i **token ID**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="2b2e1-144">Per questa esperienza sono accettabili le impostazioni predefinite rimanenti per l'app.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="2b2e1-145">Selezionare il pulsante **Salva**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-145">Select the **Save** button.</span></span>

<span data-ttu-id="2b2e1-146">In **autorizzazioni API**:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-146">In **API permissions**:</span></span>

1. <span data-ttu-id="2b2e1-147">Verificare che l'app disponga **Microsoft Graph** autorizzazione > **utente. Read** .</span><span class="sxs-lookup"><span data-stu-id="2b2e1-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="2b2e1-148">Selezionare **Aggiungi un'autorizzazione** seguita da **API personali**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="2b2e1-149">Selezionare l' *app per le API server* dalla colonna **nome** , ad esempio **Blazor AAD B2C server**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="2b2e1-150">Aprire l'elenco di **API** .</span><span class="sxs-lookup"><span data-stu-id="2b2e1-150">Open the **API** list.</span></span>
1. <span data-ttu-id="2b2e1-151">Abilitare l'accesso all'API, ad esempio `API.Access`.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="2b2e1-152">Selezionare **Aggiungi autorizzazioni**.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="2b2e1-153">Selezionare il pulsante **Concedi contenuto amministratore per {tenant Name}** .</span><span class="sxs-lookup"><span data-stu-id="2b2e1-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="2b2e1-154">Selezionare **Sì** per confermare.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="2b2e1-155">In **Home** > **Azure ad B2C** > **flussi utente**:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-155">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="2b2e1-156">Creare un flusso utente di iscrizione e accesso</span><span class="sxs-lookup"><span data-stu-id="2b2e1-156">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="2b2e1-157">Registrare le seguenti informazioni:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-157">Record the following information:</span></span>

* <span data-ttu-id="2b2e1-158">Registrare l'ID applicazione dell' *app client* (ID client), ad esempio `33333333-3333-3333-3333-333333333333`.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-158">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="2b2e1-159">Registrare il nome del flusso utente di iscrizione e accesso creato per l'app, ad esempio `B2C_1_signupsignin`.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-159">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="2b2e1-160">Creare l'app</span><span class="sxs-lookup"><span data-stu-id="2b2e1-160">Create the app</span></span>

<span data-ttu-id="2b2e1-161">Sostituire i segnaposto nel comando seguente con le informazioni registrate in precedenza ed eseguire il comando in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-161">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="2b2e1-162">Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso, ad esempio `-o BlazorSample`.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-162">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="2b2e1-163">Il nome della cartella diventa anche parte del nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-163">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="2b2e1-164">Configurazione dell'app Server</span><span class="sxs-lookup"><span data-stu-id="2b2e1-164">Server app configuration</span></span>

<span data-ttu-id="2b2e1-165">*Questa sezione è relativa all'app **Server** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="2b2e1-165">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="2b2e1-166">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="2b2e1-166">Authentication package</span></span>

<span data-ttu-id="2b2e1-167">Il supporto per l'autenticazione e l'autorizzazione delle chiamate a ASP.NET Core API Web viene fornito dal `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-167">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="2b2e1-168">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="2b2e1-168">Authentication service support</span></span>

<span data-ttu-id="2b2e1-169">Il metodo `AddAuthentication` configura i servizi di autenticazione all'interno dell'app e configura il gestore di JWT Bearer come metodo di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-169">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="2b2e1-170">Il metodo `AddAzureADBearer` imposta i parametri specifici nel gestore di JWT Bearer necessario per convalidare i token emessi dall'Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-170">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="2b2e1-171">`UseAuthentication` e `UseAuthorization` assicurarsi che:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-171">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="2b2e1-172">L'app tenta di analizzare e convalidare i token nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-172">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="2b2e1-173">Eventuali richieste che tentano di accedere a una risorsa protetta senza credenziali appropriate hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-173">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="2b2e1-174">Impostazioni app</span><span class="sxs-lookup"><span data-stu-id="2b2e1-174">App settings</span></span>

<span data-ttu-id="2b2e1-175">Il file *appSettings. JSON* contiene le opzioni per configurare il gestore di connessione JWT usato per convalidare i token di accesso.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="2b2e1-176">Controller WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="2b2e1-176">WeatherForecast controller</span></span>

<span data-ttu-id="2b2e1-177">Il controller WeatherForecast (*Controllers/WeatherForecastController. cs*) espone un'API protetta con l'attributo `[Authorize]` applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-177">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="2b2e1-178">È **importante** comprendere che:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-178">It's **important** to understand that:</span></span>

* <span data-ttu-id="2b2e1-179">L'attributo `[Authorize]` in questo controller API è l'unico elemento che protegge questa API da accessi non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-179">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="2b2e1-180">L'attributo `[Authorize]` usato nell'app Blazor webassembly funge solo da hint per l'app che l'utente deve essere autorizzato affinché l'app funzioni correttamente.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-180">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="2b2e1-181">Configurazione dell'app client</span><span class="sxs-lookup"><span data-stu-id="2b2e1-181">Client app configuration</span></span>

<span data-ttu-id="2b2e1-182">*Questa sezione riguarda l'app **client** della soluzione.*</span><span class="sxs-lookup"><span data-stu-id="2b2e1-182">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="2b2e1-183">Pacchetto di autenticazione</span><span class="sxs-lookup"><span data-stu-id="2b2e1-183">Authentication package</span></span>

<span data-ttu-id="2b2e1-184">Quando viene creata un'app per usare un singolo account B2C (`IndividualB2C`), l'app riceve automaticamente un riferimento al pacchetto per [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="2b2e1-184">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="2b2e1-185">Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-185">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="2b2e1-186">Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-186">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="2b2e1-187">Sostituire `{VERSION}` nel riferimento al pacchetto precedente con la versione del pacchetto `Microsoft.AspNetCore.Blazor.Templates` illustrato nell'articolo <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-187">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="2b2e1-188">Il pacchetto di `Microsoft.Authentication.WebAssembly.Msal` aggiunge in modo transitivo il pacchetto di `Microsoft.AspNetCore.Components.WebAssembly.Authentication` all'app.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-188">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="2b2e1-189">Supporto del servizio di autenticazione</span><span class="sxs-lookup"><span data-stu-id="2b2e1-189">Authentication service support</span></span>

<span data-ttu-id="2b2e1-190">Il supporto per l'autenticazione degli utenti viene registrato nel contenitore del servizio con il metodo di estensione `AddMsalAuthentication` fornito dal pacchetto di `Microsoft.Authentication.WebAssembly.Msal`.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-190">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="2b2e1-191">Questo metodo configura tutti i servizi necessari per l'interazione dell'app con il provider di identità (IP).</span><span class="sxs-lookup"><span data-stu-id="2b2e1-191">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="2b2e1-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{DEFAULT SCOPE}");
});
```

<span data-ttu-id="2b2e1-193">Il metodo `AddMsalAuthentication` accetta un callback per configurare i parametri necessari per autenticare un'app.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-193">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="2b2e1-194">Quando si registra l'app, è possibile ottenere i valori necessari per la configurazione dell'app dalla configurazione di AAD del portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-194">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="2b2e1-195">Il modello di Blazor webassembly configura automaticamente l'app in modo da richiedere un token di accesso per un'API protetta per l'ambito predefinito fornito al comando `dotnet new` (`{APP ID URI}/{DEFAULT SCOPE}`).</span><span class="sxs-lookup"><span data-stu-id="2b2e1-195">The Blazor WebAssembly template automatically configures the app to request an access token for a secure API for the default scope provided to the `dotnet new` command (`{APP ID URI}/{DEFAULT SCOPE}`).</span></span>

<span data-ttu-id="2b2e1-196">Gli ambiti dei token di accesso predefiniti rappresentano l'elenco degli ambiti dei token di accesso:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-196">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="2b2e1-197">Incluso per impostazione predefinita nella richiesta di accesso.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-197">Included by default in the sign in request.</span></span>
* <span data-ttu-id="2b2e1-198">Utilizzato per eseguire il provisioning di un token di accesso immediatamente dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-198">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="2b2e1-199">Tutti gli ambiti devono appartenere alla stessa app per ogni regola di Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="2b2e1-199">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="2b2e1-200">Per altre app per le API è possibile aggiungere altri ambiti, se necessario:</span><span class="sxs-lookup"><span data-stu-id="2b2e1-200">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="2b2e1-201">Pagina di indice</span><span class="sxs-lookup"><span data-stu-id="2b2e1-201">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="2b2e1-202">Componente app</span><span class="sxs-lookup"><span data-stu-id="2b2e1-202">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="2b2e1-203">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="2b2e1-203">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="2b2e1-204">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="2b2e1-204">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="2b2e1-205">Componente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="2b2e1-205">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="2b2e1-206">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="2b2e1-206">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="2b2e1-207">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2b2e1-207">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="2b2e1-208">Esercitazione: Creare un tenant di Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="2b2e1-208">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
