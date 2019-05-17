---
title: Creare la prima app Blazor
author: guardrex
description: Istruzioni dettagliate per creare un'app Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: c1b142ebdbd85eb10ddf8c8b70edd9782732a4f1
ms.sourcegitcommit: 3ee6ee0051c3d2c8d47a58cb17eef1a84a4c46a0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65621098"
---
# <a name="build-your-first-blazor-app"></a>Creare la prima app Blazor

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Questa esercitazione mostra come compilare e modificare un'app Blazor.

Seguire le indicazioni contenute nell'articolo <xref:blazor/get-started> per creare un progetto Blazor per questa esercitazione.

## <a name="build-components"></a>Compilare i componenti

1. Passare a ognuna delle tre pagine dell'app nella cartella *Pages*: Home, Counter e Fetch data (Home, Contatore e Recupera dati). Queste pagine vengono implementate dai file dei componenti Razor *Index.razor*, *Counter.razor* e *FetchData.razor*.

1. Nella pagina Counter selezionare il pulsante **Click me** per incrementare il contatore senza un aggiornamento della pagina. Per incrementare un contatore in una pagina Web è in genere richiesta la scrittura di codice JavaScript, ma Blazor offre un approccio migliore usando C#.

1. Esaminare l'implementazione del componente Counter nel file *Counter.razor*.

   *Pages/Counter.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   L'interfaccia utente del componente Counter viene definita tramite codice HTML. La logica di rendering dinamico (ad esempio, cicli, condizioni, espressioni) viene aggiunta usando una sintassi C# incorporata chiamata [Razor](xref:mvc/views/razor). Il markup HTML e la logica di rendering C# vengono convertiti un una classe del componente in fase di compilazione. Il nome della classe .NET generata corrisponde al nome del file.

   I membri della classe del componente vengono definiti in un blocco `@functions`. Nel blocco `@functions`, lo stato del componente (proprietà, campi) e i metodi vengono specificati per la gestione degli eventi o per definire la logica di altri componenti. Questi membri vengono quindi usati come parte della logica di rendering del componente e per la gestione degli eventi.

   Quando viene selezionato il pulsante **Click me**:

   * Viene chiamato il gestore `onclick` registrato del componente Counter (metodo `IncrementCount`).
   * Il componente Counter rigenera il relativo albero di rendering.
   * Il nuovo albero di rendering viene confrontato con quello precedente.
   * Vengono applicate solo le modifiche al modello DOM (Document Object Model). Il conteggio visualizzato viene aggiornato.

1. Modificare la logica C# del componente Counter per incrementare il contatore di due unità anziché una.

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Ricompilare ed eseguire l'app per visualizzare le modifiche. Selezionare il pulsante **Fare clic qui**. Il contatore viene incrementato di due.

## <a name="use-components"></a>Usare i componenti

Includere un componente in un altro componente usando una sintassi HTML.

1. Aggiungere il componente Counter al componente Index dell'app aggiungendo un elemento `<Counter />` al componente Index (*Index.razor*).

   Se si usa il lato client di Blazor per questa esperienza, il componente Index include un componente Survey Prompt (elemento `<SurveyPrompt>`). Sostituire l'elemento `<SurveyPrompt>` con l'elemento `<Counter>`. Se si usa un'app lato server Blazor per questa esperienza, aggiungere l'elemento `<Counter>` al componente Index:

   *Pages/Index.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Ricompilare ed eseguire l'app. Il componente Index ha il proprio contatore.

## <a name="component-parameters"></a>Parametri del componente

I componenti possono avere anche parametri, che vengono definiti usando proprietà non pubbliche nella classe del componente decorata con `[Parameter]`. Usare gli attributi per specificare gli argomenti per un componente nel markup.

1. Aggiornare il codice C# `@functions` del componente:

   * Aggiungere una proprietà `IncrementAmount` decorata con l'attributo `[Parameter]`.
   * Modificare il metodo `IncrementCount` per usare `IncrementAmount` quando si aumenta il valore di `currentCount`.

   *Pages/Counter.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. Specificare un parametro `IncrementAmount` nell'elemento `<Counter>` del componente Index usando un attributo. Impostare il valore per incrementare il contatore di dieci unità.

   *Pages/Index.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Ricaricare il componente Index. Il contatore viene incrementato di dieci unità ogni volta che viene selezionato il pulsante **Click me**. Il contatore nel componente Counter continua a essere incrementato di uno.

## <a name="route-to-components"></a>Indirizzare le richieste ai componenti

La direttiva `@page` all'inizio del file *Counter.razor* specifica che il componente Counter è un endpoint di routing. Il componente Counter gestisce le richieste inviate a `/counter`. Senza la direttiva `@page`, un componente non gestisce le richieste instradate, ma può comunque essere usato da altri componenti.

## <a name="dependency-injection"></a>Inserimento di dipendenze

