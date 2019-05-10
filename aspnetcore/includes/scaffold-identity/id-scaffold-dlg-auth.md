<span data-ttu-id="98ea1-101">Eseguire l'utilità di scaffolding di identità:</span><span class="sxs-lookup"><span data-stu-id="98ea1-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98ea1-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98ea1-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="98ea1-103">Dal **Esplora soluzioni**, fare doppio clic sul progetto > **Add** > **nuovo elemento di scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="98ea1-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="98ea1-104">Dal riquadro sinistro della finestra di **Add Scaffold** finestra di dialogo, seleziona **identità** > **aggiungere**.</span><span class="sxs-lookup"><span data-stu-id="98ea1-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="98ea1-105">Nel **identità ADD** finestra di dialogo, selezionare le opzioni desiderate.</span><span class="sxs-lookup"><span data-stu-id="98ea1-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="98ea1-106">Selezionare la pagina di layout esistente verrà sovrascritto il file di layout con markup non corretto.</span><span class="sxs-lookup"><span data-stu-id="98ea1-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="98ea1-107">Quando un oggetto esistente  *\_layout. cshtml* è selezionato un file, è **non** sovrascritto.</span><span class="sxs-lookup"><span data-stu-id="98ea1-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="98ea1-108">Ad esempio `~/Pages/Shared/_Layout.cshtml` per le pagine Razor `~/Views/Shared/_Layout.cshtml` per i progetti MVC</span><span class="sxs-lookup"><span data-stu-id="98ea1-108">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="98ea1-109">Per usare il contesto dati esistente, selezionare almeno un file per eseguire l'override.</span><span class="sxs-lookup"><span data-stu-id="98ea1-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="98ea1-110">È necessario selezionare almeno un file per aggiungere il contesto dei dati.</span><span class="sxs-lookup"><span data-stu-id="98ea1-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="98ea1-111">Selezionare la classe del contesto dati.</span><span class="sxs-lookup"><span data-stu-id="98ea1-111">Select your data context class.</span></span>
  * <span data-ttu-id="98ea1-112">Selezionare **aggiungere**.</span><span class="sxs-lookup"><span data-stu-id="98ea1-112">Select **ADD**.</span></span>
* <span data-ttu-id="98ea1-113">Per creare un nuovo contesto utente e possibilmente creare una classe utente personalizzata per l'identità:</span><span class="sxs-lookup"><span data-stu-id="98ea1-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="98ea1-114">Selezionare il **+** per creare un nuovo pulsante **classe contesto dati**.</span><span class="sxs-lookup"><span data-stu-id="98ea1-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="98ea1-115">Selezionare **aggiungere**.</span><span class="sxs-lookup"><span data-stu-id="98ea1-115">Select **ADD**.</span></span>

<span data-ttu-id="98ea1-116">Nota: Se si crea un nuovo contesto utente, non è necessario selezionare un file per eseguire l'override.</span><span class="sxs-lookup"><span data-stu-id="98ea1-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="98ea1-117">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="98ea1-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="98ea1-118">Se l'utilità di scaffolding di ASP.NET Core non è stato precedentemente installato, installarlo ora:</span><span class="sxs-lookup"><span data-stu-id="98ea1-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="98ea1-119">Aggiungere un riferimento al pacchetto [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) al progetto (\*csproj) file.</span><span class="sxs-lookup"><span data-stu-id="98ea1-119">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="98ea1-120">Eseguire il comando seguente nella directory del progetto:</span><span class="sxs-lookup"><span data-stu-id="98ea1-120">Run the following command in the project directory:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="98ea1-121">Eseguire il comando seguente per elencare le opzioni di utilità di scaffolding di identità:</span><span class="sxs-lookup"><span data-stu-id="98ea1-121">Run the following command to list the Identity scaffolder options:</span></span>

```console
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="98ea1-122">Nella cartella del progetto, eseguire l'utilità di scaffolding di identità con le opzioni desiderate.</span><span class="sxs-lookup"><span data-stu-id="98ea1-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="98ea1-123">Ad esempio, per la configurazione di identità con l'interfaccia utente predefinita e il numero minimo di file, eseguire il comando seguente.</span><span class="sxs-lookup"><span data-stu-id="98ea1-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="98ea1-124">Usare il nome completo corretto per il contesto del database:</span><span class="sxs-lookup"><span data-stu-id="98ea1-124">Use the correct fully qualified name for your DB context:</span></span>

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

<span data-ttu-id="98ea1-125">PowerShell Usa un punto e virgola come separatore di comandi.</span><span class="sxs-lookup"><span data-stu-id="98ea1-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="98ea1-126">Quando si usa PowerShell, eseguire l'escape di punti e virgola nell'elenco di file o inserire l'elenco di file tra virgolette doppie.</span><span class="sxs-lookup"><span data-stu-id="98ea1-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="98ea1-127">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="98ea1-127">For example:</span></span>

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="98ea1-128">Se si esegue l'utilità di scaffolding di identità senza specificare il `--files` flag o `--useDefaultUI` flag, tutte le pagine dell'interfaccia utente di identità disponibili verranno create nel progetto.</span><span class="sxs-lookup"><span data-stu-id="98ea1-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---
