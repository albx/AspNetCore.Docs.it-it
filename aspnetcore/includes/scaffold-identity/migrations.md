<span data-ttu-id="a4630-101">È necessario il codice di database di identità generato [migrazioni di Entity Framework Core](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="a4630-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="a4630-102">Creare una migrazione e aggiornare il database.</span><span class="sxs-lookup"><span data-stu-id="a4630-102">Create a migration and update the database.</span></span> <span data-ttu-id="a4630-103">Ad esempio, eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="a4630-103">For example, run the following commands:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a4630-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4630-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a4630-105">In Visual Studio **Console di gestione pacchetti**:</span><span class="sxs-lookup"><span data-stu-id="a4630-105">In the Visual Studio **Package Manager Console**:</span></span>

```PMC
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a4630-106">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="a4630-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="a4630-107">Il parametro di nome "CreateIdentitySchema" per il `Add-Migration` comando è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="a4630-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="a4630-108">`"CreateIdentitySchema"` descrive la migrazione.</span><span class="sxs-lookup"><span data-stu-id="a4630-108">`"CreateIdentitySchema"` describes the migration.</span></span>
