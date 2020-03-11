---
title: Aggiungere un modello a un'app ASP.NET Core MVC
author: rick-anderson
description: Aggiungere un modello a una app semplice di ASP.NET Core.
ms.author: riande
ms.date: 01/13/2020
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: d044ae4416c4528791755506314fc81275474f79
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660237"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="6287e-103">Aggiungere un modello a un'app ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="6287e-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="6287e-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="6287e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="6287e-105">In questa sezione si aggiungono alcune classi per la gestione di filmati in un database.</span><span class="sxs-lookup"><span data-stu-id="6287e-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="6287e-106">Queste classi saranno la parte "**M**odel" dell'app **M**VC.</span><span class="sxs-lookup"><span data-stu-id="6287e-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="6287e-107">Si usano queste classi con [Entity Framework Core](/ef/core) (EF Core) per usare un database.</span><span class="sxs-lookup"><span data-stu-id="6287e-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="6287e-108">EF Core è un framework object-relational mapping (ORM) che semplifica il codice di accesso ai dati che è necessario scrivere.</span><span class="sxs-lookup"><span data-stu-id="6287e-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="6287e-109">Le classi di modello create sono dette classi POCO (da **P**lain **O**ld **C**LR **O**bjects) perché non hanno alcuna dipendenza in EF Core.</span><span class="sxs-lookup"><span data-stu-id="6287e-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="6287e-110">Definiscono semplicemente le proprietà dei dati che verranno archiviati nel database.</span><span class="sxs-lookup"><span data-stu-id="6287e-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="6287e-111">In questa esercitazione si scrivono innanzitutto le classi di modello e EF Core crea il database.</span><span class="sxs-lookup"><span data-stu-id="6287e-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="6287e-112">Aggiungere una classe del modello di dati</span><span class="sxs-lookup"><span data-stu-id="6287e-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6287e-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6287e-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6287e-114">Fare clic con il pulsante destro del mouse sulla cartella *Models* > **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6287e-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="6287e-115">Denominare il file *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="6287e-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6287e-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6287e-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6287e-117">Aggiungere un file denominato *Movie.cs* alla cartella *Models*.</span><span class="sxs-lookup"><span data-stu-id="6287e-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6287e-118">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6287e-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6287e-119">Fare clic con il pulsante destro del mouse sulla cartella *models* > **Aggiungi** > **nuova classe** > **classe vuota**.</span><span class="sxs-lookup"><span data-stu-id="6287e-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="6287e-120">Denominare il file *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="6287e-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="6287e-121">Aggiornare il file *Movie.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="6287e-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="6287e-122">La classe `Movie` contiene un campo `Id`, richiesto dal database per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="6287e-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="6287e-123">L'attributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) in `ReleaseDate` specifica il tipo di dati (`Date`).</span><span class="sxs-lookup"><span data-stu-id="6287e-123">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6287e-124">Con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="6287e-124">With this attribute:</span></span>

