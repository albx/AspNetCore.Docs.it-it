---
title: Introduzione ad ASP.NET Core
author: rick-anderson
description: Un'esercitazione rapida per creare ed eseguire una semplice app Hello World usando ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
uid: getting-started
ms.openlocfilehash: c806bd1e79dea9119f1c9e99d0a2b9742a10987a
ms.sourcegitcommit: ef1720cb733908f36a54825d84c3461c5280bdbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737477"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="97b24-103">Esercitazione: Introduzione ad ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="97b24-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="97b24-104">Questa esercitazione illustra come usare l'interfaccia della riga di comando di .NET Core per creare ed eseguire un'app Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97b24-104">This tutorial shows how to use the .NET Core command-line interface to create and run an ASP.NET Core web app.</span></span>

<span data-ttu-id="97b24-105">Si imparerà a eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="97b24-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="97b24-106">Creare un progetto di app Web.</span><span class="sxs-lookup"><span data-stu-id="97b24-106">Create a web app project.</span></span>
> * <span data-ttu-id="97b24-107">Considerare attendibile il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="97b24-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="97b24-108">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="97b24-108">Run the app.</span></span>
> * <span data-ttu-id="97b24-109">Modificare una pagina Razor.</span><span class="sxs-lookup"><span data-stu-id="97b24-109">Edit a Razor page.</span></span>

<span data-ttu-id="97b24-110">Al termine, si avrà un'app Web funzionante che viene eseguita nel computer locale.</span><span class="sxs-lookup"><span data-stu-id="97b24-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Home page dell'app Web](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="97b24-112">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="97b24-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="97b24-113">Creare un progetto di app Web</span><span class="sxs-lookup"><span data-stu-id="97b24-113">Create a web app project</span></span>

<span data-ttu-id="97b24-114">Aprire una shell dei comandi e immettere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="97b24-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="97b24-115">Il comando precedente:</span><span class="sxs-lookup"><span data-stu-id="97b24-115">The preceding command:</span></span>

* <span data-ttu-id="97b24-116">Crea una nuova app Web.</span><span class="sxs-lookup"><span data-stu-id="97b24-116">Creates a new web app.</span></span>  
* <span data-ttu-id="97b24-117">Il parametro `-o aspnetcoreapp` crea una directory denominata *aspnetcoreapp* con i file di origine per l'app.</span><span class="sxs-lookup"><span data-stu-id="97b24-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="97b24-118">Considerare attendibile il certificato di sviluppo</span><span class="sxs-lookup"><span data-stu-id="97b24-118">Trust the development certificate</span></span>

<span data-ttu-id="97b24-119">Considerare attendibile il certificato di sviluppo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="97b24-119">Trust the HTTPS development certificate:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="97b24-120">Windows</span><span class="sxs-lookup"><span data-stu-id="97b24-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="97b24-121">Il comando precedente consente di visualizzare la finestra di dialogo seguente:</span><span class="sxs-lookup"><span data-stu-id="97b24-121">The preceding command displays the following dialog:</span></span>

![Finestra di dialogo Avviso di sicurezza](~/getting-started/_static/cert.png)

<span data-ttu-id="97b24-123">Selezionare **Sì** se si accetta di considerare attendibile il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="97b24-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="97b24-124">macOS</span><span class="sxs-lookup"><span data-stu-id="97b24-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="97b24-125">Il comando precedente consente di visualizzare il messaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="97b24-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="97b24-126">*È stato richiesto l'attendibilità del certificato di sviluppo HTTPS. Se il certificato non è già attendibile, verrà eseguito il comando seguente:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="97b24-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="97b24-127">Questo comando potrebbe richiedere la password per installare il certificato nel keychain di sistema.</span><span class="sxs-lookup"><span data-stu-id="97b24-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="97b24-128">Inserire la password se si accetta di considerare attendibile il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="97b24-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="97b24-129">Linux</span><span class="sxs-lookup"><span data-stu-id="97b24-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="97b24-130">Vedere la documentazione della distribuzione di Linux su come rendere attendibile il certificato di sviluppo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="97b24-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="97b24-131">Per altre informazioni, vedere [Considerare attendibile il certificato di sviluppo di ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="97b24-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="97b24-132">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="97b24-132">Run the app</span></span>

<span data-ttu-id="97b24-133">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="97b24-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="97b24-134">Dopo che la shell dei comandi indica che l'app è stata avviata, passare a [https://localhost:5001](https://localhost:5001).</span><span class="sxs-lookup"><span data-stu-id="97b24-134">After the command shell indicates that the app has started, browse to [https://localhost:5001](https://localhost:5001).</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="97b24-135">Modificare una pagina Razor</span><span class="sxs-lookup"><span data-stu-id="97b24-135">Edit a Razor page</span></span>

<span data-ttu-id="97b24-136">Aprire *pages/index. cshtml* e modificare e salvare la pagina con il markup evidenziato seguente:</span><span class="sxs-lookup"><span data-stu-id="97b24-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="97b24-137">Passare a [https://localhost:5001](https://localhost:5001), aggiornare la pagina e verificare che le modifiche vengano visualizzate.</span><span class="sxs-lookup"><span data-stu-id="97b24-137">Browse to [https://localhost:5001](https://localhost:5001), refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="97b24-138">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="97b24-138">Next steps</span></span>

<span data-ttu-id="97b24-139">In questa esercitazione si è appreso come:</span><span class="sxs-lookup"><span data-stu-id="97b24-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="97b24-140">Creare un progetto di app Web.</span><span class="sxs-lookup"><span data-stu-id="97b24-140">Create a web app project.</span></span>
> * <span data-ttu-id="97b24-141">Considerare attendibile il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="97b24-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="97b24-142">Eseguire il progetto.</span><span class="sxs-lookup"><span data-stu-id="97b24-142">Run the project.</span></span>
> * <span data-ttu-id="97b24-143">Apportare una modifica.</span><span class="sxs-lookup"><span data-stu-id="97b24-143">Make a change.</span></span>

<span data-ttu-id="97b24-144">Per altre informazioni su ASP.NET Core, vedere il percorso di apprendimento consigliato nell'introduzione:</span><span class="sxs-lookup"><span data-stu-id="97b24-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
