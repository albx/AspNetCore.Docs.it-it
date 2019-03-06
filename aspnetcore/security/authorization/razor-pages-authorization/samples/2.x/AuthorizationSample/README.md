# <a name="aspnet-core-authorization-sample"></a>Esempio di autorizzazione di ASP.NET Core

In questo esempio viene illustrato l'utilizzo di autorizzazione di Razor Pages dalle convenzioni. In questo esempio illustra le funzionalità descritte nel [convenzioni di autorizzazione di Razor Pages](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization) argomento.

Autorizzazione utente in questo esempio Usa l'autenticazione dei cookie funzionalità descritte nel [Usa autenticazione di cookie senza ASP.NET Core Identity](https://docs.microsoft.com/aspnet/core/security/authentication/cookie) argomento. I concetti e gli esempi illustrati in questo argomento sono ugualmente applicabili alle App che usano ASP.NET Core Identity. Per informazioni sull'uso di ASP.NET Core Identity, vedere [Introduzione all'identità in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/authentication/identity).

Usare l'indirizzo di posta elettronica **maria.rodriguez@contoso.com** per autenticare l'utente con una password. L'utente viene autenticato nel `AuthenticateUser` metodo nella *Pages/Account/Login.cshtml.cs* file. In un esempio reale, l'utente viene autenticato in un database.

## <a name="examples-in-this-sample"></a>Esempi

| Funzionalità | Descrizione |
| --- | --- |
| [AuthorizePage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) | Aggiunge un [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) alla pagina con il percorso specificato. |
| [AuthorizeFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) | Aggiunge un [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) per tutte le pagine in una cartella con il percorso specificato. |
| [AllowAnonymousToPage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) | Aggiunge un [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) a una pagina con il percorso specificato. |
| [AllowAnonymousToFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) | Aggiunge un [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) per tutte le pagine in una cartella con il percorso specificato. |
