---
title: Gestione dello stato di ASP.NET Core Blazer
author: guardrex
description: Informazioni su come salvare in modo permanente lo stato nelle app del server blazer.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/state-management
ms.openlocfilehash: 67042fa9b86125fe95d877dbce246abeb6f35dd0
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391268"
---
# <a name="aspnet-core-blazor-state-management"></a><span data-ttu-id="ee4b3-103">Gestione dello stato di ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="ee4b3-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="ee4b3-104">Di [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="ee4b3-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ee4b3-105">Il server blazer è un Framework di app con stato.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="ee4b3-106">Nella maggior parte dei casi, l'app mantiene una connessione continuativa al server.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="ee4b3-107">Lo stato dell'utente viene mantenuto nella memoria del server in un *circuito*.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="ee4b3-108">Di seguito sono riportati alcuni esempi di stato utilizzati per il circuito di un utente:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="ee4b3-109">La gerarchia dell'interfaccia utente sottoposta a rendering @ no__t-0The delle istanze dei componenti e l'output di rendering più recente.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="ee4b3-110">Valori dei campi e delle proprietà nelle istanze del componente.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="ee4b3-111">Dati contenuti nelle istanze del servizio [di inserimento delle dipendenze](xref:fundamentals/dependency-injection) che hanno come ambito il circuito.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="ee4b3-112">Questo articolo illustra la persistenza dello stato nelle app del server blazer.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-112">This article addresses state persistence in Blazor Server apps.</span></span> <span data-ttu-id="ee4b3-113">Le app webassembly Blazer possono sfruttare [la persistenza dello stato sul lato client nel browser](#client-side-in-the-browser) , ma richiedono soluzioni personalizzate o pacchetti di terze parti oltre l'ambito di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-113">Blazor WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="blazor-circuits"></a><span data-ttu-id="ee4b3-114">Circuiti Blazer</span><span class="sxs-lookup"><span data-stu-id="ee4b3-114">Blazor circuits</span></span>

<span data-ttu-id="ee4b3-115">Se un utente si verifica una perdita di connessione di rete temporanea, blazer tenta di riconnettere l'utente al circuito originale per poter continuare a usare l'app.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="ee4b3-116">Tuttavia, la riconnessione di un utente al circuito originale nella memoria del server non è sempre possibile:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="ee4b3-117">Il server non è in grado di mantenere sempre un circuito disconnesso.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="ee4b3-118">Il server deve rilasciare un circuito disconnesso dopo un timeout o quando il server è sottoposto a un numero eccessivo di richieste di memoria.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="ee4b3-119">Negli ambienti di distribuzione con bilanciamento del carico multiserver tutte le richieste di elaborazione del server potrebbero non essere più disponibili in un determinato momento.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="ee4b3-120">I singoli server possono avere esito negativo o essere rimossi automaticamente quando non sono più necessari per gestire il volume complessivo delle richieste.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="ee4b3-121">Il server originale potrebbe non essere disponibile quando l'utente tenta di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="ee4b3-122">L'utente potrebbe chiudere e riaprire il browser o ricaricare la pagina, che rimuove qualsiasi stato contenuto nella memoria del browser.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="ee4b3-123">Ad esempio, i valori impostati tramite le chiamate di interoperabilità JavaScript andranno perduti.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="ee4b3-124">Quando un utente non può essere riconnesso al circuito originale, l'utente riceve un nuovo circuito con uno stato vuoto.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="ee4b3-125">Questa operazione equivale a chiudere e riaprire un'app desktop.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="ee4b3-126">Mantieni stato tra circuiti</span><span class="sxs-lookup"><span data-stu-id="ee4b3-126">Preserve state across circuits</span></span>

