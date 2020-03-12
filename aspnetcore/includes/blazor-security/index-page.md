La pagina di indice (*wwwroot/index.html*) include uno script che definisce la `AuthenticationService` in JavaScript. `AuthenticationService` gestisce i dettagli di basso livello del protocollo OIDC. L'app chiama internamente i metodi definiti nello script per eseguire le operazioni di autenticazione.

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
