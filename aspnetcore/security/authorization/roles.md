---
title: Autorizzazione basata sui ruoli in ASP.NET Core
author: rick-anderson
description: Informazioni su come limitare l'accesso ASP.NET Core controller e le azioni passando i ruoli all'attributo di autorizzazione.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: 28aa3df6aa661d0b762df78fe611cd827af43f75
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73660043"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="ec5dd-103">Autorizzazione basata sui ruoli in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ec5dd-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="ec5dd-104">Quando viene creata un'identità, può appartenere a uno o più ruoli.</span><span class="sxs-lookup"><span data-stu-id="ec5dd-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="ec5dd-105">Ad esempio, Tracy può appartenere ai ruoli amministratore e utente mentre Scott può appartenere solo al ruolo utente.</span><span class="sxs-lookup"><span data-stu-id="ec5dd-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="ec5dd-106">La modalità di creazione e gestione di questi ruoli dipende dall'archivio di backup del processo di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="ec5dd-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="ec5dd-107">I ruoli vengono esposti allo sviluppatore tramite il metodo [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) nella classe [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .</span><span class="sxs-lookup"><span data-stu-id="ec5dd-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="ec5dd-108">Aggiunta di controlli di ruolo</span><span class="sxs-lookup"><span data-stu-id="ec5dd-108">Adding role checks</span></span>

<span data-ttu-id="ec5dd-109">I controlli delle autorizzazioni in base al ruolo sono dichiarativi&mdash;lo sviluppatore li incorpora nel codice, a fronte di un controller o di un'azione all'interno di un controller, specificando i ruoli di cui l'utente corrente deve essere membro per accedere alla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="ec5dd-109">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="ec5dd-110">Il codice seguente, ad esempio, limita l'accesso a qualsiasi azione sul `AdministrationController` agli utenti che sono membri del ruolo `Administrator`:</span><span class="sxs-lookup"><span data-stu-id="ec5dd-110">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="ec5dd-111">È possibile specificare più ruoli come un elenco delimitato da virgole:</span><span class="sxs-lookup"><span data-stu-id="ec5dd-111">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="ec5dd-112">Questo controller sarebbe accessibile solo agli utenti che sono membri del ruolo `HRManager` o del ruolo `Finance`.</span><span class="sxs-lookup"><span data-stu-id="ec5dd-112">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="ec5dd-113">Se si applicano più attributi, un utente che esegue l'accesso deve essere un membro di tutti i ruoli specificati. per l'esempio seguente è necessario che un utente sia membro del ruolo `PowerUser` e `ControlPanelUser`.</span><span class="sxs-lookup"><span data-stu-id="ec5dd-113">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="ec5dd-114">È possibile limitare ulteriormente l'accesso applicando attributi di autorizzazione del ruolo aggiuntivi a livello di azione:</span><span class="sxs-lookup"><span data-stu-id="ec5dd-114">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

<span data-ttu-id="ec5dd-115">Nel frammento di codice precedente i membri del ruolo `Administrator` o del ruolo `PowerUser` possono accedere al controller e all'azione `SetTime`, ma solo i membri del ruolo `Administrator` possono accedere all'azione `ShutDown`.</span><span class="sxs-lookup"><span data-stu-id="ec5dd-115">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="ec5dd-116">È anche possibile bloccare un controller, ma consentire l'accesso anonimo e non autenticato a singole azioni.</span><span class="sxs-lookup"><span data-stu-id="ec5dd-116">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="ec5dd-117">Per Razor Pages, il `AuthorizeAttribute` può essere applicato da uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="ec5dd-117">For Razor Pages, the `AuthorizeAttribute` can be applied by either:</span></span>

* <span data-ttu-id="ec5dd-118">Utilizzando una [convenzione](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)o</span><span class="sxs-lookup"><span data-stu-id="ec5dd-118">Using a [convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), or</span></span>
* <span data-ttu-id="ec5dd-119">Applicazione dell'`AuthorizeAttribute` all'istanza di `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="ec5dd-119">Applying the `AuthorizeAttribute` to the `PageModel` instance:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> <span data-ttu-id="ec5dd-120">Gli attributi di filtro, inclusi `AuthorizeAttribute`, possono essere applicati solo a PageModel e non possono essere applicati a metodi specifici del gestore di pagine.</span><span class="sxs-lookup"><span data-stu-id="ec5dd-120">Filter attributes, including `AuthorizeAttribute`, can only be applied to PageModel and cannot be applied to specific page handler methods.</span></span>
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="ec5dd-121">Controlli dei ruoli basati su criteri</span><span class="sxs-lookup"><span data-stu-id="ec5dd-121">Policy based role checks</span></span>

<span data-ttu-id="ec5dd-122">I requisiti del ruolo possono essere espressi anche usando la nuova sintassi dei criteri, in cui uno sviluppatore registra un criterio all'avvio come parte della configurazione del servizio di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="ec5dd-122">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="ec5dd-123">Questa situazione si verifica in genere in `ConfigureServices()` nel file *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="ec5dd-123">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

<span data-ttu-id="ec5dd-124">I criteri vengono applicati utilizzando la proprietà `Policy` sull'attributo `AuthorizeAttribute`:</span><span class="sxs-lookup"><span data-stu-id="ec5dd-124">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="ec5dd-125">Se si desidera specificare più ruoli consentiti in un requisito, è possibile specificarli come parametri per il metodo `RequireRole`:</span><span class="sxs-lookup"><span data-stu-id="ec5dd-125">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="ec5dd-126">Questo esempio autorizza gli utenti che appartengono ai ruoli `Administrator`, `PowerUser` o `BackupAdministrator`.</span><span class="sxs-lookup"><span data-stu-id="ec5dd-126">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-identity"></a><span data-ttu-id="ec5dd-127">Aggiungere servizi ruolo all'identità</span><span class="sxs-lookup"><span data-stu-id="ec5dd-127">Add Role services to Identity</span></span>

<span data-ttu-id="ec5dd-128">Aggiungere [Aggiungi ruoli](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) per aggiungere servizi ruolo:</span><span class="sxs-lookup"><span data-stu-id="ec5dd-128">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

