---
title: Usare l'autenticazione tramite cookie senza ASP.NET Core Identity
author: rick-anderson
description: Una spiegazione dell'uso l'autenticazione tramite cookie senza ASP.NET Core Identity
ms.author: riande
ms.date: 02/25/2019
uid: security/authentication/cookie
ms.openlocfilehash: 7e975da3a276ffb6a3de7ee02f7cc5be67cbbebe
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833618"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="b897c-103">Usare l'autenticazione tramite cookie senza ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="b897c-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="b897c-104">Dal [Rick Anderson](https://twitter.com/RickAndMSFT) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b897c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b897c-105">Come si è visto negli argomenti precedenti authentication [ASP.NET Core Identity](xref:security/authentication/identity) è un provider di autenticazione completa e completo per creare e gestire gli account di accesso.</span><span class="sxs-lookup"><span data-stu-id="b897c-105">As you've seen in the earlier authentication topics, [ASP.NET Core Identity](xref:security/authentication/identity) is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="b897c-106">Tuttavia, è possibile usare la propria logica di autenticazione personalizzato con autenticazione basata su cookie in alcuni casi.</span><span class="sxs-lookup"><span data-stu-id="b897c-106">However, you may want to use your own custom authentication logic with cookie-based authentication at times.</span></span> <span data-ttu-id="b897c-107">È possibile usare l'autenticazione basata su cookie come un provider di autenticazione autonomo senza ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="b897c-107">You can use cookie-based authentication as a standalone authentication provider without ASP.NET Core Identity.</span></span>

<span data-ttu-id="b897c-108">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b897c-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b897c-109">A scopo dimostrativo nell'app di esempio, l'account utente per l'utente ipotetica, Maria Rodriguez, è hardcoded nell'app.</span><span class="sxs-lookup"><span data-stu-id="b897c-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="b897c-110">Usare il nome utente di posta elettronica "maria.rodriguez@contoso.com" e qualsiasi password di accesso dell'utente.</span><span class="sxs-lookup"><span data-stu-id="b897c-110">Use the Email username "maria.rodriguez@contoso.com" and any password to sign in the user.</span></span> <span data-ttu-id="b897c-111">L'utente viene autenticato nel `AuthenticateUser` metodo nella *Pages/Account/Login.cshtml.cs* file.</span><span class="sxs-lookup"><span data-stu-id="b897c-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="b897c-112">In un esempio reale, l'utente viene autenticato in un database.</span><span class="sxs-lookup"><span data-stu-id="b897c-112">In a real-world example, the user would be authenticated against a database.</span></span>

<span data-ttu-id="b897c-113">Per informazioni sulla migrazione di autenticazione basata su cookie da ASP.NET Core 1.x a 2.0, vedere [eseguire la migrazione di autenticazione e identità ad ASP.NET Core 2.0 argomento (autenticazione basata su Cookie)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).</span><span class="sxs-lookup"><span data-stu-id="b897c-113">For information on migrating cookie-based authentication from ASP.NET Core 1.x to 2.0, see [Migrate Authentication and Identity to ASP.NET Core 2.0 topic (Cookie-based Authentication)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).</span></span>

<span data-ttu-id="b897c-114">Per usare ASP.NET Core Identity, vedere la [Introduzione all'identità](xref:security/authentication/identity) argomento.</span><span class="sxs-lookup"><span data-stu-id="b897c-114">To use ASP.NET Core Identity, see the [Introduction to Identity](xref:security/authentication/identity) topic.</span></span>

## <a name="configuration"></a><span data-ttu-id="b897c-115">Configurazione</span><span class="sxs-lookup"><span data-stu-id="b897c-115">Configuration</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b897c-116">Se l'app non usa la [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), creare un riferimento al pacchetto nel file di progetto per il [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package (versione 2.1.0 o in un secondo momento).</span><span class="sxs-lookup"><span data-stu-id="b897c-116">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package (version 2.1.0 or later).</span></span>

<span data-ttu-id="b897c-117">Nel `ConfigureServices` metodo, creare il servizio di Middleware di autenticazione con il `AddAuthentication` e `AddCookie` metodi:</span><span class="sxs-lookup"><span data-stu-id="b897c-117">In the `ConfigureServices` method, create the Authentication Middleware service with the `AddAuthentication` and `AddCookie` methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="b897c-118">`AuthenticationScheme` passato a `AddAuthentication` imposta lo schema di autenticazione predefinito per l'app.</span><span class="sxs-lookup"><span data-stu-id="b897c-118">`AuthenticationScheme` passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="b897c-119">`AuthenticationScheme` è utile quando sono presenti più istanze di autenticazione dei cookie e si desidera [autorizzare con uno schema specifico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="b897c-119">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="b897c-120">Impostando il `AuthenticationScheme` a `CookieAuthenticationDefaults.AuthenticationScheme` fornisce un valore di "Cookie" per lo schema.</span><span class="sxs-lookup"><span data-stu-id="b897c-120">Setting the `AuthenticationScheme` to `CookieAuthenticationDefaults.AuthenticationScheme` provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="b897c-121">È possibile specificare qualsiasi valore stringa che distingue lo schema.</span><span class="sxs-lookup"><span data-stu-id="b897c-121">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="b897c-122">Schema di autenticazione dell'app è diverso dallo schema di autenticazione cookie dell'app.</span><span class="sxs-lookup"><span data-stu-id="b897c-122">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="b897c-123">Quando non viene fornito uno schema di autenticazione del cookie a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, viene utilizzato [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookie").</span><span class="sxs-lookup"><span data-stu-id="b897c-123">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies").</span></span>

<span data-ttu-id="b897c-124">Il cookie di autenticazione <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> è impostata su `true` per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="b897c-124">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="b897c-125">I cookie di autenticazione sono consentiti quando un visitatore non ha fornito il consenso alla raccolta di dati.</span><span class="sxs-lookup"><span data-stu-id="b897c-125">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="b897c-126">Per altre informazioni, vedere <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="b897c-126">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="b897c-127">Nel `Configure` metodo, usare il `UseAuthentication` metodo da richiamare il Middleware di autenticazione che consente di impostare il `HttpContext.User` proprietà.</span><span class="sxs-lookup"><span data-stu-id="b897c-127">In the `Configure` method, use the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="b897c-128">Chiamare il `UseAuthentication` metodo prima di chiamare `UseMvcWithDefaultRoute` o `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="b897c-128">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="b897c-129">**Opzioni AddCookie**</span><span class="sxs-lookup"><span data-stu-id="b897c-129">**AddCookie Options**</span></span>

<span data-ttu-id="b897c-130">Il [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) classe viene utilizzata per configurare le opzioni del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="b897c-130">The [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) class is used to configure the authentication provider options.</span></span>

| <span data-ttu-id="b897c-131">Opzione</span><span class="sxs-lookup"><span data-stu-id="b897c-131">Option</span></span> | <span data-ttu-id="b897c-132">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b897c-132">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="b897c-133">AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="b897c-133">AccessDeniedPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath?view=aspnetcore-2.0) | <span data-ttu-id="b897c-134">Fornisce il percorso per fornire un 302 trovato (reindirizzamento URL) quando viene attivata da `HttpContext.ForbidAsync`.</span><span class="sxs-lookup"><span data-stu-id="b897c-134">Provides the path to supply with a 302 Found (URL redirect) when triggered by `HttpContext.ForbidAsync`.</span></span> <span data-ttu-id="b897c-135">Il valore predefinito è `/Account/AccessDenied`.</span><span class="sxs-lookup"><span data-stu-id="b897c-135">The default value is `/Account/AccessDenied`.</span></span> |
| [<span data-ttu-id="b897c-136">ClaimsIssuer</span><span class="sxs-lookup"><span data-stu-id="b897c-136">ClaimsIssuer</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.claimsissuer?view=aspnetcore-2.0) | <span data-ttu-id="b897c-137">L'autorità emittente da usare per la [Issuer](/dotnet/api/system.security.claims.claim.issuer) proprietà in tutte le attestazioni creato dal servizio di autenticazione del cookie.</span><span class="sxs-lookup"><span data-stu-id="b897c-137">The issuer to use for the [Issuer](/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication service.</span></span> |
| [<span data-ttu-id="b897c-138">Cookie.Domain</span><span class="sxs-lookup"><span data-stu-id="b897c-138">Cookie.Domain</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain?view=aspnetcore-2.0) | <span data-ttu-id="b897c-139">Il nome di dominio in cui viene servito il cookie.</span><span class="sxs-lookup"><span data-stu-id="b897c-139">The domain name where the cookie is served.</span></span> <span data-ttu-id="b897c-140">Per impostazione predefinita, questo è il nome host della richiesta.</span><span class="sxs-lookup"><span data-stu-id="b897c-140">By default, this is the host name of the request.</span></span> <span data-ttu-id="b897c-141">Il browser invia solo il cookie nelle richieste a un nome host corrispondente.</span><span class="sxs-lookup"><span data-stu-id="b897c-141">The browser only sends the cookie in requests to a matching host name.</span></span> <span data-ttu-id="b897c-142">È possibile modificare questa opzione per disporre i cookie disponibili a tutti gli host nel dominio.</span><span class="sxs-lookup"><span data-stu-id="b897c-142">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="b897c-143">Ad esempio, l'impostazione del dominio del cookie `.contoso.com` renderli disponibili agli `contoso.com`, `www.contoso.com`, e `staging.www.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="b897c-143">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, and `staging.www.contoso.com`.</span></span> |
| [<span data-ttu-id="b897c-144">Cookie.HttpOnly</span><span class="sxs-lookup"><span data-stu-id="b897c-144">Cookie.HttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly?view=aspnetcore-2.0) | <span data-ttu-id="b897c-145">Un flag che indica se il cookie deve essere accessibile solo ai server.</span><span class="sxs-lookup"><span data-stu-id="b897c-145">A flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="b897c-146">Se si modifica questo valore per `false` consente a script lato client per accedere al cookie e può aprire l'app al furto di cookie che l'app deve avere una [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerabilità.</span><span class="sxs-lookup"><span data-stu-id="b897c-146">Changing this value to `false` permits client-side scripts to access the cookie and may open your app to cookie theft should your app have a [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerability.</span></span> <span data-ttu-id="b897c-147">Il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="b897c-147">The default value is `true`.</span></span> |
| [<span data-ttu-id="b897c-148">Cookie.Name</span><span class="sxs-lookup"><span data-stu-id="b897c-148">Cookie.Name</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name?view=aspnetcore-2.0) | <span data-ttu-id="b897c-149">Imposta il nome del cookie.</span><span class="sxs-lookup"><span data-stu-id="b897c-149">Sets the name of the cookie.</span></span> |
| [<span data-ttu-id="b897c-150">Cookie.Path</span><span class="sxs-lookup"><span data-stu-id="b897c-150">Cookie.Path</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path?view=aspnetcore-2.0) | <span data-ttu-id="b897c-151">Consente di isolare le App in esecuzione sullo stesso nome host.</span><span class="sxs-lookup"><span data-stu-id="b897c-151">Used to isolate apps running on the same host name.</span></span> <span data-ttu-id="b897c-152">Se si dispone di un'app in esecuzione in `/app1` e si desidera limitare i cookie per tale app, impostare il `CookiePath` proprietà `/app1`.</span><span class="sxs-lookup"><span data-stu-id="b897c-152">If you have an app running at `/app1` and want to restrict cookies to that app, set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="b897c-153">In questo modo, il cookie è disponibile solo nelle richieste al `/app1` e a qualsiasi app in esso contenuti.</span><span class="sxs-lookup"><span data-stu-id="b897c-153">By doing so, the cookie is only available on requests to `/app1` and any app underneath it.</span></span> |
| [<span data-ttu-id="b897c-154">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="b897c-154">Cookie.SameSite</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite?view=aspnetcore-2.0) | <span data-ttu-id="b897c-155">Indica se il browser deve consentire i cookie da allegare al solo le richieste nello stesso sito (`SameSiteMode.Strict`) o le richieste intersito tramite i metodi HTTP sicuro e nello stesso sito richieste (`SameSiteMode.Lax`).</span><span class="sxs-lookup"><span data-stu-id="b897c-155">Indicates whether the browser should allow the cookie to be attached to same-site requests only (`SameSiteMode.Strict`) or cross-site requests using safe HTTP methods and same-site requests (`SameSiteMode.Lax`).</span></span> <span data-ttu-id="b897c-156">Se impostato su `SameSiteMode.None`, non è impostato il valore dell'intestazione cookie.</span><span class="sxs-lookup"><span data-stu-id="b897c-156">When set to `SameSiteMode.None`, the cookie header value isn't set.</span></span> <span data-ttu-id="b897c-157">Si noti che [Middleware dei Cookie criteri](#cookie-policy-middleware) potrebbe sovrascrivere il valore fornito dall'utente.</span><span class="sxs-lookup"><span data-stu-id="b897c-157">Note that [Cookie Policy Middleware](#cookie-policy-middleware) might overwrite the value that you provide.</span></span> <span data-ttu-id="b897c-158">Per supportare l'autenticazione OAuth, il valore predefinito è `SameSiteMode.Lax`.</span><span class="sxs-lookup"><span data-stu-id="b897c-158">To support OAuth authentication, the default value is `SameSiteMode.Lax`.</span></span> <span data-ttu-id="b897c-159">Per altre informazioni, vedere [l'autenticazione OAuth interrotto a causa di criteri per i cookie SameSite](https://github.com/aspnet/Security/issues/1231).</span><span class="sxs-lookup"><span data-stu-id="b897c-159">For more information, see [OAuth authentication broken due to SameSite cookie policy](https://github.com/aspnet/Security/issues/1231).</span></span> |
| [<span data-ttu-id="b897c-160">Cookie.SecurePolicy</span><span class="sxs-lookup"><span data-stu-id="b897c-160">Cookie.SecurePolicy</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.securepolicy?view=aspnetcore-2.0) | <span data-ttu-id="b897c-161">Un flag che indica se il cookie creato deve essere limitato a HTTPS (`CookieSecurePolicy.Always`), HTTP o HTTPS (`CookieSecurePolicy.None`), o lo stesso protocollo della richiesta (`CookieSecurePolicy.SameAsRequest`).</span><span class="sxs-lookup"><span data-stu-id="b897c-161">A flag indicating if the cookie created should be limited to HTTPS (`CookieSecurePolicy.Always`), HTTP or HTTPS (`CookieSecurePolicy.None`), or the same protocol as the request (`CookieSecurePolicy.SameAsRequest`).</span></span> <span data-ttu-id="b897c-162">Il valore predefinito è `CookieSecurePolicy.SameAsRequest`.</span><span class="sxs-lookup"><span data-stu-id="b897c-162">The default value is `CookieSecurePolicy.SameAsRequest`.</span></span> |
| [<span data-ttu-id="b897c-163">DataProtectionProvider</span><span class="sxs-lookup"><span data-stu-id="b897c-163">DataProtectionProvider</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0) | <span data-ttu-id="b897c-164">Imposta il `DataProtectionProvider` che consente di creare il valore predefinito `TicketDataFormat`.</span><span class="sxs-lookup"><span data-stu-id="b897c-164">Sets the `DataProtectionProvider` that's used to create the default `TicketDataFormat`.</span></span> <span data-ttu-id="b897c-165">Se il `TicketDataFormat` è impostata, il `DataProtectionProvider` non viene usata l'opzione.</span><span class="sxs-lookup"><span data-stu-id="b897c-165">If the `TicketDataFormat` property is set, the `DataProtectionProvider` option isn't used.</span></span> <span data-ttu-id="b897c-166">Se non specificato, viene utilizzato provider di protezione dati predefinito dell'app.</span><span class="sxs-lookup"><span data-stu-id="b897c-166">If not provided, the app's default data protection provider is used.</span></span> |
| [<span data-ttu-id="b897c-167">Eventi</span><span class="sxs-lookup"><span data-stu-id="b897c-167">Events</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.events?view=aspnetcore-2.0) | <span data-ttu-id="b897c-168">Il gestore chiama i metodi sul provider che offrono il controllo delle app in determinati punti di elaborazione.</span><span class="sxs-lookup"><span data-stu-id="b897c-168">The handler calls methods on the provider that give the app control at certain processing points.</span></span> <span data-ttu-id="b897c-169">Se `Events` non vengono forniti, viene fornita un'istanza predefinita che non esegue alcuna operazione quando i metodi vengono chiamati.</span><span class="sxs-lookup"><span data-stu-id="b897c-169">If `Events` aren't provided, a default instance is supplied that does nothing when the methods are called.</span></span> |
| [<span data-ttu-id="b897c-170">EventsType</span><span class="sxs-lookup"><span data-stu-id="b897c-170">EventsType</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.eventstype?view=aspnetcore-2.0) | <span data-ttu-id="b897c-171">Utilizzato come tipo di servizio per ottenere il `Events` istanza anziché la proprietà.</span><span class="sxs-lookup"><span data-stu-id="b897c-171">Used as the service type to get the `Events` instance instead of the property.</span></span> |
| [<span data-ttu-id="b897c-172">ExpireTimeSpan</span><span class="sxs-lookup"><span data-stu-id="b897c-172">ExpireTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan?view=aspnetcore-2.0) | <span data-ttu-id="b897c-173">Il `TimeSpan` dopo che scade il ticket di autenticazione archiviato all'interno del cookie.</span><span class="sxs-lookup"><span data-stu-id="b897c-173">The `TimeSpan` after which the authentication ticket stored inside the cookie expires.</span></span> <span data-ttu-id="b897c-174">`ExpireTimeSpan` viene aggiunto all'ora corrente per creare l'ora di scadenza per il ticket.</span><span class="sxs-lookup"><span data-stu-id="b897c-174">`ExpireTimeSpan` is added to the current time to create the expiration time for the ticket.</span></span> <span data-ttu-id="b897c-175">Il `ExpiredTimeSpan` valore di passi sempre nei ticket di autenticazione crittografato verificati dal server.</span><span class="sxs-lookup"><span data-stu-id="b897c-175">The `ExpiredTimeSpan` value always goes into the encrypted AuthTicket verified by the server.</span></span> <span data-ttu-id="b897c-176">È anche possibile che passi nel [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) intestazione, ma solo se `IsPersistent` è impostata.</span><span class="sxs-lookup"><span data-stu-id="b897c-176">It may also go into the [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) header, but only if `IsPersistent` is set.</span></span> <span data-ttu-id="b897c-177">Per impostare `IsPersistent` al `true`, configurare il [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) passato a `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="b897c-177">To set `IsPersistent` to `true`, configure the [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) passed to `SignInAsync`.</span></span> <span data-ttu-id="b897c-178">Il valore predefinito di `ExpireTimeSpan` è 14 giorni.</span><span class="sxs-lookup"><span data-stu-id="b897c-178">The default value of `ExpireTimeSpan` is 14 days.</span></span> |
| [<span data-ttu-id="b897c-179">LoginPath</span><span class="sxs-lookup"><span data-stu-id="b897c-179">LoginPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath?view=aspnetcore-2.0) | <span data-ttu-id="b897c-180">Fornisce il percorso per fornire un 302 trovato (reindirizzamento URL) quando viene attivata da `HttpContext.ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="b897c-180">Provides the path to supply with a 302 Found (URL redirect) when triggered by `HttpContext.ChallengeAsync`.</span></span> <span data-ttu-id="b897c-181">L'URL corrente che ha generato il codice 401 viene aggiunto per il `LoginPath` come parametro di stringa di query denominato dal `ReturnUrlParameter`.</span><span class="sxs-lookup"><span data-stu-id="b897c-181">The current URL that generated the 401 is added to the `LoginPath` as a query string parameter named by the `ReturnUrlParameter`.</span></span> <span data-ttu-id="b897c-182">Una volta una richiesta per il `LoginPath` concede una nuova identità di accesso, il `ReturnUrlParameter` valore viene usato per reindirizzare il browser all'URL che ha causato il codice di stato unauthorized originale.</span><span class="sxs-lookup"><span data-stu-id="b897c-182">Once a request to the `LoginPath` grants a new sign-in identity, the `ReturnUrlParameter` value is used to redirect the browser back to the URL that caused the original unauthorized status code.</span></span> <span data-ttu-id="b897c-183">Il valore predefinito è `/Account/Login`.</span><span class="sxs-lookup"><span data-stu-id="b897c-183">The default value is `/Account/Login`.</span></span> |
| [<span data-ttu-id="b897c-184">LogoutPath</span><span class="sxs-lookup"><span data-stu-id="b897c-184">LogoutPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath?view=aspnetcore-2.0) | <span data-ttu-id="b897c-185">Se il `LogoutPath` viene fornito al gestore, quindi reindirizza una richiesta a tale percorso in base al valore della `ReturnUrlParameter`.</span><span class="sxs-lookup"><span data-stu-id="b897c-185">If the `LogoutPath` is provided to the handler, then a request to that path redirects based on the value of the `ReturnUrlParameter`.</span></span> <span data-ttu-id="b897c-186">Il valore predefinito è `/Account/Logout`.</span><span class="sxs-lookup"><span data-stu-id="b897c-186">The default value is `/Account/Logout`.</span></span> |
| [<span data-ttu-id="b897c-187">ReturnUrlParameter</span><span class="sxs-lookup"><span data-stu-id="b897c-187">ReturnUrlParameter</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.returnurlparameter?view=aspnetcore-2.0) | <span data-ttu-id="b897c-188">Determina il nome del parametro della stringa di query che viene aggiunto dal gestore per una risposta 302 trovato (reindirizzamento URL).</span><span class="sxs-lookup"><span data-stu-id="b897c-188">Determines the name of the query string parameter that's appended by the handler for a 302 Found (URL redirect) response.</span></span> <span data-ttu-id="b897c-189">`ReturnUrlParameter` viene usato quando una richiesta arriva attraverso il `LoginPath` o `LogoutPath` per restituire il browser all'URL originale dopo che viene eseguita l'azione di connessione o disconnessione.</span><span class="sxs-lookup"><span data-stu-id="b897c-189">`ReturnUrlParameter` is used when a request arrives on the `LoginPath` or `LogoutPath` to return the browser to the original URL after the login or logout action is performed.</span></span> <span data-ttu-id="b897c-190">Il valore predefinito è `ReturnUrl`.</span><span class="sxs-lookup"><span data-stu-id="b897c-190">The default value is `ReturnUrl`.</span></span> |
| [<span data-ttu-id="b897c-191">SessionStore</span><span class="sxs-lookup"><span data-stu-id="b897c-191">SessionStore</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.sessionstore?view=aspnetcore-2.0) | <span data-ttu-id="b897c-192">Contenitore facoltativo usato per archiviare l'identità tra le richieste.</span><span class="sxs-lookup"><span data-stu-id="b897c-192">An optional container used to store identity across requests.</span></span> <span data-ttu-id="b897c-193">Quando usato, solo un identificatore di sessione viene inviato al client.</span><span class="sxs-lookup"><span data-stu-id="b897c-193">When used, only a session identifier is sent to the client.</span></span> <span data-ttu-id="b897c-194">`SessionStore` Consente di attenuare i potenziali problemi con le identità di grandi dimensioni.</span><span class="sxs-lookup"><span data-stu-id="b897c-194">`SessionStore` can be used to mitigate potential problems with large identities.</span></span> |
| [<span data-ttu-id="b897c-195">SlidingExpiration</span><span class="sxs-lookup"><span data-stu-id="b897c-195">SlidingExpiration</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-2.0) | <span data-ttu-id="b897c-196">Un flag che indica se un nuovo cookie con una scadenza aggiornato deve essere visualizzato in modo dinamico.</span><span class="sxs-lookup"><span data-stu-id="b897c-196">A flag indicating if a new cookie with an updated expiration time should be issued dynamically.</span></span> <span data-ttu-id="b897c-197">Questa situazione può verificarsi in qualsiasi richiesta in cui il periodo di scadenza cookie corrente è superiore al 50% scaduto.</span><span class="sxs-lookup"><span data-stu-id="b897c-197">This can happen on any request where the current cookie expiration period is more than 50% expired.</span></span> <span data-ttu-id="b897c-198">La nuova data di scadenza viene spostata in avanti è la data corrente più il `ExpireTimespan`.</span><span class="sxs-lookup"><span data-stu-id="b897c-198">The new expiration date is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="b897c-199">Un' [data di scadenza assoluto cookie](xref:security/authentication/cookie#absolute-cookie-expiration) può essere impostata tramite il `AuthenticationProperties` classe quando si chiama `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="b897c-199">An [absolute cookie expiration time](xref:security/authentication/cookie#absolute-cookie-expiration) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="b897c-200">Un'ora di scadenza assoluto può migliorare la sicurezza dell'app, limitando la quantità di tempo in cui il cookie di autenticazione è valido.</span><span class="sxs-lookup"><span data-stu-id="b897c-200">An absolute expiration time can improve the security of your app by limiting the amount of time that the authentication cookie is valid.</span></span> <span data-ttu-id="b897c-201">Il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="b897c-201">The default value is `true`.</span></span> |
| [<span data-ttu-id="b897c-202">TicketDataFormat</span><span class="sxs-lookup"><span data-stu-id="b897c-202">TicketDataFormat</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.ticketdataformat?view=aspnetcore-2.0) | <span data-ttu-id="b897c-203">Il `TicketDataFormat` viene usato per proteggere e rimuovere la protezione dell'identità e altre proprietà che vengono memorizzate nel valore del cookie.</span><span class="sxs-lookup"><span data-stu-id="b897c-203">The `TicketDataFormat` is used to protect and unprotect the identity and other properties that are stored in the cookie value.</span></span> <span data-ttu-id="b897c-204">Se non specificato, un `TicketDataFormat` viene creato usando il [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="b897c-204">If not provided, a `TicketDataFormat` is created using the [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0).</span></span> |
| [<span data-ttu-id="b897c-205">Validate</span><span class="sxs-lookup"><span data-stu-id="b897c-205">Validate</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.validate?view=aspnetcore-2.0) | <span data-ttu-id="b897c-206">Metodo che verifica che le opzioni sono valide.</span><span class="sxs-lookup"><span data-stu-id="b897c-206">Method that checks that the options are valid.</span></span> |

<span data-ttu-id="b897c-207">Impostare `CookieAuthenticationOptions` nella configurazione del servizio per l'autenticazione nel `ConfigureServices` metodo:</span><span class="sxs-lookup"><span data-stu-id="b897c-207">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b897c-208">ASP.NET Core 1.x Usa cookie [middleware](xref:fundamentals/middleware/index) che serializza un'entità utente in un cookie crittografato.</span><span class="sxs-lookup"><span data-stu-id="b897c-208">ASP.NET Core 1.x uses cookie [middleware](xref:fundamentals/middleware/index) that serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="b897c-209">Nelle richieste successive, il cookie viene convalidato e l'entità viene ricreata e assegnato al `HttpContext.User` proprietà.</span><span class="sxs-lookup"><span data-stu-id="b897c-209">On subsequent requests, the cookie is validated, and the principal is recreated and assigned to the `HttpContext.User` property.</span></span>

<span data-ttu-id="b897c-210">Installare il [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) pacchetto NuGet nel progetto.</span><span class="sxs-lookup"><span data-stu-id="b897c-210">Install the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) NuGet package in your project.</span></span> <span data-ttu-id="b897c-211">Questo pacchetto contiene il middleware dei cookie.</span><span class="sxs-lookup"><span data-stu-id="b897c-211">This package contains the cookie middleware.</span></span>

<span data-ttu-id="b897c-212">Usare il `UseCookieAuthentication` metodo nel `Configure` metodo nel *Startup.cs* file prima `UseMvc` o `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="b897c-212">Use the `UseCookieAuthentication` method in the `Configure` method in your *Startup.cs* file before `UseMvc` or `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
{
    AccessDeniedPath = "/Account/Forbidden/",
    AuthenticationScheme = CookieAuthenticationDefaults.AuthenticationScheme,
    AutomaticAuthenticate = true,
    AutomaticChallenge = true,
    LoginPath = "/Account/Unauthorized/"
});
```

<span data-ttu-id="b897c-213">**Opzioni di CookieAuthenticationOptions**</span><span class="sxs-lookup"><span data-stu-id="b897c-213">**CookieAuthenticationOptions Options**</span></span>

<span data-ttu-id="b897c-214">Il [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) classe viene utilizzata per configurare le opzioni del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="b897c-214">The [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) class is used to configure the authentication provider options.</span></span>

| <span data-ttu-id="b897c-215">Opzione</span><span class="sxs-lookup"><span data-stu-id="b897c-215">Option</span></span> | <span data-ttu-id="b897c-216">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b897c-216">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="b897c-217">AuthenticationScheme</span><span class="sxs-lookup"><span data-stu-id="b897c-217">AuthenticationScheme</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.authenticationscheme?view=aspnetcore-1.1) | <span data-ttu-id="b897c-218">Imposta lo schema di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="b897c-218">Sets the authentication scheme.</span></span> <span data-ttu-id="b897c-219">`AuthenticationScheme` è utile quando sono presenti più istanze di autenticazione e si desidera [autorizzare con uno schema specifico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="b897c-219">`AuthenticationScheme` is useful when there are multiple instances of authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="b897c-220">Impostando il `AuthenticationScheme` a `CookieAuthenticationDefaults.AuthenticationScheme` fornisce un valore di "Cookie" per lo schema.</span><span class="sxs-lookup"><span data-stu-id="b897c-220">Setting the `AuthenticationScheme` to `CookieAuthenticationDefaults.AuthenticationScheme` provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="b897c-221">È possibile specificare qualsiasi valore stringa che distingue lo schema.</span><span class="sxs-lookup"><span data-stu-id="b897c-221">You can supply any string value that distinguishes the scheme.</span></span> |
| [<span data-ttu-id="b897c-222">AutomaticAuthenticate</span><span class="sxs-lookup"><span data-stu-id="b897c-222">AutomaticAuthenticate</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticauthenticate?view=aspnetcore-1.1) | <span data-ttu-id="b897c-223">Imposta un valore per indicare che l'autenticazione dei cookie debba eseguire in ogni richiesta e tenta di convalidare e ricostruire qualsiasi entità serializzato, che creato.</span><span class="sxs-lookup"><span data-stu-id="b897c-223">Sets a value to indicate that the cookie authentication should run on every request and attempt to validate and reconstruct any serialized principal it created.</span></span> |
| [<span data-ttu-id="b897c-224">AutomaticChallenge</span><span class="sxs-lookup"><span data-stu-id="b897c-224">AutomaticChallenge</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticchallenge?view=aspnetcore-1.1) | <span data-ttu-id="b897c-225">Se true, il middleware di autenticazione gestisce le sfide automatica.</span><span class="sxs-lookup"><span data-stu-id="b897c-225">If true, the authentication middleware handles automatic challenges.</span></span> <span data-ttu-id="b897c-226">Se false, il middleware di autenticazione altera solo le risposte quando richiesto esplicitamente dal `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="b897c-226">If false, the authentication middleware only alters responses when explicitly indicated by the `AuthenticationScheme`.</span></span> |
| [<span data-ttu-id="b897c-227">ClaimsIssuer</span><span class="sxs-lookup"><span data-stu-id="b897c-227">ClaimsIssuer</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.claimsissuer?view=aspnetcore-1.1) | <span data-ttu-id="b897c-228">L'autorità emittente da usare per la [Issuer](/dotnet/api/system.security.claims.claim.issuer) proprietà in tutte le attestazioni creato dal middleware di autenticazione dei cookie.</span><span class="sxs-lookup"><span data-stu-id="b897c-228">The issuer to use for the [Issuer](/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication middleware.</span></span> |
| [<span data-ttu-id="b897c-229">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="b897c-229">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiedomain?view=aspnetcore-1.1) | <span data-ttu-id="b897c-230">Il nome di dominio in cui viene servito il cookie.</span><span class="sxs-lookup"><span data-stu-id="b897c-230">The domain name where the cookie is served.</span></span> <span data-ttu-id="b897c-231">Per impostazione predefinita, questo è il nome host della richiesta.</span><span class="sxs-lookup"><span data-stu-id="b897c-231">By default, this is the host name of the request.</span></span> <span data-ttu-id="b897c-232">Il browser viene usato solo il cookie a un nome host corrispondente.</span><span class="sxs-lookup"><span data-stu-id="b897c-232">The browser only serves the cookie to a matching host name.</span></span> <span data-ttu-id="b897c-233">È possibile modificare questa opzione per disporre i cookie disponibili a tutti gli host nel dominio.</span><span class="sxs-lookup"><span data-stu-id="b897c-233">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="b897c-234">Ad esempio, l'impostazione del dominio del cookie `.contoso.com` renderli disponibili agli `contoso.com`, `www.contoso.com`, e `staging.www.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="b897c-234">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, and `staging.www.contoso.com`.</span></span> |
| [<span data-ttu-id="b897c-235">CookieHttpOnly</span><span class="sxs-lookup"><span data-stu-id="b897c-235">CookieHttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiehttponly?view=aspnetcore-1.1) | <span data-ttu-id="b897c-236">Un flag che indica se il cookie deve essere accessibile solo ai server.</span><span class="sxs-lookup"><span data-stu-id="b897c-236">A flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="b897c-237">Se si modifica questo valore per `false` consente a script lato client per accedere al cookie e può aprire l'app al furto di cookie che l'app deve avere una [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerabilità.</span><span class="sxs-lookup"><span data-stu-id="b897c-237">Changing this value to `false` permits client-side scripts to access the cookie and may open your app to cookie theft should your app have a [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerability.</span></span> <span data-ttu-id="b897c-238">Il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="b897c-238">The default value is `true`.</span></span> |
| [<span data-ttu-id="b897c-239">CookiePath</span><span class="sxs-lookup"><span data-stu-id="b897c-239">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiepath?view=aspnetcore-1.1) | <span data-ttu-id="b897c-240">Consente di isolare le App in esecuzione sullo stesso nome host.</span><span class="sxs-lookup"><span data-stu-id="b897c-240">Used to isolate apps running on the same host name.</span></span> <span data-ttu-id="b897c-241">Se si dispone di un'app in esecuzione in `/app1` e si desidera limitare i cookie per tale app, impostare il `CookiePath` proprietà `/app1`.</span><span class="sxs-lookup"><span data-stu-id="b897c-241">If you have an app running at `/app1` and want to restrict cookies to that app, set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="b897c-242">In questo modo, il cookie è disponibile solo nelle richieste al `/app1` e a qualsiasi app in esso contenuti.</span><span class="sxs-lookup"><span data-stu-id="b897c-242">By doing so, the cookie is only available on requests to `/app1` and any app underneath it.</span></span> |
| [<span data-ttu-id="b897c-243">CookieSecure</span><span class="sxs-lookup"><span data-stu-id="b897c-243">CookieSecure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiesecure?view=aspnetcore-1.1) | <span data-ttu-id="b897c-244">Un flag che indica se il cookie creato deve essere limitato a HTTPS (`CookieSecurePolicy.Always`), HTTP o HTTPS (`CookieSecurePolicy.None`), o lo stesso protocollo della richiesta (`CookieSecurePolicy.SameAsRequest`).</span><span class="sxs-lookup"><span data-stu-id="b897c-244">A flag indicating if the cookie created should be limited to HTTPS (`CookieSecurePolicy.Always`), HTTP or HTTPS (`CookieSecurePolicy.None`), or the same protocol as the request (`CookieSecurePolicy.SameAsRequest`).</span></span> <span data-ttu-id="b897c-245">Il valore predefinito è `CookieSecurePolicy.SameAsRequest`.</span><span class="sxs-lookup"><span data-stu-id="b897c-245">The default value is `CookieSecurePolicy.SameAsRequest`.</span></span> |
| [<span data-ttu-id="b897c-246">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b897c-246">Description</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.description?view=aspnetcore-1.1) | <span data-ttu-id="b897c-247">Informazioni aggiuntive sul tipo di autenticazione che viene reso disponibile per l'app.</span><span class="sxs-lookup"><span data-stu-id="b897c-247">Additional information about the authentication type which is made available to the app.</span></span> |
| [<span data-ttu-id="b897c-248">ExpireTimeSpan</span><span class="sxs-lookup"><span data-stu-id="b897c-248">ExpireTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.expiretimespan?view=aspnetcore-1.1) | <span data-ttu-id="b897c-249">Il `TimeSpan` dopo che scade il ticket di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="b897c-249">The `TimeSpan` after which the authentication ticket expires.</span></span> <span data-ttu-id="b897c-250">Viene aggiunto all'ora corrente per creare l'ora di scadenza per il ticket.</span><span class="sxs-lookup"><span data-stu-id="b897c-250">It's added to the current time to create the expiration time for the ticket.</span></span> <span data-ttu-id="b897c-251">Per utilizzare `ExpireTimeSpan`, è necessario impostare `IsPersistent` a `true` nel `AuthenticationProperties` passato a `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="b897c-251">To use `ExpireTimeSpan`, you must set `IsPersistent` to `true` in the `AuthenticationProperties` passed to `SignInAsync`.</span></span> <span data-ttu-id="b897c-252">Il valore predefinito è 14 giorni.</span><span class="sxs-lookup"><span data-stu-id="b897c-252">The default value is 14 days.</span></span> |
| [<span data-ttu-id="b897c-253">SlidingExpiration</span><span class="sxs-lookup"><span data-stu-id="b897c-253">SlidingExpiration</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-1.1) | <span data-ttu-id="b897c-254">Un flag che indica se la data di scadenza del cookie viene reimpostato quando più della metà del `ExpireTimeSpan` superamento dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="b897c-254">A flag indicating whether the cookie expiration date resets when more than half of the `ExpireTimeSpan` interval has passed.</span></span> <span data-ttu-id="b897c-255">La nuova ora exipiration viene spostata in avanti è la data corrente più il `ExpireTimespan`.</span><span class="sxs-lookup"><span data-stu-id="b897c-255">The new exipiration time is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="b897c-256">Un' [data di scadenza assoluto cookie](xref:security/authentication/cookie#absolute-cookie-expiration) può essere impostata tramite il `AuthenticationProperties` classe quando si chiama `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="b897c-256">An [absolute cookie expiration time](xref:security/authentication/cookie#absolute-cookie-expiration) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="b897c-257">Un'ora di scadenza assoluto può migliorare la sicurezza dell'app, limitando la quantità di tempo in cui il cookie di autenticazione è valido.</span><span class="sxs-lookup"><span data-stu-id="b897c-257">An absolute expiration time can improve the security of your app by limiting the amount of time that the authentication cookie is valid.</span></span> <span data-ttu-id="b897c-258">Il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="b897c-258">The default value is `true`.</span></span> |

<span data-ttu-id="b897c-259">Impostare `CookieAuthenticationOptions` per il Middleware di autenticazione del Cookie nel `Configure` metodo:</span><span class="sxs-lookup"><span data-stu-id="b897c-259">Set `CookieAuthenticationOptions` for the Cookie Authentication Middleware in the `Configure` method:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    ...
});
```

::: moniker-end

## <a name="cookie-policy-middleware"></a><span data-ttu-id="b897c-260">Middleware cookie criteri</span><span class="sxs-lookup"><span data-stu-id="b897c-260">Cookie Policy Middleware</span></span>

<span data-ttu-id="b897c-261">[Middleware cookie criteri](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) offre funzionalità di criteri di cookie in un'app.</span><span class="sxs-lookup"><span data-stu-id="b897c-261">[Cookie Policy Middleware](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) enables cookie policy capabilities in an app.</span></span> <span data-ttu-id="b897c-262">Aggiungere il middleware alla pipeline di elaborazione delle app è ordine sensibili; riguarda solo i componenti registrati dopo di esso nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="b897c-262">Adding the middleware to the app processing pipeline is order sensitive; it only affects components registered after it in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

 <span data-ttu-id="b897c-263">Il [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) fornito per il Middleware dei Cookie criteri consentono di controllare caratteristiche globali del cookie elaborazione e di hook in elaborazione cookie gestori quando i cookie vengono aggiunti o eliminati.</span><span class="sxs-lookup"><span data-stu-id="b897c-263">The [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) provided to the Cookie Policy Middleware allow you to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

| <span data-ttu-id="b897c-264">Proprietà</span><span class="sxs-lookup"><span data-stu-id="b897c-264">Property</span></span> | <span data-ttu-id="b897c-265">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b897c-265">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="b897c-266">HttpOnly</span><span class="sxs-lookup"><span data-stu-id="b897c-266">HttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.httponly) | <span data-ttu-id="b897c-267">Interessa se i cookie devono essere HttpOnly, che è un flag che indica se il cookie deve essere accessibile solo ai server.</span><span class="sxs-lookup"><span data-stu-id="b897c-267">Affects whether cookies must be HttpOnly, which is a flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="b897c-268">Il valore predefinito è `HttpOnlyPolicy.None`.</span><span class="sxs-lookup"><span data-stu-id="b897c-268">The default value is `HttpOnlyPolicy.None`.</span></span> |
| [<span data-ttu-id="b897c-269">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="b897c-269">MinimumSameSitePolicy</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.minimumsamesitepolicy) | <span data-ttu-id="b897c-270">Interessa attributo SameSite del cookie (vedere sotto).</span><span class="sxs-lookup"><span data-stu-id="b897c-270">Affects the cookie's same-site attribute (see below).</span></span> <span data-ttu-id="b897c-271">Il valore predefinito è `SameSiteMode.Lax`.</span><span class="sxs-lookup"><span data-stu-id="b897c-271">The default value is `SameSiteMode.Lax`.</span></span> <span data-ttu-id="b897c-272">Questa opzione è disponibile per ASP.NET Core 2.0 +.</span><span class="sxs-lookup"><span data-stu-id="b897c-272">This option is available for ASP.NET Core 2.0+.</span></span> |
| [<span data-ttu-id="b897c-273">OnAppendCookie</span><span class="sxs-lookup"><span data-stu-id="b897c-273">OnAppendCookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.onappendcookie) | <span data-ttu-id="b897c-274">Chiamato quando viene aggiunto un cookie.</span><span class="sxs-lookup"><span data-stu-id="b897c-274">Called when a cookie is appended.</span></span> |
| [<span data-ttu-id="b897c-275">OnDeleteCookie</span><span class="sxs-lookup"><span data-stu-id="b897c-275">OnDeleteCookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.ondeletecookie) | <span data-ttu-id="b897c-276">Chiamato quando viene eliminato un cookie.</span><span class="sxs-lookup"><span data-stu-id="b897c-276">Called when a cookie is deleted.</span></span> |
| [<span data-ttu-id="b897c-277">Proteggere</span><span class="sxs-lookup"><span data-stu-id="b897c-277">Secure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.secure) | <span data-ttu-id="b897c-278">Determina se i cookie devono essere sicura.</span><span class="sxs-lookup"><span data-stu-id="b897c-278">Affects whether cookies must be Secure.</span></span> <span data-ttu-id="b897c-279">Il valore predefinito è `CookieSecurePolicy.None`.</span><span class="sxs-lookup"><span data-stu-id="b897c-279">The default value is `CookieSecurePolicy.None`.</span></span> |

<span data-ttu-id="b897c-280">**MinimumSameSitePolicy** (ASP.NET Core 2.0 + solo)</span><span class="sxs-lookup"><span data-stu-id="b897c-280">**MinimumSameSitePolicy** (ASP.NET Core 2.0+ only)</span></span>

<span data-ttu-id="b897c-281">Il valore predefinito `MinimumSameSitePolicy` valore è `SameSiteMode.Lax` per consentire l'autenticazione OAuth2.</span><span class="sxs-lookup"><span data-stu-id="b897c-281">The default `MinimumSameSitePolicy` value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="b897c-282">Per applicare rigorosamente criteri nello stesso sito di `SameSiteMode.Strict`, impostare il `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="b897c-282">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="b897c-283">Anche se questa impostazione interrompe OAuth2 e altri schemi di autenticazione tra le origini, eleva il livello di protezione dei cookie per altri tipi di App che non richiedono l'elaborazione della richiesta multiorigine.</span><span class="sxs-lookup"><span data-stu-id="b897c-283">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="b897c-284">L'impostazione di criteri Middleware dei Cookie per `MinimumSameSitePolicy` può influire sull'impostazione di `Cookie.SameSite` in `CookieAuthenticationOptions` impostazioni a seconda della matrice riportata di seguito.</span><span class="sxs-lookup"><span data-stu-id="b897c-284">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect your setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="b897c-285">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="b897c-285">MinimumSameSitePolicy</span></span> | <span data-ttu-id="b897c-286">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="b897c-286">Cookie.SameSite</span></span> | <span data-ttu-id="b897c-287">Impostazione Cookie.SameSite risultante</span><span class="sxs-lookup"><span data-stu-id="b897c-287">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="b897c-288">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="b897c-288">SameSiteMode.None</span></span>     | <span data-ttu-id="b897c-289">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="b897c-289">SameSiteMode.None</span></span><br><span data-ttu-id="b897c-290">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="b897c-290">SameSiteMode.Lax</span></span><br><span data-ttu-id="b897c-291">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="b897c-291">SameSiteMode.Strict</span></span> | <span data-ttu-id="b897c-292">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="b897c-292">SameSiteMode.None</span></span><br><span data-ttu-id="b897c-293">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="b897c-293">SameSiteMode.Lax</span></span><br><span data-ttu-id="b897c-294">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="b897c-294">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="b897c-295">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="b897c-295">SameSiteMode.Lax</span></span>      | <span data-ttu-id="b897c-296">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="b897c-296">SameSiteMode.None</span></span><br><span data-ttu-id="b897c-297">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="b897c-297">SameSiteMode.Lax</span></span><br><span data-ttu-id="b897c-298">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="b897c-298">SameSiteMode.Strict</span></span> | <span data-ttu-id="b897c-299">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="b897c-299">SameSiteMode.Lax</span></span><br><span data-ttu-id="b897c-300">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="b897c-300">SameSiteMode.Lax</span></span><br><span data-ttu-id="b897c-301">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="b897c-301">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="b897c-302">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="b897c-302">SameSiteMode.Strict</span></span>   | <span data-ttu-id="b897c-303">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="b897c-303">SameSiteMode.None</span></span><br><span data-ttu-id="b897c-304">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="b897c-304">SameSiteMode.Lax</span></span><br><span data-ttu-id="b897c-305">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="b897c-305">SameSiteMode.Strict</span></span> | <span data-ttu-id="b897c-306">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="b897c-306">SameSiteMode.Strict</span></span><br><span data-ttu-id="b897c-307">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="b897c-307">SameSiteMode.Strict</span></span><br><span data-ttu-id="b897c-308">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="b897c-308">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="b897c-309">Creare un cookie di autenticazione</span><span class="sxs-lookup"><span data-stu-id="b897c-309">Create an authentication cookie</span></span>

<span data-ttu-id="b897c-310">Per creare un cookie che contiene informazioni sull'utente, è necessario creare un [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal).</span><span class="sxs-lookup"><span data-stu-id="b897c-310">To create a cookie holding user information, you must construct a [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal).</span></span> <span data-ttu-id="b897c-311">Le informazioni dell'utente vengano serializzate e archiviate nel cookie.</span><span class="sxs-lookup"><span data-stu-id="b897c-311">The user information is serialized and stored in the cookie.</span></span> 

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b897c-312">Creare un [ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity) con le operazioni [attestazione](/dotnet/api/system.security.claims.claim)s e chiamare [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) per l'accesso dell'utente:</span><span class="sxs-lookup"><span data-stu-id="b897c-312">Create a [ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity) with any required [Claim](/dotnet/api/system.security.claims.claim)s and call [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b897c-313">Chiamare [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) per l'accesso dell'utente:</span><span class="sxs-lookup"><span data-stu-id="b897c-313">Call [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) to sign in the user:</span></span>

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker-end

<span data-ttu-id="b897c-314">`SignInAsync` Crea un cookie crittografato e lo aggiunge alla risposta corrente.</span><span class="sxs-lookup"><span data-stu-id="b897c-314">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="b897c-315">Se non si specifica un `AuthenticationScheme`, viene usato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="b897c-315">If you don't specify an `AuthenticationScheme`, the default scheme is used.</span></span>

<span data-ttu-id="b897c-316">Dietro le quinte, la crittografia usata è ASP.NET Core [protezione dati](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) sistema.</span><span class="sxs-lookup"><span data-stu-id="b897c-316">Under the covers, the encryption used is ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) system.</span></span> <span data-ttu-id="b897c-317">Se si ospita app su più computer, il bilanciamento del carico tra le app o utilizzando una web farm, quindi devi [configurare la protezione dati](xref:security/data-protection/configuration/overview) usare il Keyring stesso e l'identificatore dell'app.</span><span class="sxs-lookup"><span data-stu-id="b897c-317">If you're hosting app on multiple machines, load balancing across apps, or using a web farm, then you must [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="b897c-318">Esci</span><span class="sxs-lookup"><span data-stu-id="b897c-318">Sign out</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b897c-319">Per disconnettere l'utente corrente ed eliminare i cookie, chiamare [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):</span><span class="sxs-lookup"><span data-stu-id="b897c-319">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b897c-320">Per disconnettere l'utente corrente ed eliminare i cookie, chiamare [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):</span><span class="sxs-lookup"><span data-stu-id="b897c-320">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):</span></span>

```csharp
await HttpContext.Authentication.SignOutAsync(
    CookieAuthenticationDefaults.AuthenticationScheme);
```

::: moniker-end

<span data-ttu-id="b897c-321">Se non si usa `CookieAuthenticationDefaults.AuthenticationScheme` (o "Cookie") come lo schema (ad esempio, "ContosoCookie"), specificare lo schema usato durante la configurazione del provider di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="b897c-321">If you aren't using `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") as the scheme (for example, "ContosoCookie"), supply the scheme you used when configuring the authentication provider.</span></span> <span data-ttu-id="b897c-322">In caso contrario, viene usato lo schema predefinito.</span><span class="sxs-lookup"><span data-stu-id="b897c-322">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="b897c-323">Reagire alle modifiche di back-end</span><span class="sxs-lookup"><span data-stu-id="b897c-323">React to back-end changes</span></span>

<span data-ttu-id="b897c-324">Dopo aver creato un cookie, diventa l'unica origine di identità.</span><span class="sxs-lookup"><span data-stu-id="b897c-324">Once a cookie is created, it becomes the single source of identity.</span></span> <span data-ttu-id="b897c-325">Anche se si disabilita un utente nei sistemi back-end, il sistema di autenticazione del cookie non è a conoscenza di questo oggetto e un utente rimane connesso, purché il cookie è valido.</span><span class="sxs-lookup"><span data-stu-id="b897c-325">Even if you disable a user in your back-end systems, the cookie authentication system has no knowledge of this, and a user stays logged in as long as their cookie is valid.</span></span>

<span data-ttu-id="b897c-326">Il [ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) evento in ASP.NET Core 2.x o il [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) metodo in ASP.NET Core 1.x è utilizzabile per intercettare ed eseguire l'override di convalida dell'identità del cookie.</span><span class="sxs-lookup"><span data-stu-id="b897c-326">The [ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) event in ASP.NET Core 2.x or the [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) method in ASP.NET Core 1.x can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="b897c-327">Questo approccio riduce il rischio di revocato agli utenti l'accesso all'app.</span><span class="sxs-lookup"><span data-stu-id="b897c-327">This approach mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="b897c-328">Un approccio alla convalida dei cookie si basa su tenere traccia di quando il database utente è stato modificato.</span><span class="sxs-lookup"><span data-stu-id="b897c-328">One approach to cookie validation is based on keeping track of when the user database has been changed.</span></span> <span data-ttu-id="b897c-329">Se il database non è stato modificato perché è stato emesso il cookie dell'utente, non è necessario autenticare nuovamente l'utente se il cookie sia ancora valido.</span><span class="sxs-lookup"><span data-stu-id="b897c-329">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="b897c-330">Per implementare questo scenario, il database, che viene implementato in `IUserRepository` in questo esempio archivia un `LastChanged` valore.</span><span class="sxs-lookup"><span data-stu-id="b897c-330">To implement this scenario, the database, which is implemented in `IUserRepository` for this example, stores a `LastChanged` value.</span></span> <span data-ttu-id="b897c-331">Quando un utente viene aggiornato nel database, il `LastChanged` valore è impostato sull'ora corrente.</span><span class="sxs-lookup"><span data-stu-id="b897c-331">When any user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="b897c-332">Per poter invalidare un cookie durante le modifiche del database in base il `LastChanged` valore, creare il cookie con un `LastChanged` attestazione contenente l'oggetto corrente `LastChanged` valore dal database:</span><span class="sxs-lookup"><span data-stu-id="b897c-332">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b897c-333">Per implementare una sostituzione per il `ValidatePrincipal` evento, scrivere un metodo con la firma seguente in una classe derivata da [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):</span><span class="sxs-lookup"><span data-stu-id="b897c-333">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that you derive from [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="b897c-334">Un esempio è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b897c-334">An example looks like the following:</span></span>

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

<span data-ttu-id="b897c-335">Registrare l'istanza di eventi durante la registrazione del servizio nel cookie di `ConfigureServices` (metodo).</span><span class="sxs-lookup"><span data-stu-id="b897c-335">Register the events instance during cookie service registration in the `ConfigureServices` method.</span></span> <span data-ttu-id="b897c-336">Fornire una registrazione del servizio con ambito per il `CustomCookieAuthenticationEvents` classe:</span><span class="sxs-lookup"><span data-stu-id="b897c-336">Provide a scoped service registration for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b897c-337">Per implementare una sostituzione per il `ValidateAsync` evento, scrivere un metodo con la firma seguente:</span><span class="sxs-lookup"><span data-stu-id="b897c-337">To implement an override for the `ValidateAsync` event, write a method with the following signature:</span></span>

```csharp
ValidateAsync(CookieValidatePrincipalContext)
```

<span data-ttu-id="b897c-338">ASP.NET Core Identity implementa questo controllo come parte del relativo [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync).</span><span class="sxs-lookup"><span data-stu-id="b897c-338">ASP.NET Core Identity implements this check as part of its [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync).</span></span> <span data-ttu-id="b897c-339">Un esempio è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b897c-339">An example looks like the following:</span></span>

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = 
            context.HttpContext.RequestServices
                .GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="b897c-340">Registrare l'evento durante la configurazione dell'autenticazione nel cookie di `Configure` metodo:</span><span class="sxs-lookup"><span data-stu-id="b897c-340">Register the event during cookie authentication configuration in the `Configure` method:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    Events = new CookieAuthenticationEvents
    {
        OnValidatePrincipal = LastChangedValidator.ValidateAsync
    }
});
```

::: moniker-end

<span data-ttu-id="b897c-341">Si consideri una situazione in cui viene aggiornato il nome dell'utente &mdash; una decisione che non influiscono sulla sicurezza in alcun modo.</span><span class="sxs-lookup"><span data-stu-id="b897c-341">Consider a situation in which the user's name is updated &mdash; a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="b897c-342">Se si desidera aggiornare in modo non distruttivo l'entità utente, chiamare `context.ReplacePrincipal` e impostare il `context.ShouldRenew` proprietà `true`.</span><span class="sxs-lookup"><span data-stu-id="b897c-342">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="b897c-343">L'approccio descritto di seguito viene attivato a ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="b897c-343">The approach described here is triggered on every request.</span></span> <span data-ttu-id="b897c-344">Ciò può comportare una riduzione delle prestazioni di grandi dimensioni per l'app.</span><span class="sxs-lookup"><span data-stu-id="b897c-344">This can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="b897c-345">Cookie permanenti</span><span class="sxs-lookup"><span data-stu-id="b897c-345">Persistent cookies</span></span>

<span data-ttu-id="b897c-346">È possibile il cookie per rendere persistenti le sessioni del browser.</span><span class="sxs-lookup"><span data-stu-id="b897c-346">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="b897c-347">La persistenza deve essere abilitata solo con il consenso dell'utente esplicita con una "Memorizza password" casella di controllo in account di accesso o un meccanismo simile.</span><span class="sxs-lookup"><span data-stu-id="b897c-347">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on login or a similar mechanism.</span></span> 

<span data-ttu-id="b897c-348">Il frammento di codice seguente crea un'identità e un corrispondente cookie in cui viene conservata tramite le chiusure del browser.</span><span class="sxs-lookup"><span data-stu-id="b897c-348">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="b897c-349">Vengono rispettate le impostazioni di scadenza scorrevole configurate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="b897c-349">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="b897c-350">Se il cookie scade mentre il browser viene chiuso, il browser Cancella il cookie dopo il riavvio.</span><span class="sxs-lookup"><span data-stu-id="b897c-350">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="b897c-351">Il [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) risiede nella classe di `Microsoft.AspNetCore.Authentication` dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="b897c-351">The [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) class resides in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="b897c-352">Il [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) risiede nella classe di `Microsoft.AspNetCore.Http.Authentication` dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="b897c-352">The [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) class resides in the `Microsoft.AspNetCore.Http.Authentication` namespace.</span></span>

::: moniker-end

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="b897c-353">Scadenza del cookie assoluto</span><span class="sxs-lookup"><span data-stu-id="b897c-353">Absolute cookie expiration</span></span>

<span data-ttu-id="b897c-354">È possibile impostare un'ora di scadenza assoluto con `ExpiresUtc`.</span><span class="sxs-lookup"><span data-stu-id="b897c-354">You can set an absolute expiration time with `ExpiresUtc`.</span></span> <span data-ttu-id="b897c-355">È necessario impostare anche `IsPersistent`; in caso contrario, `ExpiresUtc` viene ignorato e viene creato un cookie di sessione singola.</span><span class="sxs-lookup"><span data-stu-id="b897c-355">You must also set `IsPersistent`; otherwise, `ExpiresUtc` is ignored and a single-session cookie is created.</span></span> <span data-ttu-id="b897c-356">Quando `ExpiresUtc` è nastavit `SignInAsync`, sostituisce il valore della `ExpireTimeSpan` opzione di `CookieAuthenticationOptions`, se impostata.</span><span class="sxs-lookup"><span data-stu-id="b897c-356">When `ExpiresUtc` is set on `SignInAsync`, it overrides the value of the `ExpireTimeSpan` option of `CookieAuthenticationOptions`, if set.</span></span>

<span data-ttu-id="b897c-357">Il frammento di codice seguente crea un'identità e un corrispondente cookie che dura per 20 minuti.</span><span class="sxs-lookup"><span data-stu-id="b897c-357">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="b897c-358">Ignora eventuali impostazioni di scadenza scorrevole configurati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="b897c-358">This ignores any sliding expiration settings previously configured.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
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

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b897c-359">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b897c-359">Additional resources</span></span>

* [<span data-ttu-id="b897c-360">Modifiche di autorizzazione 2.0 / migrazione annuncio</span><span class="sxs-lookup"><span data-stu-id="b897c-360">Auth 2.0 Changes / Migration Announcement</span></span>](https://github.com/aspnet/Announcements/issues/262)
* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="b897c-361">Controlli di ruolo basata su criteri</span><span class="sxs-lookup"><span data-stu-id="b897c-361">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
