---
title: Implementazione del server Web HTTP.sys in ASP.NET Core
author: guardrex
description: Informazioni su HTTP.sys, un server Web per ASP.NET Core in Windows. Basato sul driver in modalità kernel HTTP.sys, HTTP.sys è un'alternativa a Kestrel che consente la connessione diretta a Internet senza IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/14/2019
uid: fundamentals/servers/httpsys
ms.openlocfilehash: f9e564119604e13bdc48a6c36de7d283c56f68f0
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333809"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>Implementazione del server Web HTTP.sys in ASP.NET Core

Di [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) e [Luke Latham](https://github.com/guardrex)

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) è un [server Web per ASP.NET Core](xref:fundamentals/servers/index) che può essere eseguito solo in Windows. HTTP.sys è un'alternativa al server [Kestrel](xref:fundamentals/servers/kestrel) e offre alcune funzionalità non presenti in Kestrel.

> [!IMPORTANT]
> HTTP.sys non è compatibile con il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) e non può essere usato con IIS o IIS Express.

HTTP.sys supporta le funzionalità seguenti:

* [Autenticazione di Windows](xref:security/authentication/windowsauth)
* Condivisione delle porte
* HTTPS con SNI
* HTTP/2 su TLS (Windows 10 o versioni successive)
* Trasmissione diretta dei file
* Memorizzazione nella cache delle risposte
* WebSockets (Windows 8 o versioni successive)

Versioni di Windows supportate:

* Windows 7 o versione successiva
* Windows Server 2008 R2 o versioni successive

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Quando usare HTTP.sys

HTTP.sys è utile per le distribuzioni nei casi seguenti:

* È necessario esporre il server direttamente a Internet senza usare IIS.

  ![HTTP.sys comunica direttamente con Internet](httpsys/_static/httpsys-to-internet.png)

* Una distribuzione interna richiede una funzionalità non disponibile in Kestrel, ad esempio l'[autenticazione di Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys comunica direttamente con la rete interna](httpsys/_static/httpsys-to-internal.png)

HTTP.sys è una tecnologia consolidata che protegge da molti tipi di attacchi e offre l'affidabilità, la sicurezza e la scalabilità di un server Web con funzionalità complete. IIS viene eseguito come listener HTTP in HTTP.sys.

## <a name="http2-support"></a>Supporto per HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) è abilitato per le app ASP.NET Core se vengono soddisfatti i requisiti di base seguenti:

* Windows Server 2016/Windows 10 o versioni successive
* Connessione [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* Connessione TLS 1.2 o successiva

::: moniker range=">= aspnetcore-2.2"

Se viene stabilita una connessione HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) corrisponde a `HTTP/2`.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Se viene stabilita una connessione HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) corrisponde a `HTTP/1.1`.

::: moniker-end

HTTP/2 è abilitato per impostazione predefinita. Se non viene stabilita una connessione HTTP/2, la connessione esegue il fallback a HTTP/1.1. In una versione futura di Windows sarà disponibile il flag di configurazione di HTTP/2 e sarà possibile disabilitare il protocollo HTTP/2 con HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Autenticazione in modalità kernel con Kerberos

Per la delega all'autenticazione in modalità kernel, HTTP.sys usa il protocollo di autenticazione Kerberos. L'autenticazione in modalità utente non è supportata con Kerberos e HTTP.sys. È necessario usare l'account del computer per decrittografare il token/ticket Kerberos ottenuto da Active Directory e inoltrato dal client al server per autenticare l'utente. Registrare il nome dell'entità servizio per l'host, non l'utente dell'app.

## <a name="how-to-use-httpsys"></a>Come usare HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Configurare l'app ASP.NET Core per l'uso di HTTP.sys

::: moniker range="< aspnetcore-3.0"

Non è necessario un riferimento al pacchetto nel file di progetto quando si usa il [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)). Se non si usa il metapacchetto `Microsoft.AspNetCore.App` aggiungere un riferimento al pacchetto a [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).

::: moniker-end

Chiamare il metodo di estensione <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> quando si compila l'host, specificando le eventuali <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> necessarie. L'esempio seguente imposta le opzioni sui rispettivi valori predefiniti:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