* <span data-ttu-id="6287e-125">l'utente non deve immettere le informazioni temporali nel campo della data.</span><span class="sxs-lookup"><span data-stu-id="6287e-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="6287e-126">Viene visualizzata solo la data, non le informazioni temporali.</span><span class="sxs-lookup"><span data-stu-id="6287e-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="6287e-127">L'attributo [DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) viene analizzato in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="6287e-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="6287e-128">Aggiungere i pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="6287e-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6287e-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6287e-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6287e-130">Dal menu **strumenti** selezionare **gestione pacchetti NuGet** > console di **Gestione pacchetti** (PMC).</span><span class="sxs-lookup"><span data-stu-id="6287e-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu della Console di Gestione pacchetti](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="6287e-132">Nella console di Gestione pacchetti eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="6287e-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="6287e-133">Il comando precedente aggiunge il provider EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6287e-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="6287e-134">Il pacchetto del provider installa il pacchetto di EF Core come dipendenza.</span><span class="sxs-lookup"><span data-stu-id="6287e-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="6287e-135">I pacchetti aggiuntivi vengono installati automaticamente nel passaggio di scaffolding più avanti nell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6287e-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6287e-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6287e-137">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6287e-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6287e-138">Scegliere **Gestisci pacchetti NuGet**dal menu **progetto** .</span><span class="sxs-lookup"><span data-stu-id="6287e-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="6287e-139">Nel campo di **ricerca** in alto a destra immettere `Microsoft.EntityFrameworkCore.SQLite` e premere il tasto **invio** per eseguire la ricerca.</span><span class="sxs-lookup"><span data-stu-id="6287e-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="6287e-140">Selezionare il pacchetto NuGet corrispondente e premere il pulsante **Aggiungi pacchetto** .</span><span class="sxs-lookup"><span data-stu-id="6287e-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Aggiungi Entity Framework Core pacchetto NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="6287e-142">Verrà visualizzata la finestra di dialogo **Seleziona progetti** con il progetto `MvcMovie` selezionato.</span><span class="sxs-lookup"><span data-stu-id="6287e-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="6287e-143">Premere il pulsante **OK** .</span><span class="sxs-lookup"><span data-stu-id="6287e-143">Press the **Ok** button.</span></span>

<span data-ttu-id="6287e-144">Verrà visualizzata una finestra di dialogo **accettazione licenza** .</span><span class="sxs-lookup"><span data-stu-id="6287e-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="6287e-145">Esaminare le licenze nel modo desiderato, quindi fare clic sul pulsante **Accetto** .</span><span class="sxs-lookup"><span data-stu-id="6287e-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="6287e-146">Ripetere i passaggi precedenti per installare i pacchetti NuGet seguenti:</span><span class="sxs-lookup"><span data-stu-id="6287e-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="6287e-147">Creare una classe di contesto di database</span><span class="sxs-lookup"><span data-stu-id="6287e-147">Create a database context class</span></span>

<span data-ttu-id="6287e-148">Una classe di contesto di database è necessaria per coordinare le funzionalità di EF Core (creazione, lettura, aggiornamento, eliminazione) per il modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6287e-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="6287e-149">Il contesto di database viene derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e specifica le entità da includere nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="6287e-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="6287e-150">Creare una cartella *Data*.</span><span class="sxs-lookup"><span data-stu-id="6287e-150">Create a *Data* folder.</span></span>

<span data-ttu-id="6287e-151">Aggiungere un file *Data/MvcMovieContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="6287e-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="6287e-152">Il codice precedente crea una proprietà [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="6287e-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="6287e-153">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="6287e-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="6287e-154">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="6287e-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="6287e-155">Registrare il contesto del database</span><span class="sxs-lookup"><span data-stu-id="6287e-155">Register the database context</span></span>

<span data-ttu-id="6287e-156">ASP.NET Core viene compilato con l'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6287e-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6287e-157">I servizi, ad esempio il contesto di database di EF Core, devono essere registrati per l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="6287e-158">Questi servizi vengono quindi offerti ai componenti per cui sono necessari (ad esempio Razor Pages) tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="6287e-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6287e-159">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="6287e-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="6287e-160">In questa sezione viene registrato il contesto di database per il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6287e-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="6287e-161">Aggiungere le istruzioni `using` seguenti all'inizio di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6287e-161">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="6287e-162">Aggiungere il codice evidenziato seguente in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6287e-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6287e-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6287e-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6287e-164">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6287e-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="6287e-165">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="6287e-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6287e-166">Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6287e-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="6287e-167">Aggiungere una stringa di connessione del database</span><span class="sxs-lookup"><span data-stu-id="6287e-167">Add a database connection string</span></span>

<span data-ttu-id="6287e-168">Aggiungere una stringa di connessione al file *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6287e-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6287e-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6287e-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6287e-170">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6287e-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="6287e-171">Compilare il progetto per controllare se sono presenti errori del compilatore.</span><span class="sxs-lookup"><span data-stu-id="6287e-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="6287e-172">Eseguire lo scaffolding delle pagine Movie</span><span class="sxs-lookup"><span data-stu-id="6287e-172">Scaffold movie pages</span></span>

<span data-ttu-id="6287e-173">Usare lo strumento di scaffolding per generare le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello Movie.</span><span class="sxs-lookup"><span data-stu-id="6287e-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6287e-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6287e-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6287e-175">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Controller* **> Aggiungi > Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="6287e-175">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del passaggio precedente](adding-model/_static/add_controller21.png)

<span data-ttu-id="6287e-177">Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Controller MVC con visualizzazioni, che usa Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="6287e-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Finestra di dialogo Aggiungi scaffolding](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="6287e-179">Completare la finestra di dialogo **Aggiungi controller**:</span><span class="sxs-lookup"><span data-stu-id="6287e-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="6287e-180">**Classe modello:** *Movie (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="6287e-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="6287e-181">**Classe del contesto dati:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="6287e-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Aggiungere il contesto dati](adding-model/_static/dc3.png)

* <span data-ttu-id="6287e-183">**Viste:** mantenere il valore predefinito di ogni opzione selezionata</span><span class="sxs-lookup"><span data-stu-id="6287e-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="6287e-184">**Nome del controller:** mantenere il valore predefinito *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="6287e-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="6287e-185">Selezionare **Aggiungi**</span><span class="sxs-lookup"><span data-stu-id="6287e-185">Select **Add**</span></span>

