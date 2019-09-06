---
title: Usa autenticazione cookie senza ASP.NET Core identità
author: rick-anderson
description: Informazioni su come usare l'autenticazione tramite cookie senza ASP.NET Core identità.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/20/2019
uid: security/authentication/cookie
ms.openlocfilehash: 76c7fc20c8870668ca7c65d975e2ed59f40f7dc8
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384825"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>Usa autenticazione cookie senza ASP.NET Core identità

Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core identità è un provider di autenticazione completo e completo per la creazione e la gestione degli account di accesso. Tuttavia, è possibile usare un provider di autenticazione basata su cookie senza ASP.NET Core identità. Per altre informazioni, vedere <xref:security/authentication/identity>.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

A scopo dimostrativo nell'app di esempio, l'account utente per l'utente ipotetico, Maria Rodriguez, è hardcoded nell'app. Usare l'indirizzo `maria.rodriguez@contoso.com` di **posta elettronica** e qualsiasi password per accedere all'utente. L'utente viene autenticato nel `AuthenticateUser` metodo nel file *pages/account/login. cshtml. cs* . In un esempio reale, l'utente viene autenticato a fronte di un database.

## <a name="configuration"></a>Configurazione

Se l'app non usa il [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), creare un riferimento al pacchetto nel file di progetto per il pacchetto [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

Nel metodo creare i servizi del middleware di autenticazione con i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> metodi <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>e: `Startup.ConfigureServices`

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passato a `AddAuthentication` imposta lo schema di autenticazione predefinito per l'app. `AuthenticationScheme`è utile quando sono presenti più istanze dell'autenticazione dei cookie e si desidera [autorizzare con uno schema specifico](xref:security/authorization/limitingidentitybyscheme). L' `AuthenticationScheme` impostazione di su [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornisce il valore "cookie" per lo schema. È possibile specificare qualsiasi valore stringa che distingua lo schema.

Lo schema di autenticazione dell'app è diverso dallo schema di autenticazione dei cookie dell'app. Quando non viene fornito uno schema di autenticazione <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>dei cookie a `CookieAuthenticationDefaults.AuthenticationScheme` , viene usato ("cookie").

La <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> proprietà del cookie di autenticazione è impostata `true` su per impostazione predefinita. I cookie di autenticazione sono consentiti quando un visitatore del sito non ha acconsentito alla raccolta dei dati. Per altre informazioni, vedere <xref:security/gdpr#essential-cookies>.

In `Startup.Configure`, chiamare `UseAuthentication` e `UseAuthorization` per impostare la `HttpContext.User` proprietà ed eseguire il middleware di autorizzazione per le richieste. Chiamare i `UseAuthentication` metodi `UseAuthorization` e prima di `UseEndpoints`chiamare:

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe viene utilizzata per configurare le opzioni del provider di autenticazione.

Impostare `CookieAuthenticationOptions` nella configurazione del servizio per l'autenticazione `Startup.ConfigureServices` nel metodo:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Middleware dei criteri dei cookie

Il [middleware dei criteri](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) dei cookie Abilita le funzionalità dei criteri dei cookie. L'aggiunta del middleware alla pipeline di elaborazione delle app è&mdash;sensibile agli ordini, ma interessa solo i componenti downstream registrati nella pipeline.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Usare <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornito al middleware dei criteri dei cookie per controllare le caratteristiche globali dell'elaborazione dei cookie e l'hook nei gestori di elaborazione dei cookie quando i cookie vengono aggiunti o eliminati.

Il valore <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> predefinito è `SameSiteMode.Lax` consentire l'autenticazione OAuth2. Per applicare rigorosamente un criterio dello stesso sito `SameSiteMode.Strict`di, impostare `MinimumSameSitePolicy`il. Sebbene questa impostazione interrompa OAuth2 e altri schemi di autenticazione tra le origini, eleva il livello di sicurezza dei cookie per altri tipi di app che non si basano sull'elaborazione di richieste tra origini.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

L'impostazione del middleware per i `MinimumSameSitePolicy` criteri dei cookie per può `Cookie.SameSite` influire sull'impostazione di in impostazioni in `CookieAuthenticationOptions` base alla matrice riportata di seguito.

| MinimumSameSitePolicy | Cookie.SameSite | Impostazione cookie. navigava sullostesso sito risultante |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Creare un cookie di autenticazione

Per creare un cookie che contiene informazioni sull'utente, <xref:System.Security.Claims.ClaimsPrincipal>costruire un oggetto. Le informazioni utente vengono serializzate e archiviate nel cookie. 