I servizi registrati nel contenitore del servizio dell'app sono disponibili per i componenti tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection). Inserire i servizi in un componente usando la direttiva `@inject`.

Esaminare le direttive del componente FetchData.

Se si usa un'app lato server Blazor, il servizio `WeatherForecastService` viene registrato come [singleton](xref:fundamentals/dependency-injection#service-lifetimes), in modo che un'istanza del servizio sia disponibile nell'intera app. La direttiva `@inject` viene usata per inserire l'istanza del servizio `WeatherForecastService` nel componente.

*Pages/FetchData.razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Il componente FetchData usa il servizio inserito, come `ForecastService`, per recuperare una matrice di oggetti `WeatherForecast`:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

Se si usa un'app lato client Blazor, viene inserito `HttpClient` per ottenere i dati delle previsioni meteo dal file *weather.json* nella cartella *wwwroot/sample-data*:

*Pages/FetchData.razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

Viene usato un ciclo [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) per eseguire il rendering di ogni istanza di previsione come riga nella tabella dei dati meteo:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]


## <a name="build-a-todo-list"></a>Compilare un elenco attività

Aggiungere all'app un nuovo componente che implementa un semplice elenco attività.

1. Aggiungere un file vuoto denominato *Todo.razor* all'app nella cartella *Pages*:

1. Specificare il markup iniziale per il componente:

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. Aggiungere il componente Todo alla barra di spostamento.

   Il componente NavMenu (*Shared/NavMenu.razor*) viene usato nel layout dell'app. I layout sono componenti che consentono di evitare la duplicazione del contenuto nell'app. Per ulteriori informazioni, vedere <xref:blazor/layouts>.

   Aggiungere un elemento `<NavLink>` per il componente Todo aggiungendo il markup seguente per la voce di elenco sotto le voci di elenco esistenti nel file *Shared/NavMenu.razor*:

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Ricompilare ed eseguire l'app. Visitare la nuova pagina Todo per confermare che il collegamento al componente Todo funzioni correttamente.

1. Se si compila un'app lato server Blazor, aggiungere lo spazio dei nomi dell'app al file *\_Imports.razor*. L'istruzione `@using` seguente presuppone che lo spazio dei nomi dell'app sia `WebApplication`:

   ```cshtml
   @using WebApplication
   ```
   
   Le app lato client Blazor includono lo spazio dei nomi dell'app per impostazione predefinita nel file *\_Imports.razor*.

1. Aggiungere un file *TodoItem.cs* nella radice del progetto per contenere una classe per rappresentare un elemento attività. Usare il codice C# seguente per la classe `TodoItem`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Tornare al componente Todo (*Pages/Todo.razor*):

   * Aggiungere un campo per le voci todo in un blocco `@functions`. Il componente Todo usa questo campo per mantenere lo stato dell'elenco attività.
   * Aggiungere il markup per l'elenco non ordinato e un ciclo `foreach` per eseguire il rendering di ogni elemento attività come elemento di elenco.

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. L'app richiede elementi dell'interfaccia utente per l'aggiunta di voci todo all'elenco. Aggiungere un input di testo e un pulsante sotto l'elenco:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Ricompilare ed eseguire l'app. Quando viene selezionato il pulsante **Add todo** non accade nulla perché al pulsante non è associato un gestore eventi.

1. Aggiungere un metodo `AddTodo` al componente Todo e registrarlo per i clic sul pulsante con l'attributo `onclick`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   Il metodo C# `AddTodo` viene chiamato quando viene selezionato il pulsante.

1. Per ottenere il titolo del nuovo elemento attività, aggiungere un campo stringa `newTodo` e associarlo al valore dell'input di testo tramite l'attributo `bind`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo">
   ```

1. Aggiornare il metodo `AddTodo` per aggiungere l'elemento `TodoItem` con il titolo specificato all'elenco. Cancellare il valore dell'input di testo impostando `newTodo` su una stringa vuota:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Ricompilare ed eseguire l'app. Aggiungere alcune voci todo all'elenco todo per testare il nuovo codice.

1. Il testo del titolo per ogni elemento attività può essere reso modificabile e una casella di controllo può consentire all'utente di tenere traccia degli elementi completati. Aggiungere un input casella di controllo per ogni elemento attività e associarne il valore alla proprietà `IsDone`. Modificare `@todo.Title` in un elemento `<input>` associato a `@todo.Title`:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Per verificare che questi valori siano associati, aggiornare l'intestazione `<h1>` per visualizzare un conteggio del numero di elementi attività non completati (`IsDone` è `false`).

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. Componente Todo completato (*Pages/Todo.razor*):

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Ricompilare ed eseguire l'app. Aggiungere elementi attività per testare il nuovo codice.

## <a name="publish-and-deploy-the-app"></a>Pubblicare e distribuire l'app

Per pubblicare l'app, vedere <xref:host-and-deploy/blazor/index>.
