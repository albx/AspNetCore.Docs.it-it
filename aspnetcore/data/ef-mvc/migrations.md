---
title: 'Esercitazione: Usare la funzionalità delle migrazioni - ASP.NET MVC con EF Core'
description: In questa esercitazione si inizia a usare la funzionalità delle migrazioni EF Core per la gestione delle modifiche al modello di dati in un'applicazione ASP.NET Core MVC.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 6d4ed0e95499c30417e1cfd07f57de824a8a62ed
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265528"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="21184-103">Esercitazione: Usare la funzionalità delle migrazioni - ASP.NET MVC con EF Core</span><span class="sxs-lookup"><span data-stu-id="21184-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="21184-104">In questa esercitazione si inizia a usare la funzionalità delle migrazioni EF Core per la gestione delle modifiche al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="21184-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="21184-105">Nelle esercitazioni successive si aggiungeranno altre migrazioni quando si modifica il modello di dati.</span><span class="sxs-lookup"><span data-stu-id="21184-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="21184-106">Le attività di questa esercitazione sono le seguenti:</span><span class="sxs-lookup"><span data-stu-id="21184-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="21184-107">Ottenere informazioni sulle migrazioni</span><span class="sxs-lookup"><span data-stu-id="21184-107">Learn about migrations</span></span>
> * <span data-ttu-id="21184-108">Scoprire di più sui pacchetti di migrazione NuGet</span><span class="sxs-lookup"><span data-stu-id="21184-108">Learn about NuGet migration packages</span></span>
> * <span data-ttu-id="21184-109">Modificare la stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="21184-109">Change the connection string</span></span>
> * <span data-ttu-id="21184-110">Creare una migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="21184-110">Create an initial migration</span></span>
> * <span data-ttu-id="21184-111">Esaminare i metodi Up e Down</span><span class="sxs-lookup"><span data-stu-id="21184-111">Examine Up and Down methods</span></span>
> * <span data-ttu-id="21184-112">Esaminare lo snapshot del modello di dati</span><span class="sxs-lookup"><span data-stu-id="21184-112">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="21184-113">Applicare la migrazione</span><span class="sxs-lookup"><span data-stu-id="21184-113">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="21184-114">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="21184-114">Prerequisites</span></span>

