---
title: Usare un database e ASP.NET Core
author: rick-anderson
description: Questa sezione illustra l'utilizzo di un database e di ASP.NET Core.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 87d27b60940826e21b060f2e07d344b30ff75b27
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259790"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="89d8a-103">Usare un database e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89d8a-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="89d8a-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="89d8a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="89d8a-105">L'oggetto `RazorPagesMovieContext` gestisce l'attività di connessione al database e di mapping degli oggetti `Movie` ai record di database.</span><span class="sxs-lookup"><span data-stu-id="89d8a-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="89d8a-106">Il contesto del database viene registrato con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) nel metodo `ConfigureServices` di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="89d8a-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="89d8a-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89d8a-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="89d8a-108">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="89d8a-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="89d8a-109">Il sistema di [configurazione](xref:fundamentals/configuration/index) di ASP.NET Core legge la `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="89d8a-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="89d8a-110">Per lo sviluppo locale, ottiene la stringa di connessione dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="89d8a-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="89d8a-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89d8a-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="89d8a-112">Il valore del nome per il database (`Database={Database name}`) sarà diverso per il codice generato.</span><span class="sxs-lookup"><span data-stu-id="89d8a-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="89d8a-113">Il valore del nome è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="89d8a-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="89d8a-114">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="89d8a-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="89d8a-115">Quando l'app viene distribuita in un server di test o produzione, è possibile utilizzare una variabile di ambiente per impostare la stringa di connessione su un server di database reale.</span><span class="sxs-lookup"><span data-stu-id="89d8a-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="89d8a-116">Per altre informazioni, vedere [Configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="89d8a-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="89d8a-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89d8a-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="89d8a-118">LocalDB di SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="89d8a-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="89d8a-119">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di programmi.</span><span class="sxs-lookup"><span data-stu-id="89d8a-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="89d8a-120">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="89d8a-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="89d8a-121">Per impostazione predefinita, Local DB crea file con estensione `*.mdf` nella directory `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="89d8a-121">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="89d8a-122">Dal menu **Visualizzazione** aprire **Esplora oggetti di SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="89d8a-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu View](sql/_static/ssox.png)

* <span data-ttu-id="89d8a-124">Fare clic con il pulsante destro del mouse sulla tabella `Movie` e selezionare**Progettazione viste**:</span><span class="sxs-lookup"><span data-stu-id="89d8a-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Menu di scelta rapida aperti per la tabella Movie](sql/_static/design.png)

  ![Tabelle Movie aperte nella finestra di progettazione](sql/_static/dv.png)

<span data-ttu-id="89d8a-127">Si noti l'icona a forma di chiave accanto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="89d8a-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="89d8a-128">Per impostazione predefinita, Entity Framework crea una proprietà denominata `ID` per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="89d8a-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="89d8a-129">Fare clic con il pulsante destro del mouse sulla tabella `Movie` e selezionare**Visualizza dati**:</span><span class="sxs-lookup"><span data-stu-id="89d8a-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabella Movie aperta con i dati della tabella](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="89d8a-131">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="89d8a-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="89d8a-132">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="89d8a-132">Seed the database</span></span>

<span data-ttu-id="89d8a-133">Creare una nuova classe denominata `SeedData` nella cartella *Models* usando il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="89d8a-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="89d8a-134">Se sono presenti eventuali film nel database, l'inizializzatore di valore viene restituito e non vengono aggiunti film.</span><span class="sxs-lookup"><span data-stu-id="89d8a-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="89d8a-135">Aggiungere l'inizializzatore del valore di inizializzazione</span><span class="sxs-lookup"><span data-stu-id="89d8a-135">Add the seed initializer</span></span>

<span data-ttu-id="89d8a-136">In *Program.cs* modificare il metodo `Main` per eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="89d8a-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="89d8a-137">Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="89d8a-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="89d8a-138">Chiamare il metodo di inizializzazione, passandolo al contesto.</span><span class="sxs-lookup"><span data-stu-id="89d8a-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="89d8a-139">Eliminare il contesto dopo che il metodo di inizializzazione è stato completato.</span><span class="sxs-lookup"><span data-stu-id="89d8a-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="89d8a-140">Il codice seguente illustra il file *Program.cs* aggiornato.</span><span class="sxs-lookup"><span data-stu-id="89d8a-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="89d8a-141">Quando `Update-Database` non è stata eseguita, si verifica l'eccezione seguente:</span><span class="sxs-lookup"><span data-stu-id="89d8a-141">The following exception occurs when `Update-Database` has not been run:</span></span>

`SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
`Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="89d8a-142">Eseguire il test dell'app</span><span class="sxs-lookup"><span data-stu-id="89d8a-142">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="89d8a-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89d8a-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89d8a-144">Eliminare tutti i record nel database.</span><span class="sxs-lookup"><span data-stu-id="89d8a-144">Delete all the records in the DB.</span></span> <span data-ttu-id="89d8a-145">È possibile eseguire questa operazione con i collegamenti di eliminazione nel browser o da [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="89d8a-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="89d8a-146">Forzare l'inizializzazione dell'app (chiamare i metodi nella classe `Startup`) in modo che venga eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="89d8a-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="89d8a-147">Per forzare l'inizializzazione, IIS Express deve essere arrestato e riavviato.</span><span class="sxs-lookup"><span data-stu-id="89d8a-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="89d8a-148">È possibile eseguire questa operazione adottando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="89d8a-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="89d8a-149">Fare clic con il pulsante destro del mouse sull'icona dell'area di notifica di IIS Express e toccare **Esci** o **Arresta sito**:</span><span class="sxs-lookup"><span data-stu-id="89d8a-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Icona dell'area di notifica di IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu di scelta rapida](sql/_static/stopIIS.png)

    * <span data-ttu-id="89d8a-152">Se Visual Studio è in esecuzione in modalità non di debug, premere F5 per attivare la modalità di debug.</span><span class="sxs-lookup"><span data-stu-id="89d8a-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="89d8a-153">Se Visual Studio è in esecuzione in modalità di debug, arrestare il debugger e premere F5.</span><span class="sxs-lookup"><span data-stu-id="89d8a-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="89d8a-154">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="89d8a-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="89d8a-155">Eliminare tutti i record del database; verrà quindi eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="89d8a-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="89d8a-156">Arrestare e avviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="89d8a-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="89d8a-157">L'app mostra i dati inizializzati.</span><span class="sxs-lookup"><span data-stu-id="89d8a-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="89d8a-158">L'esercitazione successiva consentirà di migliorare la presentazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="89d8a-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89d8a-159">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="89d8a-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="89d8a-160">[Precedente: Pagine Razor create tramite scaffolding](xref:tutorials/razor-pages/page)
