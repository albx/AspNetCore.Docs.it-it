---
title: Autenticare gli utenti con WS-Federation in ASP.NET Core
author: chlowell
description: Questa esercitazione illustra come usare WS-Federation in un'app ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: d82421a14ede6cb6b01ef59f233bb2eba6b56aec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655428"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="eb499-103">Autenticare gli utenti con WS-Federation in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb499-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="eb499-104">Questa esercitazione illustra come consentire agli utenti di accedere con un provider di autenticazione WS-Federation, ad esempio Active Directory Federation Services (ADFS) o [Azure Active Directory](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="eb499-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="eb499-105">Usa l'app di esempio ASP.NET Core 2,0 descritta in [Facebook, Google e l'autenticazione del provider esterno](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="eb499-105">It uses the ASP.NET Core 2.0 sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="eb499-106">Per ASP.NET Core app 2,0, il supporto di WS-Federation viene fornito da [Microsoft. AspNetCore. Authentication. WSFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span><span class="sxs-lookup"><span data-stu-id="eb499-106">For ASP.NET Core 2.0 apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="eb499-107">Questo componente viene trasferito da [Microsoft. Owin. Security. WSFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) e condivide molti dei meccanismi di tale componente.</span><span class="sxs-lookup"><span data-stu-id="eb499-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="eb499-108">Tuttavia, i componenti differiscono in un paio di modi importanti.</span><span class="sxs-lookup"><span data-stu-id="eb499-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="eb499-109">Per impostazione predefinita, il nuovo middleware:</span><span class="sxs-lookup"><span data-stu-id="eb499-109">By default, the new middleware:</span></span>

* <span data-ttu-id="eb499-110">Non consente accessi non richiesti.</span><span class="sxs-lookup"><span data-stu-id="eb499-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="eb499-111">Questa funzionalità del protocollo WS-Federation è vulnerabile agli attacchi XSRF.</span><span class="sxs-lookup"><span data-stu-id="eb499-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="eb499-112">Tuttavia, può essere abilitata con l'opzione `AllowUnsolicitedLogins`.</span><span class="sxs-lookup"><span data-stu-id="eb499-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="eb499-113">Non controlla ogni post di form per i messaggi di accesso.</span><span class="sxs-lookup"><span data-stu-id="eb499-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="eb499-114">Vengono verificate solo le richieste al `CallbackPath` per gli accessi. `CallbackPath` valori predefiniti `/signin-wsfed` ma possono essere modificati tramite la proprietà [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) .</span><span class="sxs-lookup"><span data-stu-id="eb499-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="eb499-115">Questo percorso può essere condiviso con altri provider di autenticazione abilitando l'opzione [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .</span><span class="sxs-lookup"><span data-stu-id="eb499-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="eb499-116">Registrare l'app con Active Directory</span><span class="sxs-lookup"><span data-stu-id="eb499-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="eb499-117">Active Directory Federation Services</span><span class="sxs-lookup"><span data-stu-id="eb499-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="eb499-118">Aprire l' **Aggiunta guidata del trust della relying party** dalla console di gestione di ADFS:</span><span class="sxs-lookup"><span data-stu-id="eb499-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Aggiunta guidata trust della relying party: Benvenuti](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="eb499-120">Scegliere di immettere manualmente i dati:</span><span class="sxs-lookup"><span data-stu-id="eb499-120">Choose to enter data manually:</span></span>

![Aggiunta guidata attendibilità relying party: selezionare l'origine dati](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="eb499-122">Immettere un nome visualizzato per il relying party.</span><span class="sxs-lookup"><span data-stu-id="eb499-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="eb499-123">Il nome non è importante per l'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eb499-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="eb499-124">[Microsoft. AspNetCore. Authentication. WSFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) non dispone del supporto per la crittografia dei token, quindi non configurare un certificato di crittografia del token:</span><span class="sxs-lookup"><span data-stu-id="eb499-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Aggiunta guidata trust della relying party: configurare il certificato](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="eb499-126">Abilitare il supporto per il protocollo passivo WS-Federation usando l'URL dell'app.</span><span class="sxs-lookup"><span data-stu-id="eb499-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="eb499-127">Verificare che la porta sia corretta per l'app:</span><span class="sxs-lookup"><span data-stu-id="eb499-127">Verify the port is correct for the app:</span></span>

![Aggiunta guidata attendibilità relying party: Configura URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="eb499-129">Deve essere un URL HTTPS.</span><span class="sxs-lookup"><span data-stu-id="eb499-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="eb499-130">IIS Express possibile fornire un certificato autofirmato quando si ospita l'app durante lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="eb499-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="eb499-131">Il gheppio richiede la configurazione manuale del certificato.</span><span class="sxs-lookup"><span data-stu-id="eb499-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="eb499-132">Per altri dettagli, vedere la [documentazione di Gheppio](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="eb499-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="eb499-133">Fare clic su **Avanti** nel resto della procedura guidata e **chiudere** alla fine.</span><span class="sxs-lookup"><span data-stu-id="eb499-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="eb499-134">Per ASP.NET Core identità è necessaria un'attestazione **ID nome** .</span><span class="sxs-lookup"><span data-stu-id="eb499-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="eb499-135">Aggiungerne uno dalla finestra di dialogo **modifica regole attestazione** :</span><span class="sxs-lookup"><span data-stu-id="eb499-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Modifica delle regole delle attestazioni](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="eb499-137">Nella **procedura guidata Aggiungi regola attestazione di trasformazione**lasciare selezionata l'opzione predefinita **Invia attributi LDAP come modello di attestazioni** e fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="eb499-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="eb499-138">Aggiungere una regola mapping dell'attributo LDAP **SAM-account-name** all'attestazione in uscita **ID nome** :</span><span class="sxs-lookup"><span data-stu-id="eb499-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Aggiunta guidata regola attestazione di trasformazione: Configura regola attestazione](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="eb499-140">Fare clic su **fine** > **OK** nella finestra **modifica regole attestazione** .</span><span class="sxs-lookup"><span data-stu-id="eb499-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="eb499-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="eb499-141">Azure Active Directory</span></span>

* <span data-ttu-id="eb499-142">Passare al pannello registrazioni per l'app del tenant AAD.</span><span class="sxs-lookup"><span data-stu-id="eb499-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="eb499-143">Fare clic su **registrazione nuova applicazione**:</span><span class="sxs-lookup"><span data-stu-id="eb499-143">Click **New application registration**:</span></span>

![Azure Active Directory: Registrazioni app](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="eb499-145">Immettere un nome per la registrazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="eb499-145">Enter a name for the app registration.</span></span> <span data-ttu-id="eb499-146">Questa operazione non è importante per l'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eb499-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="eb499-147">Immettere l'URL su cui è in ascolto l'app come **URL di accesso**:</span><span class="sxs-lookup"><span data-stu-id="eb499-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: creare la registrazione dell'app](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="eb499-149">Fare clic su **endpoint** e prendere nota dell'URL del **documento dei metadati di Federazione** .</span><span class="sxs-lookup"><span data-stu-id="eb499-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="eb499-150">Si tratta dell'`MetadataAddress`del middleware WS-Federation:</span><span class="sxs-lookup"><span data-stu-id="eb499-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: endpoint](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="eb499-152">Passare alla registrazione della nuova app.</span><span class="sxs-lookup"><span data-stu-id="eb499-152">Navigate to the new app registration.</span></span> <span data-ttu-id="eb499-153">Fare clic su **impostazioni** > **Proprietà** e prendere nota dell' **URI ID app**.</span><span class="sxs-lookup"><span data-stu-id="eb499-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="eb499-154">Si tratta dell'`Wtrealm`del middleware WS-Federation:</span><span class="sxs-lookup"><span data-stu-id="eb499-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: Proprietà registrazione app](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="eb499-156">Usare WS-Federation senza ASP.NET Core identità</span><span class="sxs-lookup"><span data-stu-id="eb499-156">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="eb499-157">Il middleware WS-Federation può essere utilizzato senza Identity.</span><span class="sxs-lookup"><span data-stu-id="eb499-157">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="eb499-158">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="eb499-158">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="eb499-159">Aggiungere WS-Federation come provider di accesso esterno per ASP.NET Core identità</span><span class="sxs-lookup"><span data-stu-id="eb499-159">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="eb499-160">Aggiungere una dipendenza da [Microsoft. AspNetCore. Authentication. WSFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) al progetto.</span><span class="sxs-lookup"><span data-stu-id="eb499-160">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="eb499-161">Aggiungere WS-Federation a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="eb499-161">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="eb499-162">Accedere con WS-Federation</span><span class="sxs-lookup"><span data-stu-id="eb499-162">Log in with WS-Federation</span></span>

<span data-ttu-id="eb499-163">Passare all'app e fare clic sul collegamento **Accedi** nell'intestazione NAV.</span><span class="sxs-lookup"><span data-stu-id="eb499-163">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="eb499-164">È disponibile un'opzione per eseguire l'accesso con WsFederation: ![pagina di accesso](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="eb499-164">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="eb499-165">Con ADFS come provider, il pulsante reindirizza a una pagina di accesso ad ad FS: ![pagina di accesso ad ADFS](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="eb499-165">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="eb499-166">Con Azure Active Directory come provider, il pulsante reindirizza a una pagina di accesso di AAD: ![pagina di accesso di AAD](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="eb499-166">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="eb499-167">Un accesso riuscito per un nuovo utente reindirizza alla pagina di registrazione dell'utente dell'app: ![pagina Register](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="eb499-167">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>