* [<span data-ttu-id="21184-115">Aggiungere funzionalità di ordinamento, filtro e suddivisione in pagine con EF Core in un'app ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="21184-115">Add sorting, filtering, and paging with EF Core in an ASP.NET Core MVC app</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="21184-116">Informazioni sulle migrazioni</span><span class="sxs-lookup"><span data-stu-id="21184-116">About migrations</span></span>

<span data-ttu-id="21184-117">Quando si sviluppa una nuova applicazione, il modello di dati cambia di frequente e, a ogni cambiamento, non è più sincronizzato con il database.</span><span class="sxs-lookup"><span data-stu-id="21184-117">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="21184-118">La serie di esercitazioni è iniziata con la configurazione di Entity Framework per creare il database se non esiste già.</span><span class="sxs-lookup"><span data-stu-id="21184-118">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="21184-119">Quindi, ogni volta che si modifica il modello di dati, ovvero si aggiungono, rimuovono, o modificano le classi di entità o si modifica la classe DbContext, è possibile eliminare il database. In seguito EF ne crea uno nuovo corrispondente al modello ed esegue il seeding con dati di test.</span><span class="sxs-lookup"><span data-stu-id="21184-119">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="21184-120">Questo metodo che consiste nel mantenere il database sincronizzato con il modello di dati funziona bene fino a quando non si distribuisce l'applicazione nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="21184-120">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="21184-121">Quando l'applicazione è in esecuzione nell'ambiente di produzione in genere archivia dati che devono essere mantenuti ed è necessario evitare di perdere tutto ad ogni modifica, come ad esempio all'aggiunta di una colonna.</span><span class="sxs-lookup"><span data-stu-id="21184-121">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="21184-122">La funzionalità delle migrazioni di EF Core risolve questo problema consentendo a EF Core di aggiornare lo schema del database anziché crearne uno nuovo.</span><span class="sxs-lookup"><span data-stu-id="21184-122">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

## <a name="about-nuget-migration-packages"></a><span data-ttu-id="21184-123">Informazioni sui pacchetti di migrazione NuGet</span><span class="sxs-lookup"><span data-stu-id="21184-123">About NuGet migration packages</span></span>

<span data-ttu-id="21184-124">Per usare le migrazioni, è possibile usare la **Console di Gestione pacchetti** o l'interfaccia della riga di comando (CLI).</span><span class="sxs-lookup"><span data-stu-id="21184-124">To work with migrations, you can use the **Package Manager Console** (PMC) or the command-line interface (CLI).</span></span>  <span data-ttu-id="21184-125">Queste esercitazioni illustrano come usare i comandi dell'interfaccia della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="21184-125">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="21184-126">Per informazioni sulla Console di Gestione pacchetti, vedere [la fine di questa esercitazione](#pmc).</span><span class="sxs-lookup"><span data-stu-id="21184-126">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

<span data-ttu-id="21184-127">Gli strumenti di Entity Framework per l'interfaccia della riga di comando (CLI) sono disponibili in [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet).</span><span class="sxs-lookup"><span data-stu-id="21184-127">The EF tools for the command-line interface (CLI) are provided in [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet).</span></span> <span data-ttu-id="21184-128">Per installare il pacchetto, aggiungerlo alla raccolta `DotNetCliToolReference` nel file con estensione *.csproj*, come illustrato.</span><span class="sxs-lookup"><span data-stu-id="21184-128">To install this package, add it to the `DotNetCliToolReference` collection in the *.csproj* file, as shown.</span></span> <span data-ttu-id="21184-129">**Nota:** è necessario installare questo pacchetto modificando il file *.csproj*. Non è possibile usare il comando `install-package` o la GUI di gestione di pacchetti.</span><span class="sxs-lookup"><span data-stu-id="21184-129">**Note:** You have to install this package by editing the *.csproj* file; you can't use the `install-package` command or the package manager GUI.</span></span> <span data-ttu-id="21184-130">È possibile modificare il file con estensione *.csproj* facendo clic con il pulsante destro del mouse sul nome del progetto in **Esplora soluzioni** e selezionando **Modifica ContosoUniversity.csproj**.</span><span class="sxs-lookup"><span data-stu-id="21184-130">You can edit the *.csproj* file by right-clicking the project name in **Solution Explorer** and selecting **Edit ContosoUniversity.csproj**.</span></span>

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?range=12-15&highlight=2)]

<span data-ttu-id="21184-131">I numeri di versione dell'esempio erano quelli correnti nel momento in cui l'esercitazione è stata scritta.</span><span class="sxs-lookup"><span data-stu-id="21184-131">(The version numbers in this example were current when the tutorial was written.)</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="21184-132">Modificare la stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="21184-132">Change the connection string</span></span>

<span data-ttu-id="21184-133">Nel file *appsettings.json* cambiare il nome del database nella stringa di connessione digitando ContosoUniversity2 o un altro nome non ancora adottato nel computer in uso.</span><span class="sxs-lookup"><span data-stu-id="21184-133">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="21184-134">Questa modifica configura il progetto in modo che la prima migrazione crei un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="21184-134">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="21184-135">Questa operazione non è necessaria per iniziare a usare le migrazioni, ma si capirà più avanti perché è utile eseguirla.</span><span class="sxs-lookup"><span data-stu-id="21184-135">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="21184-136">In alternativa alla modifica del nome del database, è possibile eliminare il database.</span><span class="sxs-lookup"><span data-stu-id="21184-136">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="21184-137">Usare **Esplora oggetti di SQL Server** o il comando della CLI `database drop`:</span><span class="sxs-lookup"><span data-stu-id="21184-137">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```console
> dotnet ef database drop
> ```
> <span data-ttu-id="21184-138">Nella sezione seguente viene illustrato come eseguire i comandi della CLI.</span><span class="sxs-lookup"><span data-stu-id="21184-138">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="21184-139">Creare una migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="21184-139">Create an initial migration</span></span>

