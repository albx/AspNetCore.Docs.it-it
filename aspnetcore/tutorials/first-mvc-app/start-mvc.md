---
title: Introduzione ad ASP.NET Core MVC
author: rick-anderson
description: Informazioni introduttive su ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 901257efdfbc7b36249233745175f5ed253da2c7
ms.sourcegitcommit: da2fb2d78ce70accdba903ccbfdcfffdd0112123
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722874"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Introduzione ad ASP.NET Core MVC

Di [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Questa esercitazione illustra le nozioni di base della creazione di un'app Web ASP.NET Core MVC.

L'app gestisce un database di titoli di film. Vengono illustrate le seguenti procedure:

> [!div class="checklist"]
> * Creare un'app Web.
> * Aggiungere un modello ed eseguirne lo scaffolding.
> * Usare un database.
> * Aggiungere ricerca e convalida.

Al termine di queste operazioni si ottiene un'app che può gestire e visualizzare dati relativi ai film.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Creare un'app Web

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dal menu di Visual Studio selezionare **Crea un nuovo progetto**.

* Selezionare **Applicazione Web ASP.NET Core** e quindi selezionare **Avanti**.

![nuova applicazione Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* Assegnare al progetto il nome **MvcMovie** e selezionare **Crea**. È importante assegnare al progetto il nome **MvcMovie**, in modo che quando si copia il codice lo spazio dei nomi corrisponda.

  ![nuova applicazione Web ASP.NET Core](start-mvc/_static/config.png)

* Selezionare **Applicazione Web (MVC)** e quindi selezionare **Crea**.

![Finestra di dialogo Nuovo progetto, .NET Core nel riquadro sinistro, Web ASP.NET Core ](start-mvc/_static/new_project30.png)

Per il progetto MVC appena creato Visual Studio ha usato il modello predefinito. Ora è possibile disporre di un'app funzionante immettendo un nome progetto e selezionando alcune opzioni. Si tratta di un progetto iniziale di base.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Nell'esercitazione si presuppone una familarità con Visual Studio Code. Vedere [Introduzione a VS Code](https://code.visualstudio.com/docs) e [Guida a Visual Studio Code](#visual-studio-code-help) per altre informazioni.

* Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Cambiare directory (`cd`) e passare alla cartella che conterrà il progetto.
* Eseguire il comando seguente:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Viene visualizzata una finestra di dialogo con le **risorse necessarie per la compilazione e il debug non è presente in ' MvcMovie '. Aggiungerli?**  Selezionare **Sì**.

  * `dotnet new mvc -o MvcMovie`: crea un nuovo progetto ASP.NET Core MVC nella cartella *MvcMovie*.
  * `code -r MvcMovie`: carica il file di progetto *MvcMovie. csproj* in Visual Studio Code.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Selezionare **File** > **nuova soluzione**.

  ![Nuova soluzione macOS](./start-mvc/_static/new_project_vsmac.png)

* Selezionare l'applicazione Web **.NET Core** > **app** > **(Model-View-Controller)** > **Avanti**.

  ![Finestra di dialogo Nuovo progetto di macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* Nella finestra di dialogo **configurare la nuova ASP.NET Core API Web** impostare il **Framework di destinazione** di **.NET Core 3,1**.

  ![selezione di macOS .NET Core 3,1](./start-mvc/_static/new_project_31_vsmac.png)

* Denominare il progetto **MvcMovie**, quindi selezionare **Crea**.

---

### <a name="run-the-app"></a>Eseguire l'app

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Selezionare **CTRL+F5** per eseguire l'app in modalità non di debug.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app. Si noti che la barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Quando Visual Studio crea un progetto Web, per il server Web viene usata una porta casuale.
* Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice. Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.
* È possibile scegliere se avviare l'app in modalità di debug o non di debug nella voce di menu **Debug**:

  ![Debug - menu](start-mvc/_static/debug_menu.png)

* È possibile eseguire il debug dell'app toccando il pulsante **IIS Express**.

  ![IIS Express](start-mvc/_static/iis_express.png)

  La figura seguente mostra l'app:

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Premere CTRL+F5 per l'esecuzione senza il debugger.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `https://localhost:5001`. La barra degli indirizzi visualizza `localhost:port:5001` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Localhost viene usato solo per le richieste web del computer locale.

  Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice. Molti sviluppatori preferiscono usare la modalità non di debug per aggiornare la pagina e visualizzare le modifiche.

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Per avviare l'app, selezionare **Esegui** > **Avvia senza eseguire debug**. Visual Studio per Mac avvia il server [Kestrel](xref:fundamentals/servers/index#kestrel), apre un browser e naviga all'indirizzo `http://localhost:port`, dove *port* è un numero di porta selezionato a caso.

[!INCLUDE[](~/includes/trustCertMac.md)]

* La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Quando Visual Studio crea un progetto Web, per il server Web viene usata una porta casuale. Quando si esegue l'app verrà visualizzato un numero di porta diverso.
* È possibile scegliere se avviare l'app in modalità di debug o non di dal menu **Esegui**.

  La figura seguente mostra l'app:

  ![Pagina Home o di indice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Nella parte seguente di questa esercitazione vengono fornite informazioni su MVC e istruzioni per iniziare a creare codice.

> [!div class="step-by-step"]
> [Successivo](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Questa esercitazione illustra le nozioni di base della creazione di un'app Web ASP.NET Core MVC.

L'app gestisce un database di titoli di film. Vengono illustrate le seguenti procedure:

> [!div class="checklist"]
> * Creare un'app Web.
> * Aggiungere un modello ed eseguirne lo scaffolding.
> * Usare un database.
> * Aggiungere ricerca e convalida.

Al termine di queste operazioni si ottiene un'app che può gestire e visualizzare dati relativi ai film.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a>Creare un'app Web

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Dal menu di Visual Studio selezionare **Crea un nuovo progetto**.

* Selezionare **Applicazione Web ASP.NET Core** e quindi selezionare **Avanti**.

![nuova applicazione Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* Assegnare al progetto il nome **MvcMovie** e selezionare **Crea**. È importante assegnare al progetto il nome **MvcMovie**, in modo che quando si copia il codice lo spazio dei nomi corrisponda.

  ![nuova applicazione Web ASP.NET Core](start-mvc/_static/config.png)


* Selezionare **Applicazione Web (MVC)** e quindi selezionare **Crea**.

![Finestra di dialogo Nuovo progetto, .NET Core nel riquadro sinistro, Web ASP.NET Core ](start-mvc/_static/new_project22-21.png)

Per il progetto MVC appena creato Visual Studio ha usato il modello predefinito. Ora è possibile disporre di un'app funzionante immettendo un nome progetto e selezionando alcune opzioni. Si tratta di un progetto iniziale di base che rappresenta un ottimo punto di partenza.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Nell'esercitazione si presuppone una familarità con Visual Studio Code. Vedere [Introduzione a VS Code](https://code.visualstudio.com/docs) e [Guida a Visual Studio Code](#visual-studio-code-help) per altre informazioni.

* Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Cambiare directory (`cd`) e passare alla cartella che conterrà il progetto.
* Eseguire il comando seguente:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Viene visualizzata una finestra di dialogo con le **risorse necessarie per la compilazione e il debug non è presente in ' MvcMovie '. Aggiungerli?**  Selezionare **Sì**.

  * `dotnet new mvc -o MvcMovie`: crea un nuovo progetto ASP.NET Core MVC nella cartella *MvcMovie*.
  * `code -r MvcMovie`: carica il file di progetto *MvcMovie. csproj* in Visual Studio Code.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Selezionare **File** > **nuova soluzione**.

  ![Nuova soluzione macOS](./start-mvc/_static/new_project_vsmac.png)

* Selezionare l'applicazione Web **.NET Core** > **app** > **(Model-View-Controller)** > **Avanti**.

  ![Finestra di dialogo Nuovo progetto di macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* Nella finestra di dialogo **Configura la nuova API Web ASP.NET Core** accettare l'impostazione predefinita di **Framework di destinazione**, ovvero **.NET Core 2.2**.

  ![Selezione di .NET Core 2.2 per macOS](./start-mvc/_static/new_project_22_vsmac.png)

* Denominare il progetto **MvcMovie**, quindi selezionare **Crea**.

---

### <a name="run-the-app"></a>Eseguire l'app

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Selezionare **CTRL+F5** per eseguire l'app in modalità non di debug.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio avvia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ed esegue l'app. Si noti che la barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Quando Visual Studio crea un progetto Web, per il server Web viene usata una porta casuale.
* Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice. Molti sviluppatori preferiscono usare la modalità non di debug per avviare l'app rapidamente e visualizzare le modifiche.
* È possibile scegliere se avviare l'app in modalità di debug o non di debug nella voce di menu **Debug**:

  ![Debug - menu](start-mvc/_static/debug_menu.png)

* È possibile eseguire il debug dell'app toccando il pulsante **IIS Express**.

  ![IIS Express](start-mvc/_static/iis_express.png)

* Selezionare **Accept** (Accetto) per autorizzare il rilevamento. Questa app non rileva informazioni personali. Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).

  ![Pagina Home o di indice](start-mvc/_static/privacy.png)

  La figura seguente illustra l'app dopo aver accettato il rilevamento:

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Premere CTRL+F5 per l'esecuzione senza il debugger.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code avvia [Kestrel](xref:fundamentals/servers/kestrel), avvia un browser e passa a `https://localhost:5001`. La barra degli indirizzi visualizza `localhost:port:5001` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Localhost viene usato solo per le richieste web del computer locale.

  Se si avvia l'app con CTRL+F5 (modalità non di debug) sarà possibile apportare modifiche al codice, salvare il file, aggiornare il browser e visualizzare le modifiche del codice. Molti sviluppatori preferiscono usare la modalità non di debug per aggiornare la pagina e visualizzare le modifiche.

* Selezionare **Accept** (Accetto) per autorizzare il rilevamento. Questa app non rileva informazioni personali. Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).

  ![Pagina Home o di indice](start-mvc/_static/privacy.png)

  La figura seguente illustra l'app dopo aver accettato il rilevamento:

  ![Pagina Home o di indice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Per avviare l'app, selezionare **Esegui** > **Avvia senza eseguire debug**. Visual Studio per Mac avvia il server [Kestrel](xref:fundamentals/servers/index#kestrel), apre un browser e naviga all'indirizzo `http://localhost:port`, dove *port* è un numero di porta selezionato a caso.

[!INCLUDE[](~/includes/trustCertMac.md)]

* La barra degli indirizzi visualizza `localhost:port#` e non `example.com` o simili. Ciò accade perché `localhost` è il nome host standard per il computer locale. Quando Visual Studio crea un progetto Web, per il server Web viene usata una porta casuale. Quando si esegue l'app verrà visualizzato un numero di porta diverso.
* È possibile scegliere se avviare l'app in modalità di debug o non di dal menu **Esegui**.

* Selezionare **Accept** (Accetto) per autorizzare il rilevamento. Questa app non rileva informazioni personali. Il codice generato del modello include asset che consentono di soddisfare il [Regolamento generale sulla protezione dei dati (GDPR)](xref:security/gdpr).

  ![Pagina Home o di indice](./start-mvc/_static/output_privacy_macos.png)

  La figura seguente illustra l'app dopo aver accettato il rilevamento:

  ![Pagina Home o di indice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Nella parte seguente di questa esercitazione vengono fornite informazioni su MVC e istruzioni per iniziare a creare codice.

> [!div class="step-by-step"]
> [Successivo](adding-controller.md)

::: moniker-end
