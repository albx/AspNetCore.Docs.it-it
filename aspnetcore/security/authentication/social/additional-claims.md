---
title: Mantieni attestazioni e token aggiuntivi da provider esterni in ASP.NET Core
author: guardrex
description: Informazioni su come creare attestazioni e token aggiuntivi da provider esterni.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 72710d249d3210208dd9b0356a700ba02a0b727a
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378877"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Mantieni attestazioni e token aggiuntivi da provider esterni in ASP.NET Core

Di [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

Un'app ASP.NET Core può creare attestazioni e token aggiuntivi da provider di autenticazione esterni, ad esempio Facebook, Google, Microsoft e Twitter. Ogni provider rivela informazioni diverse sugli utenti sulla propria piattaforma, ma il modello per la ricezione e la trasformazione dei dati utente in attestazioni aggiuntive è lo stesso.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prerequisites

Decidere quali provider di autenticazione esterni supportare nell'app. Per ogni provider, registrare l'app e ottenere un ID client e un segreto client. Per ulteriori informazioni, vedere <xref:security/authentication/social/index>. L'app di esempio usa il [provider di autenticazione Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Impostare l'ID client e il segreto client

Il provider di autenticazione OAuth stabilisce una relazione di trust con un'app che usa un ID client e un segreto client. I valori relativi all'ID client e al segreto client vengono creati per l'app dal provider di autenticazione esterno quando l'app viene registrata con il provider. Ogni provider esterno usato dall'app deve essere configurato in modo indipendente con l'ID client e il segreto client del provider. Per ulteriori informazioni, vedere gli argomenti del provider di autenticazione esterno applicabili allo scenario:

* [Autenticazione Facebook](xref:security/authentication/facebook-logins)
* [Autenticazione Google](xref:security/authentication/google-logins)
* [Autenticazione Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticazione Twitter](xref:security/authentication/twitter-logins)
* [Altri provider di autenticazione](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

L'app di esempio configura il provider di autenticazione Google con un ID client e un segreto client forniti da Google:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Definire l'ambito di autenticazione

Specificare l'elenco di autorizzazioni da recuperare dal provider specificando il <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>. Gli ambiti di autenticazione per i provider esterni comuni sono riportati nella tabella seguente.

| Provider  | Scope                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

Nell'app di esempio, l'ambito `userinfo.profile` di Google viene aggiunto automaticamente dal framework quando viene chiamato <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> in <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>. Se l'app richiede ambiti aggiuntivi, aggiungerli alle opzioni. Nell'esempio seguente viene aggiunto l'ambito Google `https://www.googleapis.com/auth/user.birthday.read` per recuperare il compleanno di un utente:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Eseguire il mapping delle chiavi di dati utente e creare attestazioni

Nelle opzioni del provider specificare un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> per ogni chiave/sottochiave nei dati utente JSON del provider esterno per l'identità dell'app da leggere durante l'accesso. Per ulteriori informazioni sui tipi di attestazione, vedere <xref:System.Security.Claims.ClaimTypes>.

L'app di esempio crea attestazioni delle impostazioni locali (`urn:google:locale`) e immagini (`urn:google:picture`) dalle chiavi `locale` e `picture` nei dati utente di Google:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, un <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) viene connesso all'app con <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>. Durante il processo di accesso, il <xref:Microsoft.AspNetCore.Identity.UserManager%601> può archiviare una attestazione `ApplicationUser` per i dati utente disponibili dal <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.

Nell'app di esempio, `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. cs*) stabilisce le attestazioni delle impostazioni locali (`urn:google:locale`) e immagine (`urn:google:picture`) per l'accesso `ApplicationUser`, inclusa un'attestazione per <xref:System.Security.Claims.ClaimTypes.GivenName>:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Per impostazione predefinita, le attestazioni di un utente vengono archiviate nel cookie di autenticazione. Se il cookie di autenticazione è troppo grande, può causare un errore dell'app perché:

* Il browser rileva che l'intestazione del cookie è troppo lungo.
* La dimensione complessiva della richiesta è troppo grande.

Se per l'elaborazione delle richieste utente è richiesta una grande quantità di dati utente:

* Limitare il numero e le dimensioni delle attestazioni utente per l'elaborazione delle richieste solo ai componenti richiesti dall'app.
* Usare un <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> personalizzato per il middleware di autenticazione del cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> per archiviare l'identità tra le richieste. Mantenere grandi quantità di informazioni sull'identità sul server inviando al client solo una chiave di identificatore di sessione di piccole dimensioni.

## <a name="save-the-access-token"></a>Salvare il token di accesso

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definisce se i token di accesso e di aggiornamento devono essere archiviati nel <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> dopo una corretta autorizzazione. `SaveTokens` è impostato su `false` per impostazione predefinita per ridurre le dimensioni del cookie di autenticazione finale.

L'app di esempio imposta il valore di `SaveTokens` su `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Quando si esegue `OnPostConfirmationAsync`, archiviare il token di accesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) dal provider esterno nel `AuthenticationProperties` `ApplicationUser`.

L'app di esempio salva il token di accesso in `OnPostConfirmationAsync` (nuova registrazione utente) e `OnGetCallbackAsync` (utente registrato in precedenza) in *account/ExternalLogin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Come aggiungere token personalizzati aggiuntivi

Per illustrare come aggiungere un token personalizzato, archiviato come parte di `SaveTokens`, l'app di esempio aggiunge un <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> con il <xref:System.DateTime> corrente per un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) di `TicketCreated`:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Creazione e aggiunta di attestazioni

Il Framework fornisce azioni comuni e metodi di estensione per la creazione e l'aggiunta di attestazioni alla raccolta. Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Gli utenti possono definire azioni personalizzate derivando da <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando il metodo abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>.

Per ulteriori informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Rimozione di azioni attestazioni e attestazioni

[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) rimuove tutte le azioni di attestazione per il <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> specificato dalla raccolta. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) Elimina un'attestazione dell'<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> specificato dall'identità. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> viene usato principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) per rimuovere le attestazioni generate dal protocollo.

## <a name="sample-app-output"></a>Esempio di output dell'app

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Un'app ASP.NET Core può creare attestazioni e token aggiuntivi da provider di autenticazione esterni, ad esempio Facebook, Google, Microsoft e Twitter. Ogni provider rivela informazioni diverse sugli utenti sulla propria piattaforma, ma il modello per la ricezione e la trasformazione dei dati utente in attestazioni aggiuntive è lo stesso.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prerequisites

Decidere quali provider di autenticazione esterni supportare nell'app. Per ogni provider, registrare l'app e ottenere un ID client e un segreto client. Per ulteriori informazioni, vedere <xref:security/authentication/social/index>. L'app di esempio usa il [provider di autenticazione Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Impostare l'ID client e il segreto client

Il provider di autenticazione OAuth stabilisce una relazione di trust con un'app che usa un ID client e un segreto client. I valori relativi all'ID client e al segreto client vengono creati per l'app dal provider di autenticazione esterno quando l'app viene registrata con il provider. Ogni provider esterno usato dall'app deve essere configurato in modo indipendente con l'ID client e il segreto client del provider. Per ulteriori informazioni, vedere gli argomenti del provider di autenticazione esterno applicabili allo scenario:

* [Autenticazione Facebook](xref:security/authentication/facebook-logins)
* [Autenticazione Google](xref:security/authentication/google-logins)
* [Autenticazione Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticazione Twitter](xref:security/authentication/twitter-logins)
* [Altri provider di autenticazione](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

L'app di esempio configura il provider di autenticazione Google con un ID client e un segreto client forniti da Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Definire l'ambito di autenticazione

Specificare l'elenco di autorizzazioni da recuperare dal provider specificando il <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>. Gli ambiti di autenticazione per i provider esterni comuni sono riportati nella tabella seguente.

| Provider  | Scope                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

Nell'app di esempio, l'ambito `userinfo.profile` di Google viene aggiunto automaticamente dal framework quando viene chiamato <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> in <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>. Se l'app richiede ambiti aggiuntivi, aggiungerli alle opzioni. Nell'esempio seguente viene aggiunto l'ambito Google `https://www.googleapis.com/auth/user.birthday.read` per recuperare il compleanno di un utente:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Eseguire il mapping delle chiavi di dati utente e creare attestazioni

Nelle opzioni del provider specificare un <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> per ogni chiave/sottochiave nei dati utente JSON del provider esterno per l'identità dell'app da leggere durante l'accesso. Per ulteriori informazioni sui tipi di attestazione, vedere <xref:System.Security.Claims.ClaimTypes>.

L'app di esempio crea attestazioni delle impostazioni locali (`urn:google:locale`) e immagini (`urn:google:picture`) dalle chiavi `locale` e `picture` nei dati utente di Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, un <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) viene connesso all'app con <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>. Durante il processo di accesso, il <xref:Microsoft.AspNetCore.Identity.UserManager%601> può archiviare una attestazione `ApplicationUser` per i dati utente disponibili dal <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.

Nell'app di esempio, `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. cs*) stabilisce le attestazioni delle impostazioni locali (`urn:google:locale`) e immagine (`urn:google:picture`) per l'accesso `ApplicationUser`, inclusa un'attestazione per <xref:System.Security.Claims.ClaimTypes.GivenName>:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Per impostazione predefinita, le attestazioni di un utente vengono archiviate nel cookie di autenticazione. Se il cookie di autenticazione è troppo grande, può causare un errore dell'app perché:

* Il browser rileva che l'intestazione del cookie è troppo lungo.
* La dimensione complessiva della richiesta è troppo grande.

Se per l'elaborazione delle richieste utente è richiesta una grande quantità di dati utente:

* Limitare il numero e le dimensioni delle attestazioni utente per l'elaborazione delle richieste solo ai componenti richiesti dall'app.
* Usare un <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> personalizzato per il middleware di autenticazione del cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> per archiviare l'identità tra le richieste. Mantenere grandi quantità di informazioni sull'identità sul server inviando al client solo una chiave di identificatore di sessione di piccole dimensioni.

## <a name="save-the-access-token"></a>Salvare il token di accesso

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definisce se i token di accesso e di aggiornamento devono essere archiviati nel <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> dopo una corretta autorizzazione. `SaveTokens` è impostato su `false` per impostazione predefinita per ridurre le dimensioni del cookie di autenticazione finale.

L'app di esempio imposta il valore di `SaveTokens` su `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Quando si esegue `OnPostConfirmationAsync`, archiviare il token di accesso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) dal provider esterno nel `AuthenticationProperties` `ApplicationUser`.

L'app di esempio salva il token di accesso in `OnPostConfirmationAsync` (nuova registrazione utente) e `OnGetCallbackAsync` (utente registrato in precedenza) in *account/ExternalLogin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Come aggiungere token personalizzati aggiuntivi

Per illustrare come aggiungere un token personalizzato, archiviato come parte di `SaveTokens`, l'app di esempio aggiunge un <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> con il <xref:System.DateTime> corrente per un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) di `TicketCreated`:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Creazione e aggiunta di attestazioni

Il Framework fornisce azioni comuni e metodi di estensione per la creazione e l'aggiunta di attestazioni alla raccolta. Per altre informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> e <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Gli utenti possono definire azioni personalizzate derivando da <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando il metodo abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*>.

Per ulteriori informazioni, vedere <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Rimozione di azioni attestazioni e attestazioni

[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) rimuove tutte le azioni di attestazione per il <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> specificato dalla raccolta. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) Elimina un'attestazione dell'<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> specificato dall'identità. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> viene usato principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) per rimuovere le attestazioni generate dal protocollo.

## <a name="sample-app-output"></a>Esempio di output dell'app

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* [ASPNET/AspNetCore Engineering SocialSample app](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; l'app di esempio collegata si trova nel ramo di progettazione `master` del [repository di GitHub ASPNET/AspNetCore](https://github.com/aspnet/AspNetCore) . Il ramo `master` contiene il codice in fase di sviluppo attivo per la versione successiva di ASP.NET Core. Per visualizzare una versione dell'app di esempio per una versione rilasciata di ASP.NET Core, usare l'elenco a discesa **Branch** per selezionare un ramo di rilascio, ad esempio `release/{X.Y}`.