<span data-ttu-id="21184-140">Salvare le modifiche e compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="21184-140">Save your changes and build the project.</span></span> <span data-ttu-id="21184-141">Aprire una finestra di comando e passare alla cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="21184-141">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="21184-142">Ecco un modo rapido per eseguire questa operazione:</span><span class="sxs-lookup"><span data-stu-id="21184-142">Here's a quick way to do that:</span></span>

* <span data-ttu-id="21184-143">In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Apri cartella in Esplora File** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="21184-143">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Voce di menu Apri in Esplora file](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="21184-145">Immettere "cmd" nella barra degli indirizzi e premere INVIO.</span><span class="sxs-lookup"><span data-stu-id="21184-145">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Finestra di comando](migrations/_static/open-command-window.png)

<span data-ttu-id="21184-147">Immettere il comando seguente nella finestra Comando:</span><span class="sxs-lookup"><span data-stu-id="21184-147">Enter the following command in the command window:</span></span>

```console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="21184-148">Nella finestra di comando viene visualizzato un output come il seguente:</span><span class="sxs-lookup"><span data-stu-id="21184-148">You see output like the following in the command window:</span></span>

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="21184-149">Se viene visualizzato un messaggio di errore che indica che *non sono stati trovati eseguibili corrispondenti al comando "dotnet-ef"*, vedere [il post di questo blog](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="21184-149">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="21184-150">Se viene visualizzato il messaggio di errore "*Impossibile accedere al file  ContosoUniversity.dll perché utilizzato da un altro processo.*", individuare l'icona di IIS Express nella barra delle applicazioni di Windows, selezionarla con il pulsante destro del mouse e fare clic su **ContosoUniversity > Arresta sito**.</span><span class="sxs-lookup"><span data-stu-id="21184-150">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="21184-151">Esaminare i metodi Up e Down</span><span class="sxs-lookup"><span data-stu-id="21184-151">Examine Up and Down methods</span></span>

<span data-ttu-id="21184-152">Quando è stato eseguito il comando `migrations add`, EF ha generato il codice che crea il database da zero.</span><span class="sxs-lookup"><span data-stu-id="21184-152">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="21184-153">Questo codice si trova nella cartella *Migrations*, nel file denominato *\<timestamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="21184-153">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="21184-154">Il metodo `Up` della classe `InitialCreate` crea le tabelle di database che corrispondono ai set di entità del modello di dati, e il metodo `Down` le elimina, come illustrato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="21184-154">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="21184-155">Le migrazioni chiamano il metodo `Up` per implementare le modifiche al modello di dati per una migrazione.</span><span class="sxs-lookup"><span data-stu-id="21184-155">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="21184-156">Quando si immette un comando per annullare l'aggiornamento, le migrazioni chiamano il metodo `Down`.</span><span class="sxs-lookup"><span data-stu-id="21184-156">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="21184-157">Questo codice è per la migrazione iniziale creata al momento dell'immissione del comando `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="21184-157">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="21184-158">Il parametro del nome della migrazione, nell'esempio "InitialCreate", viene usato per il nome del file e può essere ciò che si vuole.</span><span class="sxs-lookup"><span data-stu-id="21184-158">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="21184-159">È consigliabile scegliere una parola o una frase che riepiloghi cosa viene fatto nella migrazione.</span><span class="sxs-lookup"><span data-stu-id="21184-159">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="21184-160">Ad esempio, è possibile denominare una migrazione successiva "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="21184-160">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="21184-161">Se la migrazione iniziale è stata creata quando il database esisteva già, il codice di creazione del database viene generato ma non è necessario eseguirlo perché il database corrisponde già al modello di dati.</span><span class="sxs-lookup"><span data-stu-id="21184-161">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="21184-162">Quando si distribuisce l'app in un altro ambiente in cui il database non esiste ancora, questo codice verrà eseguito per creare il database, è consigliabile quindi testarlo prima.</span><span class="sxs-lookup"><span data-stu-id="21184-162">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="21184-163">Ecco perché in precedenza è stato modificato il nome del database nella stringa di connessione: per far sì che le migrazioni possano crearne uno nuovo da zero.</span><span class="sxs-lookup"><span data-stu-id="21184-163">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="21184-164">Snapshot del modello di dati</span><span class="sxs-lookup"><span data-stu-id="21184-164">The data model snapshot</span></span>