Creare una <xref:System.Security.Claims.ClaimsIdentity> con qualsiasi richiesta <xref:System.Security.Claims.Claim>e chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> per accedere all'utente:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`Crea un cookie crittografato e lo aggiunge alla risposta corrente. Se `AuthenticationScheme` non è specificato, viene usato lo schema predefinito.

Per la crittografia viene usato il sistema di [protezione dei dati](xref:security/data-protection/using-data-protection) di ASP.NET Core. Per un'app ospitata in più computer, bilanciamento del carico tra app o uso di un Web farm, [configurare la protezione dei dati](xref:security/data-protection/configuration/overview) in modo da usare lo stesso anello chiave e l'identificatore dell'app.

## <a name="sign-out"></a>Esci

Per disconnettersi dall'utente corrente ed eliminare il cookie, chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Se `CookieAuthenticationDefaults.AuthenticationScheme` (o "cookie") non viene usato come schema (ad esempio, "ContosoCookie"), fornire lo schema usato durante la configurazione del provider di autenticazione. In caso contrario, verrà utilizzato lo schema predefinito.

## <a name="react-to-back-end-changes"></a>Reagire alle modifiche del back-end

Una volta creato un cookie, il cookie è l'unica origine di identità. Se un account utente è disabilitato nei sistemi back-end:

* Il sistema di autenticazione dei cookie dell'app continua a elaborare le richieste in base al cookie di autenticazione.
* L'utente rimane connesso all'app fino a quando il cookie di autenticazione è valido.

L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento può essere usato per intercettare ed eseguire l'override della convalida dell'identità del cookie. La convalida del cookie a ogni richiesta attenua il rischio di revocare gli utenti che accedono all'app.

Un approccio alla convalida dei cookie si basa sul tenere traccia del momento in cui il database utente viene modificato. Se il database non è stato modificato dopo l'emissione del cookie dell'utente, non è necessario autenticare di nuovo l'utente se il cookie è ancora valido. Nell'app di esempio, il database viene implementato in `IUserRepository` e archivia un `LastChanged` valore. Quando un utente viene aggiornato nel database, il `LastChanged` valore viene impostato sull'ora corrente.

Per invalidare un cookie quando il database viene modificato in base al `LastChanged` valore, creare il cookie con un' `LastChanged` attestazione contenente il valore `LastChanged` corrente dal database:

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

Per implementare una sostituzione per l' `ValidatePrincipal` evento, scrivere un metodo con la firma seguente in una classe che deriva da: <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Di seguito è riportato un esempio di `CookieAuthenticationEvents`implementazione di:

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Registrare l'istanza degli eventi durante la `Startup.ConfigureServices` registrazione del servizio cookie nel metodo. Fornire una [registrazione del servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes) per `CustomCookieAuthenticationEvents` la classe:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Si consideri una situazione in cui il nome dell'&mdash;utente viene aggiornato una decisione che non influisce sulla sicurezza in alcun modo. Se si desidera aggiornare l'entità utente in modo non distruttivo, chiamare `context.ReplacePrincipal` e impostare la `context.ShouldRenew` proprietà su `true`.

> [!WARNING]
> L'approccio descritto qui viene attivato a ogni richiesta. La convalida dei cookie di autenticazione per tutti gli utenti in ogni richiesta può comportare un notevole calo delle prestazioni per l'app.

## <a name="persistent-cookies"></a>Cookie permanenti

È possibile che il cookie venga mantenuto tra le sessioni del browser. Questa persistenza deve essere abilitata solo con il consenso esplicito dell'utente con una casella di controllo "Remember me" all'accesso o un meccanismo simile. 

Il frammento di codice seguente crea un'identità e un cookie corrispondente che sopravvivono attraverso le chiusure del browser. Vengono rispettate tutte le impostazioni di scadenza scorrevoli configurate in precedenza. Se il cookie scade quando il browser viene chiuso, il cookie viene cancellato dopo il riavvio.

Imposta <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> su `true` in :<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>Scadenza assoluta del cookie

È possibile impostare un'ora di scadenza assoluta <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>con. Per creare un cookie persistente `IsPersistent` , è necessario impostare anche. In caso contrario, il cookie viene creato con una durata basata sulla sessione e potrebbe scadere prima o dopo il ticket di autenticazione che possiede. Quando `ExpiresUtc` è impostato, esegue l'override del valore <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> dell'opzione di <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, se impostata.

