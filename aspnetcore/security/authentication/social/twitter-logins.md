---
title: Configurazione dell'accesso esterno a Twitter con ASP.NET Core
author: rick-anderson
description: Questa esercitazione illustra l'integrazione dell'autenticazione utente dell'account Twitter in un'app ASP.NET Core esistente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: b848486415fd72ce6180b4cf8fc1ba00410d694a
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989747"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="d7d57-103">Configurazione dell'accesso esterno a Twitter con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7d57-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="d7d57-104">Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d7d57-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d7d57-105">Questo esempio illustra come consentire agli utenti di [accedere con il proprio account Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) usando un progetto ASP.NET Core 3,0 di esempio creato nella [pagina precedente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d7d57-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="d7d57-106">Creare l'app in Twitter</span><span class="sxs-lookup"><span data-stu-id="d7d57-106">Create the app in Twitter</span></span>

* <span data-ttu-id="d7d57-107">Aggiungere il pacchetto NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) al progetto.</span><span class="sxs-lookup"><span data-stu-id="d7d57-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="d7d57-108">Passare a [https://apps.twitter.com/](https://apps.twitter.com/) ed eseguire l'accesso.</span><span class="sxs-lookup"><span data-stu-id="d7d57-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="d7d57-109">Se non si ha già un account Twitter, usare il collegamento **[Iscriviti ora](https://twitter.com/signup)** per crearne uno.</span><span class="sxs-lookup"><span data-stu-id="d7d57-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="d7d57-110">Selezionare **Create an app** (Crea un'app).</span><span class="sxs-lookup"><span data-stu-id="d7d57-110">Select **Create an app**.</span></span> <span data-ttu-id="d7d57-111">Compilare il **nome dell'app**, la **Descrizione dell'applicazione** e l'URI del **sito Web** pubblico. questa operazione può essere temporanea fino a quando non si registra il nome di dominio:</span><span class="sxs-lookup"><span data-stu-id="d7d57-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="d7d57-112">Selezionare la casella accanto a **Abilita accesso con Twitter**</span><span class="sxs-lookup"><span data-stu-id="d7d57-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="d7d57-113">Per impostazione predefinita, Microsoft. AspNetCore. Identity richiede che gli utenti dispongano di un indirizzo di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="d7d57-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="d7d57-114">Passare alla scheda **autorizzazioni** , fare clic sul pulsante **modifica** e selezionare la casella accanto a **Richiedi indirizzo di posta elettronica dagli utenti**.</span><span class="sxs-lookup"><span data-stu-id="d7d57-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="d7d57-115">Immettere l'URI di sviluppo con `/signin-twitter` accodato nel campo **URL di callback** (ad esempio: `https://webapp128.azurewebsites.net/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="d7d57-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="d7d57-116">Lo schema di autenticazione di Twitter configurato più avanti in questo esempio gestirà automaticamente le richieste in `/signin-twitter` route per implementare il flusso OAuth.</span><span class="sxs-lookup"><span data-stu-id="d7d57-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="d7d57-117">Il `/signin-twitter` del segmento URI è impostato come callback predefinito del provider di autenticazione Twitter.</span><span class="sxs-lookup"><span data-stu-id="d7d57-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="d7d57-118">È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione Twitter tramite la proprietà [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .</span><span class="sxs-lookup"><span data-stu-id="d7d57-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="d7d57-119">Compilare il resto del modulo e selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="d7d57-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="d7d57-120">Vengono visualizzati i dettagli della nuova applicazione:</span><span class="sxs-lookup"><span data-stu-id="d7d57-120">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="d7d57-121">Archiviare la chiave e il segreto API del consumer di Twitter</span><span class="sxs-lookup"><span data-stu-id="d7d57-121">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="d7d57-122">Archiviare le impostazioni riservate, ad esempio la chiave API del consumer di Twitter e il segreto con [gestione Secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d7d57-122">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="d7d57-123">Per questo esempio, attenersi alla procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="d7d57-123">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="d7d57-124">Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni riportate in [abilitare l'archiviazione segreta](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="d7d57-124">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="d7d57-125">Archiviare le impostazioni sensibili nell'archivio dei segreti locali con le chiavi dei segreti `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret`:</span><span class="sxs-lookup"><span data-stu-id="d7d57-125">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d7d57-126">Questi token si trovano nella scheda **chiavi e token di accesso** dopo la creazione di una nuova applicazione Twitter:</span><span class="sxs-lookup"><span data-stu-id="d7d57-126">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="d7d57-127">Configurare l'autenticazione di Twitter</span><span class="sxs-lookup"><span data-stu-id="d7d57-127">Configure Twitter Authentication</span></span>

<span data-ttu-id="d7d57-128">Aggiungere il servizio Twitter nel metodo `ConfigureServices` nel file *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="d7d57-128">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="d7d57-129">Vedere le informazioni di riferimento sull'API [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) per altre informazioni sulle opzioni di configurazione supportate dall'autenticazione Twitter.</span><span class="sxs-lookup"><span data-stu-id="d7d57-129">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="d7d57-130">Ciò può essere usato per richiedere informazioni diverse sull'utente.</span><span class="sxs-lookup"><span data-stu-id="d7d57-130">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="d7d57-131">Accedi con Twitter</span><span class="sxs-lookup"><span data-stu-id="d7d57-131">Sign in with Twitter</span></span>

<span data-ttu-id="d7d57-132">Eseguire l'app e selezionare **Accedi**.</span><span class="sxs-lookup"><span data-stu-id="d7d57-132">Run the app and select **Log in**.</span></span> <span data-ttu-id="d7d57-133">Viene visualizzata un'opzione per accedere con Twitter:</span><span class="sxs-lookup"><span data-stu-id="d7d57-133">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="d7d57-134">Clic su **Twitter** reindirizza a Twitter per l'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="d7d57-134">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="d7d57-135">Dopo aver immesso le credenziali di Twitter, viene reindirizzato di nuovo al sito Web in cui è possibile impostare la posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="d7d57-135">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="d7d57-136">A questo punto è stato effettuato l'accesso con le credenziali di Twitter:</span><span class="sxs-lookup"><span data-stu-id="d7d57-136">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="d7d57-137">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="d7d57-137">Troubleshooting</span></span>

* <span data-ttu-id="d7d57-138">**Solo ASP.NET Core 2. x:** Se l'identità non è configurata chiamando `services.AddIdentity` in `ConfigureServices`, il tentativo di eseguire l'autenticazione determinerà *ArgumentException: è necessario fornire l'opzione ' SignInScheme '* .</span><span class="sxs-lookup"><span data-stu-id="d7d57-138">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="d7d57-139">Il modello di progetto utilizzato in questo esempio garantisce che questa operazione venga eseguita.</span><span class="sxs-lookup"><span data-stu-id="d7d57-139">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="d7d57-140">Se il database del sito non è stato creato applicando la migrazione iniziale, si otterrà *un'operazione di database non riuscita durante l'elaborazione dell'* errore di richiesta.</span><span class="sxs-lookup"><span data-stu-id="d7d57-140">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="d7d57-141">Toccare **applica migrazioni** per creare il database e aggiornare per continuare a superare l'errore.</span><span class="sxs-lookup"><span data-stu-id="d7d57-141">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d7d57-142">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="d7d57-142">Next steps</span></span>

* <span data-ttu-id="d7d57-143">Questo articolo ha illustrato come è possibile eseguire l'autenticazione con Twitter.</span><span class="sxs-lookup"><span data-stu-id="d7d57-143">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="d7d57-144">È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d7d57-144">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="d7d57-145">Dopo aver pubblicato il sito Web nell'app Web di Azure, è necessario reimpostare il `ConsumerSecret` nel portale per sviluppatori di Twitter.</span><span class="sxs-lookup"><span data-stu-id="d7d57-145">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="d7d57-146">Impostare il `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret` come impostazioni dell'applicazione nella portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="d7d57-146">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="d7d57-147">Il sistema di configurazione è configurato per leggere le chiavi da variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="d7d57-147">The configuration system is set up to read keys from environment variables.</span></span>
