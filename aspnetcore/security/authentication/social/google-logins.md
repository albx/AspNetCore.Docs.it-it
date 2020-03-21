---
title: Configurazione dell'accesso esterno Google in ASP.NET Core
author: rick-anderson
description: Questa esercitazione illustra l'integrazione di autenticazione dell'utente account Google in un'app ASP.NET Core esistente.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
uid: security/authentication/google-logins
ms.openlocfilehash: a114d23c25201c9fe31ad0397efaf99fe98a312a
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989766"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Configurazione dell'accesso esterno Google in ASP.NET Core

Da [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Questa esercitazione illustra come consentire agli utenti di accedere con il proprio account Google usando il progetto ASP.NET Core 3,0 creato nella [pagina precedente](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Creare un progetto console e un ID client di Google API

* Installare [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).
* Passare all'articolo relativo all' [integrazione di Google Sign-in nell'app Web](https://developers.google.com/identity/sign-in/web/devconsole-project) e selezionare **configura un progetto**.
* Nella finestra di dialogo **Configura client OAuth** selezionare **server Web**.
* Nella casella voce di testo **URI di reindirizzamento autorizzato** impostare l'URI di reindirizzamento. Ad esempio, `https://localhost:44312/signin-google`.
* Salvare l' **ID client** e il **segreto client**.
* Quando si distribuisce il sito, registrare il nuovo URL pubblico dalla **console di Google**.

## <a name="store-the-google-client-id-and-secret"></a>Archiviare l'ID e il segreto del client Google

Archiviare le impostazioni riservate, ad esempio i valori di ID client e segreto di Google con [gestione Secret](xref:security/app-secrets). Per questo esempio, attenersi alla procedura seguente:

1. Inizializzare il progetto per l'archiviazione segreta in base alle istruzioni riportate in [abilitare l'archiviazione segreta](xref:security/app-secrets#enable-secret-storage).
1. Archiviare le impostazioni sensibili nell'archivio dei segreti locali con le chiavi segrete `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret`:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

È possibile gestire le credenziali e l'utilizzo dell'API nella [console API](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Configurare l'autenticazione di Google

Aggiungere il servizio Google a `Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Accedere con Google

* Eseguire l'app e fare clic su **Accedi**. Viene visualizzata un'opzione per accedere con Google.
* Fare clic sul pulsante **Google** , che reindirizza a Google per l'autenticazione.
* Dopo aver immesso le credenziali Google, viene reindirizzato di nuovo al sito Web.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Per altre informazioni sulle opzioni di configurazione supportate da Google Authentication, vedere le informazioni di riferimento sull'API <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>. Ciò può essere usato per richiedere informazioni diverse sull'utente.

## <a name="change-the-default-callback-uri"></a>Modificare l'URI di callback predefinito

Il `/signin-google` del segmento URI è impostato come callback predefinito del provider di autenticazione Google. È possibile modificare l'URI di callback predefinito durante la configurazione del middleware di autenticazione di Google tramite la proprietà [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) ereditata della classe [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) .

## <a name="troubleshooting"></a>Risoluzione dei problemi

* Se l'accesso non funziona e non si ricevono errori, passare alla modalità di sviluppo per semplificare il debug del problema.
* Se l'identità non è configurata chiamando `services.AddIdentity` in `ConfigureServices`, il tentativo di autenticare i risultati in *ArgumentException: è necessario specificare l'opzione ' SignInScheme '* . Il modello di progetto usato in questa esercitazione garantisce che questa operazione viene eseguita.
* Se il database del sito non è stato creato applicando la migrazione iniziale, si ottiene *un'operazione di database non riuscita durante l'elaborazione dell'* errore di richiesta. Selezionare **applica migrazioni** per creare il database e aggiornare la pagina per continuare a superare l'errore.

## <a name="next-steps"></a>Passaggi successivi

* Questo articolo ha illustrato come è possibile eseguire l'autenticazione con Google. È possibile seguire un approccio simile per l'autenticazione con altri provider elencati nella [pagina precedente](xref:security/authentication/social/index).
* Dopo aver pubblicato l'app in Azure, reimpostare il `ClientSecret` nella console API Google.
* Impostare il `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret` come impostazioni dell'applicazione nella portale di Azure. Il sistema di configurazione è configurato per leggere le chiavi da variabili di ambiente.