Il frammento di codice seguente crea un'identità e un cookie corrispondente che dura 20 minuti. Questa operazione ignora le impostazioni di scadenza scorrevoli configurate in precedenza.

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core identità è un provider di autenticazione completo e completo per la creazione e la gestione degli account di accesso. Tuttavia, è possibile usare un provider di autenticazione basata su cookie senza ASP.NET Core identità. Per altre informazioni, vedere <xref:security/authentication/identity>.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

A scopo dimostrativo nell'app di esempio, l'account utente per l'utente ipotetico, Maria Rodriguez, è hardcoded nell'app. Usare l'indirizzo `maria.rodriguez@contoso.com` di **posta elettronica** e qualsiasi password per accedere all'utente. L'utente viene autenticato nel `AuthenticateUser` metodo nel file *pages/account/login. cshtml. cs* . In un esempio reale, l'utente viene autenticato a fronte di un database.

## <a name="configuration"></a>Configurazione

Se l'app non usa il [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), creare un riferimento al pacchetto nel file di progetto per il pacchetto [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

Nel metodo creare il servizio middleware di autenticazione con i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> metodi e <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>: `Startup.ConfigureServices`

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passato a `AddAuthentication` imposta lo schema di autenticazione predefinito per l'app. `AuthenticationScheme`è utile quando sono presenti più istanze dell'autenticazione dei cookie e si desidera [autorizzare con uno schema specifico](xref:security/authorization/limitingidentitybyscheme). L' `AuthenticationScheme` impostazione di su [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornisce il valore "cookie" per lo schema. È possibile specificare qualsiasi valore stringa che distingua lo schema.

Lo schema di autenticazione dell'app è diverso dallo schema di autenticazione dei cookie dell'app. Quando non viene fornito uno schema di autenticazione <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>dei cookie a `CookieAuthenticationDefaults.AuthenticationScheme` , viene usato ("cookie").

La <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> proprietà del cookie di autenticazione è impostata `true` su per impostazione predefinita. I cookie di autenticazione sono consentiti quando un visitatore del sito non ha acconsentito alla raccolta dei dati. Per altre informazioni, vedere <xref:security/gdpr#essential-cookies>.

Nel metodo chiamare il `UseAuthentication` metodo per richiamare il middleware di autenticazione che imposta la `HttpContext.User` proprietà. `Startup.Configure` Chiamare il `UseAuthentication` metodo prima di `UseMvcWithDefaultRoute` chiamare `UseMvc`o:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

La <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe viene utilizzata per configurare le opzioni del provider di autenticazione.

Impostare `CookieAuthenticationOptions` nella configurazione del servizio per l'autenticazione `Startup.ConfigureServices` nel metodo:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Middleware dei criteri dei cookie

Il [middleware dei criteri](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) dei cookie Abilita le funzionalità dei criteri dei cookie. L'aggiunta del middleware alla pipeline di elaborazione delle app è&mdash;sensibile agli ordini, ma interessa solo i componenti downstream registrati nella pipeline.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Usare <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornito al middleware dei criteri dei cookie per controllare le caratteristiche globali dell'elaborazione dei cookie e l'hook nei gestori di elaborazione dei cookie quando i cookie vengono aggiunti o eliminati.

Il valore <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> predefinito è `SameSiteMode.Lax` consentire l'autenticazione OAuth2. Per applicare rigorosamente un criterio dello stesso sito `SameSiteMode.Strict`di, impostare `MinimumSameSitePolicy`il. Sebbene questa impostazione interrompa OAuth2 e altri schemi di autenticazione tra le origini, eleva il livello di sicurezza dei cookie per altri tipi di app che non si basano sull'elaborazione di richieste tra origini.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

L'impostazione del middleware per i `MinimumSameSitePolicy` criteri dei cookie per può `Cookie.SameSite` influire sull'impostazione di in impostazioni in `CookieAuthenticationOptions` base alla matrice riportata di seguito.

| MinimumSameSitePolicy | Cookie.SameSite | Impostazione cookie. navigava sullostesso sito risultante |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Creare un cookie di autenticazione

Per creare un cookie che contiene informazioni sull'utente, <xref:System.Security.Claims.ClaimsPrincipal>costruire un oggetto. Le informazioni utente vengono serializzate e archiviate nel cookie. 

Creare una <xref:System.Security.Claims.ClaimsIdentity> con qualsiasi richiesta <xref:System.Security.Claims.Claim>e chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> per accedere all'utente:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`Crea un cookie crittografato e lo aggiunge alla risposta corrente. Se `AuthenticationScheme` non è specificato, viene usato lo schema predefinito.

