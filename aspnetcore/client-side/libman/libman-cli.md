---
title: Usare l'interfaccia della riga di comando di LibMan con ASP.NET Core
author: scottaddie
description: Informazioni su come usare l'interfaccia della riga di comando di LibMan in un progetto ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664633"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>Usare l'interfaccia della riga di comando di LibMan con ASP.NET Core

Di [Scott Addie](https://twitter.com/Scott_Addie)

L'interfaccia della riga di comando di [LibMan](xref:client-side/libman/index) è uno strumento multipiattaforma supportato ovunque sia supportato .NET Core.

## <a name="prerequisites"></a>Prerequisites

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Installazione

Per installare l'interfaccia della riga di comando di LibMan:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

Uno [strumento .NET Core Global](/dotnet/core/tools/global-tools#install-a-global-tool) viene installato dal pacchetto NuGet [Microsoft. Web. librarymanager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) .

Per installare l'interfaccia della riga di comando di LibMan da un'origine del pacchetto NuGet specifica:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

Nell'esempio precedente, uno strumento globale di .NET Core viene installato dal file *C:\Temp\Microsoft.Web.librarymanager.cli.1.0.94-g606058a278.nupkg* del computer Windows locale.

## <a name="usage"></a>Uso

Una volta completata l'installazione dell'interfaccia della riga di comando, è possibile usare il comando seguente:

```console
libman
```

Per visualizzare la versione dell'interfaccia della riga di comando installata:

```console
libman --version
```

Per visualizzare i comandi dell'interfaccia della riga di comando disponibili:

```console
libman --help
```

Il comando precedente Visualizza un output simile al seguente:

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

Le sezioni seguenti descrivono i comandi dell'interfaccia della riga di comando disponibili.

## <a name="initialize-libman-in-the-project"></a>Inizializzare LibMan nel progetto

Il `libman init` comando crea un file *libman. JSON* , se non ne esiste uno. Il file viene creato con il contenuto del modello di elemento predefinito.

### <a name="synopsis"></a>Riepilogo

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Opzioni

Per il comando `libman init` sono disponibili le opzioni seguenti:

* `-d|--default-destination <PATH>`

  Percorso relativo alla cartella corrente. I file di libreria vengono installati in questo percorso se non è stata definita alcuna proprietà `destination` per una libreria in *libman. JSON*. Il valore `<PATH>` viene scritto nella proprietà `defaultDestination` di *libman. JSON*.

* `-p|--default-provider <PROVIDER>`

  Provider da utilizzare se non è definito alcun provider per una determinata libreria. Il valore `<PROVIDER>` viene scritto nella proprietà `defaultProvider` di *libman. JSON*. Sostituire `<PROVIDER>` con uno dei valori seguenti:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Esempi

Per creare un file *libman. JSON* in un progetto ASP.NET Core:

* Passare alla radice del progetto.
* Eseguire il comando seguente:

  ```console
  libman init
  ```

* Digitare il nome del provider predefinito oppure premere `Enter` per usare il provider CDNJS predefinito. I valori validi includono:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![comando Libman init-provider predefinito](_static/libman-init-provider.png)

Viene aggiunto un file *libman. JSON* alla radice del progetto con il contenuto seguente:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Aggiungi file di libreria

Il `libman install` comando Scarica e installa i file di libreria nel progetto. Se non ne esiste uno, viene aggiunto un file *libman. JSON* . Il file *libman. JSON* è stato modificato per archiviare i dettagli di configurazione per i file di libreria.

### <a name="synopsis"></a>Riepilogo

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Argomenti

`LIBRARY`

Nome della libreria da installare. Questo nome può includere la notazione dei numeri di versione, ad esempio `@1.2.0`.

### <a name="options"></a>Opzioni

Per il comando `libman install` sono disponibili le opzioni seguenti:

* `-d|--destination <PATH>`

  Percorso in cui installare la libreria. Se non specificato, viene utilizzato il percorso predefinito. Se non è specificata alcuna proprietà `defaultDestination` in *libman. JSON*, questa opzione è obbligatoria.

* `--files <FILE>`

  Specificare il nome del file da installare dalla libreria. Se non specificato, vengono installati tutti i file dalla libreria. Specificare una `--files` opzione per ogni file da installare. Sono supportati anche i percorsi relativi. Ad esempio: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Nome del provider da utilizzare per l'acquisizione della libreria. Sostituire `<PROVIDER>` con uno dei valori seguenti:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Se non specificato, viene usata la proprietà `defaultProvider` in *libman. JSON* . Se non è specificata alcuna proprietà `defaultProvider` in *libman. JSON*, questa opzione è obbligatoria.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Esempi

Si consideri il seguente file *libman. JSON* :

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Per installare la versione 3.2.1 di jQuery *. min. js* nella cartella *wwwroot/scripts/jQuery* usando il provider CDNJS:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

Il file *libman. JSON* è simile al seguente:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

Per installare i file *Calendar. js* e *Calendar. CSS* da *C:\\Temp\\contosoCalendar\\* usando il provider file System:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

La richiesta seguente viene visualizzata per due motivi:

* Il file *libman. JSON* non contiene una proprietà `defaultDestination`.
* Il comando `libman install` non contiene l'opzione `-d|--destination`.

![comando di installazione di Libman-destinazione](_static/libman-install-destination.png)

Dopo aver accettato la destinazione predefinita, il file *libman. JSON* è simile al seguente:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a>Ripristinare i file di libreria

Il comando `libman restore` installa i file di libreria definiti in *libman. JSON*. Sono applicabili le regole seguenti:

* Se nella radice del progetto non è presente alcun file *libman. JSON* , viene restituito un errore.
* Se una libreria specifica un provider, la proprietà `defaultProvider` in *libman. JSON* viene ignorata.
* Se una libreria specifica una destinazione, la proprietà `defaultDestination` in *libman. JSON* viene ignorata.

### <a name="synopsis"></a>Riepilogo

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Opzioni

Per il comando `libman restore` sono disponibili le opzioni seguenti:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Esempi

Per ripristinare i file di libreria definiti in *libman. JSON*:

```console
libman restore
```

## <a name="delete-library-files"></a>Elimina file di libreria

Il comando `libman clean` Elimina i file di libreria ripristinati in precedenza tramite LibMan. Cartelle che diventano vuote dopo l'eliminazione di questa operazione. Le configurazioni associate dei file di libreria nella proprietà `libraries` di *libman. JSON* non vengono rimosse.

### <a name="synopsis"></a>Riepilogo

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Opzioni

Per il comando `libman clean` sono disponibili le opzioni seguenti:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Esempi

Per eliminare i file di libreria installati tramite LibMan:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Disinstalla file di libreria

Il comando `libman uninstall`:

* Elimina tutti i file associati alla libreria specificata dalla destinazione in *libman. JSON*.
* Rimuove la configurazione della libreria associata da *libman. JSON*.

Si verifica un errore quando:

* Nessun file *libman. JSON* presente nella radice del progetto.
* La libreria specificata non esiste.

Se è installata più di una libreria con lo stesso nome, viene richiesto di sceglierne una.

### <a name="synopsis"></a>Riepilogo

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Argomenti

`LIBRARY`

Nome della libreria da disinstallare. Questo nome può includere la notazione dei numeri di versione, ad esempio `@1.2.0`.

### <a name="options"></a>Opzioni

Per il comando `libman uninstall` sono disponibili le opzioni seguenti:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Esempi

Si consideri il seguente file *libman. JSON* :

[!code-json[](samples/LibManSample/libman.json)]

* Per disinstallare jQuery, uno dei seguenti comandi ha esito positivo:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Per disinstallare i file Lodash installati tramite il provider di `filesystem`:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Aggiornare la versione della libreria

Il `libman update` comando aggiorna una libreria installata tramite LibMan alla versione specificata.

Si verifica un errore quando:

* Nessun file *libman. JSON* presente nella radice del progetto.
* La libreria specificata non esiste.

Se è installata più di una libreria con lo stesso nome, viene richiesto di sceglierne una.

### <a name="synopsis"></a>Riepilogo

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Argomenti

`LIBRARY`

Nome della libreria da aggiornare.

### <a name="options"></a>Opzioni

Per il comando `libman update` sono disponibili le opzioni seguenti:

* `-pre`

  Ottenere la versione provvisoria più recente della libreria.

* `--to <VERSION>`

  Ottenere una versione specifica della libreria.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Esempi

* Per aggiornare jQuery alla versione più recente:

  ```console
  libman update jquery
  ```

* Per aggiornare jQuery alla versione 3.3.1:

  ```console
  libman update jquery --to 3.3.1
  ```

* Per aggiornare jQuery all'ultima versione provvisoria:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Gestisci cache di libreria

Il comando `libman cache` gestisce la cache della libreria LibMan. Il provider di `filesystem` non utilizza la cache della libreria.

### <a name="synopsis"></a>Riepilogo

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Argomenti

`PROVIDER`

Utilizzato solo con il comando `clean`. Specifica la cache del provider da pulire. I valori validi includono:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Opzioni

Per il comando `libman cache` sono disponibili le opzioni seguenti:

* `--files`

  Elenca i nomi dei file memorizzati nella cache.

* `--libraries`

  Elenca i nomi delle librerie memorizzate nella cache.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Esempi

* Per visualizzare i nomi delle librerie memorizzate nella cache per ogni provider, usare uno dei comandi seguenti:

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Viene visualizzato output simile al seguente:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* Per visualizzare i nomi dei file di libreria memorizzati nella cache per ogni provider:

  ```console
  libman cache list --files
  ```

  Viene visualizzato output simile al seguente:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  Si noti che l'output precedente mostra che le versioni di jQuery 3.2.1 e 3.3.1 sono memorizzate nella cache del provider CDNJS.

* Per svuotare la cache di libreria per il provider CDNJS:

  ```console
  libman cache clean cdnjs
  ```

  Dopo aver svuotato la cache del provider CDNJS, il comando `libman cache list` Visualizza gli elementi seguenti:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* Per svuotare la cache per tutti i provider supportati:

  ```console
  libman cache clean
  ```

  Dopo aver svuotato tutte le cache del provider, il comando `libman cache list` Visualizza gli elementi seguenti:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Risorse aggiuntive

* [Installare uno strumento globale](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [Repository GitHub di LibMan](https://github.com/aspnet/LibraryManager)
