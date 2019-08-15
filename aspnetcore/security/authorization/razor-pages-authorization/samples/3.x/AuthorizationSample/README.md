# <a name="aspnet-core-authorization-sample"></a><span data-ttu-id="255ce-101">Esempio di autorizzazione ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="255ce-101">ASP.NET Core Authorization Sample</span></span>

<span data-ttu-id="255ce-102">In questo esempio viene illustrato l'utilizzo di Razor Pages autorizzazione per convenzione.</span><span class="sxs-lookup"><span data-stu-id="255ce-102">This sample illustrates use of Razor Pages authorization by conventions.</span></span> <span data-ttu-id="255ce-103">In questo esempio vengono illustrate le funzionalità descritte nell'argomento [Razor Pages convenzioni di autorizzazione](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization) .</span><span class="sxs-lookup"><span data-stu-id="255ce-103">This sample demonstrates the features described in the [Razor Pages authorization conventions](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization) topic.</span></span>

<span data-ttu-id="255ce-104">L'autorizzazione dell'utente in questo esempio usa le funzionalità di autenticazione dei cookie descritte nell'argomento [usare l'autenticazione tramite cookie senza ASP.NET Core Identity](https://docs.microsoft.com/aspnet/core/security/authentication/cookie) .</span><span class="sxs-lookup"><span data-stu-id="255ce-104">User authorization in this sample uses the cookie authentication features described in the [Use cookie authentication without ASP.NET Core Identity](https://docs.microsoft.com/aspnet/core/security/authentication/cookie) topic.</span></span> <span data-ttu-id="255ce-105">I concetti e gli esempi illustrati in questo argomento si applicano ugualmente alle app che usano ASP.NET Core identità.</span><span class="sxs-lookup"><span data-stu-id="255ce-105">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="255ce-106">Per informazioni sull'uso di ASP.NET Core identità, vedere [Introduzione all'identità su ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="255ce-106">For information on using ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/authentication/identity).</span></span>

<span data-ttu-id="255ce-107">Usare l'indirizzo **maria.rodriguez@contoso.com** di posta elettronica per autenticare l'utente con qualsiasi password.</span><span class="sxs-lookup"><span data-stu-id="255ce-107">Use the email address **maria.rodriguez@contoso.com** to authenticate the user with any password.</span></span> <span data-ttu-id="255ce-108">L'utente viene autenticato nel `AuthenticateUser` metodo nel file pages */account/login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="255ce-108">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="255ce-109">In un esempio reale, l'utente viene autenticato a fronte di un database.</span><span class="sxs-lookup"><span data-stu-id="255ce-109">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="255ce-110">Esempi</span><span class="sxs-lookup"><span data-stu-id="255ce-110">Examples in this sample</span></span>

| <span data-ttu-id="255ce-111">Funzionalità</span><span class="sxs-lookup"><span data-stu-id="255ce-111">Feature</span></span> | <span data-ttu-id="255ce-112">Descrizione</span><span class="sxs-lookup"><span data-stu-id="255ce-112">Description</span></span> |
| --- | --- |
| [<span data-ttu-id="255ce-113">AuthorizePage</span><span class="sxs-lookup"><span data-stu-id="255ce-113">AuthorizePage</span></span>](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) | <span data-ttu-id="255ce-114">Aggiunge un [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) alla pagina con il percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="255ce-114">Adds an [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page with the specified path.</span></span> |
| [<span data-ttu-id="255ce-115">AuthorizeFolder</span><span class="sxs-lookup"><span data-stu-id="255ce-115">AuthorizeFolder</span></span>](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) | <span data-ttu-id="255ce-116">Aggiunge un [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) a tutte le pagine di una cartella con il percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="255ce-116">Adds an [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to all of the pages in a folder with the specified path.</span></span> |
| [<span data-ttu-id="255ce-117">AllowAnonymousToPage</span><span class="sxs-lookup"><span data-stu-id="255ce-117">AllowAnonymousToPage</span></span>](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) | <span data-ttu-id="255ce-118">Aggiunge un [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) a una pagina con il percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="255ce-118">Adds an [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) to a page with the specified path.</span></span> |
| [<span data-ttu-id="255ce-119">AllowAnonymousToFolder</span><span class="sxs-lookup"><span data-stu-id="255ce-119">AllowAnonymousToFolder</span></span>](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) | <span data-ttu-id="255ce-120">Aggiunge un [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) a tutte le pagine di una cartella con il percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="255ce-120">Adds an [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) to all of the pages in a folder with the specified path.</span></span> |