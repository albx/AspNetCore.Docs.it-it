---
title: Proteggere un' Blazor app ospitata ASP.NET Core webassembly con Identity Server
author: guardrex
description: Per creare una nuova Blazor app ospitata con autenticazione da Visual Studio che usa un back-end [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 0fdc88e8e50856fcc4da0beb74f03925ae24401e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103971"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Proteggere un' Blazor app ospitata ASP.NET Core webassembly con Identity Server

Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)

Questo articolo illustra come creare una nuova Blazor app ospitata che usa [IdentityServer](https://identityserver.io/) per autenticare gli utenti e le chiamate API.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In Visual Studio:

1. Creare una nuova app ** Blazor webassembly** . Per altre informazioni, vedere <xref:blazor/get-started>.
1. Nella finestra di dialogo **Crea una nuova Blazor app** Selezionare **modifica** nella sezione **autenticazione** .
1. Selezionare **account utente singoli** e quindi **OK**.
1. Nella sezione **Avanzate** Selezionare la casella di controllo **ASP.NET Core Hosted** .
1. Selezionare il pulsante **Crea**.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli/)

Per creare l'app in una shell dei comandi, eseguire il comando seguente:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Per specificare il percorso di output, che crea una cartella di progetto, se non esiste, includere l'opzione di output nel comando con un percorso (ad esempio, `-o BlazorSample` ). Il nome della cartella diventa anche parte del nome del progetto.

---

## <a name="server-app-configuration"></a>Configurazione dell'app Server

Le sezioni seguenti descrivono le aggiunte al progetto quando è incluso il supporto per l'autenticazione.

### <a name="startup-class"></a>Classe di avvio

La `Startup` classe presenta le aggiunte seguenti.

