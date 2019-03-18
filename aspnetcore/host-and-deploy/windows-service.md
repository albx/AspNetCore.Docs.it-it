---
title: Ospitare ASP.NET Core in un servizio Windows
author: guardrex
description: Informazioni su come ospitare un'app ASP.NET Core in un servizio Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/08/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: ecc7f3a8cd813c2803d03294e38d726905eeb1b8
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841423"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Ospitare ASP.NET Core in un servizio Windows

Di [Luke Latham](https://github.com/guardrex) e [Tom Dykstra](https://github.com/tdykstra)

È possibile ospitare un'app ASP.NET Core in Windows come [servizio Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) senza usare IIS. Quando è ospitata come servizio Windows, l'app viene avviata automaticamente dopo ogni riavvio.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prerequisiti

* [PowerShell 6](https://github.com/PowerShell/PowerShell)

## <a name="deployment-type"></a>Tipo di distribuzione

È possibile creare una distribuzione di Windows dipendente dal framework o autonoma. Per informazioni e consigli sugli scenari di distribuzione, vedere [Distribuzione di applicazioni .NET Core](/dotnet/core/deploying/).

### <a name="framework-dependent-deployment"></a>Distribuzione dipendente dal framework

La distribuzione dipendente dal framework si basa sulla presenza di una versione condivisa a livello di sistema di .NET Core nel sistema di destinazione. Quando lo scenario di distribuzione dipendente dal framework viene usato con un'app servizio Windows ASP.NET Core, l'SDK genera un file eseguibile (*\*.exe*), denominato *eseguibile dipendente dal framework*.

### <a name="self-contained-deployment"></a>Distribuzione autonoma

Una distribuzione autonoma non si basa sulla presenza dei componenti condivisi nel sistema di destinazione. Il runtime e le dipendenze dell'app vengono distribuiti con l'app nel sistema host.

## <a name="convert-a-project-into-a-windows-service"></a>Convertire un progetto in un servizio Windows

Apportare le modifiche seguenti a un progetto ASP.NET Core esistente per eseguire l'app come servizio:

### <a name="project-file-updates"></a>Aggiornamenti ai file di progetto

In base al [tipo di distribuzione](#deployment-type) scelto, aggiornare il file di progetto:

#### <a name="framework-dependent-deployment-fdd"></a>Distribuzione dipendente dal framework

Aggiungere un [RID (Runtime Identifier)](/dotnet/core/rid-catalog) Windows al `<PropertyGroup>` che contiene il framework di destinazione. Nell'esempio seguente il RID è impostato su `win7-x64`. Aggiungere la proprietà `<SelfContained>` impostata su `false`. Queste proprietà indicano all'SDK di generare un file eseguibile (*.exe*) per Windows.

Un file *web.config*, che viene normalmente generato quando si pubblica un'app ASP.NET Core, non è necessario per un'app di servizi Windows. Per disabilitare la creazione del file *web.config*, aggiungere la proprietà `<IsTransformWebConfigDisabled>` impostata su `true`.

::: moniker range=">= aspnetcore-2.2"

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Aggiungere la proprietà `<UseAppHost>` impostata su `true`. Questa proprietà fornisce il servizio con un percorso di attivazione (un file eseguibile, *.exe*) per una distribuzione dipendente dal framework.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

#### <a name="self-contained-deployment-scd"></a>Distribuzione autonoma

Verificare la presenza di un [RID](/dotnet/core/rid-catalog) di Windows o aggiungere un RID al `<PropertyGroup>` che contiene il framework di destinazione. Disabilitare la creazione di un file *web.config* aggiungendo la proprietà `<IsTransformWebConfigDisabled>` impostata su `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Per eseguire la pubblicazione per più identificatori di runtime:

* Specificare gli identificatori di runtime in un elenco delimitato da punto e virgola.
* Usare il nome di proprietà `<RuntimeIdentifiers>` (plurale).

  Per altre informazioni, vedere il [Catalogo RID di .NET Core](/dotnet/core/rid-catalog).

Aggiungere un riferimento al pacchetto per [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).

Per abilitare la registrazione nel registro eventi di Windows, aggiungere un riferimento al pacchetto per [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Per altre informazioni, vedere la sezione [Gestire gli eventi di avvio e arresto](#handle-starting-and-stopping-events).

### <a name="programmain-updates"></a>Aggiornamenti a Program.Main

Modificare `Program.Main` nel modo seguente:

* Per eseguire test e debug durante l'esecuzione all'esterno di un servizio, aggiungere il codice per determinare se l'app è in esecuzione come servizio o è un'app console. Controllare se il debugger è collegato o se è presente un argomento della riga di comando `--console`.

  Se una delle due condizioni è vera (l'app non è eseguita come servizio), chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> sull'host Web.

  Se le condizioni sono false (l'app è eseguita come servizio):

  * Chiamare <xref:System.IO.Directory.SetCurrentDirectory*> e usare un percorso per la posizione di pubblicazione dell'app. Non chiamare <xref:System.IO.Directory.GetCurrentDirectory*> per ottenere il percorso perché un'app servizio Windows restituisce la cartella *C:\\WINDOWS\\system32* quando viene chiamato <xref:System.IO.Directory.GetCurrentDirectory*>. Per altre informazioni, vedere la sezione [Directory corrente e radice del contenuto](#current-directory-and-content-root).
  * Chiamare <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> per eseguire l'app come servizio.

  Dato che il [provider di configurazione della riga di comando](xref:fundamentals/configuration/index#command-line-configuration-provider) richiede coppie nome-valore per gli argomenti della riga di comando, l'opzione `--console` viene rimossa dagli argomenti prima che <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> li riceva.

* Per scrivere nel registro eventi di Windows, aggiungere il provider EventLog a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Impostare il livello di registrazione con la chiave `Logging:LogLevel:Default` nel file *appsettings.Production.json*. Per scopi di testing e dimostrazione, il file di impostazioni di produzione dell'app di esempio imposta il livello di registrazione su `Information`. In ambiente di produzione il valore viene in genere impostato su `Error`. Per ulteriori informazioni, vedere <xref:fundamentals/logging/index#windows-eventlog-provider>.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="publish-the-app"></a>Pubblicare l'app

Pubblicare l'app usando [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), un [profilo di pubblicazione di Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles) o Visual Studio Code. Quando si usa Visual Studio, selezionare **FolderProfile** e configurare il **Percorso di destinazione** prima di selezionare il pulsante **Pubblica**.

Per pubblicare l'app di esempio con strumenti dell'interfaccia della riga di comando, eseguire il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) da un prompt dei comandi dalla cartella del progetto passando una configurazione Versione all'opzione [-c|--configuration](/dotnet/core/tools/dotnet-publish#options). Usare l'opzione [-o|--output](/dotnet/core/tools/dotnet-publish#options) con un percorso per pubblicare in una cartella all'esterno dell'app.

### <a name="publish-a-framework-dependent-deployment-fdd"></a>Pubblicare una distribuzione dipendente dal framework

Nell'esempio seguente l'app viene pubblicata nella cartella *c:\\svc*:

```console
dotnet publish --configuration Release --output c:\svc
```

### <a name="publish-a-self-contained-deployment-scd"></a>Pubblicare una distribuzione autonoma

L'identificatore di runtime deve essere specificato nella proprietà `<RuntimeIdenfifier>` (o `<RuntimeIdentifiers>`) del file di progetto. Specificare il runtime per l'opzione [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) del comando `dotnet publish`.

Nell'esempio seguente l'app viene pubblicata per il runtime `win7-x64` nella cartella *c:\\svc*:

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

## <a name="create-a-user-account"></a>Creare un account utente

Creare un account utente per il servizio usando il comando `net user` da una shell dei comandi di PowerShell 6 amministrativa:

```powershell
net user {USER ACCOUNT} {PASSWORD} /add
```

La scadenza della password predefinita è di sei settimane.

Per l'app di esempio, creare un account utente con il nome `ServiceUser` e una password. Nel comando seguente sostituire `{PASSWORD}` con una [password complessa](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).

```powershell
net user ServiceUser {PASSWORD} /add
```

Se è necessario aggiungere l'utente a un gruppo, usare il comando `net localgroup`, dove `{GROUP}` è il nome del gruppo:

```powershell
net localgroup {GROUP} {USER ACCOUNT} /add
```

Per altre informazioni, vedere [Service User Accounts](/windows/desktop/services/service-user-accounts) (Account utente di servizio).

Un approccio alternativo per la gestione degli utenti quando si usa Active Directory consiste nell'usare account del servizio gestito. Per altre informazioni, vedere [Panoramica degli account del servizio gestito del gruppo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="set-permission-log-on-as-a-service"></a>Impostare l'autorizzazione: Accedi come servizio

Concedere l'accesso per scrittura/lettura/esecuzione alla cartella dell'app usando il comando [icacls](/windows-server/administration/windows-commands/icacls):

```powershell
icacls "{PATH}" /grant {USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS} /t
```

* `{PATH}` &ndash; Percorso della cartella dell'app.
* `{USER ACCOUNT}` &ndash; Account utente (SID).
* `(OI)` &ndash; Il flag Eredità oggetto propaga le autorizzazioni ai file subordinati.
* `(CI)` &ndash; Il flag Eredità contenitore propaga le autorizzazioni alle cartelle subordinate.
* `{PERMISSION FLAGS}` &ndash; Imposta le autorizzazioni di accesso dell'app.
  * Scrittura (`W`)
  * Lettura (`R`)
  * Esecuzione (`X`)
  * Completo (`F`)
  * Modifica (`M`)
* `/t` &ndash; Applicare in modo ricorsivo alle cartelle e ai file subordinati esistenti.

Per l'app di esempio pubblicata nella cartella *c:\\svc* e l'account `ServiceUser` con autorizzazioni di scrittura/lettura/esecuzione, usare il comando seguente:

```powershell
icacls "c:\svc" /grant ServiceUser:(OI)(CI)WRX /t
```

Per altre informazioni, vedere [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="create-the-service"></a>Creare il servizio

Usare lo script di PowerShell [RegisterService.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/scripts) per la registrazione del servizio. Da un prompt dei comandi di PowerShell 6 amministrativo, eseguire il comando seguente:

```powershell
.\RegisterService.ps1 
    -Name {NAME} 
    -DisplayName "{DISPLAY NAME}" 
    -Description "{DESCRIPTION}" 
    -Path "{PATH}" 
    -Exe {ASSEMBLY}.exe 
    -User {DOMAIN\USER}
```

Nell'esempio seguente per l'app di esempio:

* Il servizio è denominato **MyService**.
* Il servizio pubblicato risiede nella cartella *c:\\svc*. L'eseguibile dell'app è denominato *SampleApp.exe*.
* Il servizio viene eseguito nel contesto dell'account `ServiceUser`. Nell'esempio seguente il nome del computer locale è `Desktop-PC`.

```powershell
.\RegisterService.ps1 
    -Name MyService 
    -DisplayName "My Cool Service" 
    -Description "This is the Sample App service." 
    -Path "c:\svc" 
    -Exe SampleApp.exe 
    -User Desktop-PC\ServiceUser
```

## <a name="manage-the-service"></a>Gestire il servizio

### <a name="start-the-service"></a>Avviare il servizio

Avviare il servizio con il comando di PowerShell 6 `Start-Service -Name {NAME}`.

Per avviare il servizio app di esempio, usare il comando seguente:

```powershell
Start-Service -Name MyService
```

L'avvio del servizio richiede alcuni secondi.

### <a name="determine-the-service-status"></a>Determinare lo stato del servizio

Per verificare lo stato del servizio, usare il comando di PowerShell 6`Get-Service -Name {NAME}`. Lo stato viene indicato con uno dei valori seguenti:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

Usare il comando seguente per verificare lo stato del servizio app di esempio:

```powershell
Get-Service -Name MyService
```

### <a name="browse-a-web-app-service"></a>Individuare un servizio app Web

Quando il servizio è nello stato `RUNNING` e se il servizio è un'app Web, selezionare l'app dal suo percorso (per impostazione predefinita, `http://localhost:5000`, che reindirizza a `https://localhost:5001` quando si utilizza [il middleware di reindirizzamento HTTPS](xref:security/enforcing-ssl)).

Per il servizio app di esempio, selezionare l'app in `http://localhost:5000`.

### <a name="stop-the-service"></a>Arresta il servizio

Arrestare il servizio con il comando di Powershell 6 `Stop-Service -Name {NAME}`.

Per arrestare il servizio app di esempio, usare il comando seguente:

```powershell
Stop-Service -Name MyService
```

### <a name="remove-the-service"></a>Rimuovere il servizio

Dopo il breve lasso di tempo necessario per arrestare il servizio, rimuovere il servizio con il comando di PowerShell 6`Remove-Service -Name {NAME}`.

Verificare lo stato del servizio app di esempio:

```powershell
Remove-Service -Name MyService
```

## <a name="handle-starting-and-stopping-events"></a>Gestire gli eventi di avvio e arresto

Per gestire gli eventi <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, apportare le modifiche aggiuntive seguenti:

1. Creare una classe che deriva da <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> con i metodi `OnStarting`, `OnStarted` e `OnStopping`:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Creare un metodo di estensione per <xref:Microsoft.AspNetCore.Hosting.IWebHost> che passa `CustomWebHostService` a <xref:System.ServiceProcess.ServiceBase.Run*>:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. In `Program.Main` chiamare il metodo di estensione `RunAsCustomService` invece di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:

   ```csharp
   host.RunAsCustomService();
   ```

   Per visualizzare il percorso di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, fare riferimento all'esempio di codice illustrato nella sezione [Convertire un progetto in un servizio Windows](#convert-a-project-into-a-windows-service).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenari con server proxy e servizi di bilanciamento del carico

I servizi che interagiscono con le richieste da Internet o da una rete aziendale e si trovano dietro un proxy o un servizio di bilanciamento del carico potrebbero richiedere una configurazione aggiuntiva. Per ulteriori informazioni, vedere <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-https"></a>Configurare HTTPS

Per configurare il servizio con un endpoint sicuro:

1. Creare un certificato X.509 per il sistema di hosting usando i meccanismi di acquisizione e distribuzione dei certificati della piattaforma.

1. Specificare una [configurazione dell'endpoint HTTPS del server Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) per usare il certificato.

L'uso del certificato di sviluppo ASP.NET Core HTTPS per proteggere un endpoint del servizio non è supportato.

## <a name="current-directory-and-content-root"></a>Directory corrente e radice del contenuto

La directory di lavoro corrente restituita chiamando <xref:System.IO.Directory.GetCurrentDirectory*> per un servizio Windows è la cartella *C:\\WINDOWS\\system32*. La cartella *system32* non è un percorso appropriato per archiviare i file di un servizio, ad esempio i file di impostazioni. Usare uno degli approcci seguenti per gestire e accedere agli asset e ai file di impostazioni di un servizio.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Impostare il percorso radice del contenuto sulla cartella dell'app

L'elemento <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> è lo stesso percorso fornito all'argomento `binPath` durante la creazione del servizio. Invece di chiamare `GetCurrentDirectory` per creare i percorsi dei file di impostazioni, chiamare <xref:System.IO.Directory.SetCurrentDirectory*> con il percorso radice del contenuto dell'app.

In `Program.Main`, determinare il percorso della cartella dell'eseguibile del servizio e usare il percorso per stabilire la radice del contenuto dell'app:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a>Archiviare i file del servizio in un percorso appropriato nel disco

Specificare un percorso assoluto con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando si usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per la cartella contenente i file.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Configurazione dell'endpoint Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (include la configurazione HTTPS e il supporto SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>
