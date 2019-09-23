---
title: 'Razor Pages con Entity Framework Core in ASP.NET Core: esercitazione 1 di 8'
author: tdykstra
description: Viene illustrato come creare un'app Razor Pages con Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 07/22/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 6b7d2ca1cea23efd195f1ae0e0a749c6d2d9b622
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186964"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="674b6-103">Razor Pages con Entity Framework Core in ASP.NET Core: esercitazione 1 di 8</span><span class="sxs-lookup"><span data-stu-id="674b6-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="674b6-104">Di [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="674b6-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="674b6-105">Questa è la prima di una serie di esercitazioni che illustrano come usare Entity Framework (EF) Core in un'app ASP.NET Core Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="674b6-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an ASP.NET Core Razor Pages app.</span></span> <span data-ttu-id="674b6-106">Con queste esercitazioni viene creato un sito Web per una fittizia Contoso University.</span><span class="sxs-lookup"><span data-stu-id="674b6-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="674b6-107">Il sito include funzionalità, come ad esempio l'ammissione di studenti, la creazione di corsi e le assegnazioni degli insegnati.</span><span class="sxs-lookup"><span data-stu-id="674b6-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span>

[<span data-ttu-id="674b6-108">Scaricare o visualizzare l'app completata.</span><span class="sxs-lookup"><span data-stu-id="674b6-108">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="674b6-109">[Istruzioni per il download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="674b6-109">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="674b6-110">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="674b6-110">Prerequisites</span></span>

* <span data-ttu-id="674b6-111">Se non si ha familiarità con Razor Pages, vedere la serie di esercitazioni [Introduzione a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) prima di iniziare.</span><span class="sxs-lookup"><span data-stu-id="674b6-111">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="674b6-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="674b6-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="674b6-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="674b6-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="674b6-114">Motori di database</span><span class="sxs-lookup"><span data-stu-id="674b6-114">Database engines</span></span>

<span data-ttu-id="674b6-115">Le istruzioni per Visual Studio usano [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versione di SQL Server Express eseguita solo in Windows.</span><span class="sxs-lookup"><span data-stu-id="674b6-115">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="674b6-116">Le istruzioni per Visual Studio Code usano [SQLite](https://www.sqlite.org/), un motore di database multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="674b6-116">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="674b6-117">Se si sceglie di usare SQLite, scaricare e installare uno strumento di terze parti per la gestione e la visualizzazione di un database SQLite, ad esempio [DB Browser per SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="674b6-117">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="674b6-118">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="674b6-118">Troubleshooting</span></span>

<span data-ttu-id="674b6-119">Se si verifica un problema che non è possibile risolvere, confrontare il codice con il [progetto completato](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="674b6-119">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="674b6-120">Un buon metodo per ottenere assistenza è quello di pubblicare una domanda in StackOverflow.com con il [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o il [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="674b6-120">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="674b6-121">App di esempio</span><span class="sxs-lookup"><span data-stu-id="674b6-121">The sample app</span></span>

<span data-ttu-id="674b6-122">L'applicazione compilata in queste esercitazioni è il sito Web di base di un'università.</span><span class="sxs-lookup"><span data-stu-id="674b6-122">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="674b6-123">Gli utenti possono visualizzare e aggiornare le informazioni che riguardano studenti, corsi e insegnanti.</span><span class="sxs-lookup"><span data-stu-id="674b6-123">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="674b6-124">Di seguito sono disponibili alcune schermate dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="674b6-124">Here are a few of the screens created in the tutorial.</span></span>

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index30.png)

![Pagina Students Edit (Modifica studenti)](intro/_static/student-edit30.png)

<span data-ttu-id="674b6-127">Lo stile dell'interfaccia utente del sito è basato sui modelli di progetto predefiniti.</span><span class="sxs-lookup"><span data-stu-id="674b6-127">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="674b6-128">L'obiettivo dell'esercitazione è quello di usare EF Core e non di personalizzare l'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="674b6-128">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="674b6-129">Seguire il collegamento nella parte superiore della pagina per ottenere il codice sorgente per il progetto completato.</span><span class="sxs-lookup"><span data-stu-id="674b6-129">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="674b6-130">La cartella *cu30* contiene il codice per la versione di ASP.NET Core 3.0 dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="674b6-130">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="674b6-131">I file che riflettono lo stato del codice per le esercitazioni 1-7 si trovano nella cartella *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="674b6-131">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="674b6-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="674b6-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="674b6-133">Per eseguire l'app dopo il download del progetto completato:</span><span class="sxs-lookup"><span data-stu-id="674b6-133">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="674b6-134">Eliminare i tre file e la cartella con *SQLite* nel nome.</span><span class="sxs-lookup"><span data-stu-id="674b6-134">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="674b6-135">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="674b6-135">Build the project.</span></span>
* <span data-ttu-id="674b6-136">Nella console di Gestione pacchetti eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="674b6-136">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="674b6-137">Eseguire il progetto per il seeding del database.</span><span class="sxs-lookup"><span data-stu-id="674b6-137">Run the project to seed the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="674b6-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="674b6-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="674b6-139">Per eseguire l'app dopo il download del progetto completato:</span><span class="sxs-lookup"><span data-stu-id="674b6-139">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="674b6-140">Eliminare *ContosoUniversity.csproj* e rinominare *ContosoUniversitySQLite.csproj* in *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="674b6-140">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="674b6-141">Eliminare *Startup.cs* e rinominare *StartupSQLite.cs* in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="674b6-141">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="674b6-142">Eliminare *appSettings.json* e rinominare *appSettingsSQLite.json* in *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="674b6-142">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="674b6-143">Eliminare la cartella *Migrations* e rinominare *MigrationsSQL* in *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="674b6-143">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="674b6-144">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="674b6-144">Build the project.</span></span>
* <span data-ttu-id="674b6-145">Al prompt dei comandi nella cartella del progetto eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="674b6-145">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="674b6-146">Nello strumento SQLite eseguire l'istruzione SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="674b6-146">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="674b6-147">Eseguire il progetto per il seeding del database.</span><span class="sxs-lookup"><span data-stu-id="674b6-147">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="674b6-148">Creare il progetto di app Web</span><span class="sxs-lookup"><span data-stu-id="674b6-148">Create the web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="674b6-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="674b6-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="674b6-150">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="674b6-150">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="674b6-151">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="674b6-151">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="674b6-152">Denominare il progetto *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="674b6-152">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="674b6-153">È importante usare questo nome esatto, incluse le maiuscole, in modo che gli spazi dei nomi corrispondano quando il codice viene copiato e incollato.</span><span class="sxs-lookup"><span data-stu-id="674b6-153">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="674b6-154">Selezionare **.NET Core** e **ASP.NET Core 3.0** nell'elenco a discesa, quindi selezionare **Applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="674b6-154">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="674b6-155">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="674b6-155">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="674b6-156">In un terminale passare alla cartella in cui deve essere creata la cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="674b6-156">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="674b6-157">Eseguire i comandi seguenti per creare un progetto Razor Pages e `cd` nella nuova cartella di progetto:</span><span class="sxs-lookup"><span data-stu-id="674b6-157">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="674b6-158">Impostare lo stile del sito</span><span class="sxs-lookup"><span data-stu-id="674b6-158">Set up the site style</span></span>

<span data-ttu-id="674b6-159">Per impostare l'intestazione, il piè di pagina e il menu del sito, aggiornare *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="674b6-159">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="674b6-160">Modificare tutte le occorrenze di "ContosoUniversity" in "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="674b6-160">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="674b6-161">Le occorrenze sono tre.</span><span class="sxs-lookup"><span data-stu-id="674b6-161">There are three occurrences.</span></span>

* <span data-ttu-id="674b6-162">Eliminare le voci di menu **Home** e **Privacy**, quindi aggiungere le voci per **About** (Informazioni su), **Students** (Studenti), **Courses** (Corsi), **Instructors** (Insegnanti) e **Departments** (Dipartimenti).</span><span class="sxs-lookup"><span data-stu-id="674b6-162">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="674b6-163">Le modifiche vengono evidenziate.</span><span class="sxs-lookup"><span data-stu-id="674b6-163">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="674b6-164">In *Pages/Index.cshtml* sostituire il contenuto del file con il codice seguente. In questo modo il testo su ASP.NET sarà sostituito dal testo relativo a questa app:</span><span class="sxs-lookup"><span data-stu-id="674b6-164">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="674b6-165">Eseguire l'app per verificare che venga visualizzata la home page.</span><span class="sxs-lookup"><span data-stu-id="674b6-165">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="674b6-166">Modello di dati</span><span class="sxs-lookup"><span data-stu-id="674b6-166">The data model</span></span>

<span data-ttu-id="674b6-167">Nelle sezioni seguenti viene descritto come creare un modello di dati:</span><span class="sxs-lookup"><span data-stu-id="674b6-167">The following sections create a data model:</span></span>

![Diagramma modello di dati Course/Enrollment/Student (Corso/Iscrizione/Studente)](intro/_static/data-model-diagram.png)

<span data-ttu-id="674b6-169">Uno studente può iscriversi a un numero qualsiasi di corsi e un corso può avere un numero qualsiasi di studenti iscritti.</span><span class="sxs-lookup"><span data-stu-id="674b6-169">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="674b6-170">Entità Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="674b6-170">The Student entity</span></span>

![Diagramma entità Student (Studente)](intro/_static/student-entity.png)

* <span data-ttu-id="674b6-172">Creare una cartella *Models* nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="674b6-172">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="674b6-173">Creare *Models/Student.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="674b6-173">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="674b6-174">La proprietà `ID` diventa la colonna chiave primaria della tabella del database che corrisponde a questa classe.</span><span class="sxs-lookup"><span data-stu-id="674b6-174">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="674b6-175">Per impostazione predefinita, Entity Framework Core interpreta una proprietà denominata `ID` o `classnameID` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="674b6-175">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="674b6-176">Il nome alternativo riconosciuto automaticamente per la chiave primaria della classe `Student` è quindi `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="674b6-176">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span>

<span data-ttu-id="674b6-177">La proprietà `Enrollments` rappresenta una [proprietà di navigazione](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="674b6-177">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="674b6-178">Le proprietà di navigazione contengono altre entità correlate a questa entità.</span><span class="sxs-lookup"><span data-stu-id="674b6-178">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="674b6-179">In questo caso la proprietà `Enrollments` di un'entità `Student` contiene tutte le entità `Enrollment` correlate a tale studente.</span><span class="sxs-lookup"><span data-stu-id="674b6-179">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="674b6-180">Ad esempio, se una riga Student nel database presenta due righe Enrollment correlate, la proprietà di navigazione `Enrollments` contiene questi due entità Enrollment.</span><span class="sxs-lookup"><span data-stu-id="674b6-180">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="674b6-181">Nel database, una riga Enrollment è correlata a una riga Student se la relativa colonna StudentID contiene il valore ID dello studente.</span><span class="sxs-lookup"><span data-stu-id="674b6-181">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="674b6-182">Si supponga, ad esempio, che una riga Student abbia ID=1.</span><span class="sxs-lookup"><span data-stu-id="674b6-182">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="674b6-183">Le righe Enrollment correlate avranno StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="674b6-183">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="674b6-184">StudentID è una *chiave esterna* nella tabella Enrollment.</span><span class="sxs-lookup"><span data-stu-id="674b6-184">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="674b6-185">La proprietà `Enrollments` è definita come `ICollection<Enrollment>` perché potrebbero essere presenti più entità Enrollment correlate.</span><span class="sxs-lookup"><span data-stu-id="674b6-185">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="674b6-186">È possibile usare altri tipi di raccolta, ad esempio `List<Enrollment>` o `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="674b6-186">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="674b6-187">Se si specifica `ICollection<Enrollment>`, per impostazione predefinita Entity Framework Core crea una raccolta `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="674b6-187">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="674b6-188">Entità Enrollment (Iscrizione)</span><span class="sxs-lookup"><span data-stu-id="674b6-188">The Enrollment entity</span></span>

![Diagramma entità Enrollment (Iscrizione)](intro/_static/enrollment-entity.png)

<span data-ttu-id="674b6-190">Creare *Models/Enrollment.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="674b6-190">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="674b6-191">La proprietà `EnrollmentID` è la chiave primaria. Questa entità usa il modello `classnameID` anziché `ID` da solo.</span><span class="sxs-lookup"><span data-stu-id="674b6-191">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="674b6-192">Per un modello di dati di produzione, scegliere un modello e usarlo in modo coerente.</span><span class="sxs-lookup"><span data-stu-id="674b6-192">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="674b6-193">Questa esercitazione usa entrambi solo per illustrare che entrambi funzionano.</span><span class="sxs-lookup"><span data-stu-id="674b6-193">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="674b6-194">L'uso di `ID` senza `classname` rende più semplice l'implementazione di alcuni tipi di modifiche al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="674b6-194">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="674b6-195">La proprietà `Grade` è un oggetto`enum`.</span><span class="sxs-lookup"><span data-stu-id="674b6-195">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="674b6-196">Il punto interrogativo dopo la dichiarazione del tipo `Grade` indica che la proprietà `Grade` [ammette i valori Null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="674b6-196">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="674b6-197">Un voto con valore Null è diverso da un voto con valore zero. Null significa che un voto è sconosciuto o non è stato ancora assegnato.</span><span class="sxs-lookup"><span data-stu-id="674b6-197">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="674b6-198">La proprietà `StudentID` è una chiave esterna e la proprietà di navigazione corrispondente è `Student`.</span><span class="sxs-lookup"><span data-stu-id="674b6-198">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="674b6-199">Un'entità `Enrollment` è associata a un'entità `Student`, pertanto la proprietà contiene un'unica entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="674b6-199">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="674b6-200">La proprietà `CourseID` è una chiave esterna e la proprietà di navigazione corrispondente è `Course`.</span><span class="sxs-lookup"><span data-stu-id="674b6-200">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="674b6-201">Un'entità `Enrollment` è associata a un'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="674b6-201">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="674b6-202">Entity Framework Core interpreta una proprietà come chiave esterna se è denominata `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="674b6-202">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="674b6-203">Ad esempio, `StudentID` è la chiave esterna per la proprietà di navigazione `Student`, dato che la chiave primaria `Student` dell'entità è `ID`.</span><span class="sxs-lookup"><span data-stu-id="674b6-203">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="674b6-204">Le proprietà di chiave esterna possono anche essere denominate `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="674b6-204">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="674b6-205">Ad esempio, `CourseID` poiché la chiave primaria `Course` dell'entità è `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="674b6-205">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="674b6-206">Entità Course (Corso)</span><span class="sxs-lookup"><span data-stu-id="674b6-206">The Course entity</span></span>

![Diagramma entità Course (Corso)](intro/_static/course-entity.png)

<span data-ttu-id="674b6-208">Creare *Models/Course.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="674b6-208">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="674b6-209">La proprietà `Enrollments` rappresenta una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="674b6-209">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="674b6-210">È possibile correlare un'entità `Course` a un numero qualsiasi di entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="674b6-210">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="674b6-211">L'attributo `DatabaseGenerated` consente all'app di specificare la chiave primaria anziché chiedere al database di generarla.</span><span class="sxs-lookup"><span data-stu-id="674b6-211">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="674b6-212">Compilare il progetto per verificare che non siano presenti errori del compilatore.</span><span class="sxs-lookup"><span data-stu-id="674b6-212">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="674b6-213">Scaffolding delle pagine Student</span><span class="sxs-lookup"><span data-stu-id="674b6-213">Scaffold Student pages</span></span>

<span data-ttu-id="674b6-214">In questa sezione si userà lo strumento di scaffolding di ASP.NET Core per generare:</span><span class="sxs-lookup"><span data-stu-id="674b6-214">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="674b6-215">Una classe *contesto* di EF Core.</span><span class="sxs-lookup"><span data-stu-id="674b6-215">An EF Core *context* class.</span></span> <span data-ttu-id="674b6-216">Il contesto è la classe principale che coordina le funzionalità di Entity Framework per un determinato modello di dati.</span><span class="sxs-lookup"><span data-stu-id="674b6-216">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="674b6-217">Deriva dalla classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="674b6-217">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="674b6-218">Pagine Razor che gestiscono operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per l'entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="674b6-218">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="674b6-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="674b6-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="674b6-220">Creare una cartella *Students* nella cartella *Pages*.</span><span class="sxs-lookup"><span data-stu-id="674b6-220">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="674b6-221">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Pages/Students* e quindi scegliere **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="674b6-221">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="674b6-222">Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Pagine Razor che usano Entity Framework (CRUD)** > **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="674b6-222">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="674b6-223">Nella finestra di dialogo **Pagine Razor che usano Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="674b6-223">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="674b6-224">Nell'elenco a discesa **Classe modello** selezionare **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="674b6-224">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="674b6-225">Nella riga **Classe contesto di dati** selezionare il segno più **+** .</span><span class="sxs-lookup"><span data-stu-id="674b6-225">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="674b6-226">Modificare il nome del contesto di dati da *ContosoUniversity.Models.ContosoUniversityContext* a *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="674b6-226">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="674b6-227">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="674b6-227">Select **Add**.</span></span>

<span data-ttu-id="674b6-228">Vengono installati automaticamente i pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="674b6-228">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="674b6-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="674b6-229">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="674b6-230">Eseguire i comandi seguenti dell'interfaccia della riga di comando di .NET Core per installare i pacchetti NuGet necessari:</span><span class="sxs-lookup"><span data-stu-id="674b6-230">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="674b6-231">Per lo scaffolding è necessario il pacchetto Microsoft.VisualStudio.Web.CodeGeneration.Design.</span><span class="sxs-lookup"><span data-stu-id="674b6-231">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="674b6-232">Sebbene l'app non usi SQL Server, lo strumento di scaffolding richiede il pacchetto di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="674b6-232">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="674b6-233">Creare una cartella *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="674b6-233">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="674b6-234">Eseguire il comando seguente per installare lo [strumento di scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="674b6-234">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="674b6-235">Eseguire il comando seguente per eseguire lo scaffolding delle pagine Student.</span><span class="sxs-lookup"><span data-stu-id="674b6-235">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="674b6-236">**In Windows**</span><span class="sxs-lookup"><span data-stu-id="674b6-236">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="674b6-237">**In macOS o Linux**</span><span class="sxs-lookup"><span data-stu-id="674b6-237">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="674b6-238">Se si riscontra un problema con il passaggio precedente, compilare il progetto e riprovare a eseguire il passaggio di scaffolding.</span><span class="sxs-lookup"><span data-stu-id="674b6-238">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="674b6-239">Il processo di scaffolding:</span><span class="sxs-lookup"><span data-stu-id="674b6-239">The scaffolding process:</span></span>

* <span data-ttu-id="674b6-240">Crea pagine Razor nella cartella *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="674b6-240">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="674b6-241">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="674b6-241">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="674b6-242">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="674b6-242">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="674b6-243">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="674b6-243">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="674b6-244">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="674b6-244">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="674b6-245">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="674b6-245">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="674b6-246">Crea *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="674b6-246">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="674b6-247">Aggiunge il contesto all'inserimento delle dipendenze in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="674b6-247">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="674b6-248">Aggiunge la stringa di connessione al database ad *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="674b6-248">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="674b6-249">Stringa di connessione al database</span><span class="sxs-lookup"><span data-stu-id="674b6-249">Database connection string</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="674b6-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="674b6-250">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="674b6-251">La stringa di connessione specifica [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="674b6-251">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="674b6-252">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di app e non per la produzione.</span><span class="sxs-lookup"><span data-stu-id="674b6-252">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="674b6-253">Per impostazione predefinita, Local DB crea file con estensione *mdf* nella directory `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="674b6-253">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="674b6-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="674b6-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="674b6-255">Modificare la stringa di connessione in modo che punti a un file di database SQLite denominato *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="674b6-255">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="674b6-256">Aggiornare la classe del contesto di database</span><span class="sxs-lookup"><span data-stu-id="674b6-256">Update the database context class</span></span>

<span data-ttu-id="674b6-257">La classe principale che coordina le funzionalità di EF Core per un determinato modello di dati è la classe del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="674b6-257">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="674b6-258">Il contesto è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="674b6-258">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="674b6-259">Il contesto specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="674b6-259">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="674b6-260">In questo progetto la classe è denominata `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="674b6-260">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="674b6-261">Aggiornare *SchoolContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="674b6-261">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="674b6-262">Il codice evidenziato crea una proprietà [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per ogni set di entità.</span><span class="sxs-lookup"><span data-stu-id="674b6-262">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="674b6-263">Nella terminologia di Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="674b6-263">In EF Core terminology:</span></span>

* <span data-ttu-id="674b6-264">Un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="674b6-264">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="674b6-265">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="674b6-265">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="674b6-266">Dato che un set di entità contiene più entità, le proprietà DBSet devono essere nomi plurali.</span><span class="sxs-lookup"><span data-stu-id="674b6-266">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="674b6-267">Dato che lo strumento di scaffolding ha creato un DBSet `Student`, questo passaggio lo modifica nel plurale `Students`.</span><span class="sxs-lookup"><span data-stu-id="674b6-267">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="674b6-268">Per fare in modo che il codice Razor Pages riconosca il nuovo nome del DBSet, sostituire globalmente `_context.Student` con `_context.Students` nell'intero progetto.</span><span class="sxs-lookup"><span data-stu-id="674b6-268">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="674b6-269">Sono presenti 8 occorrenze.</span><span class="sxs-lookup"><span data-stu-id="674b6-269">There are 8 occurrences.</span></span>

<span data-ttu-id="674b6-270">Compilare il progetto per verificare che non siano presenti errori di compilazione.</span><span class="sxs-lookup"><span data-stu-id="674b6-270">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="674b6-271">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="674b6-271">Startup.cs</span></span>

<span data-ttu-id="674b6-272">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="674b6-272">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="674b6-273">I servizi, ad esempio il contesto di database di EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="674b6-273">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="674b6-274">Questi servizi vengono quindi offerti ai componenti per cui sono necessari (ad esempio Razor Pages) tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="674b6-274">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="674b6-275">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="674b6-275">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="674b6-276">Lo strumento di scaffolding ha registrato automaticamente la classe di contesto nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="674b6-276">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="674b6-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="674b6-277">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="674b6-278">In `ConfigureServices` le righe evidenziate sono state aggiunte dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="674b6-278">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="674b6-279">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="674b6-279">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="674b6-280">In `ConfigureServices` assicurarsi che il codice aggiunto dallo scaffolder chiami `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="674b6-280">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="674b6-281">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="674b6-281">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="674b6-282">Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="674b6-282">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="674b6-283">Creare il database</span><span class="sxs-lookup"><span data-stu-id="674b6-283">Create the database</span></span>

<span data-ttu-id="674b6-284">Aggiornare *Program.cs* per creare il database se non esiste:</span><span class="sxs-lookup"><span data-stu-id="674b6-284">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="674b6-285">Il metodo [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) non esegue alcuna azione se esiste un database per il contesto.</span><span class="sxs-lookup"><span data-stu-id="674b6-285">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="674b6-286">Se non esiste alcun database, vengono creati il database e lo schema.</span><span class="sxs-lookup"><span data-stu-id="674b6-286">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="674b6-287">`EnsureCreated` abilita il flusso di lavoro seguente per la gestione delle modifiche al modello di dati:</span><span class="sxs-lookup"><span data-stu-id="674b6-287">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="674b6-288">Eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="674b6-288">Delete the database.</span></span> <span data-ttu-id="674b6-289">Tutti i dati esistenti andranno perduti.</span><span class="sxs-lookup"><span data-stu-id="674b6-289">Any existing data is lost.</span></span>
* <span data-ttu-id="674b6-290">Modificare il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="674b6-290">Change the data model.</span></span> <span data-ttu-id="674b6-291">Ad esempio, aggiungere un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="674b6-291">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="674b6-292">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="674b6-292">Run the app.</span></span>
* <span data-ttu-id="674b6-293">`EnsureCreated` crea un database con il nuovo schema.</span><span class="sxs-lookup"><span data-stu-id="674b6-293">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="674b6-294">Questo flusso di lavoro funziona correttamente nelle fasi iniziali dello sviluppo quando lo schema è in rapida evoluzione, purché non sia necessario conservare i dati.</span><span class="sxs-lookup"><span data-stu-id="674b6-294">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="674b6-295">La situazione è diversa quando è necessario mantenere i dati immessi nel database.</span><span class="sxs-lookup"><span data-stu-id="674b6-295">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="674b6-296">In tal caso, usare le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="674b6-296">When that is the case, use migrations.</span></span>

<span data-ttu-id="674b6-297">Più avanti nella serie di esercitazioni si vedrà come eliminare il database creato da `EnsureCreated` e usare invece le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="674b6-297">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="674b6-298">Non è possibile aggiornare un database creato da `EnsureCreated` usando le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="674b6-298">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="674b6-299">Eseguire il test dell'app</span><span class="sxs-lookup"><span data-stu-id="674b6-299">Test the app</span></span>

* <span data-ttu-id="674b6-300">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="674b6-300">Run the app.</span></span>
* <span data-ttu-id="674b6-301">Selezionare il collegamento **Students** (Studenti) e quindi **Crea nuovo**.</span><span class="sxs-lookup"><span data-stu-id="674b6-301">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="674b6-302">Eseguire il test dei collegamenti Edit (Modifica), Details (Dettagli) e Delete (Elimina).</span><span class="sxs-lookup"><span data-stu-id="674b6-302">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="674b6-303">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="674b6-303">Seed the database</span></span>

<span data-ttu-id="674b6-304">Il metodo `EnsureCreated` crea un database vuoto.</span><span class="sxs-lookup"><span data-stu-id="674b6-304">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="674b6-305">In questa sezione viene aggiunto il codice che popola il database con dati di test.</span><span class="sxs-lookup"><span data-stu-id="674b6-305">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="674b6-306">Creare *Data/DbInitializer.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="674b6-306">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="674b6-307">Il codice controlla se esistono studenti nel database.</span><span class="sxs-lookup"><span data-stu-id="674b6-307">The code checks if there are any students in the database.</span></span> <span data-ttu-id="674b6-308">Se non sono presenti studenti, aggiunge i dati di test al database.</span><span class="sxs-lookup"><span data-stu-id="674b6-308">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="674b6-309">I dati di test vengono creati in matrici anziché in raccolte `List<T>` per ottimizzare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="674b6-309">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="674b6-310">In *Program.cs* sostituire la chiamata di `EnsureCreated` con una chiamata di `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="674b6-310">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ````

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="674b6-311">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="674b6-311">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="674b6-312">Arrestare l'app se è in esecuzione ed eseguire il comando seguente nella **console di Gestione pacchetti**:</span><span class="sxs-lookup"><span data-stu-id="674b6-312">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="674b6-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="674b6-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="674b6-314">Arrestare l'app se è in esecuzione ed eliminare il file *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="674b6-314">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="674b6-315">Riavviare l'app.</span><span class="sxs-lookup"><span data-stu-id="674b6-315">Restart the app.</span></span>

* <span data-ttu-id="674b6-316">Selezionare la pagina Students per visualizzare i dati di seeding.</span><span class="sxs-lookup"><span data-stu-id="674b6-316">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="674b6-317">Visualizzare il database</span><span class="sxs-lookup"><span data-stu-id="674b6-317">View the database</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="674b6-318">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="674b6-318">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="674b6-319">Aprire **Esplora oggetti di SQL Server** dal menu **Visualizza** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="674b6-319">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="674b6-320">In Esplora oggetti di SQL Server selezionare **(localdb)\MSSQLLocalDB > Database > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="674b6-320">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="674b6-321">Il nome del database viene generato dal nome del contesto specificato in precedenza con l'aggiunta di un trattino e un GUID.</span><span class="sxs-lookup"><span data-stu-id="674b6-321">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="674b6-322">Espandere il nodo **Tabelle**.</span><span class="sxs-lookup"><span data-stu-id="674b6-322">Expand the **Tables** node.</span></span>
* <span data-ttu-id="674b6-323">Fare clic con il pulsante destro del mouse sulla tabella **Student** (Studente) e fare clic su **Visualizza dati** per visualizzare le colonne create e le righe inserite nella tabella.</span><span class="sxs-lookup"><span data-stu-id="674b6-323">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="674b6-324">Fare clic con il pulsante destro del mouse sulla tabella **Student** e scegliere **Visualizza codice** per vedere il mapping tra il modello `Student` e lo schema della tabella `Student`.</span><span class="sxs-lookup"><span data-stu-id="674b6-324">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="674b6-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="674b6-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="674b6-326">Usare lo strumento SQLite per visualizzare lo schema del database e i dati di seeding.</span><span class="sxs-lookup"><span data-stu-id="674b6-326">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="674b6-327">Il file di database è denominato*CU.db* e si trova nella cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="674b6-327">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="674b6-328">Codice asincrono</span><span class="sxs-lookup"><span data-stu-id="674b6-328">Asynchronous code</span></span>

<span data-ttu-id="674b6-329">La programmazione asincrona è la modalità predefinita per ASP.NET Core ed Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="674b6-329">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="674b6-330">Per un server Web è disponibile un numero limitato di thread e in situazioni di carico elevato tutti i thread disponibili potrebbero essere in uso.</span><span class="sxs-lookup"><span data-stu-id="674b6-330">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="674b6-331">In queste circostanze il server non può elaborare nuove richieste finché i thread non saranno liberi.</span><span class="sxs-lookup"><span data-stu-id="674b6-331">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="674b6-332">Con il codice sincrono, può succedere che molti thread siano vincolati nonostante in quel momento non stiano eseguendo alcuna operazione. Rimangono tuttavia in attesa che l'operazione I/O sia completata.</span><span class="sxs-lookup"><span data-stu-id="674b6-332">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="674b6-333">Con il codice asincrono, se un processo è in attesa del completamento dell'operazione I/O, il thread viene liberato e il server lo può usare per l'elaborazione di altre richieste.</span><span class="sxs-lookup"><span data-stu-id="674b6-333">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="674b6-334">Di conseguenza, il codice asincrono consente un uso più efficiente delle risorse del server e il server può gestire una maggiore quantità di traffico senza ritardi.</span><span class="sxs-lookup"><span data-stu-id="674b6-334">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="674b6-335">Il codice asincrono comporta un minimo sovraccarico in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="674b6-335">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="674b6-336">In caso di traffico ridotto, il calo delle prestazioni è irrilevante, mentre nelle situazioni di traffico elevato, è essenziale ottimizzare le prestazioni potenziali.</span><span class="sxs-lookup"><span data-stu-id="674b6-336">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="674b6-337">Nel codice seguente la parola chiave [async](/dotnet/csharp/language-reference/keywords/async), il valore restituito `Task<T>`, la parola chiave `await` e il metodo `ToListAsync` consentono di eseguire il codice in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="674b6-337">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="674b6-338">La parola chiave `async` indica al compilatore di eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="674b6-338">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="674b6-339">Generare callback per parti del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="674b6-339">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="674b6-340">Creare l'oggetto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) restituito.</span><span class="sxs-lookup"><span data-stu-id="674b6-340">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="674b6-341">Il tipo restituito `Task<T>` rappresenta il lavoro in corso.</span><span class="sxs-lookup"><span data-stu-id="674b6-341">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="674b6-342">La parola chiave `await` indica al compilatore di suddividere il metodo in due parti.</span><span class="sxs-lookup"><span data-stu-id="674b6-342">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="674b6-343">La prima parte termina con l'operazione avviata in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="674b6-343">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="674b6-344">La seconda parte viene inserita in un metodo di callback che viene chiamato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="674b6-344">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="674b6-345">`ToListAsync` è la versione asincrona del metodo di estensione `ToList`.</span><span class="sxs-lookup"><span data-stu-id="674b6-345">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="674b6-346">Alcuni aspetti da considerare quando si scrive codice asincrono usato da Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="674b6-346">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="674b6-347">Solo le istruzioni che generano query o comandi da inviare al database vengono eseguite in modo asincrono,</span><span class="sxs-lookup"><span data-stu-id="674b6-347">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="674b6-348">Sono incluse `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="674b6-348">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="674b6-349">Non sono incluse le istruzioni che modificano solo un oggetto `IQueryable`, ad esempio `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="674b6-349">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="674b6-350">Un contesto di Entity Framework Core non è thread-safe. Non tentare di eseguire più operazioni parallelamente.</span><span class="sxs-lookup"><span data-stu-id="674b6-350">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="674b6-351">Per sfruttare i vantaggi del codice asincrono in termini di prestazioni, verificare che i pacchetti della libreria impiegati, ad esempio per il paging, usino la modalità asincrona per chiamare i metodi di EF Core che inviano query al database.</span><span class="sxs-lookup"><span data-stu-id="674b6-351">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="674b6-352">Per altre informazioni sulla programmazione asincrona in .NET, vedere [Panoramica della programmazione asincrona](/dotnet/standard/async) e [Programmazione asincrona con async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="674b6-352">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="674b6-353">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="674b6-353">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="674b6-354">Esercitazione successiva</span><span class="sxs-lookup"><span data-stu-id="674b6-354">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="674b6-355">L'app Web di esempio di Contoso University illustra come creare un'app ASP.NET Core di Razor Pages con Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="674b6-355">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="674b6-356">L'app di esempio è un sito Web per una fittizia Contoso University.</span><span class="sxs-lookup"><span data-stu-id="674b6-356">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="674b6-357">Include funzionalità, come ad esempio l'ammissione di studenti, la creazione di corsi e le assegnazioni di insegnati.</span><span class="sxs-lookup"><span data-stu-id="674b6-357">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="674b6-358">Questa pagina è il prima di una serie di esercitazioni che illustrano come compilare l'app di esempio di Contoso University.</span><span class="sxs-lookup"><span data-stu-id="674b6-358">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="674b6-359">Scaricare o visualizzare l'app completata.</span><span class="sxs-lookup"><span data-stu-id="674b6-359">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="674b6-360">[Istruzioni per il download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="674b6-360">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="674b6-361">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="674b6-361">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="674b6-362">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="674b6-362">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="674b6-363">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="674b6-363">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="674b6-364">Conoscenza di [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="674b6-364">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="674b6-365">Prima di iniziare questa serie, i programmatori non esperti dovranno completare l'[introduzione a Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="674b6-365">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="674b6-366">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="674b6-366">Troubleshooting</span></span>

<span data-ttu-id="674b6-367">Se si verifica un problema che non si sa come risolvere, è generalmente possibile trovare la soluzione confrontando il codice con il [progetto completato](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="674b6-367">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="674b6-368">Un buon metodo per ottenere assistenza è quello di pubblicare una domanda in [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) per [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="674b6-368">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="674b6-369">App Web di Contoso University</span><span class="sxs-lookup"><span data-stu-id="674b6-369">The Contoso University web app</span></span>

<span data-ttu-id="674b6-370">L'applicazione compilata in queste esercitazioni è il sito Web di base di un'università.</span><span class="sxs-lookup"><span data-stu-id="674b6-370">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="674b6-371">Gli utenti possono visualizzare e aggiornare le informazioni che riguardano studenti, corsi e insegnanti.</span><span class="sxs-lookup"><span data-stu-id="674b6-371">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="674b6-372">Di seguito sono disponibili alcune schermate dell'esercitazione.</span><span class="sxs-lookup"><span data-stu-id="674b6-372">Here are a few of the screens created in the tutorial.</span></span>

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index.png)

![Pagina Students Edit (Modifica studenti)](intro/_static/student-edit.png)

<span data-ttu-id="674b6-375">Lo stile dell'interfaccia utente del sito è simile a quanto è stato generato tramite i modelli predefiniti.</span><span class="sxs-lookup"><span data-stu-id="674b6-375">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="674b6-376">L'esercitazione è incentrata su Entity Framework Core con Razor Pages e non sull'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="674b6-376">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="674b6-377">Creare l'app Web di Razor Pages ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="674b6-377">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="674b6-378">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="674b6-378">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="674b6-379">Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="674b6-379">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="674b6-380">Creare una nuova applicazione Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="674b6-380">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="674b6-381">Denominare il progetto **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="674b6-381">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="674b6-382">È importante denominare il progetto *ContosoUniversity* in modo che gli spazi dei nomi corrispondano quando il codice viene copiato/incollato.</span><span class="sxs-lookup"><span data-stu-id="674b6-382">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="674b6-383">Selezionare **ASP.NET Core 2.1** nell'elenco a discesa, quindi selezionare **Applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="674b6-383">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="674b6-384">Per le immagini dei passaggi precedenti, vedere [Creare un'app Web Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="674b6-384">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="674b6-385">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="674b6-385">Run the app.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="674b6-386">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="674b6-386">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="674b6-387">Impostare lo stile del sito</span><span class="sxs-lookup"><span data-stu-id="674b6-387">Set up the site style</span></span>

<span data-ttu-id="674b6-388">Con alcune modifiche è possibile impostare il menu del sito, il layout e la home page.</span><span class="sxs-lookup"><span data-stu-id="674b6-388">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="674b6-389">Aggiornare *Pages/Shared/_Layout.cshtml* con le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="674b6-389">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="674b6-390">Modificare tutte le occorrenze di "ContosoUniversity" in "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="674b6-390">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="674b6-391">Le occorrenze sono tre.</span><span class="sxs-lookup"><span data-stu-id="674b6-391">There are three occurrences.</span></span>

* <span data-ttu-id="674b6-392">Aggiungere le voci di menu per **Students** (Studenti), **Courses** (Corsi), **Instructors** (Insegnanti) e **Departments** (Dipartimenti) ed eliminare la voce di menu **Contact** (Contatto).</span><span class="sxs-lookup"><span data-stu-id="674b6-392">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="674b6-393">Le modifiche vengono evidenziate.</span><span class="sxs-lookup"><span data-stu-id="674b6-393">The changes are highlighted.</span></span> <span data-ttu-id="674b6-394">*Non* viene visualizzato l'intero markup.</span><span class="sxs-lookup"><span data-stu-id="674b6-394">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="674b6-395">In *Pages/Index.cshtml* sostituire il contenuto del file con il codice seguente. In questo modo il testo su ASP.NET e MVC sarà sostituito dal testo relativo a questa app:</span><span class="sxs-lookup"><span data-stu-id="674b6-395">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="674b6-396">Creare il modello di dati</span><span class="sxs-lookup"><span data-stu-id="674b6-396">Create the data model</span></span>

<span data-ttu-id="674b6-397">Creare le classi delle entità per l'app di Contoso University.</span><span class="sxs-lookup"><span data-stu-id="674b6-397">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="674b6-398">Iniziare con le tre entità seguenti:</span><span class="sxs-lookup"><span data-stu-id="674b6-398">Start with the following three entities:</span></span>

![Diagramma modello di dati Course/Enrollment/Student (Corso/Iscrizione/Studente)](intro/_static/data-model-diagram.png)

<span data-ttu-id="674b6-400">Esiste una relazione uno-a-molti tra le entità `Student` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="674b6-400">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="674b6-401">Esiste una relazione uno-a-molti tra le entità `Course` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="674b6-401">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="674b6-402">Uno studente può iscriversi a un numero qualsiasi di corsi.</span><span class="sxs-lookup"><span data-stu-id="674b6-402">A student can enroll in any number of courses.</span></span> <span data-ttu-id="674b6-403">Un corso può avere un numero qualsiasi di studenti iscritti.</span><span class="sxs-lookup"><span data-stu-id="674b6-403">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="674b6-404">Nelle sezioni seguenti viene creata una classe per ognuna di queste entità.</span><span class="sxs-lookup"><span data-stu-id="674b6-404">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="674b6-405">Entità Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="674b6-405">The Student entity</span></span>

![Diagramma entità Student (Studente)](intro/_static/student-entity.png)

<span data-ttu-id="674b6-407">Creare una cartella *Models* (Modelli).</span><span class="sxs-lookup"><span data-stu-id="674b6-407">Create a *Models* folder.</span></span> <span data-ttu-id="674b6-408">Nella cartella *Models* (Modelli) creare un file di classe denominato *Student.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="674b6-408">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="674b6-409">La proprietà `ID` diventa la colonna di chiave primaria della tabella di database (DB) che corrisponde a questa classe.</span><span class="sxs-lookup"><span data-stu-id="674b6-409">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="674b6-410">Per impostazione predefinita, Entity Framework Core interpreta una proprietà denominata `ID` o `classnameID` come chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="674b6-410">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="674b6-411">In `classnameID` `classname` è il nome della classe.</span><span class="sxs-lookup"><span data-stu-id="674b6-411">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="674b6-412">La chiave primaria alternativa riconosciuta automaticamente è `StudentID` nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="674b6-412">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="674b6-413">La proprietà `Enrollments` rappresenta una [proprietà di navigazione](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="674b6-413">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="674b6-414">Le proprietà di navigazione si collegano ad altre entità correlate a questa entità.</span><span class="sxs-lookup"><span data-stu-id="674b6-414">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="674b6-415">In questo caso la proprietà `Enrollments` di `Student entity` contiene tutte le entità `Enrollment` correlate a `Student`.</span><span class="sxs-lookup"><span data-stu-id="674b6-415">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="674b6-416">Ad esempio, se una riga Student (Studente) nella database presenta due righe Enrollment (Iscrizione) correlate, la proprietà di navigazione `Enrollments` contiene questi due entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="674b6-416">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="674b6-417">Una riga `Enrollment` correlata è una riga che contiene il valore della chiave primaria dello studente nella colonna `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="674b6-417">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="674b6-418">Si supponga ad esempio che per lo studente con ID = 1 siano presenti due righe nella tabella `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="674b6-418">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="674b6-419">La tabella `Enrollment` contiene due righe con `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="674b6-419">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="674b6-420">`StudentID` è una chiave esterna nella tabella `Enrollment` che specifica lo studente nella tabella `Student`.</span><span class="sxs-lookup"><span data-stu-id="674b6-420">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="674b6-421">Se una proprietà di navigazione può contenere più entità, deve essere un tipo elenco, ad esempio `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="674b6-421">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="674b6-422">È possibile specificare `ICollection<T>` o un tipo, ad esempio `List<T>` o `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="674b6-422">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="674b6-423">Se si specifica `ICollection<T>`, per impostazione predefinita Entity Framework Core crea una raccolta `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="674b6-423">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="674b6-424">Le proprietà di navigazione che contengono più entità provengono da relazioni molti-a-molti e uno-a-molti.</span><span class="sxs-lookup"><span data-stu-id="674b6-424">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="674b6-425">Entità Enrollment (Iscrizione)</span><span class="sxs-lookup"><span data-stu-id="674b6-425">The Enrollment entity</span></span>

![Diagramma entità Enrollment (Iscrizione)](intro/_static/enrollment-entity.png)

<span data-ttu-id="674b6-427">Nella cartella *Models* (Modelli) creare un file *Enrollment.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="674b6-427">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="674b6-428">La proprietà `EnrollmentID` è la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="674b6-428">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="674b6-429">Questa entità usa il criterio `classnameID` anziché `ID` come l'entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="674b6-429">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="674b6-430">In genere gli sviluppatori scelgono un criterio che usano poi in tutto il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="674b6-430">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="674b6-431">In un'esercitazione successiva viene illustrato l'uso di ID senza classname. In questo modo si semplifica l'implementazione dell'ereditarietà nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="674b6-431">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="674b6-432">La proprietà `Grade` è un oggetto`enum`.</span><span class="sxs-lookup"><span data-stu-id="674b6-432">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="674b6-433">Il punto interrogativo dopo la dichiarazione del tipo `Grade` indica che la proprietà `Grade` ammette i valori Null.</span><span class="sxs-lookup"><span data-stu-id="674b6-433">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="674b6-434">Un voto il cui valore è Null è diverso da un voto con valore zero. Null significa che un voto è sconosciuto o non è stato ancora assegnato.</span><span class="sxs-lookup"><span data-stu-id="674b6-434">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="674b6-435">La proprietà `StudentID` è una chiave esterna e la proprietà di navigazione corrispondente è `Student`.</span><span class="sxs-lookup"><span data-stu-id="674b6-435">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="674b6-436">Un'entità `Enrollment` è associata a un'entità `Student`, pertanto la proprietà contiene un'unica entità `Student`.</span><span class="sxs-lookup"><span data-stu-id="674b6-436">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="674b6-437">L'entità `Student` si differenzia dalla proprietà di navigazione `Student.Enrollments` che contiene più entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="674b6-437">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="674b6-438">La proprietà `CourseID` è una chiave esterna e la proprietà di navigazione corrispondente è `Course`.</span><span class="sxs-lookup"><span data-stu-id="674b6-438">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="674b6-439">Un'entità `Enrollment` è associata a un'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="674b6-439">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="674b6-440">Entity Framework Core interpreta una proprietà come chiave esterna se è denominata `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="674b6-440">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="674b6-441">Ad esempio, `StudentID` per la proprietà di navigazione `Student`, poiché la chiave primaria `Student` dell'entità è `ID`.</span><span class="sxs-lookup"><span data-stu-id="674b6-441">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="674b6-442">Le proprietà di chiave esterna possono anche essere denominate `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="674b6-442">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="674b6-443">Ad esempio, `CourseID` poiché la chiave primaria `Course` dell'entità è `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="674b6-443">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="674b6-444">Entità Course (Corso)</span><span class="sxs-lookup"><span data-stu-id="674b6-444">The Course entity</span></span>

![Diagramma entità Course (Corso)](intro/_static/course-entity.png)

<span data-ttu-id="674b6-446">Nella cartella *Models* (Modelli) creare un file *Course.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="674b6-446">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="674b6-447">La proprietà `Enrollments` rappresenta una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="674b6-447">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="674b6-448">È possibile correlare un'entità `Course` a un numero qualsiasi di entità `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="674b6-448">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="674b6-449">L'attributo `DatabaseGenerated` consente all'app di specificare la chiave primaria anziché chiedere al database di generarla.</span><span class="sxs-lookup"><span data-stu-id="674b6-449">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="674b6-450">Eseguire lo scaffolding del modello Student (Studente)</span><span class="sxs-lookup"><span data-stu-id="674b6-450">Scaffold the student model</span></span>

<span data-ttu-id="674b6-451">In questa sezione viene eseguito lo scaffolding del modello Student (Studente).</span><span class="sxs-lookup"><span data-stu-id="674b6-451">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="674b6-452">Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello Student (Studente).</span><span class="sxs-lookup"><span data-stu-id="674b6-452">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="674b6-453">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="674b6-453">Build the project.</span></span>
* <span data-ttu-id="674b6-454">Creare la cartella *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="674b6-454">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="674b6-455">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="674b6-455">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="674b6-456">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Pages/Students* > **Aggiungi** > **Nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="674b6-456">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="674b6-457">Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Pagine Razor che usano Entity Framework (CRUD)** > **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="674b6-457">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="674b6-458">Completare la finestra di dialogo **Pagine Razor che usano Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="674b6-458">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="674b6-459">Nell'elenco a discesa **Classe modello** selezionare **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="674b6-459">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="674b6-460">Nella riga **Classe contesto di dati** selezionare il segno **+** (più) e modificare il nome generato in **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="674b6-460">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="674b6-461">Nell'elenco a discesa **Classe contesto di dati** selezionare **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="674b6-461">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="674b6-462">Selezionare **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="674b6-462">Select **Add**.</span></span>

![Finestra di dialogo CRUD](intro/_static/s1.png)

<span data-ttu-id="674b6-464">Vedere [Eseguire lo scaffolding del modello di filmato](xref:tutorials/razor-pages/model#scaffold-the-movie-model) se si riscontrano problemi con il passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="674b6-464">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="674b6-465">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="674b6-465">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="674b6-466">Eseguire i comandi seguenti per eseguire lo scaffolding del modello Student (Studente).</span><span class="sxs-lookup"><span data-stu-id="674b6-466">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="674b6-467">Il processo di scaffolding ha creato e modificato i file seguenti:</span><span class="sxs-lookup"><span data-stu-id="674b6-467">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="674b6-468">File creati</span><span class="sxs-lookup"><span data-stu-id="674b6-468">Files created</span></span>

* <span data-ttu-id="674b6-469">Pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice) di *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="674b6-469">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="674b6-470">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="674b6-470">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="674b6-471">Aggiornamenti dei file</span><span class="sxs-lookup"><span data-stu-id="674b6-471">File updates</span></span>

* <span data-ttu-id="674b6-472">*Startup.cs* : le modifiche a questo file sono descritte in dettaglio nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="674b6-472">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="674b6-473">*appsettings.json* : è stata aggiunta la stringa di connessione usata per connettersi a un database locale.</span><span class="sxs-lookup"><span data-stu-id="674b6-473">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="674b6-474">Esaminare il contesto registrato con l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="674b6-474">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="674b6-475">ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="674b6-475">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="674b6-476">I servizi, ad esempio il contesto di database di Entity Framework Core, vengono registrati tramite dependency injection durante l'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="674b6-476">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="674b6-477">Questi servizi vengono quindi offerti ai componenti per cui sono necessari (ad esempio Razor Pages) tramite i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="674b6-477">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="674b6-478">Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="674b6-478">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="674b6-479">Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato con il contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="674b6-479">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="674b6-480">Esaminare il metodo `ConfigureServices` in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="674b6-480">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="674b6-481">La riga evidenziata è stata aggiunta dallo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="674b6-481">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="674b6-482">Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="674b6-482">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="674b6-483">Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="674b6-483">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="674b6-484">Aggiornare il metodo Main</span><span class="sxs-lookup"><span data-stu-id="674b6-484">Update main</span></span>

<span data-ttu-id="674b6-485">In *Program.cs* modificare il metodo `Main` per eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="674b6-485">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="674b6-486">Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="674b6-486">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="674b6-487">Chiamare [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="674b6-487">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="674b6-488">Eliminare il contesto dopo che il metodo `EnsureCreated` è stato completato.</span><span class="sxs-lookup"><span data-stu-id="674b6-488">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="674b6-489">Il codice seguente illustra il file *Program.cs* aggiornato.</span><span class="sxs-lookup"><span data-stu-id="674b6-489">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="674b6-490">`EnsureCreated` assicura che esista il database per il contesto.</span><span class="sxs-lookup"><span data-stu-id="674b6-490">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="674b6-491">Se esiste, non viene eseguita alcuna azione.</span><span class="sxs-lookup"><span data-stu-id="674b6-491">If it exists, no action is taken.</span></span> <span data-ttu-id="674b6-492">Se non esiste, vengono creati il database e tutti i relativi schemi.</span><span class="sxs-lookup"><span data-stu-id="674b6-492">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="674b6-493">`EnsureCreated` non usa le migrazioni per creare il database.</span><span class="sxs-lookup"><span data-stu-id="674b6-493">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="674b6-494">Un database creato con `EnsureCreated` non potrà essere aggiornato successivamente usando le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="674b6-494">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="674b6-495">`EnsureCreated` viene chiamato all'avvio dell'app e consente il flusso di lavoro seguente:</span><span class="sxs-lookup"><span data-stu-id="674b6-495">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="674b6-496">Eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="674b6-496">Delete the DB.</span></span>
* <span data-ttu-id="674b6-497">Modificare lo schema di database, ad esempio, aggiungere un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="674b6-497">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="674b6-498">Eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="674b6-498">Run the app.</span></span>
* <span data-ttu-id="674b6-499">`EnsureCreated` crea un database con la colonna `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="674b6-499">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="674b6-500">`EnsureCreated` è utile nelle prime fasi di sviluppo quando lo schema è in rapida evoluzione.</span><span class="sxs-lookup"><span data-stu-id="674b6-500">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="674b6-501">Più avanti nell'esercitazione il database verrà eliminato e si useranno le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="674b6-501">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="674b6-502">Eseguire il test dell'app</span><span class="sxs-lookup"><span data-stu-id="674b6-502">Test the app</span></span>

<span data-ttu-id="674b6-503">Eseguire l'app e accettare l'informativa sui cookie.</span><span class="sxs-lookup"><span data-stu-id="674b6-503">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="674b6-504">Questa app non memorizza informazioni personali.</span><span class="sxs-lookup"><span data-stu-id="674b6-504">This app doesn't keep personal information.</span></span> <span data-ttu-id="674b6-505">È possibile consultare l'informativa sui cookie in [Supporto per il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="674b6-505">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="674b6-506">Selezionare il collegamento **Students** (Studenti) e quindi **Crea nuovo**.</span><span class="sxs-lookup"><span data-stu-id="674b6-506">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="674b6-507">Eseguire il test dei collegamenti Edit (Modifica), Details (Dettagli) e Delete (Elimina).</span><span class="sxs-lookup"><span data-stu-id="674b6-507">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="674b6-508">Esaminare il contesto di database SchoolContext</span><span class="sxs-lookup"><span data-stu-id="674b6-508">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="674b6-509">La classe principale che coordina le funzionalità di Entity Framework Core per un determinato modello di dati è la classe del contesto di database.</span><span class="sxs-lookup"><span data-stu-id="674b6-509">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="674b6-510">Il contesto dei dati è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="674b6-510">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="674b6-511">Il contesto dei dati specifica le entità incluse nel modello di dati.</span><span class="sxs-lookup"><span data-stu-id="674b6-511">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="674b6-512">In questo progetto la classe è denominata `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="674b6-512">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="674b6-513">Aggiornare *SchoolContext.cs* con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="674b6-513">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="674b6-514">Il codice evidenziato crea una proprietà [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per ogni set di entità.</span><span class="sxs-lookup"><span data-stu-id="674b6-514">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="674b6-515">Nella terminologia di Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="674b6-515">In EF Core terminology:</span></span>

* <span data-ttu-id="674b6-516">Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.</span><span class="sxs-lookup"><span data-stu-id="674b6-516">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="674b6-517">Un'entità corrisponde a una riga nella tabella.</span><span class="sxs-lookup"><span data-stu-id="674b6-517">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="674b6-518">`DbSet<Enrollment>` e `DbSet<Course>` potrebbero essere omessi.</span><span class="sxs-lookup"><span data-stu-id="674b6-518">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="674b6-519">Core Entity Framework le include in modo implicito perché l'entità `Student` fa riferimento all'entità `Enrollment` e l'entità `Enrollment` fa riferimento all'entità `Course`.</span><span class="sxs-lookup"><span data-stu-id="674b6-519">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="674b6-520">Per questa esercitazione, mantenere `DbSet<Enrollment>` e `DbSet<Course>` in `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="674b6-520">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="674b6-521">LocalDB di SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="674b6-521">SQL Server Express LocalDB</span></span>

<span data-ttu-id="674b6-522">La stringa di connessione specifica [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="674b6-522">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="674b6-523">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di app e non per la produzione.</span><span class="sxs-lookup"><span data-stu-id="674b6-523">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="674b6-524">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="674b6-524">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="674b6-525">Per impostazione predefinita, Local DB crea file di database con estensione *mdf* nella directory `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="674b6-525">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="674b6-526">Aggiungere il codice per inizializzare il database con dati di test</span><span class="sxs-lookup"><span data-stu-id="674b6-526">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="674b6-527">Entity Framework Core crea un database vuoto.</span><span class="sxs-lookup"><span data-stu-id="674b6-527">EF Core creates an empty DB.</span></span> <span data-ttu-id="674b6-528">In questa sezione viene scritto un metodo `Initialize` per popolare il database con i dati di test.</span><span class="sxs-lookup"><span data-stu-id="674b6-528">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="674b6-529">Nella cartella *Data* (Dati) creare un nuovo file di classe denominato *DbInitializer.cs* e aggiungere il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="674b6-529">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="674b6-530">Nota: Il codice precedente usa `Models` per lo spazio dei nomi (`namespace ContosoUniversity.Models`) invece di `Data`.</span><span class="sxs-lookup"><span data-stu-id="674b6-530">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="674b6-531">`Models` è coerente con il codice generato dallo scaffolder.</span><span class="sxs-lookup"><span data-stu-id="674b6-531">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="674b6-532">Per altre informazioni, vedere [questo problema relativo allo scaffolding in GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="674b6-532">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="674b6-533">Il codice controlla se esistono studenti nel database.</span><span class="sxs-lookup"><span data-stu-id="674b6-533">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="674b6-534">Se il database non contiene studenti, il database viene inizializzato con i dati di test.</span><span class="sxs-lookup"><span data-stu-id="674b6-534">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="674b6-535">I dati di test vengono caricati in matrici anziché in raccolte `List<T>` per ottimizzare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="674b6-535">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="674b6-536">Il metodo `EnsureCreated` crea automaticamente il database per il contesto di database.</span><span class="sxs-lookup"><span data-stu-id="674b6-536">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="674b6-537">Se il database esiste, `EnsureCreated` restituisce senza modificare il database.</span><span class="sxs-lookup"><span data-stu-id="674b6-537">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="674b6-538">In *Program.cs* modificare il metodo `Main` per chiamare `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="674b6-538">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="674b6-539">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="674b6-539">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="674b6-540">Arrestare l'app se è in esecuzione ed eseguire il comando seguente nella **console di Gestione pacchetti**:</span><span class="sxs-lookup"><span data-stu-id="674b6-540">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="674b6-541">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="674b6-541">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="674b6-542">Arrestare l'app se è in esecuzione ed eliminare il file *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="674b6-542">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="674b6-543">Visualizzare il database</span><span class="sxs-lookup"><span data-stu-id="674b6-543">View the DB</span></span>

<span data-ttu-id="674b6-544">Il nome del database viene generato dal nome del contesto specificato in precedenza con l'aggiunta di un trattino e un GUID.</span><span class="sxs-lookup"><span data-stu-id="674b6-544">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="674b6-545">Di conseguenza, il nome del database sarà "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="674b6-545">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="674b6-546">Il GUID sarà diverso per ogni utente.</span><span class="sxs-lookup"><span data-stu-id="674b6-546">The GUID will be different for each user.</span></span>
<span data-ttu-id="674b6-547">Aprire **Esplora oggetti di SQL Server** dal menu **Visualizza** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="674b6-547">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="674b6-548">In Esplora oggetti di SQL Server fare clic su **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="674b6-548">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="674b6-549">Espandere il nodo **Tabelle**.</span><span class="sxs-lookup"><span data-stu-id="674b6-549">Expand the **Tables** node.</span></span>

<span data-ttu-id="674b6-550">Fare clic con il pulsante destro del mouse sulla tabella **Student** (Studente) e fare clic su **Visualizza dati** per visualizzare le colonne create e le righe inserite nella tabella.</span><span class="sxs-lookup"><span data-stu-id="674b6-550">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="674b6-551">Codice asincrono</span><span class="sxs-lookup"><span data-stu-id="674b6-551">Asynchronous code</span></span>

<span data-ttu-id="674b6-552">La programmazione asincrona è la modalità predefinita per ASP.NET Core ed Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="674b6-552">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="674b6-553">Per un server Web è disponibile un numero limitato di thread e in situazioni di carico elevato tutti i thread disponibili potrebbero essere in uso.</span><span class="sxs-lookup"><span data-stu-id="674b6-553">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="674b6-554">In queste circostanze il server non può elaborare nuove richieste finché i thread non saranno liberi.</span><span class="sxs-lookup"><span data-stu-id="674b6-554">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="674b6-555">Con il codice sincrono, può succedere che molti thread siano vincolati nonostante in quel momento non stiano eseguendo alcuna operazione. Rimangono tuttavia in attesa che l'operazione I/O sia completata.</span><span class="sxs-lookup"><span data-stu-id="674b6-555">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="674b6-556">Con il codice asincrono, se un processo è in attesa del completamento dell'operazione I/O, il thread viene liberato e il server lo può usare per l'elaborazione di altre richieste.</span><span class="sxs-lookup"><span data-stu-id="674b6-556">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="674b6-557">Di conseguenza, il codice asincrono consente un uso più efficiente delle risorse e il server è abilitato per gestire una maggiore quantità di traffico senza ritardi.</span><span class="sxs-lookup"><span data-stu-id="674b6-557">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="674b6-558">Il codice asincrono comporta un minimo sovraccarico in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="674b6-558">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="674b6-559">In caso di traffico ridotto, il calo delle prestazioni è irrilevante, mentre nelle situazioni di traffico elevato, è essenziale ottimizzare le prestazioni potenziali.</span><span class="sxs-lookup"><span data-stu-id="674b6-559">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="674b6-560">Nel codice seguente la parola chiave [async](/dotnet/csharp/language-reference/keywords/async), il valore restituito `Task<T>`, la parola chiave `await` e il metodo `ToListAsync` consentono di eseguire il codice in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="674b6-560">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="674b6-561">La parola chiave `async` indica al compilatore di eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="674b6-561">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="674b6-562">Generare callback per parti del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="674b6-562">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="674b6-563">Creare automaticamente l' oggetto [Task](/dotnet/api/system.threading.tasks.task) restituito.</span><span class="sxs-lookup"><span data-stu-id="674b6-563">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="674b6-564">Per altre informazioni, vedere [Tipo restituito Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="674b6-564">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="674b6-565">Il tipo restituito implicito `Task` rappresenta il lavoro in corso.</span><span class="sxs-lookup"><span data-stu-id="674b6-565">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="674b6-566">La parola chiave `await` indica al compilatore di suddividere il metodo in due parti.</span><span class="sxs-lookup"><span data-stu-id="674b6-566">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="674b6-567">La prima parte termina con l'operazione avviata in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="674b6-567">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="674b6-568">La seconda parte viene inserita in un metodo di callback che viene chiamato al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="674b6-568">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="674b6-569">`ToListAsync` è la versione asincrona del metodo di estensione `ToList`.</span><span class="sxs-lookup"><span data-stu-id="674b6-569">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="674b6-570">Alcuni aspetti da considerare quando si scrive codice asincrono usato da Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="674b6-570">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="674b6-571">Solo le istruzioni che generano query o comandi da inviare al database vengono eseguite in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="674b6-571">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="674b6-572">Sono incluse `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="674b6-572">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="674b6-573">Non sono incluse le istruzioni che modificano solo un oggetto `IQueryable`, ad esempio `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="674b6-573">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="674b6-574">Un contesto di Entity Framework Core non è thread-safe. Non tentare di eseguire più operazioni parallelamente.</span><span class="sxs-lookup"><span data-stu-id="674b6-574">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="674b6-575">Per sfruttare i vantaggi del codice asincrono in termini di prestazioni, verificare che i pacchetti della libreria impiegati, ad esempio per il paging, usino la modalità asincrona per chiamare i metodi di Entity Framework Core che generano query da inviare al database.</span><span class="sxs-lookup"><span data-stu-id="674b6-575">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="674b6-576">Per altre informazioni sulla programmazione asincrona in .NET, vedere [Panoramica della programmazione asincrona](/dotnet/standard/async) e [Programmazione asincrona con async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="674b6-576">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="674b6-577">Nella prossima esercitazione, vengono esaminate le operazioni CRUD per creare, leggere, aggiornare, eliminare ed elencare.</span><span class="sxs-lookup"><span data-stu-id="674b6-577">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="674b6-578">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="674b6-578">Additional resources</span></span>

* [<span data-ttu-id="674b6-579">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="674b6-579">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="674b6-580">avanti</span><span class="sxs-lookup"><span data-stu-id="674b6-580">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
