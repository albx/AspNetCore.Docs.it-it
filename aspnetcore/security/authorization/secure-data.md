---
title: Create an ASP.NET Core app with user data protected by authorization
author: rick-anderson
description: Learn how to create a Razor Pages app with user data protected by authorization. Includes HTTPS, authentication, security, ASP.NET Core Identity.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 65c72d4dd457f85451796c5713bedebafec7a7de
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239828"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="d1c21-104">Create an ASP.NET Core app with user data protected by authorization</span><span class="sxs-lookup"><span data-stu-id="d1c21-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="d1c21-105">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="d1c21-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="d1c21-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span><span class="sxs-lookup"><span data-stu-id="d1c21-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="d1c21-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span><span class="sxs-lookup"><span data-stu-id="d1c21-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="d1c21-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="d1c21-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="d1c21-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="d1c21-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d1c21-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span><span class="sxs-lookup"><span data-stu-id="d1c21-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="d1c21-111">It displays a list of contacts that authenticated (registered) users have created.</span><span class="sxs-lookup"><span data-stu-id="d1c21-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="d1c21-112">There are three security groups:</span><span class="sxs-lookup"><span data-stu-id="d1c21-112">There are three security groups:</span></span>

* <span data-ttu-id="d1c21-113">**Registered users** can view all the approved data and can edit/delete their own data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="d1c21-114">**Managers** can approve or reject contact data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="d1c21-115">Only approved contacts are visible to users.</span><span class="sxs-lookup"><span data-stu-id="d1c21-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="d1c21-116">**Administrators** can approve/reject and edit/delete any data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="d1c21-117">The images in this document don't exactly match the latest templates.</span><span class="sxs-lookup"><span data-stu-id="d1c21-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="d1c21-118">In the following image, user Rick (`rick@example.com`) is signed in.</span><span class="sxs-lookup"><span data-stu-id="d1c21-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="d1c21-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="d1c21-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span><span class="sxs-lookup"><span data-stu-id="d1c21-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="d1c21-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span><span class="sxs-lookup"><span data-stu-id="d1c21-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot showing Rick signed in](secure-data/_static/rick.png)

<span data-ttu-id="d1c21-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span><span class="sxs-lookup"><span data-stu-id="d1c21-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot showing manager@contoso.com signed in](secure-data/_static/manager1.png)

<span data-ttu-id="d1c21-125">The following image shows the managers details view of a contact:</span><span class="sxs-lookup"><span data-stu-id="d1c21-125">The following image shows the managers details view of a contact:</span></span>

![Manager's view of a contact](secure-data/_static/manager.png)

<span data-ttu-id="d1c21-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span><span class="sxs-lookup"><span data-stu-id="d1c21-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="d1c21-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span><span class="sxs-lookup"><span data-stu-id="d1c21-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot showing admin@contoso.com signed in](secure-data/_static/admin.png)

<span data-ttu-id="d1c21-130">The administrator has all privileges.</span><span class="sxs-lookup"><span data-stu-id="d1c21-130">The administrator has all privileges.</span></span> <span data-ttu-id="d1c21-131">She can read/edit/delete any contact and change the status of contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="d1c21-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span><span class="sxs-lookup"><span data-stu-id="d1c21-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="d1c21-133">The sample contains the following authorization handlers:</span><span class="sxs-lookup"><span data-stu-id="d1c21-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="d1c21-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="d1c21-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="d1c21-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d1c21-137">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="d1c21-137">Prerequisites</span></span>

<span data-ttu-id="d1c21-138">This tutorial is advanced.</span><span class="sxs-lookup"><span data-stu-id="d1c21-138">This tutorial is advanced.</span></span> <span data-ttu-id="d1c21-139">You should be familiar with:</span><span class="sxs-lookup"><span data-stu-id="d1c21-139">You should be familiar with:</span></span>

