---
title: Identità di scaffolding in progetti ASP.NET Core
author: rick-anderson
description: Informazioni su come eseguire lo scaffolding di identità in un progetto ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 7445db31e461bf61e8a91af7239187a6ece9d011
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705471"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="92876-103">Identità di scaffolding in progetti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92876-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="92876-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="92876-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="92876-105">ASP.NET Core 2.1 e versioni successive offre [ASP.NET Core Identity](xref:security/authentication/identity) come una [libreria di classi Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="92876-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="92876-106">Applicazioni che includono l'identità è possono applicare l'utilità di scaffolding per aggiungere in modo selettivo il codice sorgente contenuto in libreria di classe di Razor l'identità (RCL).</span><span class="sxs-lookup"><span data-stu-id="92876-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="92876-107">Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="92876-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="92876-108">Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione.</span><span class="sxs-lookup"><span data-stu-id="92876-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="92876-109">Il codice generato ha la precedenza rispetto allo stesso codice nella libreria di classi Razor per Identity.</span><span class="sxs-lookup"><span data-stu-id="92876-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="92876-110">Per esercitare il pieno controllo dell'interfaccia utente e non usare il valore predefinito RCL, vedere la sezione [origine dell'interfaccia utente di creare identità completa](#full).</span><span class="sxs-lookup"><span data-stu-id="92876-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="92876-111">Le applicazioni che hanno **non** includono l'autenticazione è possibile applicare l'utilità di scaffolding per aggiungere il pacchetto RCL identità.</span><span class="sxs-lookup"><span data-stu-id="92876-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="92876-112">È possibile selezionare il codice Identity da generare.</span><span class="sxs-lookup"><span data-stu-id="92876-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="92876-113">Anche se l'utilità di scaffolding genera la maggior parte del codice necessario, è possibile aggiornare il progetto per completare il processo.</span><span class="sxs-lookup"><span data-stu-id="92876-113">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="92876-114">Questo documento illustra i passaggi necessari per completare un aggiornamento di scaffolding di identità.</span><span class="sxs-lookup"><span data-stu-id="92876-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="92876-115">Quando si esegue l'utilità di scaffolding di identità, un *Scaffoldingreadme* file viene creato nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="92876-115">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="92876-116">Il *Scaffoldingreadme* file contiene istruzioni generali sul cosa è necessario per completare l'aggiornamento di scaffolding di identità.</span><span class="sxs-lookup"><span data-stu-id="92876-116">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="92876-117">Questo documento contiene istruzioni più complete rispetto al *Scaffoldingreadme* file.</span><span class="sxs-lookup"><span data-stu-id="92876-117">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="92876-118">È consigliabile usare un sistema di controllo di origine che mostra le differenze tra file e consente di eseguire l'annullamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="92876-118">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="92876-119">Esaminare le modifiche dopo aver eseguito l'utilità di scaffolding di identità.</span><span class="sxs-lookup"><span data-stu-id="92876-119">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="92876-120">Servizi sono necessari quando si usa [autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), [ripristino conferma e la password dell'Account](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con identità.</span><span class="sxs-lookup"><span data-stu-id="92876-120">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="92876-121">I servizi o stub di servizio non viene generato quando lo scaffolding di identità.</span><span class="sxs-lookup"><span data-stu-id="92876-121">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="92876-122">Servizi per abilitare queste funzionalità devono essere aggiunti manualmente.</span><span class="sxs-lookup"><span data-stu-id="92876-122">Services to enable these features must be added manually.</span></span> <span data-ttu-id="92876-123">Ad esempio, vedere [richiedere conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="92876-123">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="92876-124">Identità di scaffolding in un progetto vuoto</span><span class="sxs-lookup"><span data-stu-id="92876-124">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="92876-125">Aggiungere le chiamate seguenti evidenziate per il `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="92876-125">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="92876-126">Identità di scaffolding in un progetto Razor senza autorizzazione esistenti</span><span class="sxs-lookup"><span data-stu-id="92876-126">Scaffold identity into a Razor project without existing authorization</span></span>

<!--
set projNam=RPnoAuth
set projType=webapp
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="92876-127">Identità è configurata nel *Areas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="92876-127">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="92876-128">per altre informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="92876-128">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="92876-129">Le migrazioni, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="92876-129">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="92876-130">Abilitare l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="92876-130">Enable authentication</span></span>

<span data-ttu-id="92876-131">Nel `Configure` metodo per il `Startup` classe, chiamare [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="92876-131">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="92876-132">Modifiche del layout</span><span class="sxs-lookup"><span data-stu-id="92876-132">Layout changes</span></span>

<span data-ttu-id="92876-133">Facoltative: Aggiungere l'account di accesso parziale (`_LoginPartial`) per il file di layout:</span><span class="sxs-lookup"><span data-stu-id="92876-133">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="92876-134">Identità di scaffolding in un progetto Razor con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="92876-134">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="92876-135">Alcune opzioni di identità configurati nella *Areas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="92876-135">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="92876-136">per altre informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="92876-136">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="92876-137">Identità di scaffolding in un progetto MVC senza autorizzazione esistenti</span><span class="sxs-lookup"><span data-stu-id="92876-137">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="92876-138">Facoltative: Aggiungere l'account di accesso parziale (`_LoginPartial`) per il *Views/Shared/_Layout.cshtml* file:</span><span class="sxs-lookup"><span data-stu-id="92876-138">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="92876-139">Spostare il *Pages/Shared/_LoginPartial.cshtml* file *Views/Shared/_LoginPartial.cshtml*</span><span class="sxs-lookup"><span data-stu-id="92876-139">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="92876-140">Identità è configurata nel *Areas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="92876-140">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="92876-141">Per altre informazioni, vedere IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="92876-141">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="92876-142">Chiamare [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="92876-142">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="92876-143">Identità di scaffolding in un progetto MVC con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="92876-143">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="92876-144">Eliminare il *Pages/Shared* cartella e i file in tale cartella.</span><span class="sxs-lookup"><span data-stu-id="92876-144">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="92876-145">Creare identità completa dell'interfaccia utente origine</span><span class="sxs-lookup"><span data-stu-id="92876-145">Create full identity UI source</span></span>

<span data-ttu-id="92876-146">Per mantenere il controllo completo dell'interfaccia utente identità, eseguire l'utilità di scaffolding di identità e selezionare **eseguire l'Override di tutti i file**.</span><span class="sxs-lookup"><span data-stu-id="92876-146">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="92876-147">Il codice evidenziato seguente illustra le modifiche apportate a sostituire il valore predefinito di identità dell'interfaccia utente con identità in un'app web ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="92876-147">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="92876-148">Si potrebbe voler eseguire questa operazione per disporre del controllo completo dell'interfaccia utente di identità.</span><span class="sxs-lookup"><span data-stu-id="92876-148">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="92876-149">Il valore predefinito di identità viene sostituito nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="92876-149">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="92876-150">Nell'esempio il codice imposta la [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [proprietà logoutpath dal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="92876-150">The following the code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="92876-151">Registrare un `IEmailSender` implementazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="92876-151">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="92876-152">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="92876-152">Additional resources</span></span>

* [<span data-ttu-id="92876-153">Modifiche al codice di autenticazione per ASP.NET Core 2.1 e versioni successive</span><span class="sxs-lookup"><span data-stu-id="92876-153">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)
