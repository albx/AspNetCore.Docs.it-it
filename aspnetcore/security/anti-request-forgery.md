---
title: Evitare attacchi Cross-Site Request Forgery (XSRF/CSRF) in ASP.NET Core
author: steve-smith
description: Scopri come impedire gli attacchi contro le app Web in cui un sito Web dannoso può influenzare l'interazione tra un browser client e l'app.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: security/anti-request-forgery
ms.openlocfilehash: 54e153af55f28d9a89bbf16bce1c17f876567b59
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880800"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>Evitare attacchi Cross-Site Request Forgery (XSRF/CSRF) in ASP.NET Core

Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)e [Steve Smith](https://ardalis.com/)

La richiesta tra siti falsificata (nota anche come XSRF o CSRF) è un attacco contro app ospitate sul Web, in cui un'app Web dannosa può influenzare l'interazione tra un browser client e un'app Web che considera attendibile il browser. Questi attacchi sono possibili perché i Web browser inviano automaticamente alcuni tipi di token di autenticazione con ogni richiesta a un sito Web. Questa forma di exploit è nota anche come un *attacco con un solo clic* o una *sessione di guida* perché l'attacco sfrutta la sessione precedentemente autenticata dell'utente.

Esempio di attacco CSRF:

1. Un utente accede `www.good-banking-site.com` usando l'autenticazione basata su form. Il server autentica l'utente e genera una risposta che include un cookie di autenticazione. Il sito è vulnerabile agli attacchi poiché considera attendibile qualsiasi richiesta ricevuta con un cookie di autenticazione valido.
1. L'utente visita un sito dannoso, `www.bad-crook-site.com`.

   Il sito dannoso, `www.bad-crook-site.com`, contiene un form HTML simile al seguente:

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   Si noti che il `action` del modulo viene pubblicato sul sito vulnerabile, non sul sito dannoso. Si tratta della parte "cross-site" di CSRF.

1. L'utente seleziona il pulsante Submit (Invia). Il browser esegue la richiesta e include automaticamente il cookie di autenticazione per il dominio richiesto, `www.good-banking-site.com`.
1. La richiesta viene eseguita nel server di `www.good-banking-site.com` con il contesto di autenticazione dell'utente e può eseguire qualsiasi azione che un utente autenticato è autorizzato a eseguire.

Oltre allo scenario in cui l'utente seleziona il pulsante per l'invio del modulo, il sito dannoso potrebbe:

* Eseguire uno script che invia automaticamente il modulo.
* Inviare il modulo inviato come richiesta AJAX.
* Nascondere il form usando CSS.

Questi scenari alternativi non richiedono alcuna azione o input da parte dell'utente, tranne che inizialmente visitare il sito dannoso.

L'uso di HTTPS non impedisce un attacco CSRF. Il sito dannoso può inviare una richiesta di `https://www.good-banking-site.com/` con la stessa facilità con cui è possibile inviare una richiesta non protetta.

Alcuni attacchi hanno come destinazione endpoint che rispondono alle richieste GET, nel qual caso è possibile usare un tag di immagine per eseguire l'azione. Questo tipo di attacco è comune nei siti dei forum che consentono le immagini ma bloccano JavaScript. Le app che modificano lo stato di richieste GET, in cui variabili o risorse vengono modificate, sono vulnerabili ad attacchi dannosi. **Le richieste GET che cambiano lo stato non sono sicure. Una procedura consigliata consiste nel non modificare mai lo stato di una richiesta GET.**

Gli attacchi CSRF sono possibili per le app Web che usano i cookie per l'autenticazione perché:

* I browser archiviano i cookie emessi da un'app Web.
* I cookie archiviati includono i cookie di sessione per gli utenti autenticati.
* I browser inviano tutti i cookie associati a un dominio all'app Web ogni richiesta, indipendentemente dalla modalità di generazione della richiesta all'app all'interno del browser.

Tuttavia, gli attacchi CSRF non sono limitati allo sfruttamento dei cookie. Ad esempio, anche l'autenticazione di base e del digest è vulnerabile. Dopo che un utente ha eseguito l'accesso con l'autenticazione di base o del digest, il browser invia automaticamente le credenziali fino al termine della sessione&dagger;.

&dagger;in questo contesto, *Session* fa riferimento alla sessione lato client durante la quale l'utente viene autenticato. Non è correlato alle sessioni sul lato server o al [middleware della sessione ASP.NET Core](xref:fundamentals/app-state).

Gli utenti possono proteggersi da vulnerabilità CSRF adottando le precauzioni seguenti:

* Disconnettersi dalle app Web al termine dell'uso.
* Cancellare periodicamente i cookie del browser.

Tuttavia, le vulnerabilità CSRF sono fondamentalmente un problema dell'app web, non dell'utente finale.

## <a name="authentication-fundamentals"></a>Nozioni fondamentali sull'autenticazione

L'autenticazione basata su cookie è una forma comune di autenticazione. I sistemi di autenticazione basata su token hanno una maggiore popolarità, soprattutto per le applicazioni a pagina singola (Spa).

### <a name="cookie-based-authentication"></a>Autenticazione basata su cookie

Quando un utente esegue l'autenticazione usando il nome utente e la password, viene emesso un token contenente un ticket di autenticazione che può essere usato per l'autenticazione e l'autorizzazione. Il token viene archiviato come cookie che accompagna ogni richiesta eseguita dal client. La generazione e la convalida di questo cookie viene eseguita dal middleware di autenticazione dei cookie. Il [middleware](xref:fundamentals/middleware/index) serializza un'entità utente in un cookie crittografato. Nelle richieste successive il middleware convalida il cookie, ricrea l'entità e assegna l'entità alla proprietà [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) di [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).

### <a name="token-based-authentication"></a>Autenticazione basata su token

Quando un utente viene autenticato, viene emesso un token (non un token antifalsificazione). Il token contiene informazioni sull'utente sotto forma di [attestazioni](/dotnet/framework/security/claims-based-identity-model) o un token di riferimento che punta l'app allo stato utente mantenuto nell'app. Quando un utente tenta di accedere a una risorsa che richiede l'autenticazione, il token viene inviato all'app con un'intestazione di autorizzazione aggiuntiva nel formato token di connessione. Questa operazione rende l'app senza stato. In ogni richiesta successiva il token viene passato nella richiesta di convalida lato server. Questo token non è *crittografato*; è *codificato*. Sul server, il token viene decodificato per accedere alle informazioni. Per inviare il token alle richieste successive, archiviare il token nella risorsa di archiviazione locale del browser. Non preoccuparsi della vulnerabilità CSRF se il token è archiviato nella risorsa di archiviazione locale del browser. CSRF rappresenta un problema quando il token viene archiviato in un cookie. Per ulteriori informazioni, vedere l'esempio di codice di GitHub Issue [Spa aggiunge due cookie](https://github.com/aspnet/AspNetCore.Docs/issues/13369).

### <a name="multiple-apps-hosted-at-one-domain"></a>Più app ospitate in un dominio

Gli ambienti di hosting condiviso sono vulnerabili al Hijack della sessione, all'account di accesso CSRF e ad altri attacchi.

Sebbene `example1.contoso.net` e `example2.contoso.net` siano host diversi, esiste una relazione di trust implicita tra gli host nel dominio `*.contoso.net`. Questa relazione di trust implicita consente a host potenzialmente non attendibili di influenzare gli altri cookie (i criteri di origine identici che regolano le richieste AJAX non si applicano necessariamente ai cookie HTTP).

Gli attacchi che sfruttano i cookie attendibili tra le app ospitate nello stesso dominio possono essere impediti dalla condivisione dei domini. Quando ogni app è ospitata nel proprio dominio, non esiste alcuna relazione di trust con i cookie implicita da sfruttare.

## <a name="aspnet-core-antiforgery-configuration"></a>ASP.NET Core configurazione antifalsificazione

> [!WARNING]
> ASP.NET Core implementa la antifalsificazione usando la [protezione dei dati ASP.NET Core](xref:security/data-protection/introduction). Lo stack di protezione dei dati deve essere configurato in modo da funzionare in un server farm. Per ulteriori informazioni, vedere [configurazione della protezione dei dati](xref:security/data-protection/configuration/overview) .

::: moniker range=">= aspnetcore-3.0"

Il middleware antifalsificazione viene aggiunto al contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) quando una delle API seguenti viene chiamata in `Startup.ConfigureServices`:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Il middleware antifalsificazione viene aggiunto al contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) quando viene chiamato <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> in `Startup.ConfigureServices`

