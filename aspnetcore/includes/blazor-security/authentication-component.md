<span data-ttu-id="994ab-101">La pagina prodotta dal componente `Authentication` (*pages/Authentication. Razor*) definisce le route necessarie per la gestione di diverse fasi di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="994ab-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="994ab-102">Componente `RemoteAuthenticatorView`:</span><span class="sxs-lookup"><span data-stu-id="994ab-102">The `RemoteAuthenticatorView` component:</span></span>

* <span data-ttu-id="994ab-103">Viene fornito dal pacchetto di `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.</span><span class="sxs-lookup"><span data-stu-id="994ab-103">Is provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span>
* <span data-ttu-id="994ab-104">Gestisce l'esecuzione delle azioni appropriate in ogni fase dell'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="994ab-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