> [Successiva: Aggiornamento delle pagine](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="89d8a-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="89d8a-161">L'oggetto `RazorPagesMovieContext` gestisce l'attività di connessione al database e di mapping degli oggetti `Movie` ai record di database.</span><span class="sxs-lookup"><span data-stu-id="89d8a-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="89d8a-162">Il contesto del database viene registrato con il contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) nel metodo `ConfigureServices` di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="89d8a-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="89d8a-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89d8a-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="89d8a-164">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="89d8a-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="89d8a-165">Per altre informazioni sui metodi usati in `ConfigureServices`, vedere:</span><span class="sxs-lookup"><span data-stu-id="89d8a-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="89d8a-166">[Supporto per il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea in ASP.NET Core](xref:security/gdpr) per `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="89d8a-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="89d8a-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="89d8a-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="89d8a-168">Il sistema di [configurazione](xref:fundamentals/configuration/index) di ASP.NET Core legge la `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="89d8a-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="89d8a-169">Per lo sviluppo locale, ottiene la stringa di connessione dal file *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="89d8a-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="89d8a-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89d8a-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="89d8a-171">Il valore del nome per il database (`Database={Database name}`) sarà diverso per il codice generato.</span><span class="sxs-lookup"><span data-stu-id="89d8a-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="89d8a-172">Il valore del nome è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="89d8a-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="89d8a-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89d8a-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="89d8a-174">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="89d8a-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="89d8a-175">Quando l'app viene distribuita in un server di test o produzione, è possibile utilizzare una variabile di ambiente per impostare la stringa di connessione su un server di database reale.</span><span class="sxs-lookup"><span data-stu-id="89d8a-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="89d8a-176">Per altre informazioni, vedere [Configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="89d8a-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="89d8a-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89d8a-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="89d8a-178">LocalDB di SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="89d8a-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="89d8a-179">Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di programmi.</span><span class="sxs-lookup"><span data-stu-id="89d8a-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="89d8a-180">Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa.</span><span class="sxs-lookup"><span data-stu-id="89d8a-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="89d8a-181">Per impostazione predefinita, Local DB crea file con estensione `*.mdf` nella directory `C:/Users/<user/>`.</span><span class="sxs-lookup"><span data-stu-id="89d8a-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="89d8a-182">Dal menu **Visualizzazione** aprire **Esplora oggetti di SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="89d8a-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menu View](sql/_static/ssox.png)

* <span data-ttu-id="89d8a-184">Fare clic con il pulsante destro del mouse sulla tabella `Movie` e selezionare**Progettazione viste**:</span><span class="sxs-lookup"><span data-stu-id="89d8a-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Menu di scelta rapida aperto per la tabella Movie](sql/_static/design.png)

  ![Tabella Movie aperta nella finestra di progettazione](sql/_static/dv.png)

<span data-ttu-id="89d8a-187">Si noti l'icona a forma di chiave accanto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="89d8a-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="89d8a-188">Per impostazione predefinita, Entity Framework crea una proprietà denominata `ID` per la chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="89d8a-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="89d8a-189">Fare clic con il pulsante destro del mouse sulla tabella `Movie` e selezionare**Visualizza dati**:</span><span class="sxs-lookup"><span data-stu-id="89d8a-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tabella Movie aperta con i dati della tabella](sql/_static/vd22.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="89d8a-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89d8a-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="89d8a-192">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="89d8a-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="89d8a-193">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="89d8a-193">Seed the database</span></span>

<span data-ttu-id="89d8a-194">Creare una nuova classe denominata `SeedData` nella cartella *Models* usando il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="89d8a-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="89d8a-195">Se sono presenti eventuali film nel database, l'inizializzatore di valore viene restituito e non vengono aggiunti film.</span><span class="sxs-lookup"><span data-stu-id="89d8a-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="89d8a-196">Aggiungere l'inizializzatore del valore di inizializzazione</span><span class="sxs-lookup"><span data-stu-id="89d8a-196">Add the seed initializer</span></span>

<span data-ttu-id="89d8a-197">In *Program.cs* modificare il metodo `Main` per eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="89d8a-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="89d8a-198">Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="89d8a-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="89d8a-199">Chiamare il metodo di inizializzazione, passandolo al contesto.</span><span class="sxs-lookup"><span data-stu-id="89d8a-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="89d8a-200">Eliminare il contesto dopo che il metodo di inizializzazione è stato completato.</span><span class="sxs-lookup"><span data-stu-id="89d8a-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="89d8a-201">Il codice seguente illustra il file *Program.cs* aggiornato.</span><span class="sxs-lookup"><span data-stu-id="89d8a-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="89d8a-202">Un'app di produzione non chiamerà `Database.Migrate`.</span><span class="sxs-lookup"><span data-stu-id="89d8a-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="89d8a-203">Sarà aggiunto al codice precedente per evitare l'eccezione seguente quando `Update-Database` non è stato eseguito:</span><span class="sxs-lookup"><span data-stu-id="89d8a-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="89d8a-204">SqlException: Impossibile aprire il database "RazorPagesMovieContext-21" richiesto dall'account di accesso.</span><span class="sxs-lookup"><span data-stu-id="89d8a-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="89d8a-205">Accesso non riuscito.</span><span class="sxs-lookup"><span data-stu-id="89d8a-205">The login failed.</span></span>
<span data-ttu-id="89d8a-206">Accesso non riuscito per l'utente "nome-utente".</span><span class="sxs-lookup"><span data-stu-id="89d8a-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="89d8a-207">Eseguire il test dell'app</span><span class="sxs-lookup"><span data-stu-id="89d8a-207">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="89d8a-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89d8a-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89d8a-209">Eliminare tutti i record nel database.</span><span class="sxs-lookup"><span data-stu-id="89d8a-209">Delete all the records in the DB.</span></span> <span data-ttu-id="89d8a-210">È possibile eseguire questa operazione con i collegamenti di eliminazione nel browser o da [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span><span class="sxs-lookup"><span data-stu-id="89d8a-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="89d8a-211">Forzare l'inizializzazione dell'app (chiamare i metodi nella classe `Startup`) in modo che venga eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="89d8a-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="89d8a-212">Per forzare l'inizializzazione, IIS Express deve essere arrestato e riavviato.</span><span class="sxs-lookup"><span data-stu-id="89d8a-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="89d8a-213">È possibile eseguire questa operazione adottando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="89d8a-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="89d8a-214">Fare clic con il pulsante destro del mouse sull'icona dell'area di notifica di IIS Express e toccare **Esci** o **Arresta sito**:</span><span class="sxs-lookup"><span data-stu-id="89d8a-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![Icona dell'area di notifica di IIS Express](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Menu di scelta rapida](sql/_static/stopIIS.png)

    * <span data-ttu-id="89d8a-217">Se Visual Studio è in esecuzione in modalità non di debug, premere F5 per attivare la modalità di debug.</span><span class="sxs-lookup"><span data-stu-id="89d8a-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="89d8a-218">Se Visual Studio è in esecuzione in modalità di debug, arrestare il debugger e premere F5.</span><span class="sxs-lookup"><span data-stu-id="89d8a-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="89d8a-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="89d8a-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="89d8a-220">Eliminare tutti i record del database; verrà quindi eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="89d8a-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="89d8a-221">Arrestare e avviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="89d8a-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="89d8a-222">L'app mostra i dati inizializzati.</span><span class="sxs-lookup"><span data-stu-id="89d8a-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="89d8a-223">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="89d8a-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="89d8a-224">Eliminare tutti i record del database; verrà quindi eseguito il metodo di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="89d8a-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="89d8a-225">Arrestare e avviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="89d8a-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="89d8a-226">L'app mostra i dati inizializzati.</span><span class="sxs-lookup"><span data-stu-id="89d8a-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="89d8a-227">L'app visualizza i dati sottoposti a seed:</span><span class="sxs-lookup"><span data-stu-id="89d8a-227">The app shows the seeded data:</span></span>

![App per i film aperta in Chrome con i dati sui film](sql/_static/m55.png)

<span data-ttu-id="89d8a-229">L'esercitazione successiva consentirà di pulire la presentazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="89d8a-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89d8a-230">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="89d8a-230">Additional resources</span></span>

* [<span data-ttu-id="89d8a-231">Versione YouTube dell'esercitazione</span><span class="sxs-lookup"><span data-stu-id="89d8a-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="89d8a-232">[Precedente: Pagine Razor create tramite scaffolding](xref:tutorials/razor-pages/page)
> [Successiva: Aggiornamento delle pagine](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="89d8a-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