* In `Startup.ConfigureServices`:

  * ASP.NET Core Identity :

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer con un <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodo helper aggiuntivo che imposta le convenzioni ASP.NET Core predefinite in IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticazione con un <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metodo helper aggiuntivo che configura l'app per convalidare i token JWT prodotti da IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* In `Startup.Configure`:

  * Il middleware IdentityServer espone gli endpoint Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

  * Il middleware di autenticazione è responsabile della convalida delle credenziali della richiesta e dell'impostazione dell'utente nel contesto della richiesta:

    ```csharp
    app.UseAuthentication();
    ```

  * Il middleware di autorizzazione consente le funzionalità di autorizzazione:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Il <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodo helper Configura [IdentityServer](https://identityserver.io/) per scenari ASP.NET Core. IdentityServer è un Framework potente ed estendibile per la gestione dei problemi di sicurezza delle app. IdentityServer espone una complessità superflua per gli scenari più comuni. Di conseguenza, viene fornito un set di convenzioni e opzioni di configurazione che consideriamo un valido punto di partenza. Una volta modificate le esigenze di autenticazione, è disponibile la potenza completa di IdentityServer per personalizzare l'autenticazione per soddisfare i requisiti di un'app.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Il <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metodo helper configura uno schema di criteri per l'app come gestore di autenticazione predefinito. Il criterio è configurato in modo da consentire Identity a di gestire tutte le richieste indirizzate a qualsiasi sottopercorso nello Identity spazio URL `/Identity` . <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Gestisce tutte le altre richieste. Inoltre, questo metodo:

* Registra una `{APPLICATION NAME}API` risorsa API con IdentityServer con un ambito predefinito `{APPLICATION NAME}API` .
* Configura il middleware del token di porta JWT per convalidare i token emessi da IdentityServer per l'app.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

In `WeatherForecastController` (*Controllers/WeatherForecastController. cs*) l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo viene applicato alla classe. L'attributo indica che l'utente deve essere autorizzato in base ai criteri predefiniti per accedere alla risorsa. I criteri di autorizzazione predefiniti sono configurati per l'utilizzo dello schema di autenticazione predefinito, impostato da <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . Il metodo helper viene configurato <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> come gestore predefinito per le richieste all'app.

### <a name="applicationdbcontext"></a>ApplicationDbContext

In `ApplicationDbContext` (*Data/ApplicationDbContext. cs*) <xref:Microsoft.EntityFrameworkCore.DbContext> estende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> per includere lo schema per IdentityServer. L'oggetto <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> è derivato da <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Per ottenere il controllo completo dello schema del database, ereditare da una delle Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classi disponibili e configurare il contesto per includere lo Identity schema chiamando `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` nel <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> metodo.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

In `OidcConfigurationController` (*Controllers/OidcConfigurationController. cs*), viene eseguito il provisioning dell'endpoint client per gestire i parametri di OIDC.

### <a name="app-settings-files"></a>File di impostazioni dell'app

Nel file di impostazioni dell'app (*appsettings.json*) nella radice del progetto, la `IdentityServer` sezione descrive l'elenco dei client configurati. Nell'esempio seguente è presente un singolo client. Il nome del client corrisponde al nome dell'app e viene mappato per convenzione al `ClientId` parametro OAuth. Il profilo indica il tipo di app da configurare. Il profilo viene utilizzato internamente per guidare le convenzioni che semplificano il processo di configurazione per il server. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample.Client` ).

## <a name="client-app-configuration"></a>Configurazione dell'app client

### <a name="authentication-package"></a>Pacchetto di autenticazione

Quando viene creata un'app per l'uso di singoli account utente ( `Individual` ), l'app riceve automaticamente un riferimento al pacchetto per il pacchetto [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) nel file di progetto dell'app. Il pacchetto fornisce un set di primitive che consentono all'app di autenticare gli utenti e ottenere i token per chiamare le API protette.

Se si aggiunge l'autenticazione a un'app, aggiungere manualmente il pacchetto al file di progetto dell'app:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a>Supporto dell'autorizzazione API

Il supporto per l'autenticazione degli utenti è collegato al contenitore del servizio mediante il metodo di estensione fornito nel pacchetto [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) . Questo metodo configura i servizi richiesti dall'app per interagire con il sistema di autorizzazione esistente.

```csharp
builder.Services.AddApiAuthorization();
```

Per impostazione predefinita, la configurazione per l'app viene caricata per convenzione da `_configuration/{client-id}` . Per convenzione, l'ID client è impostato sul nome dell'assembly dell'app. Questo URL può essere modificato in modo che punti a un endpoint separato chiamando l'overload con options.

### <a name="imports-file"></a>Importa file

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Pagina di indice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Componente app

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

Il `LoginDisplay` componente (*Shared/LoginDisplay. Razor*) viene sottoposto a rendering nel `MainLayout` componente (*Shared/MainLayout. Razor*) e gestisce i comportamenti seguenti:

* Per utenti autenticati:
  * Visualizza il nome utente corrente.
  * Offre un collegamento alla pagina del profilo utente in ASP.NET Core Identity .
  * Offre un pulsante per disconnettersi dall'app.
* Per utenti anonimi:
  * Offre la possibilità di effettuare la registrazione.
  * Offre la possibilità di effettuare l'accesso.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Componente di autenticazione

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Eseguire l'app

Eseguire l'app dal progetto server. Quando si usa Visual Studio, eseguire una delle operazioni seguenti:

* Impostare l'elenco a discesa **progetti di avvio** nella barra degli strumenti sull'app per le *API server* e selezionare il pulsante **Esegui** .
* Selezionare il progetto server in **Esplora soluzioni** , quindi fare clic sul pulsante **Esegui** nella barra degli strumenti o avviare l'app dal menu **debug** .

## <a name="name-and-role-claim-with-api-authorization"></a>Nome e attestazione di ruolo con autorizzazione API

### <a name="custom-user-factory"></a>Factory utente personalizzata

Nell'app client creare una factory utente personalizzata. IdentityIl server invia più ruoli come matrice JSON in un'unica `role` attestazione. Un singolo ruolo viene inviato come valore stringa nell'attestazione. La factory crea una singola `role` attestazione per ogni ruolo dell'utente.

*CustomUserFactory.cs*:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

Nell'app client registrare la factory in `Program.Main` (*Program.cs*):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

Nell'app Server, chiamare <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> sul Identity Generatore, che aggiunge i servizi correlati ai ruoli:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>Configura Identity Server

Usare **uno** degli approcci seguenti:

* [Opzioni di autorizzazione API](#api-authorization-options)
* [Servizio profili](#profile-service)

#### <a name="api-authorization-options"></a>Opzioni di autorizzazione API

Nell'app Server:

* Configurare Identity il server in modo che inserisca le `name` `role` attestazioni e nel token ID e nel token di accesso.
* Impedire il mapping predefinito per i ruoli nel gestore del token JWT.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>Servizio profili

Nell'app Server creare un' `ProfileService` implementazione.

*ProfileService.cs*:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

Nell'app server registrare il servizio profili in `Startup.ConfigureServices` :

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Usare i meccanismi di autorizzazione

Nell'app client gli approcci di autorizzazione componenti sono funzionali a questo punto. Qualsiasi meccanismo di autorizzazione nei componenti può utilizzare un ruolo per autorizzare l'utente:

* [Componente AuthorizeView](xref:blazor/security/index#authorizeview-component) (esempio: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (esempio: `@attribute [Authorize(Roles = "admin")]` )
* [Logica procedurale](xref:blazor/security/index#procedural-logic) (esempio: `if (user.IsInRole("admin")) { ... }` )

  Sono supportati più test di ruolo:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name`viene popolato nell'app client con il nome utente dell'utente, che corrisponde in genere all'indirizzo di posta elettronica di accesso.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [Distribuzione nel servizio app Azure](xref:security/authentication/identity/spa#deploy-to-production)
* [Importare un certificato da Key Vault (documentazione di Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)