<span data-ttu-id="ee4b3-127">In alcuni scenari è consigliabile mantenere lo stato tra i circuiti.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="ee4b3-128">Un'app può conservare dati importanti per un utente se:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="ee4b3-129">Il server Web non è più disponibile.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="ee4b3-130">Il browser dell'utente è forzato ad avviare un nuovo circuito con un nuovo server Web.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="ee4b3-131">In generale, la gestione dello stato tra circuiti si applica agli scenari in cui gli utenti stanno creando attivamente dati, non semplicemente leggendo i dati già esistenti.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="ee4b3-132">Per mantenere lo stato oltre un singolo circuito, *non archiviare semplicemente i dati nella memoria del server*.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="ee4b3-133">L'app deve salvare i dati in modo permanente in un altro percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="ee4b3-134">Il salvataggio permanente dello stato non è automatico @ no__t-0you deve eseguire i passaggi quando si sviluppa l'app per implementare la persistenza dei dati con stato.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="ee4b3-135">La persistenza dei dati è in genere necessaria solo per uno stato di valore elevato che gli utenti hanno impiegato per creare.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="ee4b3-136">Negli esempi seguenti lo stato di salvataggio permanente Risparmia tempo o facilita le attività commerciali:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="ee4b3-137">WebForm in più passaggi &ndash; è necessario che un utente immetta nuovamente i dati per diversi passaggi completati di un processo a più passaggi se il relativo stato viene perso.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="ee4b3-138">Un utente perde lo stato in questo scenario se si allontana dal modulo a più passaggi e torna al modulo in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="ee4b3-139">Carrello acquisti &ndash; è possibile mantenere un componente commercialmente importante di un'app che rappresenti potenziali ricavi.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="ee4b3-140">Un utente che perde il proprio stato e quindi il carrello della spesa può acquistare un minor numero di prodotti o servizi quando ritornano al sito in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="ee4b3-141">In genere non è necessario mantenere lo stato facilmente ricreato, ad esempio il nome utente immesso in una finestra di dialogo di accesso che non è stata inviata.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ee4b3-142">Un'app può solo salvare *lo stato dell'app*.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-142">An app can only persist *app state*.</span></span> <span data-ttu-id="ee4b3-143">Non è possibile rendere permanente le interfacce utente, ad esempio le istanze dei componenti e le relative strutture di rendering.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="ee4b3-144">I componenti e gli alberi di rendering non sono in genere serializzabili.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="ee4b3-145">Per salvare in modo permanente un elemento simile allo stato dell'interfaccia utente, ad esempio i nodi espansi di un TreeView, l'app deve avere codice personalizzato per modellare il comportamento come stato dell'app serializzabile.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="ee4b3-146">Posizione in cui salvare lo stato</span><span class="sxs-lookup"><span data-stu-id="ee4b3-146">Where to persist state</span></span>

