---
title: "Esercitazione: Creare un'API Web con ASP.NET Core"
author: rick-anderson
description: Informazioni su come creare un'API Web con ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 2d0eb24641c3d1f795b9e85ce10d42ee96d30846
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187304"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Esercitazione: Creare un'API Web con ASP.NET Core

Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Mike Wasson](https://github.com/mikewasson)

Questa esercitazione illustra le nozioni di base della creazione di un'API Web con ASP.NET Core.

::: moniker range=">= aspnetcore-3.0"

In questa esercitazione si imparerà a:

> [!div class="checklist"]
> * Creare un progetto di API Web.
> * Aggiungere una classe modello e un contesto di database.
> * Eseguire lo scaffolding di un controller con i metodi CRUD.
> * Configurare il routing, i percorsi URL e i valori restituiti.
> * Chiamare l'API Web con Postman.

Al termine si avrà un'API Web che può gestire gli elementi di tipo "attività" archiviati in un database.

## <a name="overview"></a>Panoramica

Questa esercitazione consente di creare l'API seguente:

|API | Descrizione | Corpo della richiesta | Corpo della risposta |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Ottiene tutti gli elementi attività | nessuno | Matrice di elementi attività|
|GET /api/TodoItems/{id} | Ottiene un elemento in base all'ID | nessuno | Elemento attività|
|POST /api/TodoItems | Aggiunge un nuovo elemento | Elemento attività | Elemento attività |
|PUT /api/TodoItems/{id} | Aggiorna un elemento esistente &nbsp; | Elemento attività | nessuno |
|DELETE /api/TodoItems/{id} &nbsp; &nbsp; | Elimina un elemento &nbsp; &nbsp; | nessuno | nessuno|

Il diagramma seguente visualizza la struttura dell'app.

![Il client è rappresentato da una casella a sinistra. Invia una richiesta e riceve una risposta dall'applicazione, una casella disegnata a destra. Nella riquadro dell'applicazione tre caselle rappresentano il controller, il modello e il livello di accesso ai dati. La richiesta viene ricevuta dal controller dell'applicazione e vengono eseguite operazioni di lettura/scrittura tra il controller e il livello di accesso ai dati. Il modello viene serializzato e restituito al client nella risposta.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a>Creare un progetto Web

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Scegliere **Nuovo** > **Progetto** dal menu **File**.
* Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti**.
* Assegnare al progetto il nome *TodoApi* e fare clic su **Crea**.
* Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.NET Core** e **ASP.NET Core 3.0**. Selezionare il modello **API** e fare clic su **Crea**. **Non** selezionare **Abilita supporto Docker**.

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.
* Eseguire i comandi seguenti:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì**.

  I comandi precedenti:

  * Crea un nuovo progetto API Web e lo apre in Visual Studio Code.
  * Aggiunge i pacchetti NuGet necessari nella sezione successiva.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Selezionare **File** > **Nuova soluzione**.

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* Selezionare **.NET Core** > **App** > **API** > **Avanti**.

  ![Finestra di dialogo Nuovo progetto di macOS](first-web-api-mac/_static/1.png)
  
* Nella finestra di dialogo **Configura la nuova API Web ASP.NET Core** selezionare il **framework di destinazione** * *.NET Core 3.0*.

* Immettere *TodoApi* in **Nome progetto**, quindi selezionare **Crea**.

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Aprire un terminale di comando nella cartella di progetto ed eseguire i comandi seguenti:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>Testare l'API

Il modello di progetto crea un'API `WeatherForecast`. Chiamare il metodo `Get` da un browser per eseguire il test dell'app.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Premere CTRL+F5 per eseguire l'app. Visual Studio apre un browser e naviga all'indirizzo `https://localhost:<port>/WeatherForecast`, dove `<port>` è un numero di porta selezionato a caso.

Se in una finestra di dialogo viene chiesto se considerare attendibile il certificato IIS Express, selezionare **Sì**. Nella finestra di dialogo **Avviso di sicurezza** successiva selezionare **Sì**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Premere CTRL+F5 per eseguire l'app. In un browser passare all'URL seguente: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Selezionare **Esegui** > **Avvia debug** per avviare l'app. Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso. Viene restituito un errore HTTP 404 (Non trovato). Accodare `/WeatherForecast` all'URL (impostare l'URL su `https://localhost:<port>/WeatherForecast`).

---

Viene restituito un codice JSON simile al seguente:

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a>Aggiungere una classe del modello

Un *modello* è un set di classi che rappresentano i dati gestiti dall'app. Il modello per questa app è una classe `TodoItem` singola.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto. Selezionare **Aggiungi** > **Nuova cartella**. Assegnare il nome *Models* alla cartella.

* Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**. Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi**.

* Sostituire il codice del modello con il codice seguente:

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Aggiungere una cartella denominata *Models*.

* Aggiungere una classe `TodoItem` alla cartella *Models* con il codice seguente:

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Fare clic con il pulsante destro del mouse sul progetto. Selezionare **Aggiungi** > **Nuova cartella**. Assegnare il nome *Models* alla cartella.

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Nuovo file** > **Generale** > **Classe vuota**.

* Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo**.

* Sostituire il codice del modello con il codice seguente:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

La proprietà `Id` funziona come chiave univoca in un database relazionale.

È possibile inserire le classi del modello in qualsiasi punto del progetto, ma per convenzione viene usata la cartella *Models*.

## <a name="add-a-database-context"></a>Aggiungere un contesto di database

Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati. Questa classe viene creata mediante derivazione dalla classe `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a>Aggiungere Microsoft.EntityFrameworkCore.SqlServer

* Dal menu **Strumenti** selezionare **Gestione pacchetti NuGet > Gestisci pacchetti NuGet per la soluzione**.
* Selezionare la casella di controllo **Includi versione preliminare**.
* Selezionare la scheda **Esplora** e quindi immettere **Microsoft.EntityFrameworkCore.SqlServer** nella casella di ricerca.
* Selezionare **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** nel riquadro sinistro.
* Selezionare la casella di controllo **Progetto** nel riquadro di destra e quindi selezionare **Installa**.
* Usare le istruzioni precedenti per aggiungere il pacchetto NuGet `Microsoft.EntityFrameworkCore.InMemory`.

![Gestione pacchetti NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>Aggiungere il contesto del database TodoContext

* Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**. Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

* Aggiungere una classe `TodoContext` alla cartella *Models*.

---

* Immettere il codice seguente:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrare il contesto del database

In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection). Il contenitore rende disponibile il servizio ai controller.

Aggiornare *Startup.cs* con il codice evidenziato seguente:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Il codice precedente:

* Rimuove le dichiarazioni `using` inutilizzate.
* Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.
* Specifica che il contesto del database userà un database in memoria.

## <a name="scaffold-a-controller"></a>Eseguire lo scaffolding di un controller

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Fare clic con il pulsante destro del mouse sulla cartella *Controllers*.
* Selezionare **Aggiungi** > **Nuovo elemento di scaffolding**.
* Selezionare **Controller API con azioni, che usa Entity Framework** e quindi selezionare **Aggiungi**.
* Nella finestra di dialogo **Add API Controller with actions, using Entity Framework** (Aggiungi controller API con azioni, che usa Entity Framework):

  * Selezionare **TodoItem (TodoAPI.Models)** in **Classe modello**.
  * Selezionare **TodoContext (TodoAPI.Models)** in **Classe contesto dei dati**.
  * Selezionare **Aggiungi**

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

Eseguire i comandi seguenti:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

I comandi precedenti:

* Aggiungere i pacchetti NuGet necessari per lo scaffolding.
* Installa il motore di scaffolding (`dotnet-aspnet-codegenerator`).
* Esegue lo scaffolding di `TodoItemsController`.

---

Il codice generato:

* Definisce una classe controller API senza metodi.
* Contrassegna la classe con l'attributo [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute). L'attributo indica che il controller risponde alle richieste di API Web. Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.
* Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller. Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.

## <a name="examine-the-posttodoitem-create-method"></a>Esaminare il metodo di creazione PostTodoItem

Sostituire l'istruzione return in `PostTodoItem` per usare l'operatore [nameof](/dotnet/csharp/language-reference/operators/nameof):

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Il codice precedente è un metodo HTTP POST, come indicato dall'attributo [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute). Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.

Il metodo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:

* Restituisce un codice di stato HTTP 201 in caso di esito positivo. HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.
* Aggiunge un'intestazione [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) alla risposta. L'intestazione `Location` specifica l'[URI](https://developer.mozilla.org/docs/Glossary/URI) dell'elemento attività appena creato. Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`. La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.

### <a name="install-postman"></a>Installare Postman

Questa esercitazione usa Postman per testare l'API Web.

* Installare [Postman](https://www.getpostman.com/downloads/)
* Avviare l'app Web.
* Avviare Postman.
* Disattivare **SSL certificate verification** (Verifica certificato SSL)
* From  **File > Settings** (File > Impostazioni) (scheda **General* (Generale)), disattivare **SSL certificate verification** (Verifica certificato SSL).
    > [!WARNING]
    > Riattivare la verifica dei certificati SSL al termine del test del controller.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Testare PostTodoItem con Postman

* Creare una nuova richiesta.
* Impostare il metodo HTTP su `POST`.
* Fare clic sulla scheda **Body** (Corpo).
* Selezionare il pulsante di opzione **raw** (non elaborato).
* Impostare il tipo su **JSON (application/json)** .
* Nel corpo della richiesta immettere codice JSON per un elemento attività:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Selezionare **Send** (Invia).

  ![Postman con richiesta di creazione](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Testare l'URI dell'intestazione della posizione

* Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).
* Copiare il valore dell'intestazione **Location** (Posizione):

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/3/create.png)

* Impostare il metodo su GET.
* Incollare l'URI (ad esempio `https://localhost:5001/api/TodoItems/1`)
* Selezionare **Send** (Invia).

## <a name="examine-the-get-methods"></a>Esaminare i metodi GET

Questi metodi implementano due metodi GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Testare l'app chiamando i due endpoint da un browser o da Postman. Ad esempio:

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

Una risposta simile alla seguente viene generata dalla chiamata a `GetTodoItems`:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Testare Get con Postman

* Creare una nuova richiesta.
* Impostare il metodo HTTP su **GET**.
* Impostare l'URL della richiesta su `https://localhost:<port>/api/TodoItems`. Ad esempio `https://localhost:5001/api/TodoItems`.
* Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.
* Selezionare **Send** (Invia).

Questa app usa un database in memoria. Se l'app viene arrestata e avviata, la richiesta GET precedente non restituirà alcun dato. Se non vengono restituiti dati, eseguire [POST](#post) per pubblicare i dati nell'app.

## <a name="routing-and-url-paths"></a>Routing e percorsi di URL

L'attributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica un metodo che risponde a una richiesta HTTP GET. Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:

* Iniziare con la stringa di modello nell'attributo `Route` del controller:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller". In questo esempio il nome della classe controller è **TodoItems**Controller, quindi il nome del controller è "TodoItems". Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.
* Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso. In questo esempio non si usa un modello. Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività. Quando viene chiamato `GetTodoItem`, il valore di `"{id}"` viene specificato per il metodo nel rispettivo parametro `id`.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Valori restituiti

Il tipo restituito dei metodi `GetTodoItems` e `GetTodoItem` è [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta. Il codice di risposta per questo tipo restituito è 200, presupponendo che non esistano eccezioni non gestite. Le eccezioni non gestite vengono convertite in errori 5xx.

I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP. Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:

* Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un codice di errore 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).
* In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON. La restituzione di `item` risulta in una risposta HTTP 200.

## <a name="the-puttodoitem-method"></a>Metodo PutTodoItem

Esaminare il metodo `PutTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT. La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche. Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.

### <a name="test-the-puttodoitem-method"></a>Testare il metodo PutTodoItem

Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata. Deve esistere un elemento nel database prima di eseguire una chiamata PUT. Chiamare GET per assicurarsi che sia presente un elemento nel database prima di eseguire una chiamata PUT.

Aggiornare l'elemento attività con ID = 1 e impostarne il nome su "feed fish":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

L'immagine seguente visualizza l'aggiornamento di Postman:

![Console Postman con risposta 204 (No Content)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>Metodo DeleteTodoItem

Esaminare il metodo `DeleteTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

La risposta `DeleteTodoItem` è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Testare il metodo DeleteTodoItem

Usare Postman per eliminare un elemento attività:

* Impostare il metodo su `DELETE`.
* Impostare l'URI dell'oggetto da eliminare, ad esempio `https://localhost:5001/api/TodoItems/1`
* Selezionare **Send** (Invia).

## <a name="call-the-web-api-with-javascript"></a>Chiamare l'API Web con JavaScript

Vedere [l'esercitazione: Chiamare un'API Web ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In questa esercitazione si imparerà a:

> [!div class="checklist"]
> * Creare un progetto di API Web.
> * Aggiungere una classe modello e un contesto di database.
> * Aggiungere un controller.
> * Aggiungere metodi CRUD.
> * Configurare routing e percorsi URL.
> * Specificare valori restituiti.
> * Chiamare l'API Web con Postman.
> * Chiamare l'API Web con JavaScript.

Al termine si dispone di un'API web che può gestire gli elementi di tipo "attività" archiviati in un database relazionale.

## <a name="overview"></a>Panoramica

Questa esercitazione consente di creare l'API seguente:

|API | Descrizione | Corpo della richiesta | Corpo della risposta |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Ottiene tutti gli elementi attività | nessuno | Matrice di elementi attività|
|GET /api/TodoItems/{id} | Ottiene un elemento in base all'ID | nessuno | Elemento attività|
|POST /api/TodoItems | Aggiunge un nuovo elemento | Elemento attività | Elemento attività |
|PUT /api/TodoItems/{id} | Aggiorna un elemento esistente &nbsp; | Elemento attività | nessuno |
|DELETE /api/TodoItems/{id} &nbsp; &nbsp; | Elimina un elemento &nbsp; &nbsp; | nessuno | nessuno|

Il diagramma seguente visualizza la struttura dell'app.

![Il client è rappresentato da una casella a sinistra. Invia una richiesta e riceve una risposta dall'applicazione, una casella disegnata a destra. Nella riquadro dell'applicazione tre caselle rappresentano il controller, il modello e il livello di accesso ai dati. La richiesta viene ricevuta dal controller dell'applicazione e vengono eseguite operazioni di lettura/scrittura tra il controller e il livello di accesso ai dati. Il modello viene serializzato e restituito al client nella risposta.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Prerequisiti

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a>Creare un progetto Web

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Scegliere **Nuovo** > **Progetto** dal menu **File**.
* Selezionare il modello **Applicazione Web ASP.NET Core** e fare clic su **Avanti**.
* Assegnare al progetto il nome *TodoApi* e fare clic su **Crea**.
* Nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core** verificare che siano selezionati **.NET Core** e **ASP.NET Core 2.2**. Selezionare il modello **API** e fare clic su **Crea**. **Non** selezionare **Abilita supporto Docker**.

![Finestra di dialogo Nuovo progetto di Visual Studio](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Aprire il [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Cambiare directory (`cd`) e passare alla cartella che conterrà la cartella del progetto.
* Eseguire i comandi seguenti:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Questi comandi creano un nuovo progetto API Web e aprono una nuova istanza di Visual Studio Code nella nuova cartella di progetto.

* Quando una finestra di dialogo chiede se si vuole aggiungere gli asset necessari al progetto, selezionare **Sì**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Selezionare **File** > **Nuova soluzione**.

  ![Nuova soluzione macOS](first-web-api-mac/_static/sln.png)

* Selezionare **.NET Core** > **App** > **API** > **Avanti**.

  ![Finestra di dialogo Nuovo progetto di macOS](first-web-api-mac/_static/1.png)
  
* Nella finestra di dialogo **Configure your new ASP.NET Core Web API** (Configura la nuova API Web ASP.NET Core), accettare l'impostazione predefinita di **Framework di destinazione**, ovvero * *.NET Core 2.2*.

* Immettere *TodoApi* in **Nome progetto**, quindi selezionare **Crea**.

  ![Finestra di dialogo di configurazione](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>Testare l'API

Il modello di progetto crea un'API `values`. Chiamare il metodo `Get` da un browser per eseguire il test dell'app.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Premere CTRL+F5 per eseguire l'app. Visual Studio apre un browser e naviga all'indirizzo `https://localhost:<port>/api/values`, dove `<port>` è un numero di porta selezionato a caso.

Se in una finestra di dialogo viene chiesto se considerare attendibile il certificato IIS Express, selezionare **Sì**. Nella finestra di dialogo **Avviso di sicurezza** successiva selezionare **Sì**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Premere CTRL+F5 per eseguire l'app. In un browser passare all'URL seguente: [https://localhost:5001/api/values](https://localhost:5001/api/values).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

Selezionare **Esegui** > **Avvia debug** per avviare l'app. Visual Studio per Mac apre un browser e naviga all'indirizzo `https://localhost:<port>`, dove `<port>` è un numero di porta selezionato a caso. Viene restituito un errore HTTP 404 (Non trovato). Accodare `/api/values` all'URL (impostare l'URL su `https://localhost:<port>/api/values`).

---

Viene restituito il codice JSON seguente:

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>Aggiungere una classe del modello

Un *modello* è un set di classi che rappresentano i dati gestiti dall'app. Il modello per questa app è una classe `TodoItem` singola.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto. Selezionare **Aggiungi** > **Nuova cartella**. Assegnare il nome *Models* alla cartella.

* Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**. Assegnare alla classe il nome *TodoItem* e selezionare **Aggiungi**.

* Sostituire il codice del modello con il codice seguente:

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Aggiungere una cartella denominata *Models*.

* Aggiungere una classe `TodoItem` alla cartella *Models* con il codice seguente:

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio per Mac](#tab/visual-studio-mac)

* Fare clic con il pulsante destro del mouse sul progetto. Selezionare **Aggiungi** > **Nuova cartella**. Assegnare il nome *Models* alla cartella.

  ![Nuova cartella](first-web-api-mac/_static/folder.png)

* Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Nuovo file** > **Generale** > **Classe vuota**.

* Assegnare alla classe il nome *TodoItem* e fare clic su **Nuovo**.

* Sostituire il codice del modello con il codice seguente:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

La proprietà `Id` funziona come chiave univoca in un database relazionale.

È possibile inserire le classi del modello in qualsiasi punto del progetto, ma per convenzione viene usata la cartella *Models*.

## <a name="add-a-database-context"></a>Aggiungere un contesto di database

Il *contesto di database* è la classe principale che coordina le funzionalità di Entity Framework per un modello di dati. Questa classe viene creata mediante derivazione dalla classe `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Fare clic con il pulsante destro del mouse sulla cartella *Models* e scegliere **Aggiungi** > **Classe**. Assegnare alla classe il nome *TodoContext* e fare clic su **Aggiungi**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

* Aggiungere una classe `TodoContext` alla cartella *Models*.

---

* Sostituire il codice del modello con il codice seguente:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrare il contesto del database

In ASP.NET Core i servizi come il contesto del database devono essere registrati con il [contenitore di inserimento delle dipendenze](xref:fundamentals/dependency-injection). Il contenitore rende disponibile il servizio ai controller.

Aggiornare *Startup.cs* con il codice evidenziato seguente:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Il codice precedente:

* Rimuove le dichiarazioni `using` inutilizzate.
* Aggiunge il contesto del database al contenitore di inserimento delle dipendenze.
* Specifica che il contesto del database userà un database in memoria.

## <a name="add-a-controller"></a>Aggiungere un controller

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Fare clic con il pulsante destro del mouse sulla cartella *Controllers*.
* Selezionare **Aggiungi** > **Nuovo elemento**.
* Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare il modello **API Controller Class** (Classe controller API).
* Assegnare alla classe il nome *TodoController* e selezionare **Aggiungi**.

  ![Finestra di dialogo Aggiungi nuovo elemento con controller nella casella di ricerca e controller API Web selezionato](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

* Nella cartella *Controllers* creare una classe denominata `TodoController`.

---

* Sostituire il codice del modello con il codice seguente:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Il codice precedente:

* Definisce una classe controller API senza metodi.
* Contrassegna la classe con l'attributo [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute). L'attributo indica che il controller risponde alle richieste di API Web. Per informazioni sui comportamenti specifici consentiti dall'attributo, vedere <xref:web-api/index>.
* Usa l'inserimento delle dipendenze per inserire il contesto del database (`TodoContext`) nel controller. Il contesto di database viene usato in ognuno dei metodi [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) nel controller.
* Aggiunge un elemento denominato `Item1` al database se il database è vuoto. Questo codice si trova nel costruttore, quindi viene eseguito ogni volta che è presente una nuova richiesta HTTP. Se si eliminano tutti gli elementi, il costruttore crea di nuovo `Item1` quando viene nuovamente chiamato un metodo API. Potrebbe pertanto sembrare che l'eliminazione non abbia funzionato, mentre di fatto ha funzionato.

## <a name="add-get-methods"></a>Aggiungere metodi Get

Per specificare un'API che recupera elementi attività, aggiungere i metodi seguenti alla classe `TodoController`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Questi metodi implementano due metodi GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Arrestare l'app se è ancora in esecuzione. Quindi eseguirla di nuovo per includere le modifiche più recenti.

Testare l'app chiamando i due endpoint da un browser. Ad esempio:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

La chiamata a `GetTodoItems` genera la risposta HTTP seguente:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>Routing e percorsi di URL

L'attributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica un metodo che risponde a una richiesta HTTP GET. Il percorso dell'URL per ogni metodo viene costruito nel modo seguente:

* Iniziare con la stringa di modello nell'attributo `Route` del controller:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Sostituire `[controller]` con il nome del controller, ovvero, per convenzione, il nome della classe controller meno il suffisso "Controller". In questo esempio il nome della classe controller è **Todo**Controller, pertanto il nome del controller è "todo". Il [routing](xref:mvc/controllers/routing) ASP.NET Core non fa distinzione tra maiuscole e minuscole.
* Se l'attributo `[HttpGet]` ha un modello di route, ad esempio `[HttpGet("products")]`, aggiungerlo al percorso. In questo esempio non si usa un modello. Per altre informazioni, vedere [Routing con attributi Http[verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

Nel metodo `GetTodoItem` seguente, `"{id}"` è una variabile segnaposto per l'identificatore univoco dell'elemento attività. Quando viene chiamato `GetTodoItem`, il valore di `"{id}"` viene specificato per il metodo nel rispettivo parametro `id`.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Valori restituiti

Il tipo restituito dei metodi `GetTodoItems` e `GetTodoItem` è [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core serializza automaticamente l'oggetto su [JSON](https://www.json.org/) e scrive il codice JSON nel corpo del messaggio di risposta. Il codice di risposta per questo tipo restituito è 200, presupponendo che non esistano eccezioni non gestite. Le eccezioni non gestite vengono convertite in errori 5xx.

I tipi restituiti `ActionResult` possono rappresentare un ampio intervallo di codici di stato HTTP. Ad esempio, `GetTodoItem` può restituire due valori di stato diversi:

* Se nessun elemento corrisponde all'ID richiesto, il metodo restituisce un codice di errore 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).
* In caso contrario, il metodo restituisce il codice 200 con un corpo della risposta JSON. La restituzione di `item` risulta in una risposta HTTP 200.

## <a name="test-the-gettodoitems-method"></a>Testare il metodo GetTodoItems

Questa esercitazione usa Postman per testare l'API Web.

* Installare [Postman](https://www.getpostman.com/downloads/)
* Avviare l'app Web.
* Avviare Postman.
* Disattivare **SSL certificate verification** (Verifica certificato SSL)

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Da **File** > **Settings** (Impostazioni) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL).

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio per Mac](#tab/visual-studio-code+visual-studio-mac)

* Da **Postman**  >  **Preferences** (Preferenze) (scheda **General** (Generale)) disabilitare **SSL certificate verification** (Verifica certificato SSL). In alternativa, selezionare la chiave inglese e selezionare **Settings** (Impostazioni), quindi disabilitare la verifica del certificato SSL.

---
  
> [!WARNING]
> Riattivare la verifica dei certificati SSL al termine del test del controller.

* Creare una nuova richiesta.
  * Impostare il metodo HTTP su **GET**.
  * Impostare l'URL della richiesta su `https://localhost:<port>/api/todo`. Ad esempio `https://localhost:5001/api/todo`.
* Impostare **Two pane view** (Visualizzazione in due riquadri) in Postman.
* Selezionare **Send** (Invia).

![Postman con richiesta GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Aggiungere un metodo Create

Aggiungere il metodo `PostTodoItem` seguente in *Controllers/TodoController.cs*: 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Il codice precedente è un metodo HTTP POST, come indicato dall'attributo [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute). Il metodo ottiene il valore dell'elemento attività dal corpo della richiesta HTTP.

Il metodo `CreatedAtAction`:

* Restituisce un codice di stato HTTP 201 in caso di esito positivo. HTTP 201 è la risposta standard per un metodo HTTP POST che crea una nuova risorsa nel server.
* Aggiunge un'intestazione `Location` alla risposta. L'intestazione `Location` specifica l'URI dell'elemento attività appena creato. Per altre informazioni, vedere [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Fa riferimento all'azione `GetTodoItem` per creare l'URI dell'intestazione `Location`. La parola chiave `nameof` C# viene usata per evitare di impostare il nome dell'azione come hardcoded nella chiamata a `CreatedAtAction`.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>Testare il metodo PostTodoItem

* Compilare il progetto.
* In Postman impostare il metodo HTTP su `POST`.
* Fare clic sulla scheda **Body** (Corpo).
* Selezionare il pulsante di opzione **raw** (non elaborato).
* Impostare il tipo su **JSON (application/json)** .
* Nel corpo della richiesta immettere codice JSON per un elemento attività:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Selezionare **Send** (Invia).

  ![Postman con richiesta di creazione](first-web-api/_static/create.png)

  Se viene visualizzato un errore HTTP 405 - Metodo non consentito, è probabile che il progetto non sia stato compilato dopo l'aggiunta del metodo `PostTodoItem`.

### <a name="test-the-location-header-uri"></a>Testare l'URI dell'intestazione della posizione

* Selezionare la scheda **Headers** (Intestazioni) nel riquadro **Response** (Risposta).
* Copiare il valore dell'intestazione **Location** (Posizione):

  ![Scheda Headers (Intestazioni) della console Postman](first-web-api/_static/pmc2.png)

* Impostare il metodo su GET.
* Incollare l'URI (ad esempio `https://localhost:5001/api/Todo/2`)
* Selezionare **Send** (Invia).

## <a name="add-a-puttodoitem-method"></a>Aggiungere un metodo PutTodoItem

Aggiungere il metodo `PutTodoItem` seguente:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` è simile a `PostTodoItem` ma usa la richiesta HTTP PUT. La risposta è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). In base alla specifica HTTP, una richiesta PUT richiede che il client invii l'intera entità aggiornata e non solo le modifiche. Per supportare gli aggiornamenti parziali, usare [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Se si riceve un errore chiamando `PutTodoItem`, chiamare `GET` per verificare che il database includa un elemento.

### <a name="test-the-puttodoitem-method"></a>Testare il metodo PutTodoItem

Questo esempio usa un database in memoria che deve essere inizializzato ogni volta che l'app viene avviata. Deve esistere un elemento nel database prima di eseguire una chiamata PUT. Chiamare GET per assicurarsi che sia presente un elemento nel database prima di eseguire una chiamata PUT.

Aggiornare l'elemento attività con id = 1 e impostarne il nome su "feed fish":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

L'immagine seguente visualizza l'aggiornamento di Postman:

![Console Postman con risposta 204 (No Content)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>Aggiungere un metodo DeleteTodoItem

Aggiungere il metodo `DeleteTodoItem` seguente:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

La risposta `DeleteTodoItem` è [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Testare il metodo DeleteTodoItem

Usare Postman per eliminare un elemento attività:

* Impostare il metodo su `DELETE`.
* Impostare l'URI dell'oggetto da eliminare, ad esempio `https://localhost:5001/api/todo/1`
* Selezionare **Send** (Invia).

L'app di esempio consente di eliminare tutti gli elementi. Quando viene eliminato l'ultimo elemento, tuttavia, ne viene creato uno nuovo dal costruttore della classe modello alla successiva chiamata dell'API.

## <a name="call-the-web-api-with-javascript"></a>Chiamare l'API Web con JavaScript

In questa sezione viene aggiunta una pagina HTML che usa JavaScript per chiamare l'API Web. L'API Fetch avvia la richiesta. JavaScript aggiorna la pagina con i dettagli della risposta dell'API Web.

Configurare l'app per [servire file statici](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [abilitare il mapping del file predefinito](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) aggiornando *Startup.cs* con il codice evidenziato seguente:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Creare una cartella *wwwroot* nella directory del progetto.

Aggiungere un file HTML denominato *index.html* alla directory *wwwroot*. Sostituire il contenuto con il markup seguente:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Aggiungere un file JavaScript con nome *site.js* alla directory *wwwroot*. Sostituire il contenuto con il codice seguente:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Può essere necessario modificare le impostazioni di avvio del progetto ASP.NET Core per il test della pagina HTML in locale:

* Aprire *Properties\launchSettings.json*.
* Rimuovere la proprietà `launchUrl` per forzare l'app ad aprire *index.html*, il file predefinito del progetto.

In questo esempio vengono chiamati tutti i metodi CRUD dell'API Web. Di seguito sono incluse le spiegazioni delle chiamate all'API.

### <a name="get-a-list-of-to-do-items"></a>Ottenere un elenco di elementi attività

Fetch invia una richiesta HTTP GET all'API Web, la quale restituisce codice JSON che rappresenta una matrice di elementi attività. La funzione di callback `success` viene chiamata se la richiesta ha esito positivo. Nel callback il modello DOM viene aggiornato con le informazioni dell'elemento attività.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Aggiungere un elemento attività

Fetch invia una richiesta HTTP POST con l'elemento attività nel corpo della richiesta. Le opzioni `accepts` e `contentType` sono impostate su `application/json` per specificare il tipo di supporto ricevuto e inviato. L'elemento attività viene convertito in JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Quando l'API restituisce un codice di stato di esecuzione riuscita, viene chiamata la funzione `getData` per aggiornare la tabella HTML.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Aggiornare un elemento attività

L'aggiornamento di un elemento attività è simile all'aggiunta di un elemento di questo tipo. L'`url` cambia per includere l'identificatore univoco dell'elemento e `type` è `PUT`.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Eliminare un elemento attività

È possibile eliminare un elemento attività impostando `type` su `DELETE` nella chiamata AJAX e specificando l'identificatore univoco dell'elemento nell'URL.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

[Visualizzare o scaricare il codice di esempio per questa esercitazione](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Vedere [come scaricare un esempio](xref:index#how-to-download-a-sample).

Per altre informazioni, vedere le seguenti risorse:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Versione YouTube dell'esercitazione](https://www.youtube.com/watch?v=TTkhEyGBfAk)