* [<span data-ttu-id="d1c21-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1c21-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="d1c21-141">Autenticazione</span><span class="sxs-lookup"><span data-stu-id="d1c21-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="d1c21-142">Conferma dell'account e recupero della password</span><span class="sxs-lookup"><span data-stu-id="d1c21-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="d1c21-143">Autorizzazione</span><span class="sxs-lookup"><span data-stu-id="d1c21-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="d1c21-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="d1c21-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="d1c21-145">The starter and completed app</span><span class="sxs-lookup"><span data-stu-id="d1c21-145">The starter and completed app</span></span>

<span data-ttu-id="d1c21-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span><span class="sxs-lookup"><span data-stu-id="d1c21-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="d1c21-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span><span class="sxs-lookup"><span data-stu-id="d1c21-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="d1c21-148">The starter app</span><span class="sxs-lookup"><span data-stu-id="d1c21-148">The starter app</span></span>

<span data-ttu-id="d1c21-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span><span class="sxs-lookup"><span data-stu-id="d1c21-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="d1c21-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span><span class="sxs-lookup"><span data-stu-id="d1c21-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="d1c21-151">Secure user data</span><span class="sxs-lookup"><span data-stu-id="d1c21-151">Secure user data</span></span>

<span data-ttu-id="d1c21-152">The following sections have all the major steps to create the secure user data app.</span><span class="sxs-lookup"><span data-stu-id="d1c21-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="d1c21-153">You may find it helpful to refer to the completed project.</span><span class="sxs-lookup"><span data-stu-id="d1c21-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="d1c21-154">Tie the contact data to the user</span><span class="sxs-lookup"><span data-stu-id="d1c21-154">Tie the contact data to the user</span></span>

<span data-ttu-id="d1c21-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="d1c21-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span><span class="sxs-lookup"><span data-stu-id="d1c21-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="d1c21-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span><span class="sxs-lookup"><span data-stu-id="d1c21-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="d1c21-158">The `Status` field determines if a contact is viewable by general users.</span><span class="sxs-lookup"><span data-stu-id="d1c21-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="d1c21-159">Create a new migration and update the database:</span><span class="sxs-lookup"><span data-stu-id="d1c21-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="d1c21-160">Add Role services to Identity</span><span class="sxs-lookup"><span data-stu-id="d1c21-160">Add Role services to Identity</span></span>

<span data-ttu-id="d1c21-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span><span class="sxs-lookup"><span data-stu-id="d1c21-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="d1c21-162">Require authenticated users</span><span class="sxs-lookup"><span data-stu-id="d1c21-162">Require authenticated users</span></span>

<span data-ttu-id="d1c21-163">Set the default authentication policy to require users to be authenticated:</span><span class="sxs-lookup"><span data-stu-id="d1c21-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="d1c21-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span><span class="sxs-lookup"><span data-stu-id="d1c21-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="d1c21-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span><span class="sxs-lookup"><span data-stu-id="d1c21-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="d1c21-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span><span class="sxs-lookup"><span data-stu-id="d1c21-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="d1c21-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span><span class="sxs-lookup"><span data-stu-id="d1c21-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="d1c21-168">Configure the test account</span><span class="sxs-lookup"><span data-stu-id="d1c21-168">Configure the test account</span></span>

<span data-ttu-id="d1c21-169">The `SeedData` class creates two accounts: administrator and manager.</span><span class="sxs-lookup"><span data-stu-id="d1c21-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="d1c21-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="d1c21-171">Set the password from the project directory (the directory containing *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="d1c21-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="d1c21-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span><span class="sxs-lookup"><span data-stu-id="d1c21-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="d1c21-173">Update `Main` to use the test password:</span><span class="sxs-lookup"><span data-stu-id="d1c21-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="d1c21-174">Create the test accounts and update the contacts</span><span class="sxs-lookup"><span data-stu-id="d1c21-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="d1c21-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span><span class="sxs-lookup"><span data-stu-id="d1c21-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="d1c21-176">Add the administrator user ID and `ContactStatus` to the contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="d1c21-177">Make one of the contacts "Submitted" and one "Rejected".</span><span class="sxs-lookup"><span data-stu-id="d1c21-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="d1c21-178">Add the user ID and status to all the contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="d1c21-179">Only one contact is shown:</span><span class="sxs-lookup"><span data-stu-id="d1c21-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="d1c21-180">Create owner, manager, and administrator authorization handlers</span><span class="sxs-lookup"><span data-stu-id="d1c21-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="d1c21-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span><span class="sxs-lookup"><span data-stu-id="d1c21-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d1c21-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span><span class="sxs-lookup"><span data-stu-id="d1c21-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="d1c21-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span><span class="sxs-lookup"><span data-stu-id="d1c21-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="d1c21-184">Authorization handlers generally:</span><span class="sxs-lookup"><span data-stu-id="d1c21-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="d1c21-185">Return `context.Succeed` when the requirements are met.</span><span class="sxs-lookup"><span data-stu-id="d1c21-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="d1c21-186">Return `Task.CompletedTask` when requirements aren't met.</span><span class="sxs-lookup"><span data-stu-id="d1c21-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="d1c21-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span><span class="sxs-lookup"><span data-stu-id="d1c21-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="d1c21-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="d1c21-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="d1c21-189">The app allows contact owners to edit/delete/create their own data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="d1c21-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span><span class="sxs-lookup"><span data-stu-id="d1c21-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="d1c21-191">Create a manager authorization handler</span><span class="sxs-lookup"><span data-stu-id="d1c21-191">Create a manager authorization handler</span></span>

<span data-ttu-id="d1c21-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span><span class="sxs-lookup"><span data-stu-id="d1c21-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d1c21-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span><span class="sxs-lookup"><span data-stu-id="d1c21-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="d1c21-194">Only managers can approve or reject content changes (new or changed).</span><span class="sxs-lookup"><span data-stu-id="d1c21-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="d1c21-195">Create an administrator authorization handler</span><span class="sxs-lookup"><span data-stu-id="d1c21-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="d1c21-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span><span class="sxs-lookup"><span data-stu-id="d1c21-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d1c21-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span><span class="sxs-lookup"><span data-stu-id="d1c21-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="d1c21-198">Administrator can do all operations.</span><span class="sxs-lookup"><span data-stu-id="d1c21-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="d1c21-199">Register the authorization handlers</span><span class="sxs-lookup"><span data-stu-id="d1c21-199">Register the authorization handlers</span></span>

<span data-ttu-id="d1c21-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="d1c21-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="d1c21-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d1c21-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="d1c21-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d1c21-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d1c21-203">Add the following code to the end of `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d1c21-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="d1c21-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span><span class="sxs-lookup"><span data-stu-id="d1c21-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="d1c21-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span><span class="sxs-lookup"><span data-stu-id="d1c21-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="d1c21-206">Support authorization</span><span class="sxs-lookup"><span data-stu-id="d1c21-206">Support authorization</span></span>

<span data-ttu-id="d1c21-207">In this section, you update the Razor Pages and add an operations requirements class.</span><span class="sxs-lookup"><span data-stu-id="d1c21-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="d1c21-208">Review the contact operations requirements class</span><span class="sxs-lookup"><span data-stu-id="d1c21-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="d1c21-209">Review the `ContactOperations` class.</span><span class="sxs-lookup"><span data-stu-id="d1c21-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="d1c21-210">This class contains the requirements the app supports:</span><span class="sxs-lookup"><span data-stu-id="d1c21-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="d1c21-211">Create a base class for the Contacts Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d1c21-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="d1c21-212">Create a base class that contains the services used in the contacts Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d1c21-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="d1c21-213">The base class puts the initialization code in one location:</span><span class="sxs-lookup"><span data-stu-id="d1c21-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="d1c21-214">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="d1c21-214">The preceding code:</span></span>

* <span data-ttu-id="d1c21-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span><span class="sxs-lookup"><span data-stu-id="d1c21-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="d1c21-216">Adds the Identity `UserManager` service.</span><span class="sxs-lookup"><span data-stu-id="d1c21-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="d1c21-217">Aggiungere l'oggetto `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="d1c21-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="d1c21-218">Update the CreateModel</span><span class="sxs-lookup"><span data-stu-id="d1c21-218">Update the CreateModel</span></span>

<span data-ttu-id="d1c21-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span><span class="sxs-lookup"><span data-stu-id="d1c21-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="d1c21-220">Update the `CreateModel.OnPostAsync` method to:</span><span class="sxs-lookup"><span data-stu-id="d1c21-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="d1c21-221">Add the user ID to the `Contact` model.</span><span class="sxs-lookup"><span data-stu-id="d1c21-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="d1c21-222">Call the authorization handler to verify the user has permission to create contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="d1c21-223">Update the IndexModel</span><span class="sxs-lookup"><span data-stu-id="d1c21-223">Update the IndexModel</span></span>

<span data-ttu-id="d1c21-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span><span class="sxs-lookup"><span data-stu-id="d1c21-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="d1c21-225">Update the EditModel</span><span class="sxs-lookup"><span data-stu-id="d1c21-225">Update the EditModel</span></span>

<span data-ttu-id="d1c21-226">Add an authorization handler to verify the user owns the contact.</span><span class="sxs-lookup"><span data-stu-id="d1c21-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="d1c21-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span><span class="sxs-lookup"><span data-stu-id="d1c21-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="d1c21-228">The app doesn't have access to the resource when attributes are evaluated.</span><span class="sxs-lookup"><span data-stu-id="d1c21-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="d1c21-229">Resource-based authorization must be imperative.</span><span class="sxs-lookup"><span data-stu-id="d1c21-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="d1c21-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span><span class="sxs-lookup"><span data-stu-id="d1c21-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="d1c21-231">You frequently access the resource by passing in the resource key.</span><span class="sxs-lookup"><span data-stu-id="d1c21-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="d1c21-232">Update the DeleteModel</span><span class="sxs-lookup"><span data-stu-id="d1c21-232">Update the DeleteModel</span></span>

<span data-ttu-id="d1c21-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span><span class="sxs-lookup"><span data-stu-id="d1c21-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="d1c21-234">Inject the authorization service into the views</span><span class="sxs-lookup"><span data-stu-id="d1c21-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="d1c21-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span><span class="sxs-lookup"><span data-stu-id="d1c21-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="d1c21-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span><span class="sxs-lookup"><span data-stu-id="d1c21-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="d1c21-237">The preceding markup adds several `using` statements.</span><span class="sxs-lookup"><span data-stu-id="d1c21-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="d1c21-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span><span class="sxs-lookup"><span data-stu-id="d1c21-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="d1c21-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span><span class="sxs-lookup"><span data-stu-id="d1c21-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="d1c21-240">Hiding links makes the app more user-friendly by displaying only valid links.</span><span class="sxs-lookup"><span data-stu-id="d1c21-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="d1c21-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span><span class="sxs-lookup"><span data-stu-id="d1c21-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="d1c21-242">The Razor Page or controller must enforce access checks to secure the data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="d1c21-243">Update Details</span><span class="sxs-lookup"><span data-stu-id="d1c21-243">Update Details</span></span>

<span data-ttu-id="d1c21-244">Update the details view so managers can approve or reject contacts:</span><span class="sxs-lookup"><span data-stu-id="d1c21-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="d1c21-245">Update the details page model:</span><span class="sxs-lookup"><span data-stu-id="d1c21-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="d1c21-246">Add or remove a user to a role</span><span class="sxs-lookup"><span data-stu-id="d1c21-246">Add or remove a user to a role</span></span>

<span data-ttu-id="d1c21-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span><span class="sxs-lookup"><span data-stu-id="d1c21-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="d1c21-248">Removing privileges from a user.</span><span class="sxs-lookup"><span data-stu-id="d1c21-248">Removing privileges from a user.</span></span> <span data-ttu-id="d1c21-249">For example, muting a user in a chat app.</span><span class="sxs-lookup"><span data-stu-id="d1c21-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="d1c21-250">Adding privileges to a user.</span><span class="sxs-lookup"><span data-stu-id="d1c21-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="d1c21-251">Differences between Challenge and Forbid</span><span class="sxs-lookup"><span data-stu-id="d1c21-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="d1c21-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span><span class="sxs-lookup"><span data-stu-id="d1c21-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="d1c21-253">The following code allows anonymous users.</span><span class="sxs-lookup"><span data-stu-id="d1c21-253">The following code allows anonymous users.</span></span> <span data-ttu-id="d1c21-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span><span class="sxs-lookup"><span data-stu-id="d1c21-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="d1c21-255">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="d1c21-255">In the preceding code:</span></span>

* <span data-ttu-id="d1c21-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span><span class="sxs-lookup"><span data-stu-id="d1c21-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="d1c21-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span><span class="sxs-lookup"><span data-stu-id="d1c21-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="d1c21-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span><span class="sxs-lookup"><span data-stu-id="d1c21-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="d1c21-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span><span class="sxs-lookup"><span data-stu-id="d1c21-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="d1c21-260">Test the completed app</span><span class="sxs-lookup"><span data-stu-id="d1c21-260">Test the completed app</span></span>

<span data-ttu-id="d1c21-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span><span class="sxs-lookup"><span data-stu-id="d1c21-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="d1c21-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span><span class="sxs-lookup"><span data-stu-id="d1c21-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="d1c21-263">For example, `Passw0rd!` meets the strong password requirements.</span><span class="sxs-lookup"><span data-stu-id="d1c21-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="d1c21-264">Execute the following command from the project's folder, where `<PW>` is the password:</span><span class="sxs-lookup"><span data-stu-id="d1c21-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="d1c21-265">If the app has contacts:</span><span class="sxs-lookup"><span data-stu-id="d1c21-265">If the app has contacts:</span></span>

* <span data-ttu-id="d1c21-266">Delete all of the records in the `Contact` table.</span><span class="sxs-lookup"><span data-stu-id="d1c21-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="d1c21-267">Restart the app to seed the database.</span><span class="sxs-lookup"><span data-stu-id="d1c21-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="d1c21-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span><span class="sxs-lookup"><span data-stu-id="d1c21-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="d1c21-269">In one browser, register a new user (for example, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="d1c21-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="d1c21-270">Sign in to each browser with a different user.</span><span class="sxs-lookup"><span data-stu-id="d1c21-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="d1c21-271">Verify the following operations:</span><span class="sxs-lookup"><span data-stu-id="d1c21-271">Verify the following operations:</span></span>

* <span data-ttu-id="d1c21-272">Registered users can view all of the approved contact data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="d1c21-273">Registered users can edit/delete their own data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="d1c21-274">Managers can approve/reject contact data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="d1c21-275">The `Details` view shows **Approve** and **Reject** buttons.</span><span class="sxs-lookup"><span data-stu-id="d1c21-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="d1c21-276">Administrators can approve/reject and edit/delete all data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="d1c21-277">Utente</span><span class="sxs-lookup"><span data-stu-id="d1c21-277">User</span></span>                | <span data-ttu-id="d1c21-278">Seeded by the app</span><span class="sxs-lookup"><span data-stu-id="d1c21-278">Seeded by the app</span></span> | <span data-ttu-id="d1c21-279">Opzioni</span><span class="sxs-lookup"><span data-stu-id="d1c21-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="d1c21-280">No</span><span class="sxs-lookup"><span data-stu-id="d1c21-280">No</span></span>                | <span data-ttu-id="d1c21-281">Edit/delete the own data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="d1c21-282">Yes</span><span class="sxs-lookup"><span data-stu-id="d1c21-282">Yes</span></span>               | <span data-ttu-id="d1c21-283">Approve/reject and edit/delete own data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="d1c21-284">Yes</span><span class="sxs-lookup"><span data-stu-id="d1c21-284">Yes</span></span>               | <span data-ttu-id="d1c21-285">Approve/reject and edit/delete all data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="d1c21-286">Create a contact in the administrator's browser.</span><span class="sxs-lookup"><span data-stu-id="d1c21-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="d1c21-287">Copy the URL for delete and edit from the administrator contact.</span><span class="sxs-lookup"><span data-stu-id="d1c21-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="d1c21-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span><span class="sxs-lookup"><span data-stu-id="d1c21-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="d1c21-289">Create the starter app</span><span class="sxs-lookup"><span data-stu-id="d1c21-289">Create the starter app</span></span>

* <span data-ttu-id="d1c21-290">Create a Razor Pages app named "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="d1c21-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="d1c21-291">Create the app with **Individual User Accounts**.</span><span class="sxs-lookup"><span data-stu-id="d1c21-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="d1c21-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span><span class="sxs-lookup"><span data-stu-id="d1c21-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="d1c21-293">`-uld` specifies LocalDB instead of SQLite</span><span class="sxs-lookup"><span data-stu-id="d1c21-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="d1c21-294">Add *Models/Contact.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1c21-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="d1c21-295">Scaffold the `Contact` model.</span><span class="sxs-lookup"><span data-stu-id="d1c21-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="d1c21-296">Create initial migration and update the database:</span><span class="sxs-lookup"><span data-stu-id="d1c21-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="d1c21-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="d1c21-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="d1c21-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span><span class="sxs-lookup"><span data-stu-id="d1c21-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="d1c21-299">Test the app by creating, editing, and deleting a contact</span><span class="sxs-lookup"><span data-stu-id="d1c21-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="d1c21-300">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="d1c21-300">Seed the database</span></span>

<span data-ttu-id="d1c21-301">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span><span class="sxs-lookup"><span data-stu-id="d1c21-301">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="d1c21-302">Call `SeedData.Initialize` from `Main`:</span><span class="sxs-lookup"><span data-stu-id="d1c21-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="d1c21-303">Test that the app seeded the database.</span><span class="sxs-lookup"><span data-stu-id="d1c21-303">Test that the app seeded the database.</span></span> <span data-ttu-id="d1c21-304">If there are any rows in the contact DB, the seed method doesn't run.</span><span class="sxs-lookup"><span data-stu-id="d1c21-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="d1c21-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span><span class="sxs-lookup"><span data-stu-id="d1c21-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="d1c21-306">It displays a list of contacts that authenticated (registered) users have created.</span><span class="sxs-lookup"><span data-stu-id="d1c21-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="d1c21-307">There are three security groups:</span><span class="sxs-lookup"><span data-stu-id="d1c21-307">There are three security groups:</span></span>

* <span data-ttu-id="d1c21-308">**Registered users** can view all the approved data and can edit/delete their own data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="d1c21-309">**Managers** can approve or reject contact data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="d1c21-310">Only approved contacts are visible to users.</span><span class="sxs-lookup"><span data-stu-id="d1c21-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="d1c21-311">**Administrators** can approve/reject and edit/delete any data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="d1c21-312">In the following image, user Rick (`rick@example.com`) is signed in.</span><span class="sxs-lookup"><span data-stu-id="d1c21-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="d1c21-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="d1c21-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span><span class="sxs-lookup"><span data-stu-id="d1c21-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="d1c21-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span><span class="sxs-lookup"><span data-stu-id="d1c21-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot showing Rick signed in](secure-data/_static/rick.png)

<span data-ttu-id="d1c21-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span><span class="sxs-lookup"><span data-stu-id="d1c21-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot showing manager@contoso.com signed in](secure-data/_static/manager1.png)

<span data-ttu-id="d1c21-319">The following image shows the managers details view of a contact:</span><span class="sxs-lookup"><span data-stu-id="d1c21-319">The following image shows the managers details view of a contact:</span></span>

![Manager's view of a contact](secure-data/_static/manager.png)

<span data-ttu-id="d1c21-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span><span class="sxs-lookup"><span data-stu-id="d1c21-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="d1c21-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span><span class="sxs-lookup"><span data-stu-id="d1c21-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot showing admin@contoso.com signed in](secure-data/_static/admin.png)

<span data-ttu-id="d1c21-324">The administrator has all privileges.</span><span class="sxs-lookup"><span data-stu-id="d1c21-324">The administrator has all privileges.</span></span> <span data-ttu-id="d1c21-325">She can read/edit/delete any contact and change the status of contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="d1c21-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span><span class="sxs-lookup"><span data-stu-id="d1c21-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="d1c21-327">The sample contains the following authorization handlers:</span><span class="sxs-lookup"><span data-stu-id="d1c21-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="d1c21-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="d1c21-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="d1c21-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d1c21-331">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="d1c21-331">Prerequisites</span></span>

<span data-ttu-id="d1c21-332">This tutorial is advanced.</span><span class="sxs-lookup"><span data-stu-id="d1c21-332">This tutorial is advanced.</span></span> <span data-ttu-id="d1c21-333">You should be familiar with:</span><span class="sxs-lookup"><span data-stu-id="d1c21-333">You should be familiar with:</span></span>

* [<span data-ttu-id="d1c21-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1c21-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="d1c21-335">Autenticazione</span><span class="sxs-lookup"><span data-stu-id="d1c21-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="d1c21-336">Conferma dell'account e recupero della password</span><span class="sxs-lookup"><span data-stu-id="d1c21-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="d1c21-337">Autorizzazione</span><span class="sxs-lookup"><span data-stu-id="d1c21-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="d1c21-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="d1c21-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="d1c21-339">The starter and completed app</span><span class="sxs-lookup"><span data-stu-id="d1c21-339">The starter and completed app</span></span>

<span data-ttu-id="d1c21-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span><span class="sxs-lookup"><span data-stu-id="d1c21-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="d1c21-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span><span class="sxs-lookup"><span data-stu-id="d1c21-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="d1c21-342">The starter app</span><span class="sxs-lookup"><span data-stu-id="d1c21-342">The starter app</span></span>

<span data-ttu-id="d1c21-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span><span class="sxs-lookup"><span data-stu-id="d1c21-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="d1c21-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span><span class="sxs-lookup"><span data-stu-id="d1c21-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="d1c21-345">Secure user data</span><span class="sxs-lookup"><span data-stu-id="d1c21-345">Secure user data</span></span>

<span data-ttu-id="d1c21-346">The following sections have all the major steps to create the secure user data app.</span><span class="sxs-lookup"><span data-stu-id="d1c21-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="d1c21-347">You may find it helpful to refer to the completed project.</span><span class="sxs-lookup"><span data-stu-id="d1c21-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="d1c21-348">Tie the contact data to the user</span><span class="sxs-lookup"><span data-stu-id="d1c21-348">Tie the contact data to the user</span></span>

<span data-ttu-id="d1c21-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="d1c21-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span><span class="sxs-lookup"><span data-stu-id="d1c21-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="d1c21-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span><span class="sxs-lookup"><span data-stu-id="d1c21-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="d1c21-352">The `Status` field determines if a contact is viewable by general users.</span><span class="sxs-lookup"><span data-stu-id="d1c21-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="d1c21-353">Create a new migration and update the database:</span><span class="sxs-lookup"><span data-stu-id="d1c21-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="d1c21-354">Add Role services to Identity</span><span class="sxs-lookup"><span data-stu-id="d1c21-354">Add Role services to Identity</span></span>

<span data-ttu-id="d1c21-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span><span class="sxs-lookup"><span data-stu-id="d1c21-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="d1c21-356">Require authenticated users</span><span class="sxs-lookup"><span data-stu-id="d1c21-356">Require authenticated users</span></span>

<span data-ttu-id="d1c21-357">Set the default authentication policy to require users to be authenticated:</span><span class="sxs-lookup"><span data-stu-id="d1c21-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="d1c21-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span><span class="sxs-lookup"><span data-stu-id="d1c21-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="d1c21-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span><span class="sxs-lookup"><span data-stu-id="d1c21-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="d1c21-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span><span class="sxs-lookup"><span data-stu-id="d1c21-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="d1c21-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span><span class="sxs-lookup"><span data-stu-id="d1c21-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="d1c21-362">Configure the test account</span><span class="sxs-lookup"><span data-stu-id="d1c21-362">Configure the test account</span></span>

<span data-ttu-id="d1c21-363">The `SeedData` class creates two accounts: administrator and manager.</span><span class="sxs-lookup"><span data-stu-id="d1c21-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="d1c21-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="d1c21-365">Set the password from the project directory (the directory containing *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="d1c21-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="d1c21-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span><span class="sxs-lookup"><span data-stu-id="d1c21-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="d1c21-367">Update `Main` to use the test password:</span><span class="sxs-lookup"><span data-stu-id="d1c21-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="d1c21-368">Create the test accounts and update the contacts</span><span class="sxs-lookup"><span data-stu-id="d1c21-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="d1c21-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span><span class="sxs-lookup"><span data-stu-id="d1c21-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="d1c21-370">Add the administrator user ID and `ContactStatus` to the contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="d1c21-371">Make one of the contacts "Submitted" and one "Rejected".</span><span class="sxs-lookup"><span data-stu-id="d1c21-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="d1c21-372">Add the user ID and status to all the contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="d1c21-373">Only one contact is shown:</span><span class="sxs-lookup"><span data-stu-id="d1c21-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="d1c21-374">Create owner, manager, and administrator authorization handlers</span><span class="sxs-lookup"><span data-stu-id="d1c21-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="d1c21-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span><span class="sxs-lookup"><span data-stu-id="d1c21-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="d1c21-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span><span class="sxs-lookup"><span data-stu-id="d1c21-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="d1c21-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span><span class="sxs-lookup"><span data-stu-id="d1c21-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="d1c21-378">Authorization handlers generally:</span><span class="sxs-lookup"><span data-stu-id="d1c21-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="d1c21-379">Return `context.Succeed` when the requirements are met.</span><span class="sxs-lookup"><span data-stu-id="d1c21-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="d1c21-380">Return `Task.CompletedTask` when requirements aren't met.</span><span class="sxs-lookup"><span data-stu-id="d1c21-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="d1c21-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span><span class="sxs-lookup"><span data-stu-id="d1c21-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="d1c21-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="d1c21-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="d1c21-383">The app allows contact owners to edit/delete/create their own data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="d1c21-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span><span class="sxs-lookup"><span data-stu-id="d1c21-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="d1c21-385">Create a manager authorization handler</span><span class="sxs-lookup"><span data-stu-id="d1c21-385">Create a manager authorization handler</span></span>

<span data-ttu-id="d1c21-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span><span class="sxs-lookup"><span data-stu-id="d1c21-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d1c21-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span><span class="sxs-lookup"><span data-stu-id="d1c21-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="d1c21-388">Only managers can approve or reject content changes (new or changed).</span><span class="sxs-lookup"><span data-stu-id="d1c21-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="d1c21-389">Create an administrator authorization handler</span><span class="sxs-lookup"><span data-stu-id="d1c21-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="d1c21-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span><span class="sxs-lookup"><span data-stu-id="d1c21-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d1c21-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span><span class="sxs-lookup"><span data-stu-id="d1c21-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="d1c21-392">Administrator can do all operations.</span><span class="sxs-lookup"><span data-stu-id="d1c21-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="d1c21-393">Register the authorization handlers</span><span class="sxs-lookup"><span data-stu-id="d1c21-393">Register the authorization handlers</span></span>

<span data-ttu-id="d1c21-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="d1c21-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="d1c21-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d1c21-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="d1c21-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d1c21-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d1c21-397">Add the following code to the end of `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d1c21-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="d1c21-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span><span class="sxs-lookup"><span data-stu-id="d1c21-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="d1c21-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span><span class="sxs-lookup"><span data-stu-id="d1c21-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="d1c21-400">Support authorization</span><span class="sxs-lookup"><span data-stu-id="d1c21-400">Support authorization</span></span>

<span data-ttu-id="d1c21-401">In this section, you update the Razor Pages and add an operations requirements class.</span><span class="sxs-lookup"><span data-stu-id="d1c21-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="d1c21-402">Review the contact operations requirements class</span><span class="sxs-lookup"><span data-stu-id="d1c21-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="d1c21-403">Review the `ContactOperations` class.</span><span class="sxs-lookup"><span data-stu-id="d1c21-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="d1c21-404">This class contains the requirements the app supports:</span><span class="sxs-lookup"><span data-stu-id="d1c21-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="d1c21-405">Create a base class for the Contacts Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d1c21-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="d1c21-406">Create a base class that contains the services used in the contacts Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d1c21-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="d1c21-407">The base class puts the initialization code in one location:</span><span class="sxs-lookup"><span data-stu-id="d1c21-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="d1c21-408">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="d1c21-408">The preceding code:</span></span>

* <span data-ttu-id="d1c21-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span><span class="sxs-lookup"><span data-stu-id="d1c21-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="d1c21-410">Adds the Identity `UserManager` service.</span><span class="sxs-lookup"><span data-stu-id="d1c21-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="d1c21-411">Aggiungere l'oggetto `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="d1c21-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="d1c21-412">Update the CreateModel</span><span class="sxs-lookup"><span data-stu-id="d1c21-412">Update the CreateModel</span></span>

<span data-ttu-id="d1c21-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span><span class="sxs-lookup"><span data-stu-id="d1c21-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="d1c21-414">Update the `CreateModel.OnPostAsync` method to:</span><span class="sxs-lookup"><span data-stu-id="d1c21-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="d1c21-415">Add the user ID to the `Contact` model.</span><span class="sxs-lookup"><span data-stu-id="d1c21-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="d1c21-416">Call the authorization handler to verify the user has permission to create contacts.</span><span class="sxs-lookup"><span data-stu-id="d1c21-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="d1c21-417">Update the IndexModel</span><span class="sxs-lookup"><span data-stu-id="d1c21-417">Update the IndexModel</span></span>

<span data-ttu-id="d1c21-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span><span class="sxs-lookup"><span data-stu-id="d1c21-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="d1c21-419">Update the EditModel</span><span class="sxs-lookup"><span data-stu-id="d1c21-419">Update the EditModel</span></span>

<span data-ttu-id="d1c21-420">Add an authorization handler to verify the user owns the contact.</span><span class="sxs-lookup"><span data-stu-id="d1c21-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="d1c21-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span><span class="sxs-lookup"><span data-stu-id="d1c21-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="d1c21-422">The app doesn't have access to the resource when attributes are evaluated.</span><span class="sxs-lookup"><span data-stu-id="d1c21-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="d1c21-423">Resource-based authorization must be imperative.</span><span class="sxs-lookup"><span data-stu-id="d1c21-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="d1c21-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span><span class="sxs-lookup"><span data-stu-id="d1c21-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="d1c21-425">You frequently access the resource by passing in the resource key.</span><span class="sxs-lookup"><span data-stu-id="d1c21-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="d1c21-426">Update the DeleteModel</span><span class="sxs-lookup"><span data-stu-id="d1c21-426">Update the DeleteModel</span></span>

<span data-ttu-id="d1c21-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span><span class="sxs-lookup"><span data-stu-id="d1c21-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="d1c21-428">Inject the authorization service into the views</span><span class="sxs-lookup"><span data-stu-id="d1c21-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="d1c21-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span><span class="sxs-lookup"><span data-stu-id="d1c21-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="d1c21-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span><span class="sxs-lookup"><span data-stu-id="d1c21-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="d1c21-431">The preceding markup adds several `using` statements.</span><span class="sxs-lookup"><span data-stu-id="d1c21-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="d1c21-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span><span class="sxs-lookup"><span data-stu-id="d1c21-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="d1c21-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span><span class="sxs-lookup"><span data-stu-id="d1c21-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="d1c21-434">Hiding links makes the app more user-friendly by displaying only valid links.</span><span class="sxs-lookup"><span data-stu-id="d1c21-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="d1c21-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span><span class="sxs-lookup"><span data-stu-id="d1c21-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="d1c21-436">The Razor Page or controller must enforce access checks to secure the data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="d1c21-437">Update Details</span><span class="sxs-lookup"><span data-stu-id="d1c21-437">Update Details</span></span>

<span data-ttu-id="d1c21-438">Update the details view so managers can approve or reject contacts:</span><span class="sxs-lookup"><span data-stu-id="d1c21-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="d1c21-439">Update the details page model:</span><span class="sxs-lookup"><span data-stu-id="d1c21-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="d1c21-440">Add or remove a user to a role</span><span class="sxs-lookup"><span data-stu-id="d1c21-440">Add or remove a user to a role</span></span>

<span data-ttu-id="d1c21-441">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span><span class="sxs-lookup"><span data-stu-id="d1c21-441">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="d1c21-442">Removing privileges from a user.</span><span class="sxs-lookup"><span data-stu-id="d1c21-442">Removing privileges from a user.</span></span> <span data-ttu-id="d1c21-443">For example, muting a user in a chat app.</span><span class="sxs-lookup"><span data-stu-id="d1c21-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="d1c21-444">Adding privileges to a user.</span><span class="sxs-lookup"><span data-stu-id="d1c21-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="d1c21-445">Test the completed app</span><span class="sxs-lookup"><span data-stu-id="d1c21-445">Test the completed app</span></span>

<span data-ttu-id="d1c21-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span><span class="sxs-lookup"><span data-stu-id="d1c21-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="d1c21-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span><span class="sxs-lookup"><span data-stu-id="d1c21-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="d1c21-448">For example, `Passw0rd!` meets the strong password requirements.</span><span class="sxs-lookup"><span data-stu-id="d1c21-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="d1c21-449">Execute the following command from the project's folder, where `<PW>` is the password:</span><span class="sxs-lookup"><span data-stu-id="d1c21-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="d1c21-450">Drop and update the Database</span><span class="sxs-lookup"><span data-stu-id="d1c21-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="d1c21-451">Restart the app to seed the database.</span><span class="sxs-lookup"><span data-stu-id="d1c21-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="d1c21-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span><span class="sxs-lookup"><span data-stu-id="d1c21-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="d1c21-453">In one browser, register a new user (for example, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="d1c21-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="d1c21-454">Sign in to each browser with a different user.</span><span class="sxs-lookup"><span data-stu-id="d1c21-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="d1c21-455">Verify the following operations:</span><span class="sxs-lookup"><span data-stu-id="d1c21-455">Verify the following operations:</span></span>

* <span data-ttu-id="d1c21-456">Registered users can view all of the approved contact data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="d1c21-457">Registered users can edit/delete their own data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="d1c21-458">Managers can approve/reject contact data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="d1c21-459">The `Details` view shows **Approve** and **Reject** buttons.</span><span class="sxs-lookup"><span data-stu-id="d1c21-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="d1c21-460">Administrators can approve/reject and edit/delete all data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="d1c21-461">Utente</span><span class="sxs-lookup"><span data-stu-id="d1c21-461">User</span></span>                | <span data-ttu-id="d1c21-462">Seeded by the app</span><span class="sxs-lookup"><span data-stu-id="d1c21-462">Seeded by the app</span></span> | <span data-ttu-id="d1c21-463">Opzioni</span><span class="sxs-lookup"><span data-stu-id="d1c21-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="d1c21-464">No</span><span class="sxs-lookup"><span data-stu-id="d1c21-464">No</span></span>                | <span data-ttu-id="d1c21-465">Edit/delete the own data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="d1c21-466">Yes</span><span class="sxs-lookup"><span data-stu-id="d1c21-466">Yes</span></span>               | <span data-ttu-id="d1c21-467">Approve/reject and edit/delete own data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="d1c21-468">Yes</span><span class="sxs-lookup"><span data-stu-id="d1c21-468">Yes</span></span>               | <span data-ttu-id="d1c21-469">Approve/reject and edit/delete all data.</span><span class="sxs-lookup"><span data-stu-id="d1c21-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="d1c21-470">Create a contact in the administrator's browser.</span><span class="sxs-lookup"><span data-stu-id="d1c21-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="d1c21-471">Copy the URL for delete and edit from the administrator contact.</span><span class="sxs-lookup"><span data-stu-id="d1c21-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="d1c21-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span><span class="sxs-lookup"><span data-stu-id="d1c21-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="d1c21-473">Create the starter app</span><span class="sxs-lookup"><span data-stu-id="d1c21-473">Create the starter app</span></span>

* <span data-ttu-id="d1c21-474">Create a Razor Pages app named "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="d1c21-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="d1c21-475">Create the app with **Individual User Accounts**.</span><span class="sxs-lookup"><span data-stu-id="d1c21-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="d1c21-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span><span class="sxs-lookup"><span data-stu-id="d1c21-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="d1c21-477">`-uld` specifies LocalDB instead of SQLite</span><span class="sxs-lookup"><span data-stu-id="d1c21-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="d1c21-478">Add *Models/Contact.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1c21-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="d1c21-479">Scaffold the `Contact` model.</span><span class="sxs-lookup"><span data-stu-id="d1c21-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="d1c21-480">Create initial migration and update the database:</span><span class="sxs-lookup"><span data-stu-id="d1c21-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="d1c21-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span><span class="sxs-lookup"><span data-stu-id="d1c21-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="d1c21-482">Test the app by creating, editing, and deleting a contact</span><span class="sxs-lookup"><span data-stu-id="d1c21-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="d1c21-483">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="d1c21-483">Seed the database</span></span>

<span data-ttu-id="d1c21-484">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span><span class="sxs-lookup"><span data-stu-id="d1c21-484">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="d1c21-485">Call `SeedData.Initialize` from `Main`:</span><span class="sxs-lookup"><span data-stu-id="d1c21-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="d1c21-486">Test that the app seeded the database.</span><span class="sxs-lookup"><span data-stu-id="d1c21-486">Test that the app seeded the database.</span></span> <span data-ttu-id="d1c21-487">If there are any rows in the contact DB, the seed method doesn't run.</span><span class="sxs-lookup"><span data-stu-id="d1c21-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="d1c21-488">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d1c21-488">Additional resources</span></span>

* [<span data-ttu-id="d1c21-489">Build a .NET Core and SQL Database web app in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d1c21-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="d1c21-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="d1c21-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="d1c21-491">This lab goes into more detail on the security features introduced in this tutorial.</span><span class="sxs-lookup"><span data-stu-id="d1c21-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="d1c21-492">Autorizzazione personalizzata basata su criteri</span><span class="sxs-lookup"><span data-stu-id="d1c21-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
