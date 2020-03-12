<span data-ttu-id="f3759-101">Il componente `LoginDisplay` (*Shared/LoginDisplay. Razor*) viene sottoposto a rendering nel componente `MainLayout` (*Shared/MainLayout. Razor*) e gestisce i comportamenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="f3759-101">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="f3759-102">Per utenti autenticati:</span><span class="sxs-lookup"><span data-stu-id="f3759-102">For authenticated users:</span></span>
  * <span data-ttu-id="f3759-103">Visualizza il nome utente corrente.</span><span class="sxs-lookup"><span data-stu-id="f3759-103">Displays the current username.</span></span>
  * <span data-ttu-id="f3759-104">Offre un pulsante per disconnettersi dall'app.</span><span class="sxs-lookup"><span data-stu-id="f3759-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="f3759-105">Per gli utenti anonimi, offre la possibilità di eseguire l'accesso.</span><span class="sxs-lookup"><span data-stu-id="f3759-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