<span data-ttu-id="ee4b3-147">Sono disponibili tre posizioni comuni per lo stato permanente in un'app Server blazer.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="ee4b3-148">Ogni approccio è più adatto a scenari diversi e presenta diverse avvertenze:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="ee4b3-149">Lato server in un database</span><span class="sxs-lookup"><span data-stu-id="ee4b3-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="ee4b3-150">URL</span><span class="sxs-lookup"><span data-stu-id="ee4b3-150">URL</span></span>](#url)
* [<span data-ttu-id="ee4b3-151">Lato client nel browser</span><span class="sxs-lookup"><span data-stu-id="ee4b3-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="ee4b3-152">Lato server in un database</span><span class="sxs-lookup"><span data-stu-id="ee4b3-152">Server-side in a database</span></span>

<span data-ttu-id="ee4b3-153">Per la persistenza dei dati permanente o per tutti i dati che devono estendersi a più utenti o dispositivi, un database lato server indipendente è quasi certamente la scelta migliore.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="ee4b3-154">Le opzioni includono:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-154">Options include:</span></span>

* <span data-ttu-id="ee4b3-155">Database SQL relazionale</span><span class="sxs-lookup"><span data-stu-id="ee4b3-155">Relational SQL database</span></span>
* <span data-ttu-id="ee4b3-156">Archivio chiave-valore</span><span class="sxs-lookup"><span data-stu-id="ee4b3-156">Key-value store</span></span>
* <span data-ttu-id="ee4b3-157">Archivio BLOB</span><span class="sxs-lookup"><span data-stu-id="ee4b3-157">Blob store</span></span>
* <span data-ttu-id="ee4b3-158">Archivio tabelle</span><span class="sxs-lookup"><span data-stu-id="ee4b3-158">Table store</span></span>

<span data-ttu-id="ee4b3-159">Dopo che i dati sono stati salvati nel database, un nuovo circuito può essere avviato da un utente in qualsiasi momento.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="ee4b3-160">I dati dell'utente vengono conservati e resi disponibili in qualsiasi nuovo circuito.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="ee4b3-161">Per altre informazioni sulle opzioni di archiviazione dei dati di Azure, vedere la [documentazione di archiviazione](/azure/storage/) di Azure e i [database di Azure](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="ee4b3-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="ee4b3-162">URL</span><span class="sxs-lookup"><span data-stu-id="ee4b3-162">URL</span></span>

<span data-ttu-id="ee4b3-163">Per i dati temporanei che rappresentano lo stato di navigazione, modellare i dati come parte dell'URL.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="ee4b3-164">Esempi di stato modellati nell'URL includono:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="ee4b3-165">ID di un'entità visualizzata.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="ee4b3-166">Numero di pagina corrente in una griglia di paging.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="ee4b3-167">Il contenuto della barra degli indirizzi del browser viene mantenuto:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="ee4b3-168">Se l'utente ricarica manualmente la pagina.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="ee4b3-169">Se il server Web non è più disponibile, l'utente @ no__t-0The è forzato a ricaricare la pagina per potersi connettere a un altro server.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="ee4b3-170">Per informazioni sulla definizione di modelli URL con la direttiva `@page`, vedere <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="ee4b3-171">Lato client nel browser</span><span class="sxs-lookup"><span data-stu-id="ee4b3-171">Client-side in the browser</span></span>

<span data-ttu-id="ee4b3-172">Per i dati temporanei che l'utente sta creando attivamente, un archivio di backup comune è costituito dalle raccolte `localStorage` e `sessionStorage` del browser.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="ee4b3-173">L'app non è necessaria per gestire o cancellare lo stato archiviato se il circuito viene abbandonato, il che rappresenta un vantaggio rispetto all'archiviazione sul lato server.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="ee4b3-174">"Lato client" in questa sezione si riferisce agli scenari lato client nel browser, non al [modello di hosting Webassembly Blazer](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="ee4b3-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="ee4b3-175">`localStorage` e `sessionStorage` possono essere usati nelle app webassembly blazer, ma solo scrivendo codice personalizzato o usando un pacchetto di terze parti.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="ee4b3-176">`localStorage` e `sessionStorage` si differenziano nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="ee4b3-177">`localStorage` ha come ambito il browser dell'utente.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="ee4b3-178">Se l'utente ricarica la pagina o chiude e riapre il browser, lo stato è permanente.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="ee4b3-179">Se l'utente apre più schede del browser, lo stato viene condiviso tra le schede.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="ee4b3-180">I dati vengono mantenuti in `localStorage` finché non vengono cancellati in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="ee4b3-181">`sessionStorage` ha come ambito la scheda del browser dell'utente. Se l'utente ricarica la scheda, lo stato è permanente.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="ee4b3-182">Se l'utente chiude la scheda o il browser, lo stato viene perso.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="ee4b3-183">Se l'utente apre più schede del browser, ogni scheda ha una propria versione indipendente dei dati.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="ee4b3-184">In genere, `sessionStorage` è più sicuro da usare.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="ee4b3-185">`sessionStorage` evita il rischio che un utente apra più schede e riscontri quanto segue:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="ee4b3-186">Bug nell'archiviazione dello stato tra le schede.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="ee4b3-187">Comportamento confuso quando una scheda sovrascrive lo stato di altre schede.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="ee4b3-188">`localStorage` è la scelta migliore se l'applicazione deve conservare lo stato tra la chiusura e la riapertura del browser.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="ee4b3-189">Avvertenze per l'uso dell'archiviazione del browser:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="ee4b3-190">Analogamente all'utilizzo di un database lato server, il caricamento e il salvataggio dei dati sono asincroni.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="ee4b3-191">A differenza di un database lato server, l'archiviazione non è disponibile durante il prerendering perché la pagina richiesta non esiste nel browser durante la fase di pre-rendering.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="ee4b3-192">L'archiviazione di alcuni kilobyte di dati è ragionevole per essere resa permanente per le app del server blazer.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="ee4b3-193">Oltre alcuni kilobyte, è necessario considerare le implicazioni relative alle prestazioni perché i dati vengono caricati e salvati in rete.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="ee4b3-194">Gli utenti possono visualizzare o manomettere i dati.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="ee4b3-195">ASP.NET Core [protezione dei dati](xref:security/data-protection/introduction) può ridurre il rischio.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="ee4b3-196">Soluzioni di archiviazione del browser di terze parti</span><span class="sxs-lookup"><span data-stu-id="ee4b3-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="ee4b3-197">I pacchetti NuGet di terze parti forniscono le API per lavorare con `localStorage` e `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="ee4b3-198">Vale la pena considerare la scelta di un pacchetto che usa in modo trasparente la [protezione dei dati](xref:security/data-protection/introduction)ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="ee4b3-199">ASP.NET Core protezione dei dati consente di crittografare i dati archiviati e di ridurre il rischio potenziale di manomissioni dei dati archiviati.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="ee4b3-200">Se i dati serializzati in JSON vengono archiviati in testo non crittografato, gli utenti possono visualizzare i dati usando gli strumenti di sviluppo del browser e modificare anche i dati archiviati.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="ee4b3-201">La protezione dei dati non è sempre un problema perché i dati potrebbero essere di natura banale.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="ee4b3-202">Ad esempio, la lettura o la modifica del colore archiviato di un elemento dell'interfaccia utente non costituisce un rischio di sicurezza significativo per l'utente o l'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="ee4b3-203">Evitare di consentire agli utenti di ispezionare o manomettere *i dati sensibili*.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="ee4b3-204">Pacchetto sperimentale di archiviazione del browser protetto</span><span class="sxs-lookup"><span data-stu-id="ee4b3-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="ee4b3-205">Un esempio di pacchetto NuGet che fornisce la [protezione dei dati](xref:security/data-protection/introduction) per `localStorage` e `sessionStorage` è [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="ee4b3-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="ee4b3-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` è un pacchetto sperimentale non supportato non adatto per l'uso in fase di produzione.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="ee4b3-207">Installazione</span><span class="sxs-lookup"><span data-stu-id="ee4b3-207">Installation</span></span>

<span data-ttu-id="ee4b3-208">Per installare il pacchetto `Microsoft.AspNetCore.ProtectedBrowserStorage`:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="ee4b3-209">Nel progetto di app del server Blazer aggiungere un riferimento al pacchetto a [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="ee4b3-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="ee4b3-210">Nel codice HTML di primo livello (ad esempio, nel file *pages/_Host. cshtml* nel modello di progetto predefinito) aggiungere il seguente tag `<script>`:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="ee4b3-211">Nel metodo `Startup.ConfigureServices` chiamare `AddProtectedBrowserStorage` per aggiungere servizi `localStorage` e `sessionStorage` alla raccolta di servizi:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="ee4b3-212">Salvare e caricare i dati all'interno di un componente</span><span class="sxs-lookup"><span data-stu-id="ee4b3-212">Save and load data within a component</span></span>

<span data-ttu-id="ee4b3-213">In tutti i componenti che richiedono il caricamento o il salvataggio dei dati nell'archiviazione del browser, utilizzare [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) per inserire un'istanza di uno dei seguenti elementi:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-213">In any component that requires loading or saving data to browser storage, use [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="ee4b3-214">La scelta dipende dall'archivio di backup che si desidera utilizzare.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="ee4b3-215">Nell'esempio seguente viene utilizzato `sessionStorage`:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-215">In the following example, `sessionStorage` is used:</span></span>

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="ee4b3-216">L'istruzione `@using` può essere inserita in un file *_Imports. Razor* anziché nel componente.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="ee4b3-217">L'uso del file *_Imports. Razor* rende lo spazio dei nomi disponibile a segmenti più grandi dell'app o dell'intera app.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="ee4b3-218">Per salvare in modo permanente il valore `currentCount` nel componente `Counter` del modello di progetto, modificare il metodo `IncrementCount` in modo da usare `ProtectedSessionStore.SetAsync`:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-218">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="ee4b3-219">Nelle app più grandi e più realistiche, l'archiviazione dei singoli campi è uno scenario improbabile.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="ee4b3-220">È più probabile che le app memorizzino interi oggetti modello che includono lo stato complesso.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="ee4b3-221">`ProtectedSessionStore` serializza e deserializza automaticamente i dati JSON.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="ee4b3-222">Nell'esempio di codice precedente, i dati `currentCount` vengono archiviati come `sessionStorage['count']` nel browser dell'utente.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-222">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="ee4b3-223">I dati non vengono archiviati in testo non crittografato, ma vengono protetti usando la [protezione dei dati](xref:security/data-protection/introduction)di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="ee4b3-224">I dati crittografati possono essere visualizzati se `sessionStorage['count']` viene valutato nella console per sviluppatori del browser.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="ee4b3-225">Per ripristinare i dati `currentCount` se l'utente torna al componente `Counter` in un secondo momento (anche se si trovano in un circuito completamente nuovo), usare `ProtectedSessionStore.GetAsync`:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-225">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="ee4b3-226">Se i parametri del componente includono lo stato di navigazione, chiamare `ProtectedSessionStore.GetAsync` e assegnare il risultato in `OnParametersSetAsync`, non `OnInitializedAsync`.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="ee4b3-227">`OnInitializedAsync` viene chiamato solo una volta quando viene creata la prima istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="ee4b3-228">`OnInitializedAsync` non viene chiamato di nuovo in un secondo momento se l'utente passa a un URL diverso rimanendo nella stessa pagina.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span>

> [!WARNING]
> <span data-ttu-id="ee4b3-229">Gli esempi in questa sezione funzionano solo se per il server non è abilitato il prerendering.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-229">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="ee4b3-230">Con il prerendering abilitato, viene generato un errore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-230">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="ee4b3-231">Non è possibile eseguire le chiamate di interoperabilità JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-231">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="ee4b3-232">Il motivo è che il componente viene preeseguito.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-232">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="ee4b3-233">Disabilitare il prerendering o aggiungere altro codice per lavorare con il prerendering.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-233">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="ee4b3-234">Per ulteriori informazioni sulla scrittura di codice che funziona con il prerendering, vedere la sezione [handle prerendering](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="ee4b3-234">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="ee4b3-235">Gestire lo stato di caricamento</span><span class="sxs-lookup"><span data-stu-id="ee4b3-235">Handle the loading state</span></span>

<span data-ttu-id="ee4b3-236">Poiché l'archiviazione del browser è asincrona (a cui si accede tramite una connessione di rete), si verifica sempre un periodo di tempo prima che i dati vengano caricati e disponibili per l'uso da parte di un componente.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-236">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="ee4b3-237">Per ottenere risultati ottimali, è possibile eseguire il rendering di un messaggio di stato di caricamento mentre è in corso il caricamento anziché visualizzare dati vuoti o predefiniti.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-237">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="ee4b3-238">Un approccio consiste nel rilevare se i dati sono `null` (ancora in caricamento) o meno.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-238">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="ee4b3-239">Nel componente `Counter` predefinito, il conteggio viene mantenuto in un `int`.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-239">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="ee4b3-240">Make `currentCount` nullable aggiungendo un punto interrogativo (`?`) al tipo (`int`):</span><span class="sxs-lookup"><span data-stu-id="ee4b3-240">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="ee4b3-241">Anziché visualizzare in modo non condizionale il pulsante conteggio e **incremento** , scegliere di visualizzare questi elementi solo se i dati vengono caricati:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-241">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```cshtml
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="ee4b3-242">Gestione preliminare</span><span class="sxs-lookup"><span data-stu-id="ee4b3-242">Handle prerendering</span></span>

<span data-ttu-id="ee4b3-243">Durante il prerendering:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-243">During prerendering:</span></span>

* <span data-ttu-id="ee4b3-244">Una connessione interattiva al browser dell'utente non esiste.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-244">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="ee4b3-245">Il browser non dispone ancora di una pagina in cui è possibile eseguire il codice JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-245">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="ee4b3-246">`localStorage` o `sessionStorage` non sono disponibili durante il prerendering.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-246">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="ee4b3-247">Se il componente tenta di interagire con l'archiviazione, viene generato un errore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-247">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="ee4b3-248">Non è possibile eseguire le chiamate di interoperabilità JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-248">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="ee4b3-249">Il motivo è che il componente viene preeseguito.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-249">This is because the component is being prerendered.</span></span>

<span data-ttu-id="ee4b3-250">Un modo per risolvere l'errore consiste nel disabilitare il prerendering.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-250">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="ee4b3-251">Questa è in genere la scelta migliore se l'app usa in modo intensivo l'archiviazione basata su browser.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-251">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="ee4b3-252">Il prerendering aggiunge complessità e non è vantaggioso per l'app perché l'app non può eseguire il prerendering di contenuto utile fino a quando non sono disponibili `localStorage` o `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-252">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="ee4b3-253">Per disabilitare il prerendering, aprire il file *pages/_Host. cshtml* e modificare la chiamata a `Html.RenderComponentAsync<App>(RenderMode.Server)`.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-253">To disable prerendering, open the *Pages/_Host.cshtml* file and change the call to `Html.RenderComponentAsync<App>(RenderMode.Server)`.</span></span>

<span data-ttu-id="ee4b3-254">Il prerendering può essere utile per altre pagine che non utilizzano `localStorage` o `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-254">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="ee4b3-255">Per rendere abilitato il prerendering, rinviare l'operazione di caricamento finché il browser non è connesso al circuito.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-255">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="ee4b3-256">Di seguito è riportato un esempio per l'archiviazione di un valore del contatore:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-256">The following is an example for storing a counter value:</span></span>

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="ee4b3-257">Fattorizzare la conservazione dello stato in una posizione comune</span><span class="sxs-lookup"><span data-stu-id="ee4b3-257">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="ee4b3-258">Se molti componenti si basano sull'archiviazione basata su browser, la reimplementazione del codice del provider di stato più volte crea una duplicazione del codice.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-258">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="ee4b3-259">Un'opzione per evitare la duplicazione del codice consiste nel creare un *componente padre del provider di stato* che incapsula la logica del provider di stato.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-259">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="ee4b3-260">I componenti figlio possono funzionare con i dati persistenti senza considerare il meccanismo di persistenza dello stato.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-260">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="ee4b3-261">Nell'esempio seguente di un componente `CounterStateProvider`, i dati del contatore vengono mantenuti:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-261">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="ee4b3-262">Il componente `CounterStateProvider` gestisce la fase di caricamento non eseguendo il rendering del relativo contenuto figlio fino al completamento del caricamento.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-262">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="ee4b3-263">Per utilizzare il componente `CounterStateProvider`, eseguire il wrapping di un'istanza del componente intorno a qualsiasi altro componente che richiede l'accesso allo stato del contatore.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-263">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="ee4b3-264">Per rendere lo stato accessibile a tutti i componenti di un'app, eseguire il wrapping del componente `CounterStateProvider` intorno al `Router` nel componente `App` (*app. Razor*):</span><span class="sxs-lookup"><span data-stu-id="ee4b3-264">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="ee4b3-265">I componenti di cui è stato eseguito il wrapper ricevono e possono modificare lo stato del contatore permanente.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-265">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="ee4b3-266">Il componente `Counter` seguente implementa il modello:</span><span class="sxs-lookup"><span data-stu-id="ee4b3-266">The following `Counter` component implements the pattern:</span></span>

```cshtml
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="ee4b3-267">Il componente precedente non è necessario per interagire con `ProtectedBrowserStorage`, né gestire una fase di "caricamento".</span><span class="sxs-lookup"><span data-stu-id="ee4b3-267">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="ee4b3-268">Per gestire il prerendering come descritto in precedenza, è possibile modificare `CounterStateProvider` in modo che tutti i componenti che utilizzano i dati del contatore funzionino automaticamente con il prerendering.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-268">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="ee4b3-269">Per informazioni dettagliate, vedere la sezione [handle prerendering](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="ee4b3-269">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="ee4b3-270">In generale, è consigliabile usare il modello di *componente padre del provider di stato* :</span><span class="sxs-lookup"><span data-stu-id="ee4b3-270">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="ee4b3-271">Per utilizzare lo stato in molti altri componenti.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-271">To consume state in many other components.</span></span>
* <span data-ttu-id="ee4b3-272">Se è presente un solo oggetto di stato di primo livello da salvare in modo permanente.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-272">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="ee4b3-273">Per salvare in modo permanente molti oggetti di stato diversi e utilizzare subset diversi di oggetti in posizioni diverse, è preferibile evitare di gestire il caricamento e il salvataggio dello stato a livello globale.</span><span class="sxs-lookup"><span data-stu-id="ee4b3-273">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