Per la crittografia viene usato il sistema di [protezione dei dati](xref:security/data-protection/using-data-protection) di ASP.NET Core. Per un'app ospitata in più computer, bilanciamento del carico tra app o uso di un Web farm, [configurare la protezione dei dati](xref:security/data-protection/configuration/overview) in modo da usare lo stesso anello chiave e l'identificatore dell'app.

## <a name="sign-out"></a>Esci

Per disconnettersi dall'utente corrente ed eliminare il cookie, chiamare <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Se `CookieAuthenticationDefaults.AuthenticationScheme` (o "cookie") non viene usato come schema (ad esempio, "ContosoCookie"), fornire lo schema usato durante la configurazione del provider di autenticazione. In caso contrario, verrà utilizzato lo schema predefinito.

## <a name="react-to-back-end-changes"></a>Reagire alle modifiche del back-end

Una volta creato un cookie, il cookie è l'unica origine di identità. Se un account utente è disabilitato nei sistemi back-end:

* Il sistema di autenticazione dei cookie dell'app continua a elaborare le richieste in base al cookie di autenticazione.
* L'utente rimane connesso all'app fino a quando il cookie di autenticazione è valido.

L' <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento può essere usato per intercettare ed eseguire l'override della convalida dell'identità del cookie. La convalida del cookie a ogni richiesta attenua il rischio di revocare gli utenti che accedono all'app.

Un approccio alla convalida dei cookie si basa sul tenere traccia del momento in cui il database utente viene modificato. Se il database non è stato modificato dopo l'emissione del cookie dell'utente, non è necessario autenticare di nuovo l'utente se il cookie è ancora valido. Nell'app di esempio, il database viene implementato in `IUserRepository` e archivia un `LastChanged` valore. Quando un utente viene aggiornato nel database, il `LastChanged` valore viene impostato sull'ora corrente.

Per invalidare un cookie quando il database viene modificato in base al `LastChanged` valore, creare il cookie con un' `LastChanged` attestazione contenente il valore `LastChanged` corrente dal database:

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

Per implementare una sostituzione per l' `ValidatePrincipal` evento, scrivere un metodo con la firma seguente in una classe che deriva da: <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Di seguito è riportato un esempio di `CookieAuthenticationEvents`implementazione di:

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Registrare l'istanza degli eventi durante la `Startup.ConfigureServices` registrazione del servizio cookie nel metodo. Fornire una [registrazione del servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes) per `CustomCookieAuthenticationEvents` la classe:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Si consideri una situazione in cui il nome dell'&mdash;utente viene aggiornato una decisione che non influisce sulla sicurezza in alcun modo. Se si desidera aggiornare l'entità utente in modo non distruttivo, chiamare `context.ReplacePrincipal` e impostare la `context.ShouldRenew` proprietà su `true`.

> [!WARNING]
> L'approccio descritto qui viene attivato a ogni richiesta. La convalida dei cookie di autenticazione per tutti gli utenti in ogni richiesta può comportare un notevole calo delle prestazioni per l'app.

## <a name="persistent-cookies"></a>Cookie permanenti

È possibile che il cookie venga mantenuto tra le sessioni del browser. Questa persistenza deve essere abilitata solo con il consenso esplicito dell'utente con una casella di controllo "Remember me" all'accesso o un meccanismo simile. 

Il frammento di codice seguente crea un'identità e un cookie corrispondente che sopravvivono attraverso le chiusure del browser. Vengono rispettate tutte le impostazioni di scadenza scorrevoli configurate in precedenza. Se il cookie scade quando il browser viene chiuso, il cookie viene cancellato dopo il riavvio.

Imposta <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> su `true` in :<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>Scadenza assoluta del cookie

È possibile impostare un'ora di scadenza assoluta <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>con. Per creare un cookie persistente `IsPersistent` , è necessario impostare anche. In caso contrario, il cookie viene creato con una durata basata sulla sessione e potrebbe scadere prima o dopo il ticket di autenticazione che possiede. Quando `ExpiresUtc` è impostato, esegue l'override del valore <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> dell'opzione di <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, se impostata.

Il frammento di codice seguente crea un'identità e un cookie corrispondente che dura 20 minuti. Questa operazione ignora le impostazioni di scadenza scorrevoli configurate in precedenza.

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Controlli dei ruoli basati su criteri](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