::: moniker-end

In ASP.NET Core 2,0 o versioni successive, [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) inserisce i token antifalsificazione negli elementi del form HTML. Il markup seguente in un file Razor genera automaticamente i token antifalsificazione:

```cshtml
<form method="post">
    ...
</form>
```

Analogamente, [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) genera token antifalsificazione per impostazione predefinita se il metodo del modulo non è Get.

La generazione automatica dei token antifalsificazione per gli elementi del form HTML si verifica quando il tag `<form>` contiene l'attributo `method="post"` e si verifica una delle condizioni seguenti:

* L'attributo Action è vuoto (`action=""`).
* L'attributo Action non è specificato (`<form method="post">`).

La generazione automatica dei token antifalsificazione per gli elementi del modulo HTML può essere disabilitata:

* Disabilitare in modo esplicito i token antifalsificazione con l'attributo `asp-antiforgery`:

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* L'elemento form è escluso dagli Helper tag usando il simbolo Helper tag [! opt-out](xref:mvc/views/tag-helpers/intro#opt-out):

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* Rimuovere il `FormTagHelper` dalla visualizzazione. Il `FormTagHelper` può essere rimosso da una visualizzazione aggiungendo la direttiva seguente alla visualizzazione Razor:

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [Razor Pages](xref:razor-pages/index) vengono protetti automaticamente da XSRF/CSRF. Per ulteriori informazioni, vedere [XSRF/CSRF e Razor Pages](xref:razor-pages/index#xsrf).

L'approccio più comune alla difesa dagli attacchi CSRF consiste nell'usare il *modello di token di sincronizzazione* (STP). STP viene usato quando l'utente richiede una pagina con dati del modulo:

1. Il server invia al client un token associato all'identità dell'utente corrente.
1. Il client invia il token al server per la verifica.
1. Se il server riceve un token che non corrisponde all'identità dell'utente autenticato, la richiesta viene rifiutata.

Il token è univoco e imprevedibile. Il token può essere usato anche per garantire la sequenziazione corretta di una serie di richieste, ad esempio per garantire la sequenza di richiesta: pagina 1 &ndash; pagina 2 &ndash; pagina 3). Tutti i moduli nei modelli ASP.NET Core MVC e Razor Pages generano token antifalsificazione. La coppia seguente di esempi di viste genera i token antifalsificazione:

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