<span data-ttu-id="21184-165">Le migrazioni creano uno *snapshot* dello schema del database corrente in *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="21184-165">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="21184-166">Quando si aggiunge una migrazione, EF determina le modifiche apportate confrontando il modello di dati con il file dello snapshot.</span><span class="sxs-lookup"><span data-stu-id="21184-166">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="21184-167">Quando si elimina una migrazione, usare il comando [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="21184-167">When deleting a migration, use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command.</span></span> <span data-ttu-id="21184-168">`dotnet ef migrations remove` elimina la migrazione e garantisce che lo snapshot venga reimpostato correttamente.</span><span class="sxs-lookup"><span data-stu-id="21184-168">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span>

<span data-ttu-id="21184-169">Per altre informazioni sull'uso del file di snapshot, vedere [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) (Migrazioni EF Core in ambienti team).</span><span class="sxs-lookup"><span data-stu-id="21184-169">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="21184-170">Applicare la migrazione</span><span class="sxs-lookup"><span data-stu-id="21184-170">Apply the migration</span></span>

<span data-ttu-id="21184-171">Nella finestra di comando immettere il comando seguente per creare il database e le tabelle al suo interno.</span><span class="sxs-lookup"><span data-stu-id="21184-171">In the command window, enter the following command to create the database and tables in it.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="21184-172">L'output del comando è simile al comando `migrations add`, a eccezione del fatto che vengono visualizzati i log per i comandi SQL che configurano il database.</span><span class="sxs-lookup"><span data-stu-id="21184-172">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="21184-173">La maggior parte dei log viene omessa nell'output di esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="21184-173">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="21184-174">Se si vuole ridurre il livello di dettaglio nei messaggi di log, è possibile modificare i livelli di log nel file *appsettings.Development.json*.</span><span class="sxs-lookup"><span data-stu-id="21184-174">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="21184-175">Per ulteriori informazioni, vedere <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="21184-175">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (467ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (20ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20170816151242_InitialCreate', N'2.0.0-rtm-26452');
Done.
```

<span data-ttu-id="21184-176">Per controllare il database come è stato fatto nella prima esercitazione, usare **Esplora oggetti di SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="21184-176">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="21184-177">Si noterà l'aggiunta di una tabella \_\_EFMigrationsHistory che tiene traccia di quali migrazioni sono state applicate al database.</span><span class="sxs-lookup"><span data-stu-id="21184-177">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="21184-178">Visualizzare i dati nella tabella: si noterà la presenza di una riga per la prima migrazione.</span><span class="sxs-lookup"><span data-stu-id="21184-178">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="21184-179">Nell'ultimo log nell'esempio di output della CLI precedente viene visualizzata l'istruzione INSERT che crea tale riga.</span><span class="sxs-lookup"><span data-stu-id="21184-179">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="21184-180">Eseguire l'applicazione per verificare che tutto funzioni come prima.</span><span class="sxs-lookup"><span data-stu-id="21184-180">Run the application to verify that everything still works the same as before.</span></span>

![Pagina Student Index (Indice degli studenti)](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="21184-182">Interfaccia della riga di comando e console di gestione pacchetto a confronto</span><span class="sxs-lookup"><span data-stu-id="21184-182">Compare CLI and PMC</span></span>

<span data-ttu-id="21184-183">Gli strumenti di EF per la gestione delle migrazioni sono disponibili dai comandi della CLI di .NET Core o dai cmdlet di PowerShell in Visual Studio nella finestra **Console di Gestione pacchetti**.</span><span class="sxs-lookup"><span data-stu-id="21184-183">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="21184-184">In questa esercitazione viene illustrato come usare la CLI, ma se si preferisce è possibile usare la console di Gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="21184-184">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="21184-185">I comandi di EF per la console di Gestione pacchetti sono inclusi nel pacchetto [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools).</span><span class="sxs-lookup"><span data-stu-id="21184-185">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="21184-186">Questo pacchetto è incluso nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), quindi non è necessario aggiungere un riferimento al pacchetto se l'app dispone di un riferimento al pacchetto per `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="21184-186">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="21184-187">**Importante:** non si tratta dello stesso pacchetto che si installa per l'interfaccia della riga di comando modificando il file con estensione *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="21184-187">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="21184-188">Il nome di questo pacchetto termina con `Tools`, a differenza del nome del pacchetto della CLI che termina con `Tools.DotNet`.</span><span class="sxs-lookup"><span data-stu-id="21184-188">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="21184-189">Per altre informazioni sui comandi della CLI, vedere [Strumenti da riga di comando di EF Core .NET](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="21184-189">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="21184-190">Per altre informazioni sui comandi della console di Gestione pacchetti, vedere [Console di Gestione pacchetti (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="21184-190">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="21184-191">Ottenere il codice</span><span class="sxs-lookup"><span data-stu-id="21184-191">Get the code</span></span>

[<span data-ttu-id="21184-192">Scaricare o visualizzare l'applicazione completata.</span><span class="sxs-lookup"><span data-stu-id="21184-192">Download or view the completed application.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="21184-193">Passaggio successivo</span><span class="sxs-lookup"><span data-stu-id="21184-193">Next step</span></span>

<span data-ttu-id="21184-194">Le attività di questa esercitazione sono le seguenti:</span><span class="sxs-lookup"><span data-stu-id="21184-194">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="21184-195">Sono state descritte le migrazioni</span><span class="sxs-lookup"><span data-stu-id="21184-195">Learned about migrations</span></span>
> * <span data-ttu-id="21184-196">Sono stati presentati i pacchetti di migrazione NuGet</span><span class="sxs-lookup"><span data-stu-id="21184-196">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="21184-197">È stata modificata la stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="21184-197">Changed the connection string</span></span>
> * <span data-ttu-id="21184-198">È stata creata una migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="21184-198">Created an initial migration</span></span>
> * <span data-ttu-id="21184-199">Sono stati esaminati i metodi Up e Down</span><span class="sxs-lookup"><span data-stu-id="21184-199">Examined Up and Down methods</span></span>
> * <span data-ttu-id="21184-200">È stato esaminato lo snapshot del modello di dati</span><span class="sxs-lookup"><span data-stu-id="21184-200">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="21184-201">È stata applicata la migrazione</span><span class="sxs-lookup"><span data-stu-id="21184-201">Applied the migration</span></span>

<span data-ttu-id="21184-202">Passare all'articolo successivo per iniziare a esaminare argomenti più avanzati sull'espansione del modello di dati.</span><span class="sxs-lookup"><span data-stu-id="21184-202">Advance to the next article to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="21184-203">Lungo il percorso verranno create e applicate altre migrazioni.</span><span class="sxs-lookup"><span data-stu-id="21184-203">Along the way you'll create and apply additional migrations.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="21184-204">Creare e applicare altre migrazioni</span><span class="sxs-lookup"><span data-stu-id="21184-204">Create and apply additional migrations</span></span>](complex-data-model.md)
