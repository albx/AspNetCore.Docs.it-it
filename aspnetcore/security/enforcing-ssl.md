---
title: Applicare HTTPS in ASP.NET Core
author: rick-anderson
description: Informazioni su come richiedere HTTPS/TLS in un'app Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
uid: security/enforcing-ssl
ms.openlocfilehash: 032105c67e15ab94635ae6fadea103450c7eb0fb
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944239"
---
# <a name="enforce-https-in-aspnet-core"></a>Applicare HTTPS in ASP.NET Core

Di [Rick Anderson](https://twitter.com/RickAndMSFT)

Questo documento illustra come:

* Richiedi HTTPS per tutte le richieste.
* Reindirizzare tutte le richieste HTTP a HTTPS.

Nessuna API può impedire a un client di inviare dati sensibili alla prima richiesta.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a>Progetti API
>
> **Non** usare [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) su API Web che ricevono informazioni riservate. `RequireHttpsAttribute` usa i codici di stato HTTP per reindirizzare i browser da HTTP a HTTPS. I client API potrebbero non comprendere o obbedire ai reindirizzamenti da HTTP a HTTPS. Tali client possono inviare informazioni tramite HTTP. Le API Web devono:
>
> * Non è in ascolto su HTTP.
> * Chiudere la connessione con il codice di stato 400 (richiesta non valida) e non soddisfare la richiesta.
>
> ## <a name="hsts-and-api-projects"></a>HSTS e progetti API
>
> I progetti API predefiniti non includono [HSTS](#hsts) perché HSTS è in genere un'istruzione solo del browser. Altri chiamanti, ad esempio le applicazioni per telefoni o desktop, **non** rispettano l'istruzione. Anche all'interno dei browser, una singola chiamata autenticata a un'API su HTTP presenta rischi per le reti non sicure. L'approccio sicuro consiste nel configurare i progetti API in modo che ascoltino e rispondono solo tramite HTTPS.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a>Progetti API
>
> **Non** usare [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) su API Web che ricevono informazioni riservate. `RequireHttpsAttribute` usa i codici di stato HTTP per reindirizzare i browser da HTTP a HTTPS. I client API potrebbero non comprendere o obbedire ai reindirizzamenti da HTTP a HTTPS. Tali client possono inviare informazioni tramite HTTP. Le API Web devono:
>
> * Non è in ascolto su HTTP.
> * Chiudere la connessione con il codice di stato 400 (richiesta non valida) e non soddisfare la richiesta.

::: moniker-end

## <a name="require-https"></a>Richiedi HTTPS

Si consiglia di usare le app Web ASP.NET Core di produzione:

* Middleware di reindirizzamento HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) per reindirizzare le richieste HTTP a HTTPS.
* HSTS middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) per inviare intestazioni HTTP Strict Transport Security Protocol (HSTS) ai client.

> [!NOTE]
> Le app distribuite in una configurazione di proxy inverso consentono al proxy di gestire la sicurezza della connessione (HTTPS). Se il proxy gestisce anche il reindirizzamento HTTPS, non è necessario usare il middleware di reindirizzamento HTTPS. Se il server proxy gestisce anche la scrittura delle intestazioni HSTS, ad esempio il [supporto nativo di HSTS in IIS 10,0 (1709) o versioni successive](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support), il middleware HSTS non è richiesto dall'app. Per altre informazioni, vedere [rifiutare esplicitamente HTTPS/HSTS durante la creazione del progetto](#opt-out-of-httpshsts-on-project-creation).

### <a name="usehttpsredirection"></a>UseHttpsRedirection

Il codice seguente chiama `UseHttpsRedirection` nella classe `Startup`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

Il codice evidenziato precedente:

* Usa il valore predefinito [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).
* Usa il valore predefinito [HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), a meno che non venga sottoposto a override dalla variabile di ambiente `ASPNETCORE_HTTPS_PORT` o da [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).