::: moniker-end

Le configurazioni aggiuntive di HTTP.sys vengono gestite tramite [impostazioni del Registro di sistema](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

   **Opzioni di HTTP.sys**

::: moniker range=">= aspnetcore-3.1"

| proprietà | Descrizione | Impostazione predefinita |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Controllare se l'input e/o l'output sincroni sono consentiti per `HttpContext.Request.Body` e `HttpContext.Response.Body`. | `false` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Consentire richieste anonime. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specificare gli schemi di autenticazione consentiti. Può essere modificata in qualsiasi momento prima dell'eliminazione del listener. I valori sono forniti dall'[enumerazione AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None` e `NTLM`. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Tentare la memorizzazione nella cache in [modalità kernel](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) per le risposte con intestazioni idonee. La risposta potrebbe non includere intestazioni `Set-Cookie`, `Vary` o `Pragma`. Deve includere un'intestazione `Cache-Control` `public` con valore `shared-max-age` o `max-age` o un'intestazione `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Numero massimo di accettazioni simultanee. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Numero massimo di connessioni simultanee da accettare. Usare `-1` per un numero infinito. Usare `null` per usare l'impostazione a livello di computer del Registro di sistema. | `null`<br>(a livello di computer<br>impostazione |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Vedere la sezione <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30000000 byte<br>(~28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Numero massimo di richieste che è possibile accodare. | 1000 |
| `RequestQueueMode` | Indica se il server è responsabile della creazione e della configurazione della coda di richieste o se deve connettersi a una coda esistente.<br>La maggior parte delle opzioni di configurazione esistenti non si applica quando ci si connette a una coda esistente. | `RequestQueueMode.Create` |
| `RequestQueueName` | Nome della coda di richieste HTTP. sys. | `null` (coda anonima) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indica se le scritture del corpo della risposta che hanno esito negativo a causa di disconnessioni del client devono generare eccezioni o vengono completate normalmente. | `false`<br>(completamento normale) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Espone la configurazione di <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> HTTP.sys, che può essere configurata anche nel Registro di sistema. Seguire i collegamenti API per altre informazioni su ogni impostazione, inclusi i valori predefiniti:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Tempo consentito all'API HTTP Server per svuotare il corpo dell'entità in una connessione keep-alive.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Tempo consentito per l'arrivo del corpo dell'entità della richiesta.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Tempo consentito all'API del server HTTP per analizzare l'intestazione della richiesta.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Tempo consentito per una connessione inattiva.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Velocità di invio minima per la risposta.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Tempo consentito alla richiesta per rimanere in coda prima che sia selezionata dall'app.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specificare l'elemento <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> da registrare con HTTP.sys. Il più utile è il metodo [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) usato per aggiungere un prefisso alla raccolta. Queste impostazioni possono essere modificate in qualsiasi momento prima dell'eliminazione del listener. |  |

::: moniker-end

::: moniker range="= aspnetcore-3.0"

| proprietà | Descrizione | Impostazione predefinita |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Controllare se l'input e/o l'output sincroni sono consentiti per `HttpContext.Request.Body` e `HttpContext.Response.Body`. | `false` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Consentire richieste anonime. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specificare gli schemi di autenticazione consentiti. Può essere modificata in qualsiasi momento prima dell'eliminazione del listener. I valori sono forniti dall'[enumerazione AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None` e `NTLM`. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Tentare la memorizzazione nella cache in [modalità kernel](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) per le risposte con intestazioni idonee. La risposta potrebbe non includere intestazioni `Set-Cookie`, `Vary` o `Pragma`. Deve includere un'intestazione `Cache-Control` `public` con valore `shared-max-age` o `max-age` o un'intestazione `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Numero massimo di accettazioni simultanee. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Numero massimo di connessioni simultanee da accettare. Usare `-1` per un numero infinito. Usare `null` per usare l'impostazione a livello di computer del Registro di sistema. | `null`<br>(a livello di computer<br>impostazione |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Vedere la sezione <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30000000 byte<br>(~28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Numero massimo di richieste che è possibile accodare. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indica se le scritture del corpo della risposta che hanno esito negativo a causa di disconnessioni del client devono generare eccezioni o vengono completate normalmente. | `false`<br>(completamento normale) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Espone la configurazione di <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> HTTP.sys, che può essere configurata anche nel Registro di sistema. Seguire i collegamenti API per altre informazioni su ogni impostazione, inclusi i valori predefiniti:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Tempo consentito all'API HTTP Server per svuotare il corpo dell'entità in una connessione keep-alive.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Tempo consentito per l'arrivo del corpo dell'entità della richiesta.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Tempo consentito all'API del server HTTP per analizzare l'intestazione della richiesta.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Tempo consentito per una connessione inattiva.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Velocità di invio minima per la risposta.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Tempo consentito alla richiesta per rimanere in coda prima che sia selezionata dall'app.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specificare l'elemento <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> da registrare con HTTP.sys. Il più utile è il metodo [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) usato per aggiungere un prefisso alla raccolta. Queste impostazioni possono essere modificate in qualsiasi momento prima dell'eliminazione del listener. |  |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| proprietà | Descrizione | Impostazione predefinita |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Controllare se l'input e/o l'output sincroni sono consentiti per `HttpContext.Request.Body` e `HttpContext.Response.Body`. | `true` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Consentire richieste anonime. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specificare gli schemi di autenticazione consentiti. Può essere modificata in qualsiasi momento prima dell'eliminazione del listener. I valori sono forniti dall'[enumerazione AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None` e `NTLM`. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Tentare la memorizzazione nella cache in [modalità kernel](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) per le risposte con intestazioni idonee. La risposta potrebbe non includere intestazioni `Set-Cookie`, `Vary` o `Pragma`. Deve includere un'intestazione `Cache-Control` `public` con valore `shared-max-age` o `max-age` o un'intestazione `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Numero massimo di accettazioni simultanee. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Numero massimo di connessioni simultanee da accettare. Usare `-1` per un numero infinito. Usare `null` per usare l'impostazione a livello di computer del Registro di sistema. | `null`<br>(a livello di computer<br>impostazione |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Vedere la sezione <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30000000 byte<br>(~28,6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Numero massimo di richieste che è possibile accodare. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indica se le scritture del corpo della risposta che hanno esito negativo a causa di disconnessioni del client devono generare eccezioni o vengono completate normalmente. | `false`<br>(completamento normale) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Espone la configurazione di <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> HTTP.sys, che può essere configurata anche nel Registro di sistema. Seguire i collegamenti API per altre informazioni su ogni impostazione, inclusi i valori predefiniti:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Tempo consentito all'API HTTP Server per svuotare il corpo dell'entità in una connessione keep-alive.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Tempo consentito per l'arrivo del corpo dell'entità della richiesta.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Tempo consentito all'API del server HTTP per analizzare l'intestazione della richiesta.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Tempo consentito per una connessione inattiva.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Velocità di invio minima per la risposta.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Tempo consentito alla richiesta per rimanere in coda prima che sia selezionata dall'app.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specificare l'elemento <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> da registrare con HTTP.sys. Il più utile è il metodo [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*) usato per aggiungere un prefisso alla raccolta. Queste impostazioni possono essere modificate in qualsiasi momento prima dell'eliminazione del listener. |  |

::: moniker-end

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Dimensioni massime consentite per qualsiasi corpo della richiesta in byte. Con l'impostazione `null`, le dimensioni massime del corpo della richiesta sono illimitate. Questo limite non ha effetto sulle connessioni aggiornate, che sono sempre illimitate.

Il metodo consigliato per ignorare il limite in un'applicazione ASP.NET Core MVC per un singolo `IActionResult` prevede l'uso dell'attributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> in un metodo di azione:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Se l'app tenta di configurare il limite per una richiesta dopo che l'app ha avviato la lettura della richiesta stessa, viene generata un'eccezione. È possibile usare una proprietà `IsReadOnly` per indicare se la proprietà `MaxRequestBodySize` è in stato di sola lettura e pertanto è troppo tardi per configurare il limite.

Se l'app deve eseguire l'override di <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per ogni richiesta, usare <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

::: moniker-end

Se si usa Visual Studio, assicurarsi che l'app non sia configurata per l'esecuzione di IIS o IIS Express.

In Visual Studio il profilo di avvio predefinito è per IIS Express. Per eseguire il progetto come app console, modificare manualmente il profilo selezionato, come illustrato nello screenshot seguente:

![Selezionare il profilo dell'applicazione console](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Configurare Windows Server

1. Determinare le porte da aprire per l'app e usare [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) o il cmdlet di PowerShell [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) per aprire le porte del firewall per consentire al traffico di raggiungere HTTP.sys. Nei comandi seguenti e nella configurazione dell'app, viene usata la porta 443.

1. Quando si distribuisce una macchina virtuale di Azure, aprire le porte nel [gruppo di sicurezza di rete](/azure/virtual-machines/windows/nsg-quickstart-portal). Nei comandi seguenti e nella configurazione dell'app, viene usata la porta 443.

1. Ottenere e installare i certificati X.509, se necessario.

   In Windows, creare certificati autofirmati con il [cmdlet di PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Per un esempio non supportato, vedere [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Installare i certificati autofirmati o con firma nell'archivio **Computer locale** > **Personale** del server.

1. Se l'app è una [distribuzione dipendente dal framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd), installare .NET Core, .NET Framework o entrambi (se l'app è un'app .NET Core destinata a .NET Framework).

   * **.NET Core** &ndash; Se l'app richiede .NET Core, ottenere ed eseguire il programma di installazione di **.NET Core Runtime** dalla [pagina di download per .NET Core](https://dotnet.microsoft.com/download). Non installare l'SDK completo nel server.
   * **.NET Framework** &ndash; Se l'app richiede .NET Framework, vedere la [guida all'installazione di .NET Framework](/dotnet/framework/install/). Installare la versione di .NET Framework richiesta. Il programma di installazione per la versione più recente di .NET Framework è disponibile dalla pagina di [download per .NET Core](https://dotnet.microsoft.com/download).

   Se l'app è una [distribuzione autonoma](/dotnet/core/deploying/#self-contained-deployments-scd) include il runtime nella distribuzione. Non è richiesta l'installazione di un framework nel server.

1. Configurare gli URL e le porte nell'app.

   Per impostazione predefinita, ASP.NET Core è associato a `http://localhost:5000`. Per configurare le porte e i prefissi URL, è possibile usare:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * L'argomento della riga di comando `urls`
   * La variabile di ambiente `ASPNETCORE_URLS`
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   L'esempio di codice seguente mostra come usare <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> con l'indirizzo IP locale del server `10.0.0.4` sulla porta 443:

::: moniker range=">= aspnetcore-3.0"

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

::: moniker-end

   Un vantaggio offerto da `UrlPrefixes` è che viene generato immediatamente un messaggio di errore per i prefissi non formattati correttamente.

   Le impostazioni di `UrlPrefixes` sostituiscono le impostazioni `UseUrls`/`urls`/`ASPNETCORE_URLS`. Pertanto, un vantaggio offerto da `UseUrls`, `urls` e dalla variabile di ambiente `ASPNETCORE_URLS` è che risulta più semplice alternare Kestrel e HTTP.sys.

   HTTP.sys usa i [formati di stringa UrlPrefix dell'API del server HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

   > [!WARNING]
   > Le associazioni con caratteri jolly di livello superiore (`http://*:80/` e `http://+:80`) **non** devono essere usate, poiché possono creare vulnerabilità a livello di sicurezza nell'app. Questo concetto vale sia per i caratteri jolly sicuri che vulnerabili. Usare nomi host o indirizzi IP espliciti al posto di caratteri jolly. L'associazione con caratteri jolly del sottodominio (ad esempio, `*.mysub.com`) non costituisce un rischio per la sicurezza se viene controllato l'intero dominio padre (a differenza di `*.com`, che è vulnerabile). Per ulteriori informazioni, vedere [RFC 7230: Section 5,4: host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Pre-registrare i prefissi URL nel server.

   Lo strumento predefinito per la configurazione di HTTP.sys è *netsh.exe*. *Netsh.exe* viene usato per riservare i prefissi URL e assegnare i certificati X.509. Per questo strumento sono necessari privilegi di amministratore.

   Usare lo strumento *netsh.exe* per registrare gli URL per l'app:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>` &ndash; L'URL (Uniform Resource Locator) completo. Non usare un'associazione con caratteri jolly. Usare un nome host valido o un indirizzo IP locale. *L'URL deve includere una barra finale.*
   * `<USER>` &ndash; Specifica il nome dell'utente o del gruppo di utenti.

   Nell'esempio seguente, l'indirizzo IP locale del server è `10.0.0.4`:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Quando viene registrato un URL, lo strumento risponde con `URL reservation successfully added`.

   Per eliminare un URL registrato, usare il comando `delete urlacl`:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Registrare i certificati X.509 nel server.

   Usare lo strumento *netsh.exe* per registrare i certificati per l'app:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>` &ndash; Specifica l'indirizzo IP locale per l'associazione. Non usare un'associazione con caratteri jolly. Usare un indirizzo IP valido.
   * `<PORT>` &ndash; Specifica la porta per l'associazione.
   * `<THUMBPRINT>` &ndash; Identificazione digitale del certificato X.509.
   * `<GUID>` &ndash; Un GUID generato per gli sviluppatori per rappresentare l'app a scopo informativo.

   A scopo di riferimento, archiviare il GUID nell'app come tag di pacchetto:

   * In Visual Studio:
     * Aprire le proprietà del progetto dell'app facendo clic sull'app con il pulsante destro del mouse in **Esplora soluzioni** e scegliendo **Proprietà**.
     * Selezionare la scheda **Pacchetto**.
     * Immettere il GUID creato nel campo **Tag**.
   * Se non si usa Visual Studio:
     * Aprire il file di progetto dell'app.
     * Aggiungere una proprietà `<PackageTags>` a un `<PropertyGroup>` nuovo o esistente con il GUID creato:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Nell'esempio seguente:

   * L'indirizzo IP locale del server è `10.0.0.4`.
   * Un generatore di GUID casuali online fornisce il valore `appid`.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Quando viene registrato un certificato, lo strumento risponde con `SSL Certificate successfully added`.

   Per eliminare la registrazione di un certificato, usare il comando `delete sslcert`:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Documentazione di riferimento per *netsh.exe*:

   * [Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx) (Comandi di Netsh per il protocollo HTTP)
   * [UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx) (Stringhe UrlPrefix)

1. Eseguire l'app.

   Non sono necessari i privilegi di amministratore per eseguire l'app con binding a localhost tramite HTTP (non HTTPS) con un numero di porta maggiore di 1024. Per altre configurazioni (ad esempio, l'uso di un indirizzo IP locale o il binding sulla porta 443), eseguire l'app con i privilegi di amministratore.

   L'app risponde all'indirizzo IP pubblico del server. In questo esempio, il server è raggiungibile da Internet al relativo indirizzo IP pubblico `104.214.79.47`.

   In questo esempio viene usato un certificato di sviluppo. La pagina viene caricata in modo sicuro dopo aver ignorato l'avviso di certificato non attendibile del browser.

   ![Finestra del browser che mostra la pagina di indice dell'app caricata](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenari con server proxy e servizi di bilanciamento del carico

Per le app ospitate da HTTP.sys che interagiscono con richieste da Internet o da una rete aziendale, potrebbero essere necessari interventi di configurazione aggiuntivi in caso di hosting dietro server proxy e servizi di bilanciamento del carico. Per altre informazioni, vedere [Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Abilitare l'autenticazione di Windows con HTTP. sys](xref:security/authentication/windowsauth#httpsys)
* [API di HTTP Server](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [Repository di GitHub aspnet/HttpSysServer (codice sorgente)](https://github.com/aspnet/HttpSysServer/)
* [L'host](xref:fundamentals/index#host)
* <xref:test/troubleshoot>
