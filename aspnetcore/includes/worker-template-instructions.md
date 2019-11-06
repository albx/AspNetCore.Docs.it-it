# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="327de-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="327de-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="327de-102">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="327de-102">Create a new project.</span></span>
1. <span data-ttu-id="327de-103">Selezionare il **servizio Worker**.</span><span class="sxs-lookup"><span data-stu-id="327de-103">Select **Worker Service**.</span></span> <span data-ttu-id="327de-104">Scegliere **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="327de-104">Select **Next**.</span></span>
1. <span data-ttu-id="327de-105">Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="327de-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="327de-106">Scegliere **Crea**.</span><span class="sxs-lookup"><span data-stu-id="327de-106">Select **Create**.</span></span>
1. <span data-ttu-id="327de-107">Nella finestra di dialogo **Crea un nuovo servizio** del ruolo di lavoro selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="327de-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="327de-108">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="327de-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="327de-109">Creare un nuovo progetto.</span><span class="sxs-lookup"><span data-stu-id="327de-109">Create a new project.</span></span>
1. <span data-ttu-id="327de-110">Selezionare **app** in **.NET Core** nella barra laterale.</span><span class="sxs-lookup"><span data-stu-id="327de-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="327de-111">Selezionare **Worker** in **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="327de-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="327de-112">Scegliere **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="327de-112">Select **Next**.</span></span>
1. <span data-ttu-id="327de-113">Selezionare **.NET Core 3,0** o versione successiva per il **Framework di destinazione**.</span><span class="sxs-lookup"><span data-stu-id="327de-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="327de-114">Scegliere **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="327de-114">Select **Next**.</span></span>
1. <span data-ttu-id="327de-115">Specificare un nome nel campo **nome progetto** .</span><span class="sxs-lookup"><span data-stu-id="327de-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="327de-116">Scegliere **Crea**.</span><span class="sxs-lookup"><span data-stu-id="327de-116">Select **Create**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="327de-117">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="327de-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="327de-118">Usare il servizio di ruolo di lavoro (`worker`) con il comando[dotnet new](/dotnet/core/tools/dotnet-new) da una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="327de-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="327de-119">Nell'esempio seguente viene creata un'app del servizio di ruolo di lavoro denominata `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="327de-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="327de-120">Una cartella per l'app `ContosoWorker` viene creata automaticamente quando viene eseguito il comando.</span><span class="sxs-lookup"><span data-stu-id="327de-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