<span data-ttu-id="6287e-186">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="6287e-186">Visual Studio creates:</span></span>

* <span data-ttu-id="6287e-187">Un controller di filmati (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="6287e-187">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="6287e-188">File di vista Razor per le pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice) (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="6287e-188">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="6287e-189">La creazione automatica di questi file è nota come *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="6287e-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="6287e-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6287e-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="6287e-191">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="6287e-191">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="6287e-192">In Linux esportare il percorso dello strumento di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="6287e-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="6287e-193">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="6287e-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6287e-194">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6287e-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6287e-195">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="6287e-195">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="6287e-196">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="6287e-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="6287e-197">Non è possibile usare ancora le pagine sottoposte a scaffolding perché il database non esiste.</span><span class="sxs-lookup"><span data-stu-id="6287e-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="6287e-198">Se si esegue l'app e si fa clic sul collegamento dell' **app Movie** , viene visualizzato un messaggio di errore *non è possibile aprire il database* o una *tabella di questo tipo:* il messaggio di errore Movie.</span><span class="sxs-lookup"><span data-stu-id="6287e-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="6287e-199">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="6287e-199">Initial migration</span></span>

<span data-ttu-id="6287e-200">Usare la funzionalità [Migrazioni](xref:data/ef-mvc/migrations) di EF Core per creare il database.</span><span class="sxs-lookup"><span data-stu-id="6287e-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="6287e-201">Migrazioni è un set di strumenti che consentono di creare e aggiornare un database in modo che corrisponda al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="6287e-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6287e-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6287e-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6287e-203">Dal menu **strumenti** selezionare **gestione pacchetti NuGet** > console di **Gestione pacchetti** (PMC).</span><span class="sxs-lookup"><span data-stu-id="6287e-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="6287e-204">In PMC, immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6287e-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="6287e-205">`Add-Migration InitialCreate`: genera un file di migrazione *Migrations/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="6287e-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="6287e-206">L'argomento `InitialCreate` è il nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="6287e-207">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="6287e-208">Trattandosi della prima migrazione, la classe generata contiene il codice per creare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="6287e-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="6287e-209">Lo schema del database si basa sul modello specificato nella classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="6287e-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="6287e-210">`Update-Database`: aggiorna il database alla migrazione più recente, creata dal comando precedente.</span><span class="sxs-lookup"><span data-stu-id="6287e-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="6287e-211">Il comando esegue il metodo `Up` nel file *Migrations/{time-stamp}_InitialCreate.cs*, che crea il database.</span><span class="sxs-lookup"><span data-stu-id="6287e-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="6287e-212">Il comando database update genera l'avviso seguente:</span><span class="sxs-lookup"><span data-stu-id="6287e-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="6287e-213">No type was specified for the decimal column 'Price' on entity type 'Movie'. (Nessun tipo specificato per la colonna decimale 'Price' nel tipo di entità 'Movie').</span><span class="sxs-lookup"><span data-stu-id="6287e-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="6287e-214">This will cause values to be silently truncated if they do not fit in the default precision and scale. (I valori saranno quindi automaticamente troncati se non rispettano la precisione e la scala predefinite).</span><span class="sxs-lookup"><span data-stu-id="6287e-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="6287e-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Specificare in modo esplicito il tipo di colonna di SQL Server che può supportare tutti i valori usando 'HasColumnType()').</span><span class="sxs-lookup"><span data-stu-id="6287e-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="6287e-216">È possibile ignorare tale avviso. Verrà risolto in un'esercitazione successiva.</span><span class="sxs-lookup"><span data-stu-id="6287e-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6287e-217">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6287e-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6287e-218">Eseguire i seguenti comandi dell'interfaccia della riga di comando di .NET Core:</span><span class="sxs-lookup"><span data-stu-id="6287e-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="6287e-219">`ef migrations add InitialCreate`: genera un file di migrazione *Migrations/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="6287e-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="6287e-220">L'argomento `InitialCreate` è il nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="6287e-221">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="6287e-222">Trattandosi della prima migrazione, la classe generata contiene il codice per creare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="6287e-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="6287e-223">Lo schema del database è basato sul modello specificato nella classe `MvcMovieContext` (nel file *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="6287e-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="6287e-224">`ef database update`: aggiorna il database alla migrazione più recente, creata dal comando precedente.</span><span class="sxs-lookup"><span data-stu-id="6287e-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="6287e-225">Il comando esegue il metodo `Up` nel file *Migrations/{time-stamp}_InitialCreate.cs*, che crea il database.</span><span class="sxs-lookup"><span data-stu-id="6287e-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="6287e-226">Classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="6287e-226">The InitialCreate class</span></span>

<span data-ttu-id="6287e-227">Esaminare il file di migrazione *Migrations/{timestamp}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="6287e-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="6287e-228">Il metodo `Up` crea la tabella Movie e configura `Id` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="6287e-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="6287e-229">Il metodo `Down` annulla le modifiche dello schema apportate dalla migrazione `Up`.</span><span class="sxs-lookup"><span data-stu-id="6287e-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="6287e-230">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="6287e-230">Test the app</span></span>

* <span data-ttu-id="6287e-231">Eseguire l'app e fare clic sul collegamento **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="6287e-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="6287e-232">Se si ottiene un'eccezione simile a una delle seguenti:</span><span class="sxs-lookup"><span data-stu-id="6287e-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6287e-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6287e-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6287e-234">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6287e-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="6287e-235">Probabilmente non è stato eseguito il [passaggio delle migrazioni](#migration).</span><span class="sxs-lookup"><span data-stu-id="6287e-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="6287e-236">Testare la pagina **Create**.</span><span class="sxs-lookup"><span data-stu-id="6287e-236">Test the **Create** page.</span></span> <span data-ttu-id="6287e-237">Immettere e inviare i dati.</span><span class="sxs-lookup"><span data-stu-id="6287e-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="6287e-238">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="6287e-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="6287e-239">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="6287e-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="6287e-240">Per istruzioni sulla localizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="6287e-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="6287e-241">Testare le pagine **Edit**, **Details** e **Delete**.</span><span class="sxs-lookup"><span data-stu-id="6287e-241">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="6287e-242">Inserimento delle dipendenze nel controller</span><span class="sxs-lookup"><span data-stu-id="6287e-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6287e-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6287e-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6287e-244">Aprire il file *Controllers/MoviesController.cs* ed esaminare il costruttore:</span><span class="sxs-lookup"><span data-stu-id="6287e-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="6287e-245">Il costruttore usa l'[inserimento dipendenze](xref:fundamentals/dependency-injection) per inserire il contesto del database (`MvcMovieContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="6287e-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="6287e-246">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="6287e-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6287e-247">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6287e-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="6287e-248">Il costruttore usa l'[inserimento dipendenze](xref:fundamentals/dependency-injection) per inserire il contesto del database (`MvcMovieContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="6287e-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="6287e-249">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="6287e-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="6287e-250">Usare SQLite per lo sviluppo, SQL Server per la produzione</span><span class="sxs-lookup"><span data-stu-id="6287e-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="6287e-251">Quando si seleziona SQLite, il codice generato dal modello è pronto per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="6287e-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="6287e-252">Il codice seguente illustra come inserire <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> all'avvio.</span><span class="sxs-lookup"><span data-stu-id="6287e-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="6287e-253">`IWebHostEnvironment` viene inserito in modo che `ConfigureServices` possa usare SQLite per lo sviluppo e la SQL Server nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="6287e-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="6287e-254">Modelli fortemente tipizzati e parola chiave @model</span><span class="sxs-lookup"><span data-stu-id="6287e-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="6287e-255">In un passaggio precedente di questa esercitazione è stato esaminato come un controller può passare oggetti o dati a una vista usando il dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="6287e-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="6287e-256">Il dizionario `ViewData` è un oggetto dinamico che fornisce un modo pratico ad associazione tardiva per passare informazioni a una vista.</span><span class="sxs-lookup"><span data-stu-id="6287e-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="6287e-257">MVC consente anche di passare oggetti modello fortemente tipizzati a una vista.</span><span class="sxs-lookup"><span data-stu-id="6287e-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="6287e-258">Questo approccio fortemente tipizzato consente il controllo del codice in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="6287e-259">Con il meccanismo di scaffolding è stato usato questo approccio, ovvero il passaggio di un modello fortemente tipizzato, con le viste e la classe `MoviesController`.</span><span class="sxs-lookup"><span data-stu-id="6287e-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="6287e-260">Aprire il metodo `Details` nel file *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="6287e-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="6287e-261">In genere il parametro `id` viene passato come dati di route.</span><span class="sxs-lookup"><span data-stu-id="6287e-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="6287e-262">Ad esempio `https://localhost:5001/movies/details/1` imposta:</span><span class="sxs-lookup"><span data-stu-id="6287e-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="6287e-263">Il controller sul controller `movies` (primo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="6287e-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="6287e-264">L'azione su `details` (secondo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="6287e-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="6287e-265">L'ID su 1 (ultimo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="6287e-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="6287e-266">È possibile anche passare `id` con una stringa di query nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="6287e-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="6287e-267">Il parametro `id` viene definito come [tipo nullable](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) nel caso in cui non venga fornito un valore ID.</span><span class="sxs-lookup"><span data-stu-id="6287e-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="6287e-268">Un'[espressione lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) viene passata a `FirstOrDefaultAsync` per selezionare le entità film che corrispondono al valore della stringa di query o dei dati di route.</span><span class="sxs-lookup"><span data-stu-id="6287e-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="6287e-269">Se viene trovato un film, viene passata un'istanza del modello `Movie` alla vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="6287e-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="6287e-270">Esaminare il contenuto del file *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6287e-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="6287e-271">L'istruzione `@model` all'inizio del file di vista specifica il tipo di oggetto previsto dalla vista.</span><span class="sxs-lookup"><span data-stu-id="6287e-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="6287e-272">Quando è stato creato il controller dei film, è stata inclusa l'istruzione `@model` seguente:</span><span class="sxs-lookup"><span data-stu-id="6287e-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="6287e-273">Questa direttiva `@model` consente di accedere al film che il controller ha passato alla vista.</span><span class="sxs-lookup"><span data-stu-id="6287e-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="6287e-274">L'oggetto `Model` è fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="6287e-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="6287e-275">Ad esempio, nella vista *Details.cshtml* il codice passa ogni campo di film agli helper HTML `DisplayNameFor` e `DisplayFor` con l'oggetto `Model` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="6287e-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="6287e-276">Le viste e i metodi `Create` e `Edit` passano anche un oggetto modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6287e-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="6287e-277">Esaminare la vista *Index.cshtml* e il metodo `Index` nel controller Movies.</span><span class="sxs-lookup"><span data-stu-id="6287e-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="6287e-278">Si noti che il codice crea un oggetto `List` quando chiama il metodo `View`.</span><span class="sxs-lookup"><span data-stu-id="6287e-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="6287e-279">Il codice passa questo elenco `Movies` dal metodo di azione `Index` alla vista:</span><span class="sxs-lookup"><span data-stu-id="6287e-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="6287e-280">Al momento della creazione del controller di film, lo scaffolding ha incluso l'istruzione `@model` all'inizio del file *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6287e-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="6287e-281">La direttiva `@model` consente di accedere all'elenco di film che il controller ha passato alla vista usando un oggetto `Model` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="6287e-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="6287e-282">Ad esempio, nella vista *Index.cshtml* il codice scorre i film con un'istruzione `foreach` sull'oggetto fortemente tipizzato `Model`:</span><span class="sxs-lookup"><span data-stu-id="6287e-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="6287e-283">Poiché l'oggetto `Model` è fortemente tipizzato (come un oggetto `IEnumerable<Movie>`), ogni elemento nel ciclo viene tipizzato come `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6287e-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="6287e-284">Tra gli altri vantaggi, si ottiene un controllo del codice in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6287e-285">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6287e-285">Additional resources</span></span>

* [<span data-ttu-id="6287e-286">Helper tag</span><span class="sxs-lookup"><span data-stu-id="6287e-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="6287e-287">Globalizzazione e localizzazione</span><span class="sxs-lookup"><span data-stu-id="6287e-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="6287e-288">[Precedente - Aggiunta di una vista](adding-view.md)
> [Successivo - Utilizzo del linguaggio SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="6287e-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="6287e-289">Aggiungere una classe del modello di dati</span><span class="sxs-lookup"><span data-stu-id="6287e-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6287e-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6287e-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6287e-291">Fare clic con il pulsante destro del mouse sulla cartella *Models* > **Aggiungi** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6287e-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="6287e-292">Denominare la classe **Movie**.</span><span class="sxs-lookup"><span data-stu-id="6287e-292">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6287e-293">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6287e-293">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6287e-294">Aggiungere una classe alla cartella *Models* denominata *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="6287e-294">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="6287e-295">Eseguire lo scaffolding del modello *Movie*</span><span class="sxs-lookup"><span data-stu-id="6287e-295">Scaffold the movie model</span></span>

<span data-ttu-id="6287e-296">In questa sezione viene eseguito lo scaffolding del modello *Movie*.</span><span class="sxs-lookup"><span data-stu-id="6287e-296">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="6287e-297">Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello di filmato.</span><span class="sxs-lookup"><span data-stu-id="6287e-297">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6287e-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6287e-298">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6287e-299">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Controller* **> Aggiungi > Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="6287e-299">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![vista del passaggio precedente](adding-model/_static/add_controller21.png)

<span data-ttu-id="6287e-301">Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Controller MVC con visualizzazioni, che usa Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="6287e-301">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Finestra di dialogo Aggiungi scaffolding](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="6287e-303">Completare la finestra di dialogo **Aggiungi controller**:</span><span class="sxs-lookup"><span data-stu-id="6287e-303">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="6287e-304">**Classe modello:** *Movie (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="6287e-304">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="6287e-305">**Classe del contesto dati:** selezionare l'icona **+** e aggiungere il valore predefinito **MvcMovie.Models.MvcMovieContext**</span><span class="sxs-lookup"><span data-stu-id="6287e-305">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Aggiungere il contesto dati](adding-model/_static/dc.png)

* <span data-ttu-id="6287e-307">**Viste:** mantenere il valore predefinito di ogni opzione selezionata</span><span class="sxs-lookup"><span data-stu-id="6287e-307">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="6287e-308">**Nome del controller:** mantenere il valore predefinito *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="6287e-308">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="6287e-309">Selezionare **Aggiungi**</span><span class="sxs-lookup"><span data-stu-id="6287e-309">Select **Add**</span></span>

![Finestra di dialogo Aggiungi controller](adding-model/_static/add_controller2.png)

<span data-ttu-id="6287e-311">Visual Studio crea:</span><span class="sxs-lookup"><span data-stu-id="6287e-311">Visual Studio creates:</span></span>

* <span data-ttu-id="6287e-312">Una [classe del contesto di database](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="6287e-312">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="6287e-313">Un controller di filmati (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="6287e-313">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="6287e-314">File di vista Razor per le pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice) (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="6287e-314">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="6287e-315">La creazione automatica del contesto di database e di viste e metodi di azione [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, delete) è nota come *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="6287e-315">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6287e-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6287e-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="6287e-317">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="6287e-317">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="6287e-318">Installare lo strumento di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="6287e-318">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="6287e-319">In Linux esportare il percorso dello strumento di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="6287e-319">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="6287e-320">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="6287e-320">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6287e-321">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6287e-321">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6287e-322">Aprire una finestra di comando nella directory del progetto (la directory che contiene i file *Program.cs*, *Startup.cs* e *csproj*).</span><span class="sxs-lookup"><span data-stu-id="6287e-322">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="6287e-323">Installare lo strumento di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="6287e-323">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="6287e-324">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="6287e-324">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="6287e-325">Se si esegue l'app e si fa clic sul collegamento **Mvc Movie**, viene visualizzato un errore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="6287e-325">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6287e-326">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6287e-326">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6287e-327">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6287e-327">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="6287e-328">È necessario creare il database, quindi si usa la funzionalità di [migrazioni](xref:data/ef-mvc/migrations) di Entity Framework Core a tale scopo.</span><span class="sxs-lookup"><span data-stu-id="6287e-328">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="6287e-329">Le migrazioni consentono di creare un database che corrisponde al modello di dati e aggiornare lo schema del database quando cambia il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="6287e-329">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="6287e-330">Migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="6287e-330">Initial migration</span></span>

<span data-ttu-id="6287e-331">In questa sezione vengono completate le attività seguenti:</span><span class="sxs-lookup"><span data-stu-id="6287e-331">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="6287e-332">Aggiungere una migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="6287e-332">Add an initial migration.</span></span>
* <span data-ttu-id="6287e-333">Aggiornare il database con la migrazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="6287e-333">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6287e-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6287e-334">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6287e-335">Dal menu **strumenti** selezionare **gestione pacchetti NuGet** > console di **Gestione pacchetti** (PMC).</span><span class="sxs-lookup"><span data-stu-id="6287e-335">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu della Console di Gestione pacchetti](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="6287e-337">In PMC, immettere i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6287e-337">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="6287e-338">Il comando `Add-Migration` genera un codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="6287e-338">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="6287e-339">Lo schema del database si basa sul modello specificato nella classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="6287e-339">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="6287e-340">L'argomento `Initial` è il nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-340">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="6287e-341">È possibile usare qualsiasi nome, ma per convenzione viene usato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-341">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="6287e-342">Per altre informazioni, vedere <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="6287e-342">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="6287e-343">Il comando `Update-Database` esegue il metodo `Up` nel file *Migrations/{time-stamp}_InitialCreate.cs*, che crea il database.</span><span class="sxs-lookup"><span data-stu-id="6287e-343">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6287e-344">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6287e-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="6287e-345">Il comando `ef migrations add InitialCreate` genera un codice per creare lo schema del database iniziale.</span><span class="sxs-lookup"><span data-stu-id="6287e-345">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="6287e-346">Lo schema del database è basato sul modello specificato nella classe `MvcMovieContext` (nel file *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="6287e-346">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="6287e-347">L'argomento `InitialCreate` è il nome della migrazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-347">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="6287e-348">È possibile usare qualsiasi nome, ma per convenzione viene selezionato un nome che descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-348">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="6287e-349">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="6287e-349">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="6287e-350">ASP.NET Core viene compilato con l'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6287e-350">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6287e-351">I servizi, ad esempio il contesto di database di EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-351">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="6287e-352">Questi servizi vengono quindi offerti ai componenti per cui sono necessari (ad esempio Razor Pages) tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="6287e-352">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6287e-353">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="6287e-353">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6287e-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6287e-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6287e-355">Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6287e-355">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="6287e-356">Esaminare il metodo `Startup.ConfigureServices` seguente.</span><span class="sxs-lookup"><span data-stu-id="6287e-356">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6287e-357">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="6287e-357">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="6287e-358">`MvcMovieContext` coordina la funzionalità di EF Core (Create, Read, Update, Delete e così via) per il modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6287e-358">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="6287e-359">Il contesto dei dati (`MvcMovieContext`) è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="6287e-359">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="6287e-360">Il contesto dei dati specifica le entità incluse nel modello di dati:</span><span class="sxs-lookup"><span data-stu-id="6287e-360">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="6287e-361">Il codice precedente crea una proprietà [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per il set di entità.</span><span class="sxs-lookup"><span data-stu-id="6287e-361">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="6287e-362">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="6287e-362">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="6287e-363">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="6287e-363">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6287e-364">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="6287e-364">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6287e-365">Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6287e-365">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6287e-366">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="6287e-366">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6287e-367">È stato creato un contesto del database, poi registrato per il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="6287e-367">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="6287e-368">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="6287e-368">Test the app</span></span>

* <span data-ttu-id="6287e-369">Eseguire l'app e accodare `/Movies` all'URL nel browser (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="6287e-369">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="6287e-370">Se si verifica un'eccezione di database simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="6287e-370">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="6287e-371">Non è stato eseguita la [migrazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="6287e-371">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="6287e-372">Eseguire il test del collegamento **Crea**.</span><span class="sxs-lookup"><span data-stu-id="6287e-372">Test the **Create** link.</span></span> <span data-ttu-id="6287e-373">Immettere e inviare i dati.</span><span class="sxs-lookup"><span data-stu-id="6287e-373">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="6287e-374">Potrebbe non essere possibile immettere virgole decimali nel campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="6287e-374">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="6287e-375">Per supportare la [convalida jQuery](https://jqueryvalidation.org/) per impostazioni locali diverse dall'inglese che usano la virgola (",") come separatore decimale e per formati di data diversi da quello dell'inglese (Stati Uniti), è necessario localizzare l'app.</span><span class="sxs-lookup"><span data-stu-id="6287e-375">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="6287e-376">Per istruzioni sulla localizzazione, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="6287e-376">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="6287e-377">Testare i collegamenti **Modifica**, **Dettagli** ed **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="6287e-377">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="6287e-378">Esaminare la classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="6287e-378">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="6287e-379">Il codice evidenziato riportato in precedenza mostra il contesto del database dei film che viene aggiunto al contenitore di [inserimento dipendenze](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="6287e-379">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="6287e-380">`services.AddDbContext<MvcMovieContext>(options =>` specifica il database da usare e la stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="6287e-380">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="6287e-381">`=>` è un [operatore lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="6287e-381">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="6287e-382">Aprire il file *Controllers/MoviesController.cs* ed esaminare il costruttore:</span><span class="sxs-lookup"><span data-stu-id="6287e-382">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="6287e-383">Il costruttore usa l'[inserimento dipendenze](xref:fundamentals/dependency-injection) per inserire il contesto del database (`MvcMovieContext`) nel controller.</span><span class="sxs-lookup"><span data-stu-id="6287e-383">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="6287e-384">Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.</span><span class="sxs-lookup"><span data-stu-id="6287e-384">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="6287e-385">Modelli fortemente tipizzati e parola chiave @model</span><span class="sxs-lookup"><span data-stu-id="6287e-385">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="6287e-386">In un passaggio precedente di questa esercitazione è stato esaminato come un controller può passare oggetti o dati a una vista usando il dizionario `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="6287e-386">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="6287e-387">Il dizionario `ViewData` è un oggetto dinamico che fornisce un modo pratico ad associazione tardiva per passare informazioni a una vista.</span><span class="sxs-lookup"><span data-stu-id="6287e-387">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="6287e-388">MVC consente anche di passare oggetti modello fortemente tipizzati a una vista.</span><span class="sxs-lookup"><span data-stu-id="6287e-388">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="6287e-389">Questo approccio fortemente tipizzato consente un migliore controllo del codice in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="6287e-389">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="6287e-390">Con il meccanismo di scaffolding è stato usato questo approccio, ovvero il passaggio di un modello fortemente tipizzato, con le viste e la classe `MoviesController` quando sono stati creati i metodi e le viste.</span><span class="sxs-lookup"><span data-stu-id="6287e-390">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="6287e-391">Aprire il metodo `Details` nel file *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="6287e-391">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="6287e-392">In genere il parametro `id` viene passato come dati di route.</span><span class="sxs-lookup"><span data-stu-id="6287e-392">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="6287e-393">Ad esempio `https://localhost:5001/movies/details/1` imposta:</span><span class="sxs-lookup"><span data-stu-id="6287e-393">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="6287e-394">Il controller sul controller `movies` (primo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="6287e-394">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="6287e-395">L'azione su `details` (secondo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="6287e-395">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="6287e-396">L'ID su 1 (ultimo segmento di URL).</span><span class="sxs-lookup"><span data-stu-id="6287e-396">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="6287e-397">È possibile anche passare `id` con una stringa di query nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="6287e-397">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="6287e-398">Il parametro `id` viene definito come [tipo nullable](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) nel caso in cui non venga fornito un valore ID.</span><span class="sxs-lookup"><span data-stu-id="6287e-398">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="6287e-399">Un'[espressione lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) viene passata a `FirstOrDefaultAsync` per selezionare le entità film che corrispondono al valore della stringa di query o dei dati di route.</span><span class="sxs-lookup"><span data-stu-id="6287e-399">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="6287e-400">Se viene trovato un film, viene passata un'istanza del modello `Movie` alla vista `Details`:</span><span class="sxs-lookup"><span data-stu-id="6287e-400">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="6287e-401">Esaminare il contenuto del file *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6287e-401">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="6287e-402">Includendo un'istruzione `@model` all'inizio del file di vista, è possibile specificare il tipo di oggetto previsto dalla vista.</span><span class="sxs-lookup"><span data-stu-id="6287e-402">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="6287e-403">Al momento della creazione del controller di film, l'istruzione `@model` seguente è stata inclusa automaticamente all'inizio del file *Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6287e-403">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="6287e-404">Questa direttiva `@model` consente di accedere al film passato dal controller alla vista usando un oggetto `Model` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="6287e-404">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="6287e-405">Ad esempio, nella vista *Details.cshtml* il codice passa ogni campo di film agli helper HTML `DisplayNameFor` e `DisplayFor` con l'oggetto `Model` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="6287e-405">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="6287e-406">Le viste e i metodi `Create` e `Edit` passano anche un oggetto modello `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6287e-406">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="6287e-407">Esaminare la vista *Index.cshtml* e il metodo `Index` nel controller Movies.</span><span class="sxs-lookup"><span data-stu-id="6287e-407">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="6287e-408">Si noti che il codice crea un oggetto `List` quando chiama il metodo `View`.</span><span class="sxs-lookup"><span data-stu-id="6287e-408">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="6287e-409">Il codice passa questo elenco `Movies` dal metodo di azione `Index` alla vista:</span><span class="sxs-lookup"><span data-stu-id="6287e-409">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="6287e-410">Al momento della creazione del controller di film, lo scaffolding ha incluso automaticamente l'istruzione `@model` all'inizio del file *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6287e-410">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="6287e-411">La direttiva `@model` consente di accedere all'elenco di film che il controller ha passato alla vista usando un oggetto `Model` fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="6287e-411">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="6287e-412">Ad esempio, nella vista *Index.cshtml* il codice scorre i film con un'istruzione `foreach` sull'oggetto fortemente tipizzato `Model`:</span><span class="sxs-lookup"><span data-stu-id="6287e-412">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="6287e-413">Poiché l'oggetto `Model` è fortemente tipizzato (come un oggetto `IEnumerable<Movie>`), ogni elemento nel ciclo viene tipizzato come `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6287e-413">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="6287e-414">Tra gli altri vantaggi, si ottiene un controllo del codice in fase di compilazione:</span><span class="sxs-lookup"><span data-stu-id="6287e-414">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6287e-415">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6287e-415">Additional resources</span></span>

* [<span data-ttu-id="6287e-416">Helper tag</span><span class="sxs-lookup"><span data-stu-id="6287e-416">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="6287e-417">Globalizzazione e localizzazione</span><span class="sxs-lookup"><span data-stu-id="6287e-417">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="6287e-418">[Precedente aggiunta di una vista](adding-view.md)
> [successiva utilizzo di un database](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="6287e-418">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