Aggiungere in modo esplicito un token antifalsificazione a un elemento `<form>` senza usare gli helper tag con l'helper HTML [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

In ognuno dei casi precedenti, ASP.NET Core aggiunge un campo del form nascosto simile al seguente:

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core include tre [filtri](xref:mvc/controllers/filters) per l'utilizzo di token antifalsificazione:

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>Opzioni antifalsificazione

Personalizzare le [Opzioni antifalsificazione](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

&dagger;impostare le proprietà della `Cookie` antifalsificazione usando le proprietà della classe [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .

| Opzione | Descrizione |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determina le impostazioni utilizzate per creare i cookie antifalsificazione. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nome del campo del form nascosto utilizzato dal sistema antifalsificazione per il rendering dei token antifalsificazione nelle viste. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nome dell'intestazione utilizzata dal sistema antifalsificazione. Se `null`, il sistema considera solo i dati del modulo. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifica se escludere la generazione dell'intestazione `X-Frame-Options`. Per impostazione predefinita, l'intestazione viene generata con un valore "SAMEORIGIN". Il valore predefinito è `false`. |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| Opzione | Descrizione |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determina le impostazioni utilizzate per creare i cookie antifalsificazione. |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Dominio del cookie. Il valore predefinito è `null`. Questa proprietà è obsoleta e verrà rimossa in una versione futura. L'alternativa consigliata è cookie. Domain. |
| [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Nome del cookie. Se non è impostato, il sistema genera un nome univoco che inizia con [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore. antifalsificazione. "). Questa proprietà è obsoleta e verrà rimossa in una versione futura. L'alternativa consigliata è Cookie.Name. |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Percorso impostato nel cookie. Questa proprietà è obsoleta e verrà rimossa in una versione futura. L'alternativa consigliata è cookie. Path. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nome del campo del form nascosto utilizzato dal sistema antifalsificazione per il rendering dei token antifalsificazione nelle viste. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nome dell'intestazione utilizzata dal sistema antifalsificazione. Se `null`, il sistema considera solo i dati del modulo. |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Specifica se HTTPS è richiesto dal sistema antifalsificazione. Se `true`, le richieste non HTTPS hanno esito negativo. Il valore predefinito è `false`. Questa proprietà è obsoleta e verrà rimossa in una versione futura. L'alternativa consigliata consiste nell'impostare cookie. SecurePolicy. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifica se escludere la generazione dell'intestazione `X-Frame-Options`. Per impostazione predefinita, l'intestazione viene generata con un valore "SAMEORIGIN". Il valore predefinito è `false`. |

::: moniker-end

Per ulteriori informazioni, vedere [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).

## <a name="configure-antiforgery-features-with-iantiforgery"></a>Configurare le funzionalità antifalsificazione con IAntiforgery

[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) fornisce l'API per configurare le funzionalità antifalsificazione. `IAntiforgery` possibile richiedere nel metodo `Configure` della classe `Startup`. Nell'esempio seguente viene usato il middleware della home page dell'app per generare un token antifalsificazione e inviarlo nella risposta come cookie (usando la convenzione di denominazione angolare predefinita descritta più avanti in questo argomento):

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a>Richiedi convalida antifalsificazione

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) è un filtro azioni che può essere applicato a una singola azione, a un controller o a livello globale. Le richieste effettuate alle azioni con questo filtro vengono bloccate a meno che la richiesta non includa un token antifalsificazione valido.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

L'attributo `ValidateAntiForgeryToken` richiede un token per le richieste ai metodi di azione contrassegnati, incluse le richieste HTTP GET. Se l'attributo `ValidateAntiForgeryToken` viene applicato tra i controller dell'app, è possibile eseguirne l'override con l'attributo `IgnoreAntiforgeryToken`.

> [!NOTE]
> ASP.NET Core non supporta l'aggiunta di token antifalsificazione per ottenere automaticamente le richieste.

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>Convalidare automaticamente i token antifalsificazione per metodi HTTP unsafe

ASP.NET Core app non generano token antifalsificazione per metodi HTTP sicuri (GET, HEAD, OPTIONS e TRACE). Anziché applicare in larga misura l'attributo `ValidateAntiForgeryToken` e quindi eseguire l'override con `IgnoreAntiforgeryToken` attributi, è possibile usare l'attributo [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) . Questo attributo funziona in modo identico all'attributo `ValidateAntiForgeryToken`, ad eccezione del fatto che non richiede token per le richieste effettuate usando i metodi HTTP seguenti:

* GET
* HEAD
* OPZIONI
* TRACE

Per gli scenari non API si consiglia l'uso di `AutoValidateAntiforgeryToken` ampiamente. Ciò garantisce che le azioni POST siano protette per impostazione predefinita. In alternativa, è possibile ignorare i token antifalsificazione per impostazione predefinita, a meno che non `ValidateAntiForgeryToken` venga applicato a singoli metodi di azione. È più probabile che in questo scenario un metodo di azione POST venga lasciato non protetto per errore, lasciando l'app vulnerabile agli attacchi CSRF. Tutti i post devono inviare il token antifalsificazione.

Le API non hanno un meccanismo automatico per l'invio della parte non cookie del token. È probabile che l'implementazione dipenda dall'implementazione del codice client. Di seguito sono riportati alcuni esempi:

Esempio a livello di classe:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

Esempio globale:

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a>Sostituisci attributi antifalsificazione globali o controller

Il filtro [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) viene usato per eliminare la necessità di un token antifalsificazione per un'azione o un controller specifico. Quando applicato, questo filtro sostituisce `ValidateAntiForgeryToken` e `AutoValidateAntiforgeryToken` i filtri specificati a un livello superiore (globalmente o in un controller).

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a>Aggiornare i token dopo l'autenticazione

I token devono essere aggiornati dopo che l'utente è stato autenticato reindirizzando l'utente a una vista o a una pagina Razor Pages.

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX e SPA (Single Page Application)

Nelle app tradizionali basate su HTML, i token antifalsificazione vengono passati al server usando i campi dei moduli nascosti. Nelle app moderne basate su JavaScript e in Spa, molte richieste vengono eseguite a livello di programmazione. Queste richieste AJAX possono usare altre tecniche (ad esempio, intestazioni di richiesta o cookie) per inviare il token.

Se i cookie vengono usati per archiviare i token di autenticazione e per autenticare le richieste API sul server, CSRF è un potenziale problema. Se viene usata l'archiviazione locale per archiviare il token, la vulnerabilità CSRF potrebbe essere attenuata perché i valori della risorsa di archiviazione locale non vengono inviati automaticamente al server con ogni richiesta. Quindi, l'uso dell'archiviazione locale per archiviare il token antifalsificazione nel client e l'invio del token come intestazione della richiesta è un approccio consigliato.

### <a name="javascript"></a>JavaScript

Utilizzando JavaScript con le visualizzazioni, è possibile creare il token utilizzando un servizio all'interno della vista. Inserire il servizio [Microsoft. AspNetCore. antifalsification. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) nella vista e chiamare [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

Questo approccio elimina la necessità di gestire direttamente l'impostazione dei cookie dal server o di leggerli dal client.

Nell'esempio precedente viene usato JavaScript per leggere il valore del campo nascosto per l'intestazione AJAX POST.

JavaScript può anche accedere ai token nei cookie e usare il contenuto del cookie per creare un'intestazione con il valore del token.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

Supponendo che lo script richieda di inviare il token in un'intestazione denominata `X-CSRF-TOKEN`, configurare il servizio antifalsificazione in modo da cercare l'intestazione del `X-CSRF-TOKEN`:

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

Nell'esempio seguente viene usato JavaScript per creare una richiesta AJAX con l'intestazione appropriata:

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a>AngularJS

AngularJS usa una convenzione per indirizzare CSRF. Se il server invia un cookie con il nome `XSRF-TOKEN`, il servizio di `$http` AngularJS aggiunge il valore del cookie a un'intestazione quando invia una richiesta al server. Questo processo è automatico. L'intestazione non deve essere impostata in modo esplicito nel client. Il nome dell'intestazione è `X-XSRF-TOKEN`. Il server deve rilevare questa intestazione e convalidarne il contenuto.

Per ASP.NET Core API da usare con questa convenzione nell'avvio dell'applicazione:

* Configurare l'app per fornire un token in un cookie denominato `XSRF-TOKEN`.
* Configurare il servizio antifalsificazione per cercare un'intestazione denominata `X-XSRF-TOKEN`.

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="extend-antiforgery"></a>Estendi antifalsificazione

Il tipo [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) consente agli sviluppatori di estendere il comportamento del sistema anti-CSRF eseguendo il round trip di dati aggiuntivi in ogni token. Il metodo [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) viene chiamato ogni volta che viene generato un token di campo e il valore restituito viene incorporato all'interno del token generato. Un responsabile dell'implementazione può restituire un timestamp, un parametro nonce o qualsiasi altro valore, quindi chiamare [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) per convalidare questi dati quando il token viene convalidato. Il nome utente del client è già incorporato nei token generati, pertanto non è necessario includere tali informazioni. Se un token include dati supplementari ma non è configurata alcuna `IAntiForgeryAdditionalDataProvider`, i dati supplementari non vengono convalidati.

## <a name="additional-resources"></a>Risorse aggiuntive

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) in [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).
* <xref:host-and-deploy/web-farm>