È consigliabile usare reindirizzamenti temporanei anziché reindirizzamenti permanenti. La memorizzazione nella cache dei collegamenti può causare un comportamento instabile negli ambienti di sviluppo. Se si preferisce inviare un codice di stato di reindirizzamento permanente quando l'app si trova in un ambiente non di sviluppo, vedere la sezione [configurare reindirizzamenti permanenti in produzione](#configure-permanent-redirects-in-production) . È consigliabile usare [HSTS](#http-strict-transport-security-protocol-hsts) per segnalare ai client che solo le richieste di risorse protette devono essere inviate all'app (solo nell'ambiente di produzione).

### <a name="port-configuration"></a>Configurazione della porta

Una porta deve essere disponibile affinché il middleware reindirizzi una richiesta non sicura a HTTPS. Se non è disponibile alcuna porta:

* Il reindirizzamento a HTTPS non viene eseguito.
* Il middleware registra l'avviso "Impossibile determinare la porta HTTPS per il reindirizzamento".

Specificare la porta HTTPS usando uno degli approcci seguenti:

* Impostare [HttpsRedirectionOptions. HttpsPort](#options).

::: moniker range=">= aspnetcore-3.0"

* Impostare l' [impostazione host](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port)`https_port`:

  * Nella configurazione host.
  * Impostando la variabile di ambiente `ASPNETCORE_HTTPS_PORT`.
  * Aggiungendo una voce di primo livello in *appSettings. JSON*:

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* Indicare una porta con lo schema protetto usando la [variabile di ambiente ASPNETCORE_URLS](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls). La variabile di ambiente configura il server. Il middleware individua indirettamente la porta HTTPS tramite <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>. Questo approccio non funziona nelle distribuzioni di proxy inverso.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* Impostare l' [impostazione host](xref:fundamentals/host/web-host#https-port)`https_port`:

  * Nella configurazione host.
  * Impostando la variabile di ambiente `ASPNETCORE_HTTPS_PORT`.
  * Aggiungendo una voce di primo livello in *appSettings. JSON*:

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* Indicare una porta con lo schema protetto usando la [variabile di ambiente ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls). La variabile di ambiente configura il server. Il middleware individua indirettamente la porta HTTPS tramite <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>. Questo approccio non funziona nelle distribuzioni di proxy inverso.

::: moniker-end

* In sviluppo impostare un URL HTTPS in *launchsettings. JSON*. Abilitare HTTPS quando viene usato IIS Express.

* Configurare un endpoint URL HTTPS per una distribuzione perimetrale pubblica del server [gheppio](xref:fundamentals/servers/kestrel) o del server [http. sys](xref:fundamentals/servers/httpsys) . L'app usa solo **una porta HTTPS** . Il middleware individua la porta tramite <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.

> [!NOTE]
> Quando un'app viene eseguita in una configurazione di proxy inverso, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> non è disponibile. Impostare la porta usando uno degli altri approcci descritti in questa sezione.

### <a name="edge-deployments"></a>Distribuzioni Edge 

Quando gheppio o HTTP. sys viene usato come server perimetrale pubblico, gheppio o HTTP. sys deve essere configurato in modo da essere in ascolto su entrambi:

* La porta protetta in cui viene reindirizzato il client, in genere 443 in produzione e 5001 in fase di sviluppo.
* Porta non sicura, in genere 80 in produzione e 5000 in fase di sviluppo.

Per consentire all'app di ricevere una richiesta non sicura e reindirizzare il client alla porta protetta, la porta non protetta deve essere accessibile dal client.

Per altre informazioni, vedere [configurazione dell'endpoint gheppio](xref:fundamentals/servers/kestrel#endpoint-configuration) o <xref:fundamentals/servers/httpsys>.

### <a name="deployment-scenarios"></a>Scenari di distribuzione

Qualsiasi firewall tra il client e il server deve avere anche porte di comunicazione aperte per il traffico.

Se le richieste vengono inviate in una configurazione del proxy inverso, usare il [middleware delle intestazioni inoltro](xref:host-and-deploy/proxy-load-balancer) prima di chiamare il middleware di reindirizzamento HTTPS. Il middleware intestazioni inoltri aggiorna il `Request.Scheme`usando l'intestazione `X-Forwarded-Proto`. Il middleware consente l'uso corretto degli URI di reindirizzamento e di altri criteri di sicurezza. Quando il middleware delle intestazioni inoltrate non viene usato, l'app back-end potrebbe non ricevere lo schema corretto e finire in un ciclo di reindirizzamento. Un messaggio di errore dell'utente finale comune è che si sono verificati troppi reindirizzamenti.

Quando si esegue la distribuzione nel servizio app Azure, seguire le istruzioni riportate in [esercitazione: associare un certificato SSL personalizzato esistente ad app Web di Azure](/azure/app-service/app-service-web-tutorial-custom-ssl).

### <a name="options"></a>Options

Il codice evidenziato seguente chiama [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) per configurare le opzioni del middleware:


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


La chiamata di `AddHttpsRedirection` è necessaria solo per modificare i valori di `HttpsPort` o `RedirectStatusCode`.

Il codice evidenziato precedente:

* Imposta [HttpsRedirectionOptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) su <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, che corrisponde al valore predefinito. Usare i campi della classe <xref:Microsoft.AspNetCore.Http.StatusCodes> per le assegnazioni `RedirectStatusCode`.
* Imposta la porta HTTPS su 5001.

#### <a name="configure-permanent-redirects-in-production"></a>Configurare reindirizzamenti permanenti nell'ambiente di produzione

Per impostazione predefinita, il middleware Invia un [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) con tutti i reindirizzamenti. Se si preferisce inviare un codice di stato di reindirizzamento permanente quando l'app si trova in un ambiente non di sviluppo, eseguire il wrapping della configurazione delle opzioni middleware in un controllo condizionale per un ambiente non di sviluppo.

::: moniker range=">= aspnetcore-3.0"

Quando si configurano i servizi in *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Quando si configurano i servizi in *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a>Approccio alternativo del middleware di reindirizzamento HTTPS

Un'alternativa all'utilizzo del middleware di reindirizzamento HTTPS (`UseHttpsRedirection`) consiste nell'utilizzo del middleware di riscrittura URL (`AddRedirectToHttps`). `AddRedirectToHttps` inoltre possibile impostare il codice di stato e la porta quando viene eseguito il reindirizzamento. Per altre informazioni, vedere [middleware riscrittura URL](xref:fundamentals/url-rewriting).

Quando si reindirizza a HTTPS senza la necessità di regole di reindirizzamento aggiuntive, è consigliabile usare il middleware di reindirizzamento HTTPS (`UseHttpsRedirection`) descritto in questo argomento.

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a>Protocollo di sicurezza del trasporto HTTP Strict (HSTS)

Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [http Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) è un miglioramento della sicurezza esplicito che viene specificato da un'app Web tramite l'uso di un'intestazione di risposta. Quando un [browser che supporta HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) riceve questa intestazione:

* Il browser archivia la configurazione per il dominio che impedisce l'invio di comunicazioni tramite HTTP. Il browser forza tutte le comunicazioni su HTTPS.
* Il browser impedisce all'utente di usare certificati non attendibili o non validi. Il browser Disabilita i prompt che consentono a un utente di considerare temporaneamente attendibile tale certificato.

Poiché HSTS viene applicato dal client, presenta alcune limitazioni:

* Il client deve supportare HSTS.
* HSTS richiede almeno una richiesta HTTPS corretta per stabilire il criterio HSTS.
* L'applicazione deve controllare ogni richiesta HTTP e reindirizzare o rifiutare la richiesta HTTP.

ASP.NET Core 2,1 e versioni successive implementa HSTS con il metodo di estensione `UseHsts`. Il codice seguente chiama `UseHsts` quando l'app non è in [modalità di sviluppo](xref:fundamentals/environments):

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

`UseHsts` non è consigliabile per lo sviluppo poiché le impostazioni HSTS sono altamente memorizzabili nella cache dai browser. Per impostazione predefinita, `UseHsts` esclude l'indirizzo di loopback locale.

Per gli ambienti di produzione che implementano HTTPS per la prima volta, impostare il valore iniziale [HstsOptions. MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) su un valore ridotto utilizzando uno dei metodi <xref:System.TimeSpan>. Impostare il valore da ore a non più di un singolo giorno nel caso in cui sia necessario ripristinare l'infrastruttura HTTPS su HTTP. Quando si è certi della sostenibilità della configurazione HTTPS, aumentare il valore di HSTS max-age; un valore di uso comune è di un anno.

Il codice seguente:


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* Imposta il parametro preload dell'intestazione Strict-Transport-Security. Il precaricamento non fa parte della [specifica RFC HSTS](https://tools.ietf.org/html/rfc6797), ma è supportato dai Web browser per precaricare i siti HSTS in una nuova installazione. Per altre informazioni, vedere [https://hstspreload.org/](https://hstspreload.org/).
* Abilita [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), che applica il criterio HSTS ai sottodomini host.
* Imposta in modo esplicito il parametro max-age dell'intestazione Strict-Transport-Security su 60 giorni. Se non è impostato, il valore predefinito è 30 giorni. Per ulteriori informazioni, vedere la [direttiva max-age](https://tools.ietf.org/html/rfc6797#section-6.1.1) .
* Aggiunge `example.com` all'elenco di host da escludere.

`UseHsts` esclude gli host di loopback seguenti:

* `localhost`: indirizzo di loopback IPv4.
* `127.0.0.1`: indirizzo di loopback IPv4.
* `[::1]`: indirizzo di loopback IPv6.

## <a name="opt-out-of-httpshsts-on-project-creation"></a>Rifiutare esplicitamente il protocollo HTTPS/HSTS durante la creazione del progetto

In alcuni scenari di servizi back-end in cui la sicurezza della connessione viene gestita al bordo pubblico della rete, non è necessario configurare la sicurezza della connessione in ogni nodo. Le app Web generate dai modelli in Visual Studio o dal comando [DotNet New](/dotnet/core/tools/dotnet-new) abilitano il [reindirizzamento HTTPS](#require-https) e [HSTS](#http-strict-transport-security-protocol-hsts). Per le distribuzioni che non richiedono questi scenari, è possibile rifiutare esplicitamente HTTPS/HSTS quando l'app viene creata dal modello.

Per rifiutare esplicitamente il protocollo HTTPS/HSTS:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Deselezionare la casella **di controllo Configura per HTTPS** .

::: moniker range=">= aspnetcore-3.0"

![Finestra di dialogo nuova applicazione Web ASP.NET Core che mostra la casella di controllo Configura per HTTPS deselezionata.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Finestra di dialogo nuova applicazione Web ASP.NET Core che mostra la casella di controllo Configura per HTTPS deselezionata.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli) 

Usare l'opzione `--no-https`. Esempio:

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>Considera attendibile il certificato di sviluppo HTTPS ASP.NET Core in Windows e macOS

Il .NET Core SDK include un certificato di sviluppo HTTPS. Il certificato viene installato come parte dell'esperienza di prima esecuzione. Ad esempio, `dotnet --info` produce un output simile al seguente:

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

L'installazione di .NET Core SDK include l'installazione del certificato di sviluppo HTTPS ASP.NET Core nell'archivio certificati utente locale. Il certificato è stato installato, ma non è attendibile. Per considerare attendibile il certificato, eseguire il passaggio unico per eseguire lo strumento di `dev-certs` DotNet:

```dotnetcli
dotnet dev-certs https --trust
```

Il comando seguente consente di visualizzare informazioni della Guida sullo strumento `dev-certs`:

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>Come configurare un certificato per sviluppatori per Docker

Vedere [il problema in GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6199).

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a>Considera attendibile il certificato HTTPS del sottosistema Windows per Linux

Il sottosistema Windows per Linux (WSL) genera un certificato autofirmato HTTPS. Per configurare l'archivio certificati di Windows per considerare attendibile il certificato WSL:

* Eseguire il comando seguente per esportare il certificato generato da WSL: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`
* In una finestra di WSL eseguire il comando seguente: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`

  Il comando precedente imposta le variabili di ambiente in modo che Linux usi il certificato attendibile di Windows.

## <a name="troubleshoot-certificate-problems"></a>Risolvere i problemi relativi ai certificati

Questa sezione fornisce assistenza quando il certificato di sviluppo ASP.NET Core HTTPS è stato [installato e considerato attendibile](#trust), ma sono ancora presenti avvisi del browser che non sono attendibili per il certificato. Il certificato di sviluppo HTTPS ASP.NET Core viene usato da [gheppio](xref:fundamentals/servers/kestrel).

### <a name="all-platforms---certificate-not-trusted"></a>Tutte le piattaforme-certificato non attendibile

Eseguire i comandi seguenti:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Chiude tutte le istanze del browser aperte. Aprire una nuova finestra del browser per l'app. Il trust tra certificati viene memorizzato nella cache dai browser.

I comandi precedenti risolvono la maggior parte dei problemi di attendibilità del browser. Se il browser non considera ancora attendibile il certificato, attenersi ai suggerimenti specifici della piattaforma seguenti.

### <a name="docker---certificate-not-trusted"></a>Docker: certificato non attendibile

* Eliminare la cartella *C:\Users\{User} \AppData\Roaming\ASP.NET\Https*
* Pulire la soluzione. Eliminare le cartelle *bin* e *obj*.
* Riavviare lo strumento di sviluppo. Ad esempio, Visual Studio, Visual Studio Code o Visual Studio per Mac.

### <a name="windows---certificate-not-trusted"></a>Windows: certificato non attendibile

* Controllare i certificati nell'archivio certificati. Deve essere presente un certificato di `localhost` con il nome descrittivo `ASP.NET Core HTTPS development certificate` sia in `Current User > Personal > Certificates` che in `Current User > Trusted root certification authorities > Certificates`
* Rimuovere tutti i certificati trovati dalle autorità di certificazione radice personali e attendibili. Non **rimuovere il** certificato localhost IIS Express.
* Eseguire i comandi seguenti:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Chiude tutte le istanze del browser aperte. Aprire una nuova finestra del browser per l'app.

### <a name="os-x---certificate-not-trusted"></a>OS X-certificato non attendibile

* Aprire l'accesso keychain.
* Selezionare il keychain di sistema.
* Verificare la presenza di un certificato localhost.
* Verificare che contenga un simbolo di `+` sull'icona per indicare la relativa attendibilità per tutti gli utenti.
* Rimuovere il certificato dal keychain di sistema.
* Eseguire i comandi seguenti:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Chiude tutte le istanze del browser aperte. Aprire una nuova finestra del browser per l'app.

Per la risoluzione dei problemi relativi ai certificati con Visual Studio, vedere [errore HTTPS con IIS Express (ASPNET/AspNetCore #16892)](https://github.com/aspnet/AspNetCore/issues/16892) .

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a>IIS Express certificato SSL usato con Visual Studio

Per risolvere i problemi relativi al certificato IIS Express, selezionare **Ripristina** dal programma di installazione di Visual Studio.

## <a name="additional-information"></a>Informazioni aggiuntive

* <xref:host-and-deploy/proxy-load-balancer>
* [Ospitare ASP.NET Core in Linux con Apache: configurazione HTTPS](xref:host-and-deploy/linux-apache#https-configuration)
* [ASP.NET Core host in Linux con Nginx: configurazione HTTPS](xref:host-and-deploy/linux-nginx#https-configuration)
* [Come configurare SSL in IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [Supporto del browser OWASP HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
