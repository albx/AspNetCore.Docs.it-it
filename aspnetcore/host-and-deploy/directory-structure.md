---
title: Struttura di directory di ASP.NET Core
author: guardrex
description: Informazioni sulla struttura di directory delle app ASP.NET Core pubblicate.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2018
uid: host-and-deploy/directory-structure
ms.openlocfilehash: 4bc5ead8e24c4bb7fe6cd2f52fd2aa622187180c
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085487"
---
# <a name="aspnet-core-directory-structure"></a>Struttura di directory di ASP.NET Core

Di [Luke Latham](https://github.com/guardrex)

La directory *publish* contiene gli asset distribuibili prodotti dal comando [dotnet publish](/dotnet/core/tools/dotnet-publish). La directory contiene:

* File dell'applicazione
* File di configurazione
* Asset statici
* Pacchetti
* Un runtime (solo [distribuzione autonoma](/dotnet/core/deploying/#self-contained-deployments-scd))

| Tipo di app | Struttura di directory |
| -------- | ------------------- |
| [Distribuzione dipendente dal framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd) | <ul><li>publish&dagger;<ul><li>Logs&dagger; (facoltativa, a meno che non sia necessaria per ricevere i log stdout)</li><li>Views&dagger; (app MVC; se non sono precompilate visualizzazioni)</li><li>Pages&dagger; (MVC o app Razor Pages; se non sono precompilate pagine)</li><li>wwwroot&dagger;</li><li>*\.dll files</li><li>{NOME ASSEMBLY}.deps.json</li><li>{NOME ASSEMBLY}.dll</li><li>{NOME ASSEMBLY}.pdb</li><li>{NOME ASSEMBLY}.Views.dll</li><li>{NOME ASSEMBLY}.Views.pdb</li><li>{NOME ASSEMBLY}.runtimeconfig.json</li><li>web.config (distribuzioni IIS)</li></ul></li></ul> |
| [Distribuzione autonoma](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>publish&dagger;<ul><li>Logs&dagger; (facoltativa, a meno che non sia necessaria per ricevere i log stdout)</li><li>Views&dagger; (app MVC; se non sono precompilate visualizzazioni)</li><li>Pages&dagger; (MVC o app Razor Pages; se non sono precompilate pagine)</li><li>wwwroot&dagger;</li><li>\*.dll files</li><li>{NOME ASSEMBLY}.deps.json</li><li>{NOME ASSEMBLY}.dll</li><li>{NOME ASSEMBLY}.exe</li><li>{NOME ASSEMBLY}.pdb</li><li>{NOME ASSEMBLY}.Views.dll</li><li>{NOME ASSEMBLY}.Views.pdb</li><li>{NOME ASSEMBLY}.runtimeconfig.json</li><li>web.config (distribuzioni IIS)</li></ul></li></ul> |

&dagger;Indica una directory

La directory *publish* rappresenta il *percorso radice del contenuto*, anche denominato *percorso di base dell'applicazione*, della distribuzione. Qualsiasi nome venga assegnato alla directory *publish* dell'applicazione distribuita sul server, il relativo percorso viene usato come percorso fisico del server per l'app ospitata.

La directory *wwwroot*, se presente, contiene solo gli asset statici.

È possibile creare una directory *Logs* per la distribuzione usando uno dei due approcci seguenti:

* Aggiungere l'elemento `<Target>` seguente al file di progetto:

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   L'elemento `<MakeDir>` crea una cartella *Logs* vuota nell'output pubblicato. L'elemento usa la proprietà `PublishDir` per determinare il percorso di destinazione per la creazione della cartella. Diversi metodi di distribuzione, ad esempio Distribuzione Web, ignorano le cartelle vuote durante la distribuzione. L'elemento `<WriteLinesToFile>` genera un file nella cartella *Logs*, che garantisce la distribuzione della cartella nel server. La creazione della cartella con questo metodo ha esito negativo se il processo di lavoro non dispone dell'accesso in scrittura alla cartella di destinazione.

* Creare fisicamente la directory *Logs* sul server nella distribuzione.

La directory di distribuzione richiede autorizzazioni di lettura/esecuzione. La directory *Logs* richiede autorizzazioni di lettura/scrittura. Le directory aggiuntive in cui vengono scritti i file richiedono autorizzazioni di lettura/scrittura.

Per la [registrazione stdout del modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) non è richiesta una cartella *Logs* nella distribuzione. Il modulo è in grado di creare eventuali cartelle nel percorso `stdoutLogFile` quando viene creato il file di log. La creazione di una cartella *Logs* è utile per la [registrazione di debug avanzata del modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Le cartelle nel percorso specificato per il valore `<handlerSetting>` non vengono create automaticamente dal modulo e devono essere già presenti nella distribuzione per consentire al modulo di scrivere il log di debug.

## <a name="additional-resources"></a>Risorse aggiuntive

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [Distribuzione di applicazioni .NET Core](/dotnet/core/deploying/)
* [Framework di destinazione](/dotnet/standard/frameworks)
* [Catalogo RID di .NET Core](/dotnet/core/rid-catalog)
