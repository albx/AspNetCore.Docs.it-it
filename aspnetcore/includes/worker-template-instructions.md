# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Creare un nuovo progetto.
1. Selezionare il **servizio Worker**. Selezionare **Avanti**.
1. Specificare il nome di un progetto nel campo **Nome progetto** oppure accettare il nome predefinito. Selezionare **Create**.
1. Nella finestra di dialogo **Crea un nuovo servizio** del ruolo di lavoro selezionare **Crea**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

1. Creare un nuovo progetto.
1. Selezionare **app** in **.NET Core** nella barra laterale.
1. Selezionare **Worker** in **ASP.NET Core**. Selezionare **Avanti**.
1. Selezionare **.NET Core 3,0** per il **Framework di destinazione**. Selezionare **Avanti**.
1. Specificare un nome nel campo **nome progetto** . Selezionare **Create**.

# <a name="net-core-clitabnetcore-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Usare il servizio di ruolo di lavoro (`worker`) con il comando[dotnet new](/dotnet/core/tools/dotnet-new) da una shell dei comandi. Nell'esempio seguente viene creata un'app del servizio di ruolo di lavoro denominata `ContosoWorker`. Una cartella per l'app `ContosoWorker` viene creata automaticamente quando viene eseguito il comando.

```dotnetcli
dotnet new worker -o ContosoWorker
```
