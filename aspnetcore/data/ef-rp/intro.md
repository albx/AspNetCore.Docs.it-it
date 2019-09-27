---
title: 'Razor Pages con Entity Framework Core in ASP.NET Core: esercitazione 1 di 8'
author: tdykstra
description: Viene illustrato come creare un'app Razor Pages con Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: e261ccd30dc9ef0929e74fa44a5ed752d515b707
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317687"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Razor Pages con Entity Framework Core in ASP.NET Core: esercitazione 1 di 8

Di [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Questo è il primo di una serie di esercitazioni che illustrano come usare Entity Framework (EF) Core in un'app [ASP.NET Core Razor Pages](xref:razor-pages/index) . Con queste esercitazioni viene creato un sito Web per una fittizia Contoso University. Il sito include funzionalità, come ad esempio l'ammissione di studenti, la creazione di corsi e le assegnazioni degli insegnati.

[Scaricare o visualizzare l'app completata.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Istruzioni per il download](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha familiarità con Razor Pages, vedere la serie di esercitazioni [Introduzione a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) prima di iniziare.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Motori di database

Le istruzioni per Visual Studio usano [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versione di SQL Server Express eseguita solo in Windows.

Le istruzioni per Visual Studio Code usano [SQLite](https://www.sqlite.org/), un motore di database multipiattaforma.

Se si sceglie di usare SQLite, scaricare e installare uno strumento di terze parti per la gestione e la visualizzazione di un database SQLite, ad esempio [DB Browser per SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un problema che non è possibile risolvere, confrontare il codice con il [progetto completato](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Un buon metodo per ottenere assistenza è quello di pubblicare una domanda in StackOverflow.com con il [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o il [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>App di esempio

L'applicazione compilata in queste esercitazioni è il sito Web di base di un'università. Gli utenti possono visualizzare e aggiornare le informazioni che riguardano studenti, corsi e insegnanti. Di seguito sono disponibili alcune schermate dell'esercitazione.

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index30.png)

![Pagina Students Edit (Modifica studenti)](intro/_static/student-edit30.png)

Lo stile dell'interfaccia utente del sito è basato sui modelli di progetto predefiniti. L'obiettivo dell'esercitazione è quello di usare EF Core e non di personalizzare l'interfaccia utente.

Seguire il collegamento nella parte superiore della pagina per ottenere il codice sorgente per il progetto completato. La cartella *cu30* contiene il codice per la versione di ASP.NET Core 3.0 dell'esercitazione. I file che riflettono lo stato del codice per le esercitazioni 1-7 si trovano nella cartella *cu30snapshots*.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Per eseguire l'app dopo il download del progetto completato:

* Eliminare i tre file e la cartella con *SQLite* nel nome.
* Compilare il progetto.
* Nella console di Gestione pacchetti eseguire il comando seguente:

  ```powershell
  Update-Database
  ```

* Eseguire il progetto per il seeding del database.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Per eseguire l'app dopo il download del progetto completato:

* Eliminare *ContosoUniversity.csproj* e rinominare *ContosoUniversitySQLite.csproj* in *ContosoUniversity.csproj*.
* Eliminare *Startup.cs* e rinominare *StartupSQLite.cs* in *Startup.cs*.
* Eliminare *appSettings.json* e rinominare *appSettingsSQLite.json* in *appSettings.json*.
* Eliminare la cartella *Migrations* e rinominare *MigrationsSQL* in *Migrations*.
* Compilare il progetto.
* Al prompt dei comandi nella cartella del progetto eseguire i comandi seguenti:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* Nello strumento SQLite eseguire l'istruzione SQL seguente:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Eseguire il progetto per il seeding del database.

---

## <a name="create-the-web-app-project"></a>Creare il progetto di app Web

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.
* Selezionare **Applicazione Web ASP.NET Core**.
* Denominare il progetto *ContosoUniversity*. È importante usare questo nome esatto, incluse le maiuscole, in modo che gli spazi dei nomi corrispondano quando il codice viene copiato e incollato.
* Selezionare **.NET Core** e **ASP.NET Core 3.0** nell'elenco a discesa, quindi selezionare **Applicazione Web**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* In un terminale passare alla cartella in cui deve essere creata la cartella del progetto.

* Eseguire i comandi seguenti per creare un progetto Razor Pages e `cd` nella nuova cartella di progetto:

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Impostare lo stile del sito

Per impostare l'intestazione, il piè di pagina e il menu del sito, aggiornare *Pages/Shared/_Layout.cshtml*:

* Modificare tutte le occorrenze di "ContosoUniversity" in "Contoso University". Le occorrenze sono tre.

* Eliminare le voci di menu **Home** e **Privacy**, quindi aggiungere le voci per **About** (Informazioni su), **Students** (Studenti), **Courses** (Corsi), **Instructors** (Insegnanti) e **Departments** (Dipartimenti).

Le modifiche vengono evidenziate.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

In *Pages/Index.cshtml* sostituire il contenuto del file con il codice seguente. In questo modo il testo su ASP.NET sarà sostituito dal testo relativo a questa app:

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Eseguire l'app per verificare che venga visualizzata la home page.

## <a name="the-data-model"></a>Modello di dati

Nelle sezioni seguenti viene descritto come creare un modello di dati:

![Diagramma modello di dati Course/Enrollment/Student (Corso/Iscrizione/Studente)](intro/_static/data-model-diagram.png)

Uno studente può iscriversi a un numero qualsiasi di corsi e un corso può avere un numero qualsiasi di studenti iscritti.

## <a name="the-student-entity"></a>Entità Student (Studente)

![Diagramma entità Student (Studente)](intro/_static/student-entity.png)

* Creare una cartella *Models* nella cartella del progetto. 

* Creare *Models/Student.cs* con il codice seguente:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

La proprietà `ID` diventa la colonna chiave primaria della tabella del database che corrisponde a questa classe. Per impostazione predefinita, Entity Framework Core interpreta una proprietà denominata `ID` o `classnameID` come chiave primaria. Il nome alternativo riconosciuto automaticamente per la chiave primaria della classe `Student` è quindi `StudentID`.

La proprietà `Enrollments` rappresenta una [proprietà di navigazione](/ef/core/modeling/relationships). Le proprietà di navigazione contengono altre entità correlate a questa entità. In questo caso la proprietà `Enrollments` di un'entità `Student` contiene tutte le entità `Enrollment` correlate a tale studente. Ad esempio, se una riga Student nel database presenta due righe Enrollment correlate, la proprietà di navigazione `Enrollments` contiene questi due entità Enrollment. 

Nel database, una riga Enrollment è correlata a una riga Student se la relativa colonna StudentID contiene il valore ID dello studente. Si supponga, ad esempio, che una riga Student abbia ID=1. Le righe Enrollment correlate avranno StudentID = 1. StudentID è una *chiave esterna* nella tabella Enrollment. 

La proprietà `Enrollments` è definita come `ICollection<Enrollment>` perché potrebbero essere presenti più entità Enrollment correlate. È possibile usare altri tipi di raccolta, ad esempio `List<Enrollment>` o `HashSet<Enrollment>`. Se si specifica `ICollection<Enrollment>`, per impostazione predefinita Entity Framework Core crea una raccolta `HashSet<Enrollment>`.

## <a name="the-enrollment-entity"></a>Entità Enrollment (Iscrizione)

![Diagramma entità Enrollment (Iscrizione)](intro/_static/enrollment-entity.png)

Creare *Models/Enrollment.cs* con il codice seguente:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

La proprietà `EnrollmentID` è la chiave primaria. Questa entità usa il modello `classnameID` anziché `ID` da solo. Per un modello di dati di produzione, scegliere un modello e usarlo in modo coerente. Questa esercitazione usa entrambi solo per illustrare che entrambi funzionano. L'uso di `ID` senza `classname` rende più semplice l'implementazione di alcuni tipi di modifiche al modello di dati.

La proprietà `Grade` è un oggetto`enum`. Il punto interrogativo dopo la dichiarazione del tipo `Grade` indica che la proprietà `Grade` [ammette i valori Null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/). Un voto con valore Null è diverso da un voto con valore zero. Null significa che un voto è sconosciuto o non è stato ancora assegnato.

La proprietà `StudentID` è una chiave esterna e la proprietà di navigazione corrispondente è `Student`. Un'entità `Enrollment` è associata a un'entità `Student`, pertanto la proprietà contiene un'unica entità `Student`.

La proprietà `CourseID` è una chiave esterna e la proprietà di navigazione corrispondente è `Course`. Un'entità `Enrollment` è associata a un'entità `Course`.

Entity Framework Core interpreta una proprietà come chiave esterna se è denominata `<navigation property name><primary key property name>`. Ad esempio, `StudentID` è la chiave esterna per la proprietà di navigazione `Student`, dato che la chiave primaria `Student` dell'entità è `ID`. Le proprietà di chiave esterna possono anche essere denominate `<primary key property name>`. Ad esempio, `CourseID` poiché la chiave primaria `Course` dell'entità è `CourseID`.

## <a name="the-course-entity"></a>Entità Course (Corso)

![Diagramma entità Course (Corso)](intro/_static/course-entity.png)

Creare *Models/Course.cs* con il codice seguente:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

La proprietà `Enrollments` rappresenta una proprietà di navigazione. È possibile correlare un'entità `Course` a un numero qualsiasi di entità `Enrollment`.

L'attributo `DatabaseGenerated` consente all'app di specificare la chiave primaria anziché chiedere al database di generarla.

Compilare il progetto per verificare che non siano presenti errori del compilatore.

## <a name="scaffold-student-pages"></a>Scaffolding delle pagine Student

In questa sezione si userà lo strumento di scaffolding di ASP.NET Core per generare:

* Una classe *contesto* di EF Core. Il contesto è la classe principale che coordina le funzionalità di Entity Framework per un determinato modello di dati. Deriva dalla classe `Microsoft.EntityFrameworkCore.DbContext`.
* Pagine Razor che gestiscono operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per l'entità `Student`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Creare una cartella *Students* nella cartella *Pages*.
* In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Pages/Students* e quindi scegliere **Aggiungi** > **Nuovo elemento di scaffolding**.
* Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Pagine Razor che usano Entity Framework (CRUD)** > **Aggiungi**.
* Nella finestra di dialogo **Pagine Razor che usano Entity Framework (CRUD)** :
  * Nell'elenco a discesa **Classe modello** selezionare **Student (ContosoUniversity.Models)** .
  * Nella riga **Classe contesto di dati** selezionare il segno più **+** .
  * Modificare il nome del contesto di dati da *ContosoUniversity.Models.ContosoUniversityContext* a *ContosoUniversity.Data.SchoolContext*.
  * Selezionare **Aggiungi**.

Vengono installati automaticamente i pacchetti seguenti:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Eseguire i comandi seguenti dell'interfaccia della riga di comando di .NET Core per installare i pacchetti NuGet necessari:
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

  Per lo scaffolding è necessario il pacchetto Microsoft.VisualStudio.Web.CodeGeneration.Design. Sebbene l'app non usi SQL Server, lo strumento di scaffolding richiede il pacchetto di SQL Server.

* Creare una cartella *Pages/Students*.

* Eseguire il comando seguente per installare lo [strumento di scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* Eseguire il comando seguente per eseguire lo scaffolding delle pagine Student.

  **In Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **In macOS o Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

Se si riscontra un problema con il passaggio precedente, compilare il progetto e riprovare a eseguire il passaggio di scaffolding.

Il processo di scaffolding:

* Crea pagine Razor nella cartella *Pages/Students*:
  * *Create.cshtml* e *Create.cshtml.cs*
  * *Delete.cshtml* e *Delete.cshtml.cs*
  * *Details.cshtml* e *Details.cshtml.cs*
  * *Edit.cshtml* e *Edit.cshtml.cs*
  * *Index.cshtml* e *Index.cshtml.cs*
* Crea *Data/SchoolContext.cs*.
* Aggiunge il contesto all'inserimento delle dipendenze in *Startup.cs*.
* Aggiunge la stringa di connessione al database ad *appsettings.json*.

## <a name="database-connection-string"></a>Stringa di connessione al database

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

La stringa di connessione specifica [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di app e non per la produzione. Per impostazione predefinita, Local DB crea file con estensione *mdf* nella directory `C:/Users/<user>`.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Modificare la stringa di connessione in modo che punti a un file di database SQLite denominato *CU.db*:

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Aggiornare la classe del contesto di database

La classe principale che coordina le funzionalità di EF Core per un determinato modello di dati è la classe del contesto di database. Il contesto è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il contesto specifica le entità incluse nel modello di dati. In questo progetto la classe è denominata `SchoolContext`.

Aggiornare *SchoolContext.cs* con il codice seguente:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Il codice evidenziato crea una proprietà [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per ogni set di entità. Nella terminologia di Entity Framework Core:

* Un set di entità corrisponde in genere alla tabella di un database.
* Un'entità corrisponde a una riga nella tabella.

Dato che un set di entità contiene più entità, le proprietà DBSet devono essere nomi plurali. Dato che lo strumento di scaffolding ha creato un DBSet `Student`, questo passaggio lo modifica nel plurale `Students`. 

Per fare in modo che il codice Razor Pages riconosca il nuovo nome del DBSet, sostituire globalmente `_context.Student` con `_context.Students` nell'intero progetto.  Sono presenti 8 occorrenze.

Compilare il progetto per verificare che non siano presenti errori di compilazione.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection). I servizi, ad esempio il contesto di database di EF Core, vengono registrati con l'inserimento delle dipendenze durante l'avvio dell'applicazione. Questi servizi vengono quindi offerti ai componenti per cui sono necessari (ad esempio Razor Pages) tramite i parametri del costruttore. Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.

Lo strumento di scaffolding ha registrato automaticamente la classe di contesto nel contenitore di inserimento delle dipendenze.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* In `ConfigureServices` le righe evidenziate sono state aggiunte dallo scaffolder:

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* In `ConfigureServices` assicurarsi che il codice aggiunto dallo scaffolder chiami `UseSqlite`.

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.

## <a name="create-the-database"></a>Creare il database

Aggiornare *Program.cs* per creare il database se non esiste:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

Il metodo [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) non esegue alcuna azione se esiste un database per il contesto. Se non esiste alcun database, vengono creati il database e lo schema. `EnsureCreated` abilita il flusso di lavoro seguente per la gestione delle modifiche al modello di dati:

* Eliminare il database. Tutti i dati esistenti andranno perduti.
* Modificare il modello di dati. Ad esempio, aggiungere un campo `EmailAddress`.
* Eseguire l'app.
* `EnsureCreated` crea un database con il nuovo schema.

Questo flusso di lavoro funziona correttamente nelle fasi iniziali dello sviluppo quando lo schema è in rapida evoluzione, purché non sia necessario conservare i dati. La situazione è diversa quando è necessario mantenere i dati immessi nel database. In tal caso, usare le migrazioni.

Più avanti nella serie di esercitazioni si vedrà come eliminare il database creato da `EnsureCreated` e usare invece le migrazioni. Non è possibile aggiornare un database creato da `EnsureCreated` usando le migrazioni.

### <a name="test-the-app"></a>Eseguire il test dell'app

* Eseguire l'app.
* Selezionare il collegamento **Students** (Studenti) e quindi **Crea nuovo**.
* Eseguire il test dei collegamenti Edit (Modifica), Details (Dettagli) e Delete (Elimina).

## <a name="seed-the-database"></a>Specificare il valore di inizializzazione del database

Il metodo `EnsureCreated` crea un database vuoto. In questa sezione viene aggiunto il codice che popola il database con dati di test.

Creare *Data/DbInitializer.cs* con il codice seguente:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Il codice controlla se esistono studenti nel database. Se non sono presenti studenti, aggiunge i dati di test al database. I dati di test vengono creati in matrici anziché in raccolte `List<T>` per ottimizzare le prestazioni.

* In *Program.cs* sostituire la chiamata di `EnsureCreated` con una chiamata di `DbInitializer.Initialize`:

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ````

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Arrestare l'app se è in esecuzione ed eseguire il comando seguente nella **console di Gestione pacchetti**:

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Arrestare l'app se è in esecuzione ed eliminare il file *CU.db*.

---

* Riavviare l'app.

* Selezionare la pagina Students per visualizzare i dati di seeding.

## <a name="view-the-database"></a>Visualizzare il database

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Aprire **Esplora oggetti di SQL Server** dal menu **Visualizza** in Visual Studio.
* In Esplora oggetti di SQL Server selezionare **(localdb)\MSSQLLocalDB > Database > SchoolContext-{GUID}** . Il nome del database viene generato dal nome del contesto specificato in precedenza con l'aggiunta di un trattino e un GUID.
* Espandere il nodo **Tabelle**.
* Fare clic con il pulsante destro del mouse sulla tabella **Student** (Studente) e fare clic su **Visualizza dati** per visualizzare le colonne create e le righe inserite nella tabella.
* Fare clic con il pulsante destro del mouse sulla tabella **Student** e scegliere **Visualizza codice** per vedere il mapping tra il modello `Student` e lo schema della tabella `Student`.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Usare lo strumento SQLite per visualizzare lo schema del database e i dati di seeding. Il file di database è denominato*CU.db* e si trova nella cartella del progetto.

---

## <a name="asynchronous-code"></a>Codice asincrono

La programmazione asincrona è la modalità predefinita per ASP.NET Core ed Entity Framework Core.

Per un server Web è disponibile un numero limitato di thread e in situazioni di carico elevato tutti i thread disponibili potrebbero essere in uso. In queste circostanze il server non può elaborare nuove richieste finché i thread non saranno liberi. Con il codice sincrono, può succedere che molti thread siano vincolati nonostante in quel momento non stiano eseguendo alcuna operazione. Rimangono tuttavia in attesa che l'operazione I/O sia completata. Con il codice asincrono, se un processo è in attesa del completamento dell'operazione I/O, il thread viene liberato e il server lo può usare per l'elaborazione di altre richieste. Di conseguenza, il codice asincrono consente un uso più efficiente delle risorse del server e il server può gestire una maggiore quantità di traffico senza ritardi.

Il codice asincrono comporta un minimo sovraccarico in fase di esecuzione. In caso di traffico ridotto, il calo delle prestazioni è irrilevante, mentre nelle situazioni di traffico elevato, è essenziale ottimizzare le prestazioni potenziali.

Nel codice seguente la parola chiave [async](/dotnet/csharp/language-reference/keywords/async), il valore restituito `Task<T>`, la parola chiave `await` e il metodo `ToListAsync` consentono di eseguire il codice in modo asincrono.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* La parola chiave `async` indica al compilatore di eseguire le operazioni seguenti:
  * Generare callback per parti del corpo del metodo.
  * Creare l'oggetto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) restituito.
* Il tipo restituito `Task<T>` rappresenta il lavoro in corso.
* La parola chiave `await` indica al compilatore di suddividere il metodo in due parti. La prima parte termina con l'operazione avviata in modo asincrono. La seconda parte viene inserita in un metodo di callback che viene chiamato al termine dell'operazione.
* `ToListAsync` è la versione asincrona del metodo di estensione `ToList`.

Alcuni aspetti da considerare quando si scrive codice asincrono usato da Entity Framework Core:

* Solo le istruzioni che generano query o comandi da inviare al database vengono eseguite in modo asincrono, Sono incluse `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`. Non sono incluse le istruzioni che modificano solo un oggetto `IQueryable`, ad esempio `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Un contesto di Entity Framework Core non è thread-safe. Non tentare di eseguire più operazioni parallelamente.
* Per sfruttare i vantaggi del codice asincrono in termini di prestazioni, verificare che i pacchetti della libreria impiegati, ad esempio per il paging, usino la modalità asincrona per chiamare i metodi di EF Core che inviano query al database.

Per altre informazioni sulla programmazione asincrona in .NET, vedere [Panoramica della programmazione asincrona](/dotnet/standard/async) e [Programmazione asincrona con async e await](/dotnet/csharp/programming-guide/concepts/async/).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="step-by-step"]
> [Esercitazione successiva](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

L'app Web di esempio di Contoso University illustra come creare un'app ASP.NET Core di Razor Pages con Entity Framework (EF) Core.

L'app di esempio è un sito Web per una fittizia Contoso University. Include funzionalità, come ad esempio l'ammissione di studenti, la creazione di corsi e le assegnazioni di insegnati. Questa pagina è il prima di una serie di esercitazioni che illustrano come compilare l'app di esempio di Contoso University.

[Scaricare o visualizzare l'app completata.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Istruzioni per il download](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Conoscenza di [Razor Pages](xref:razor-pages/index). Prima di iniziare questa serie, i programmatori non esperti dovranno completare l'[introduzione a Razor Pages](xref:tutorials/razor-pages/razor-pages-start).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un problema che non si sa come risolvere, è generalmente possibile trovare la soluzione confrontando il codice con il [progetto completato](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Un buon metodo per ottenere assistenza è quello di pubblicare una domanda in [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) per [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-contoso-university-web-app"></a>App Web di Contoso University

L'applicazione compilata in queste esercitazioni è il sito Web di base di un'università.

Gli utenti possono visualizzare e aggiornare le informazioni che riguardano studenti, corsi e insegnanti. Di seguito sono disponibili alcune schermate dell'esercitazione.

![Pagina Student Index (Indice degli studenti)](intro/_static/students-index.png)

![Pagina Students Edit (Modifica studenti)](intro/_static/student-edit.png)

Lo stile dell'interfaccia utente del sito è simile a quanto è stato generato tramite i modelli predefiniti. L'esercitazione è incentrata su Entity Framework Core con Razor Pages e non sull'interfaccia utente.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>Creare l'app Web di Razor Pages ContosoUniversity

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**.
* Creare una nuova applicazione Web ASP.NET Core. Denominare il progetto **ContosoUniversity**. È importante denominare il progetto *ContosoUniversity* in modo che gli spazi dei nomi corrispondano quando il codice viene copiato/incollato.
* Selezionare **ASP.NET Core 2.1** nell'elenco a discesa, quindi selezionare **Applicazione Web**.

Per le immagini dei passaggi precedenti, vedere [Creare un'app Web Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Eseguire l'app.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Impostare lo stile del sito

Con alcune modifiche è possibile impostare il menu del sito, il layout e la home page. Aggiornare *Pages/Shared/_Layout.cshtml* con le modifiche seguenti:

* Modificare tutte le occorrenze di "ContosoUniversity" in "Contoso University". Le occorrenze sono tre.

* Aggiungere le voci di menu per **Students** (Studenti), **Courses** (Corsi), **Instructors** (Insegnanti) e **Departments** (Dipartimenti) ed eliminare la voce di menu **Contact** (Contatto).

Le modifiche vengono evidenziate. *Non* viene visualizzato l'intero markup.

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

In *Pages/Index.cshtml* sostituire il contenuto del file con il codice seguente. In questo modo il testo su ASP.NET e MVC sarà sostituito dal testo relativo a questa app:

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Creare il modello di dati

Creare le classi delle entità per l'app di Contoso University. Iniziare con le tre entità seguenti:

![Diagramma modello di dati Course/Enrollment/Student (Corso/Iscrizione/Studente)](intro/_static/data-model-diagram.png)

Esiste una relazione uno-a-molti tra le entità `Student` e `Enrollment`. Esiste una relazione uno-a-molti tra le entità `Course` e `Enrollment`. Uno studente può iscriversi a un numero qualsiasi di corsi. Un corso può avere un numero qualsiasi di studenti iscritti.

Nelle sezioni seguenti viene creata una classe per ognuna di queste entità.

### <a name="the-student-entity"></a>Entità Student (Studente)

![Diagramma entità Student (Studente)](intro/_static/student-entity.png)

Creare una cartella *Models* (Modelli). Nella cartella *Models* (Modelli) creare un file di classe denominato *Student.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

La proprietà `ID` diventa la colonna di chiave primaria della tabella di database (DB) che corrisponde a questa classe. Per impostazione predefinita, Entity Framework Core interpreta una proprietà denominata `ID` o `classnameID` come chiave primaria. In `classnameID` `classname` è il nome della classe. La chiave primaria alternativa riconosciuta automaticamente è `StudentID` nell'esempio precedente.

La proprietà `Enrollments` rappresenta una [proprietà di navigazione](/ef/core/modeling/relationships). Le proprietà di navigazione si collegano ad altre entità correlate a questa entità. In questo caso la proprietà `Enrollments` di `Student entity` contiene tutte le entità `Enrollment` correlate a `Student`. Ad esempio, se una riga Student (Studente) nella database presenta due righe Enrollment (Iscrizione) correlate, la proprietà di navigazione `Enrollments` contiene questi due entità `Enrollment`. Una riga `Enrollment` correlata è una riga che contiene il valore della chiave primaria dello studente nella colonna `StudentID`. Si supponga ad esempio che per lo studente con ID = 1 siano presenti due righe nella tabella `Enrollment`. La tabella `Enrollment` contiene due righe con `StudentID` = 1. `StudentID` è una chiave esterna nella tabella `Enrollment` che specifica lo studente nella tabella `Student`.

Se una proprietà di navigazione può contenere più entità, deve essere un tipo elenco, ad esempio `ICollection<T>`. È possibile specificare `ICollection<T>` o un tipo, ad esempio `List<T>` o `HashSet<T>`. Se si specifica `ICollection<T>`, per impostazione predefinita Entity Framework Core crea una raccolta `HashSet<T>`. Le proprietà di navigazione che contengono più entità provengono da relazioni molti-a-molti e uno-a-molti.

### <a name="the-enrollment-entity"></a>Entità Enrollment (Iscrizione)

![Diagramma entità Enrollment (Iscrizione)](intro/_static/enrollment-entity.png)

Nella cartella *Models* (Modelli) creare un file *Enrollment.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

La proprietà `EnrollmentID` è la chiave primaria. Questa entità usa il criterio `classnameID` anziché `ID` come l'entità `Student`. In genere gli sviluppatori scelgono un criterio che usano poi in tutto il modello di dati. In un'esercitazione successiva viene illustrato l'uso di ID senza classname. In questo modo si semplifica l'implementazione dell'ereditarietà nel modello di dati.

La proprietà `Grade` è un oggetto`enum`. Il punto interrogativo dopo la dichiarazione del tipo `Grade` indica che la proprietà `Grade` ammette i valori Null. Un voto il cui valore è Null è diverso da un voto con valore zero. Null significa che un voto è sconosciuto o non è stato ancora assegnato.

La proprietà `StudentID` è una chiave esterna e la proprietà di navigazione corrispondente è `Student`. Un'entità `Enrollment` è associata a un'entità `Student`, pertanto la proprietà contiene un'unica entità `Student`. L'entità `Student` si differenzia dalla proprietà di navigazione `Student.Enrollments` che contiene più entità `Enrollment`.

La proprietà `CourseID` è una chiave esterna e la proprietà di navigazione corrispondente è `Course`. Un'entità `Enrollment` è associata a un'entità `Course`.

Entity Framework Core interpreta una proprietà come chiave esterna se è denominata `<navigation property name><primary key property name>`. Ad esempio, `StudentID` per la proprietà di navigazione `Student`, poiché la chiave primaria `Student` dell'entità è `ID`. Le proprietà di chiave esterna possono anche essere denominate `<primary key property name>`. Ad esempio, `CourseID` poiché la chiave primaria `Course` dell'entità è `CourseID`.

### <a name="the-course-entity"></a>Entità Course (Corso)

![Diagramma entità Course (Corso)](intro/_static/course-entity.png)

Nella cartella *Models* (Modelli) creare un file *Course.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

La proprietà `Enrollments` rappresenta una proprietà di navigazione. È possibile correlare un'entità `Course` a un numero qualsiasi di entità `Enrollment`.

L'attributo `DatabaseGenerated` consente all'app di specificare la chiave primaria anziché chiedere al database di generarla.

## <a name="scaffold-the-student-model"></a>Eseguire lo scaffolding del modello Student (Studente)

In questa sezione viene eseguito lo scaffolding del modello Student (Studente). Lo strumento di scaffolding crea quindi le pagine per le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione) per il modello Student (Studente).

* Compilare il progetto.
* Creare la cartella *Pages/Students*.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Pages/Students* > **Aggiungi** > **Nuovo elemento di scaffolding**.
* Nella finestra di dialogo **Aggiungi scaffolding** selezionare **Pagine Razor che usano Entity Framework (CRUD)** > **Aggiungi**.

Completare la finestra di dialogo **Pagine Razor che usano Entity Framework (CRUD)** :

* Nell'elenco a discesa **Classe modello** selezionare **Student (ContosoUniversity.Models)** .
* Nella riga **Classe contesto di dati** selezionare il segno **+** (più) e modificare il nome generato in **ContosoUniversity.Models.SchoolContext**.
* Nell'elenco a discesa **Classe contesto di dati** selezionare **ContosoUniversity.Models.SchoolContext**.
* Selezionare **Aggiungi**.

![Finestra di dialogo CRUD](intro/_static/s1.png)

Vedere [Eseguire lo scaffolding del modello di filmato](xref:tutorials/razor-pages/model#scaffold-the-movie-model) se si riscontrano problemi con il passaggio precedente.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Eseguire i comandi seguenti per eseguire lo scaffolding del modello Student (Studente).

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

Il processo di scaffolding ha creato e modificato i file seguenti:

### <a name="files-created"></a>File creati

* Pagine Create (Crea), Delete (Elimina), Details (Dettagli), Edit (Modifica) e Index (Indice) di *Pages/Students*.
* *Data/SchoolContext.cs*

### <a name="file-updates"></a>Aggiornamenti dei file

* *Startup.cs* : le modifiche a questo file sono descritte in dettaglio nella sezione successiva.
* *appsettings.json* : è stata aggiunta la stringa di connessione usata per connettersi a un database locale.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Esaminare il contesto registrato con l'inserimento di dipendenze

ASP.NET Core viene compilato tramite [dependency injection](xref:fundamentals/dependency-injection). I servizi, ad esempio il contesto di database di Entity Framework Core, vengono registrati tramite dependency injection durante l'avvio dell'applicazione. Questi servizi vengono quindi offerti ai componenti per cui sono necessari (ad esempio Razor Pages) tramite i parametri del costruttore. Più avanti nell'esercitazione viene illustrato il codice del costruttore che ottiene un'istanza del contesto di database.

Lo strumento di scaffolding ha creato automaticamente un contesto del database e lo ha registrato con il contenitore di inserimento delle dipendenze.

Esaminare il metodo `ConfigureServices` in *Startup.cs*. La riga evidenziata è stata aggiunta dallo scaffolder:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Il nome della stringa di connessione viene passato al contesto chiamando un metodo in un oggetto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Per lo sviluppo locale, il [sistema di configurazione di ASP.NET Core](xref:fundamentals/configuration/index) legge la stringa di connessione dal file *appsettings.json*.

## <a name="update-main"></a>Aggiornare il metodo Main

In *Program.cs* modificare il metodo `Main` per eseguire le operazioni seguenti:

* Ottenere un'istanza del contesto di database dal contenitore di inserimento delle dipendenze.
* Chiamare [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).
* Eliminare il contesto dopo che il metodo `EnsureCreated` è stato completato.

Il codice seguente illustra il file *Program.cs* aggiornato.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated` assicura che esista il database per il contesto. Se esiste, non viene eseguita alcuna azione. Se non esiste, vengono creati il database e tutti i relativi schemi. `EnsureCreated` non usa le migrazioni per creare il database. Un database creato con `EnsureCreated` non potrà essere aggiornato successivamente usando le migrazioni.

`EnsureCreated` viene chiamato all'avvio dell'app e consente il flusso di lavoro seguente:

* Eliminare il database.
* Modificare lo schema di database, ad esempio, aggiungere un campo `EmailAddress`.
* Eseguire l'app.
* `EnsureCreated` crea un database con la colonna `EmailAddress`.

`EnsureCreated` è utile nelle prime fasi di sviluppo quando lo schema è in rapida evoluzione. Più avanti nell'esercitazione il database verrà eliminato e si useranno le migrazioni.

### <a name="test-the-app"></a>Eseguire il test dell'app

Eseguire l'app e accettare l'informativa sui cookie. Questa app non memorizza informazioni personali. È possibile consultare l'informativa sui cookie in [Supporto per il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea](xref:security/gdpr).

* Selezionare il collegamento **Students** (Studenti) e quindi **Crea nuovo**.
* Eseguire il test dei collegamenti Edit (Modifica), Details (Dettagli) e Delete (Elimina).

## <a name="examine-the-schoolcontext-db-context"></a>Esaminare il contesto di database SchoolContext

La classe principale che coordina le funzionalità di Entity Framework Core per un determinato modello di dati è la classe del contesto di database. Il contesto dei dati è derivato da [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il contesto dei dati specifica le entità incluse nel modello di dati. In questo progetto la classe è denominata `SchoolContext`.

Aggiornare *SchoolContext.cs* con il codice seguente:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Il codice evidenziato crea una proprietà [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) per ogni set di entità. Nella terminologia di Entity Framework Core:

* Nella terminologia di Entity Framework, un set di entità corrisponde in genere alla tabella di un database.
* Un'entità corrisponde a una riga nella tabella.

`DbSet<Enrollment>` e `DbSet<Course>` potrebbero essere omessi. Core Entity Framework le include in modo implicito perché l'entità `Student` fa riferimento all'entità `Enrollment` e l'entità `Enrollment` fa riferimento all'entità `Course`. Per questa esercitazione, mantenere `DbSet<Enrollment>` e `DbSet<Course>` in `SchoolContext`.

### <a name="sql-server-express-localdb"></a>LocalDB di SQL Server Express

La stringa di connessione specifica [SQL Server Local DB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). Local DB è una versione leggera del motore di database di SQL Server Express appositamente pensato per lo sviluppo di app e non per la produzione. Local DB viene avviato su richiesta ed eseguito in modalità utente; non richiede quindi una configurazione complessa. Per impostazione predefinita, Local DB crea file di database con estensione *mdf* nella directory `C:/Users/<user>`.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Aggiungere il codice per inizializzare il database con dati di test

Entity Framework Core crea un database vuoto. In questa sezione viene scritto un metodo `Initialize` per popolare il database con i dati di test.

Nella cartella *Data* (Dati) creare un nuovo file di classe denominato *DbInitializer.cs* e aggiungere il codice seguente:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Nota: Il codice precedente usa `Models` per lo spazio dei nomi (`namespace ContosoUniversity.Models`) invece di `Data`. `Models` è coerente con il codice generato dallo scaffolder. Per altre informazioni, vedere [questo problema relativo allo scaffolding in GitHub](https://github.com/aspnet/Scaffolding/issues/822).

Il codice controlla se esistono studenti nel database. Se il database non contiene studenti, il database viene inizializzato con i dati di test. I dati di test vengono caricati in matrici anziché in raccolte `List<T>` per ottimizzare le prestazioni.

Il metodo `EnsureCreated` crea automaticamente il database per il contesto di database. Se il database esiste, `EnsureCreated` restituisce senza modificare il database.

In *Program.cs* modificare il metodo `Main` per chiamare `Initialize`:

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Arrestare l'app se è in esecuzione ed eseguire il comando seguente nella **console di Gestione pacchetti**:

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Arrestare l'app se è in esecuzione ed eliminare il file *CU.db*.

---

## <a name="view-the-db"></a>Visualizzare il database

Il nome del database viene generato dal nome del contesto specificato in precedenza con l'aggiunta di un trattino e un GUID. Di conseguenza, il nome del database sarà "SchoolContext-{GUID}". Il GUID sarà diverso per ogni utente.
Aprire **Esplora oggetti di SQL Server** dal menu **Visualizza** in Visual Studio.
In Esplora oggetti di SQL Server fare clic su **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .

Espandere il nodo **Tabelle**.

Fare clic con il pulsante destro del mouse sulla tabella **Student** (Studente) e fare clic su **Visualizza dati** per visualizzare le colonne create e le righe inserite nella tabella.

## <a name="asynchronous-code"></a>Codice asincrono

La programmazione asincrona è la modalità predefinita per ASP.NET Core ed Entity Framework Core.

Per un server Web è disponibile un numero limitato di thread e in situazioni di carico elevato tutti i thread disponibili potrebbero essere in uso. In queste circostanze il server non può elaborare nuove richieste finché i thread non saranno liberi. Con il codice sincrono, può succedere che molti thread siano vincolati nonostante in quel momento non stiano eseguendo alcuna operazione. Rimangono tuttavia in attesa che l'operazione I/O sia completata. Con il codice asincrono, se un processo è in attesa del completamento dell'operazione I/O, il thread viene liberato e il server lo può usare per l'elaborazione di altre richieste. Di conseguenza, il codice asincrono consente un uso più efficiente delle risorse e il server è abilitato per gestire una maggiore quantità di traffico senza ritardi.

Il codice asincrono comporta un minimo sovraccarico in fase di esecuzione. In caso di traffico ridotto, il calo delle prestazioni è irrilevante, mentre nelle situazioni di traffico elevato, è essenziale ottimizzare le prestazioni potenziali.

Nel codice seguente la parola chiave [async](/dotnet/csharp/language-reference/keywords/async), il valore restituito `Task<T>`, la parola chiave `await` e il metodo `ToListAsync` consentono di eseguire il codice in modo asincrono.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* La parola chiave `async` indica al compilatore di eseguire le operazioni seguenti:
  * Generare callback per parti del corpo del metodo.
  * Creare automaticamente l' oggetto [Task](/dotnet/api/system.threading.tasks.task) restituito. Per altre informazioni, vedere [Tipo restituito Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Il tipo restituito implicito `Task` rappresenta il lavoro in corso.
* La parola chiave `await` indica al compilatore di suddividere il metodo in due parti. La prima parte termina con l'operazione avviata in modo asincrono. La seconda parte viene inserita in un metodo di callback che viene chiamato al termine dell'operazione.
* `ToListAsync` è la versione asincrona del metodo di estensione `ToList`.

Alcuni aspetti da considerare quando si scrive codice asincrono usato da Entity Framework Core:

* Solo le istruzioni che generano query o comandi da inviare al database vengono eseguite in modo asincrono. Sono incluse `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`. Non sono incluse le istruzioni che modificano solo un oggetto `IQueryable`, ad esempio `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Un contesto di Entity Framework Core non è thread-safe. Non tentare di eseguire più operazioni parallelamente.
* Per sfruttare i vantaggi del codice asincrono in termini di prestazioni, verificare che i pacchetti della libreria impiegati, ad esempio per il paging, usino la modalità asincrona per chiamare i metodi di Entity Framework Core che generano query da inviare al database.

Per altre informazioni sulla programmazione asincrona in .NET, vedere [Panoramica della programmazione asincrona](/dotnet/standard/async) e [Programmazione asincrona con async e await](/dotnet/csharp/programming-guide/concepts/async/).

Nella prossima esercitazione, vengono esaminate le operazioni CRUD per creare, leggere, aggiornare, eliminare ed elencare.



## <a name="additional-resources"></a>Risorse aggiuntive

* [Versione YouTube dell'esercitazione](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [avanti](xref:data/ef-rp/crud)

::: moniker-end
