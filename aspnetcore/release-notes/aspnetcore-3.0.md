---
title: Novità di ASP.NET Core 3.0
author: rick-anderson
description: Informazioni sulle nuove funzionalità di ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 35accdd2f65ac5430525be88cc621094e37d3e40
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667251"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="f53c0-103">Novità di ASP.NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="f53c0-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="f53c0-104">In questo articolo vengono evidenziate le modifiche più significative in ASP.NET Core 3.0 con i collegamenti alla documentazione pertinente.</span><span class="sxs-lookup"><span data-stu-id="f53c0-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

Blazor<span data-ttu-id="f53c0-105"> è un nuovo Framework in ASP.NET Core per la creazione di un'interfaccia utente Web interattiva sul lato client con .NET:</span><span class="sxs-lookup"><span data-stu-id="f53c0-105"> is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="f53c0-106">Creare interfacce utente interattive avanzate con C# invece di JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f53c0-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="f53c0-107">Condividere la logica dell'app scritta in .NET sul lato client e sul lato server.</span><span class="sxs-lookup"><span data-stu-id="f53c0-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="f53c0-108">Eseguire il rendering dell'interfaccia utente come HTML e CSS per un ampio supporto dei browser, inclusi i browser per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="f53c0-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="f53c0-109">scenari supportati di Blazor Framework:</span><span class="sxs-lookup"><span data-stu-id="f53c0-109">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="f53c0-110">Componenti dell'interfaccia utente riutilizzabili (componenti Razor)</span><span class="sxs-lookup"><span data-stu-id="f53c0-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="f53c0-111">Routing lato client</span><span class="sxs-lookup"><span data-stu-id="f53c0-111">Client-side routing</span></span>
* <span data-ttu-id="f53c0-112">Layout componenti</span><span class="sxs-lookup"><span data-stu-id="f53c0-112">Component layouts</span></span>
* <span data-ttu-id="f53c0-113">Supporto per l'inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="f53c0-113">Support for dependency injection</span></span>
* <span data-ttu-id="f53c0-114">Moduli e convalida</span><span class="sxs-lookup"><span data-stu-id="f53c0-114">Forms and validation</span></span>
* <span data-ttu-id="f53c0-115">Compilare librerie di componenti con le librerie di classi Razor</span><span class="sxs-lookup"><span data-stu-id="f53c0-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="f53c0-116">Interoperabilità JavaScript</span><span class="sxs-lookup"><span data-stu-id="f53c0-116">JavaScript interop</span></span>

<span data-ttu-id="f53c0-117">Per altre informazioni, vedere <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="f53c0-117">For more information, see <xref:blazor/index>.</span></span>

### <a name="opno-locblazor-server"></a><span data-ttu-id="f53c0-118">Server Blazor</span><span class="sxs-lookup"><span data-stu-id="f53c0-118">Blazor Server</span></span>

Blazor<span data-ttu-id="f53c0-119"> separa la logica di rendering dei componenti dal modo in cui vengono applicati gli aggiornamenti dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="f53c0-119"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="f53c0-120"> Server fornisce il supporto per l'hosting di componenti Razor sul server in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f53c0-120"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="f53c0-121">Gli aggiornamenti dell'interfaccia utente vengono gestiti tramite una connessione SignalR.</span><span class="sxs-lookup"><span data-stu-id="f53c0-121">UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="f53c0-122">il server Blazor è supportato ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="f53c0-122">Blazor Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="opno-locblazor-webassembly-preview"></a>Blazor<span data-ttu-id="f53c0-123"> webassembly (anteprima)</span><span class="sxs-lookup"><span data-stu-id="f53c0-123"> WebAssembly (Preview)</span></span>

<span data-ttu-id="f53c0-124">le app Blazor possono anche essere eseguite direttamente nel browser usando un Runtime .NET basato su webassembly.</span><span class="sxs-lookup"><span data-stu-id="f53c0-124">Blazor apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> Blazor<span data-ttu-id="f53c0-125"> webassembly è in anteprima e *non* è supportato in ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="f53c0-125"> WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> Blazor<span data-ttu-id="f53c0-126"> webassembly sarà supportato in una versione futura di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f53c0-126"> WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="f53c0-127">Componenti Razor</span><span class="sxs-lookup"><span data-stu-id="f53c0-127">Razor components</span></span>

Blazor<span data-ttu-id="f53c0-128"> app sono compilate da componenti.</span><span class="sxs-lookup"><span data-stu-id="f53c0-128"> apps are built from components.</span></span> <span data-ttu-id="f53c0-129">I componenti sono blocchi autonomi dell'interfaccia utente (UI), ad esempio una pagina, una finestra di dialogo o un form.</span><span class="sxs-lookup"><span data-stu-id="f53c0-129">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="f53c0-130">I componenti sono classi .NET normali che definiscono la logica di rendering dell'interfaccia utente e i gestori eventi sul lato client.</span><span class="sxs-lookup"><span data-stu-id="f53c0-130">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="f53c0-131">È possibile creare app Web interattive avanzate senza JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f53c0-131">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="f53c0-132">I componenti di Blazor vengono in genere creati usando sintassi Razor, una combinazione naturale di HTML C#e.</span><span class="sxs-lookup"><span data-stu-id="f53c0-132">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="f53c0-133">I componenti Razor sono simili alle visualizzazioni Razor Pages e MVC in quanto entrambi utilizzano Razor.</span><span class="sxs-lookup"><span data-stu-id="f53c0-133">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="f53c0-134">Diversamente dalle pagine e dalle viste, basate su un modello di richiesta-risposta, i componenti vengono usati in modo specifico per la gestione della composizione dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="f53c0-134">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="f53c0-135">gRPC</span><span class="sxs-lookup"><span data-stu-id="f53c0-135">gRPC</span></span>

<span data-ttu-id="f53c0-136">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="f53c0-136">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="f53c0-137">È un noto Framework RPC (Remote Procedure Call) a prestazioni elevate.</span><span class="sxs-lookup"><span data-stu-id="f53c0-137">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="f53c0-138">Offre un approccio di primo contratto per lo sviluppo di API.</span><span class="sxs-lookup"><span data-stu-id="f53c0-138">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="f53c0-139">Usa tecnologie moderne, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f53c0-139">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="f53c0-140">HTTP/2 per il trasporto.</span><span class="sxs-lookup"><span data-stu-id="f53c0-140">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="f53c0-141">Buffer del protocollo come linguaggio di descrizione dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="f53c0-141">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="f53c0-142">Formato di serializzazione binario.</span><span class="sxs-lookup"><span data-stu-id="f53c0-142">Binary serialization format.</span></span>
* <span data-ttu-id="f53c0-143">Fornisce funzionalità come:</span><span class="sxs-lookup"><span data-stu-id="f53c0-143">Provides features such as:</span></span>

  * <span data-ttu-id="f53c0-144">Authentication</span><span class="sxs-lookup"><span data-stu-id="f53c0-144">Authentication</span></span>
  * <span data-ttu-id="f53c0-145">Flusso bidirezionale e controllo di flusso.</span><span class="sxs-lookup"><span data-stu-id="f53c0-145">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="f53c0-146">Annullamento e timeout.</span><span class="sxs-lookup"><span data-stu-id="f53c0-146">Cancellation and timeouts.</span></span>

<span data-ttu-id="f53c0-147">la funzionalità gRPC in ASP.NET Core 3.0 include:</span><span class="sxs-lookup"><span data-stu-id="f53c0-147">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="f53c0-148">[Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; un framework ASP.NET Core per l'hosting di servizi Grpc.</span><span class="sxs-lookup"><span data-stu-id="f53c0-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="f53c0-149">gRPC su ASP.NET Core si integra con funzionalità di ASP.NET Core standard come la registrazione, l'inserimento DI dipendenze, l'autenticazione e l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="f53c0-149">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="f53c0-150">[Grpc .NET. client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; un client Grpc per .NET Core che si basa sulle `HttpClient`familiari.</span><span class="sxs-lookup"><span data-stu-id="f53c0-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="f53c0-151">[Grpc .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; l'integrazione client Grpc con `HttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="f53c0-152">Per altre informazioni, vedere <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="f53c0-152">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="f53c0-153">Per istruzioni sulla migrazione, vedere [aggiornare il codice SignalR](xref:migration/22-to-30#signalr) .</span><span class="sxs-lookup"><span data-stu-id="f53c0-153">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> SignalR<span data-ttu-id="f53c0-154"> USA ora `System.Text.Json` per serializzare/deserializzare i messaggi JSON.</span><span class="sxs-lookup"><span data-stu-id="f53c0-154"> now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="f53c0-155">Per istruzioni su come ripristinare il serializzatore basato su `Newtonsoft.Json`, vedere [passare a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson) .</span><span class="sxs-lookup"><span data-stu-id="f53c0-155">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="f53c0-156">Nei client JavaScript e .NET per SignalRè stato aggiunto il supporto per la riconnessione automatica.</span><span class="sxs-lookup"><span data-stu-id="f53c0-156">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="f53c0-157">Per impostazione predefinita, il client tenta di riconnettersi immediatamente e riprovare dopo 2, 10 e 30 secondi, se necessario.</span><span class="sxs-lookup"><span data-stu-id="f53c0-157">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="f53c0-158">Se il client si riconnette correttamente, riceve un nuovo ID connessione.</span><span class="sxs-lookup"><span data-stu-id="f53c0-158">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="f53c0-159">La riconnessione automatica è esplicita:</span><span class="sxs-lookup"><span data-stu-id="f53c0-159">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="f53c0-160">È possibile specificare gli intervalli di riconnessione passando una matrice di durate basate su millisecondi:</span><span class="sxs-lookup"><span data-stu-id="f53c0-160">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="f53c0-161">È possibile passare un'implementazione personalizzata per il controllo completo degli intervalli di riconnessione.</span><span class="sxs-lookup"><span data-stu-id="f53c0-161">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="f53c0-162">Se la riconnessione non riesce dopo l'ultimo intervallo di riconnessione:</span><span class="sxs-lookup"><span data-stu-id="f53c0-162">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="f53c0-163">Il client considera la connessione offline.</span><span class="sxs-lookup"><span data-stu-id="f53c0-163">The client considers the connection is offline.</span></span>
* <span data-ttu-id="f53c0-164">Il client smette di tentare di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="f53c0-164">The client stops trying to reconnect.</span></span>

<span data-ttu-id="f53c0-165">Durante i tentativi di riconnessione, aggiornare l'interfaccia utente dell'app per notificare all'utente che è in corso il tentativo di riconnessione.</span><span class="sxs-lookup"><span data-stu-id="f53c0-165">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="f53c0-166">Per fornire commenti e suggerimenti dell'interfaccia utente quando la connessione viene interrotta, l'API client SignalR è stata espansa per includere i gestori eventi seguenti:</span><span class="sxs-lookup"><span data-stu-id="f53c0-166">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="f53c0-167">`onreconnecting`: offre agli sviluppatori la possibilità di disabilitare l'interfaccia utente o di informare gli utenti che l'app è offline.</span><span class="sxs-lookup"><span data-stu-id="f53c0-167">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="f53c0-168">`onreconnected`: offre agli sviluppatori la possibilità di aggiornare l'interfaccia utente una volta ristabilita la connessione.</span><span class="sxs-lookup"><span data-stu-id="f53c0-168">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="f53c0-169">Il codice seguente usa `onreconnecting` per aggiornare l'interfaccia utente durante il tentativo di connessione:</span><span class="sxs-lookup"><span data-stu-id="f53c0-169">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="f53c0-170">Il codice seguente usa `onreconnected` per aggiornare l'interfaccia utente alla connessione:</span><span class="sxs-lookup"><span data-stu-id="f53c0-170">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR<span data-ttu-id="f53c0-171"> 3,0 e versioni successive fornisce una risorsa personalizzata ai gestori di autorizzazione quando un metodo Hub richiede l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="f53c0-171"> 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="f53c0-172">La risorsa è un'istanza di `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-172">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="f53c0-173">Il `HubInvocationContext` include:</span><span class="sxs-lookup"><span data-stu-id="f53c0-173">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="f53c0-174">Nome del metodo Hub richiamato.</span><span class="sxs-lookup"><span data-stu-id="f53c0-174">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="f53c0-175">Argomenti per il metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="f53c0-175">Arguments to the hub method.</span></span>

<span data-ttu-id="f53c0-176">Si consideri l'esempio seguente di un'app Chat Room che consente l'accesso a più organizzazioni tramite Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="f53c0-176">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="f53c0-177">Chiunque disponga di un account Microsoft può accedere a chat, ma solo i membri dell'organizzazione proprietaria possono vietare gli utenti o visualizzare le cronologie della chat degli utenti.</span><span class="sxs-lookup"><span data-stu-id="f53c0-177">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="f53c0-178">L'app potrebbe limitare determinate funzionalità di utenti specifici.</span><span class="sxs-lookup"><span data-stu-id="f53c0-178">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="f53c0-179">Nel codice precedente `DomainRestrictedRequirement` funge da `IAuthorizationRequirement`personalizzato.</span><span class="sxs-lookup"><span data-stu-id="f53c0-179">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="f53c0-180">Poiché il parametro della risorsa `HubInvocationContext` viene passato, la logica interna può:</span><span class="sxs-lookup"><span data-stu-id="f53c0-180">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="f53c0-181">Esaminare il contesto in cui viene chiamato l'hub.</span><span class="sxs-lookup"><span data-stu-id="f53c0-181">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="f53c0-182">Prendere decisioni per consentire all'utente di eseguire singoli metodi dell'hub.</span><span class="sxs-lookup"><span data-stu-id="f53c0-182">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="f53c0-183">I singoli metodi dell'hub possono essere contrassegnati con il nome dei criteri controllati dal codice in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="f53c0-183">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="f53c0-184">Poiché i client tentano di chiamare i singoli metodi dell'hub, il gestore `DomainRestrictedRequirement` esegue e controlla l'accesso ai metodi.</span><span class="sxs-lookup"><span data-stu-id="f53c0-184">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="f53c0-185">In base al modo in cui il `DomainRestrictedRequirement` controlla l'accesso:</span><span class="sxs-lookup"><span data-stu-id="f53c0-185">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="f53c0-186">Tutti gli utenti connessi possono chiamare il metodo `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-186">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="f53c0-187">Solo gli utenti che hanno eseguito l'accesso con un indirizzo di posta elettronica `@jabbr.net` possono visualizzare le cronologie degli utenti.</span><span class="sxs-lookup"><span data-stu-id="f53c0-187">Only users who have logged in with a `@jabbr.net` email address can view users’ histories.</span></span>
* <span data-ttu-id="f53c0-188">Solo `bob42@jabbr.net` possibile escludere gli utenti dalla chat room.</span><span class="sxs-lookup"><span data-stu-id="f53c0-188">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="f53c0-189">La creazione dei criteri di `DomainRestricted` può implicare:</span><span class="sxs-lookup"><span data-stu-id="f53c0-189">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="f53c0-190">In *Startup.cs*aggiungere il nuovo criterio.</span><span class="sxs-lookup"><span data-stu-id="f53c0-190">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="f53c0-191">Fornire il requisito di `DomainRestrictedRequirement` personalizzato come parametro.</span><span class="sxs-lookup"><span data-stu-id="f53c0-191">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="f53c0-192">Registrazione di `DomainRestricted` con il middleware di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="f53c0-192">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

<span data-ttu-id="f53c0-193">gli hub SignalR usano il [routing degli endpoint](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="f53c0-193">SignalR hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="f53c0-194">la connessione all'hub SignalR è stata eseguita in precedenza in modo esplicito:</span><span class="sxs-lookup"><span data-stu-id="f53c0-194">SignalR hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="f53c0-195">Nella versione precedente, gli sviluppatori dovevano collegare i controller, le pagine Razor e gli hub in un'ampia gamma di posizioni.</span><span class="sxs-lookup"><span data-stu-id="f53c0-195">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="f53c0-196">La connessione esplicita genera una serie di segmenti di routing quasi identici:</span><span class="sxs-lookup"><span data-stu-id="f53c0-196">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

SignalR<span data-ttu-id="f53c0-197"> 3,0 hub possono essere indirizzati tramite il routing degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="f53c0-197"> 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="f53c0-198">Con il routing degli endpoint, in genere è possibile configurare tutti i routing in `UseRouting`:</span><span class="sxs-lookup"><span data-stu-id="f53c0-198">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="f53c0-199">ASP.NET Core 3,0 SignalR aggiunto:</span><span class="sxs-lookup"><span data-stu-id="f53c0-199">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="f53c0-200">Streaming da client a server.</span><span class="sxs-lookup"><span data-stu-id="f53c0-200">Client-to-server streaming.</span></span> <span data-ttu-id="f53c0-201">Con il flusso da client a server, i metodi lato server possono prendere le istanze di un `IAsyncEnumerable<T>` o `ChannelReader<T>`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-201">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="f53c0-202">Nell'esempio seguente C# , il metodo `UploadStream` sull'hub riceverà un flusso di stringhe dal client:</span><span class="sxs-lookup"><span data-stu-id="f53c0-202">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="f53c0-203">Le app client .NET possono passare un'istanza di `IAsyncEnumerable<T>` o `ChannelReader<T>` come argomento `stream` del metodo dell'hub `UploadStream`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-203">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="f53c0-204">Al termine del ciclo `for` e alla chiusura della funzione locale, viene inviato il completamento del flusso:</span><span class="sxs-lookup"><span data-stu-id="f53c0-204">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="f53c0-205">Le app client JavaScript usano il SignalR `Subject` (o un [oggetto RxJS](https://rxjs.dev/api/index/class/Subject)) per l'argomento `stream` del metodo dell'hub `UploadStream` sopra.</span><span class="sxs-lookup"><span data-stu-id="f53c0-205">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="f53c0-206">Il codice JavaScript può usare il metodo `subject.next` per gestire le stringhe Man mano che vengono acquisite e pronte per l'invio al server.</span><span class="sxs-lookup"><span data-stu-id="f53c0-206">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="f53c0-207">Utilizzando codice come i due frammenti precedenti, è possibile creare esperienze di streaming in tempo reale.</span><span class="sxs-lookup"><span data-stu-id="f53c0-207">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="f53c0-208">Nuova serializzazione JSON</span><span class="sxs-lookup"><span data-stu-id="f53c0-208">New JSON serialization</span></span>

<span data-ttu-id="f53c0-209">ASP.NET Core 3,0 USA ora <xref:System.Text.Json> per impostazione predefinita per la serializzazione JSON:</span><span class="sxs-lookup"><span data-stu-id="f53c0-209">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="f53c0-210">Legge e scrive JSON in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="f53c0-210">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="f53c0-211">È ottimizzato per il testo UTF-8.</span><span class="sxs-lookup"><span data-stu-id="f53c0-211">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="f53c0-212">Prestazioni in genere superiori rispetto a `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-212">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="f53c0-213">Per aggiungere Json.NET a ASP.NET Core 3,0, vedere [aggiungere il supporto per il formato JSON basato su Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="f53c0-213">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="f53c0-214">Nuove direttive Razor</span><span class="sxs-lookup"><span data-stu-id="f53c0-214">New Razor directives</span></span>

<span data-ttu-id="f53c0-215">L'elenco seguente contiene le nuove direttive Razor:</span><span class="sxs-lookup"><span data-stu-id="f53c0-215">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="f53c0-216">[`@attribute`](xref:mvc/views/razor#attribute) &ndash; la direttiva `@attribute` applica l'attributo specificato alla classe della pagina o della visualizzazione generata.</span><span class="sxs-lookup"><span data-stu-id="f53c0-216">[`@attribute`](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="f53c0-217">Ad esempio: `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-217">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="f53c0-218">[`@implements`](xref:mvc/views/razor#implements) &ndash; la direttiva `@implements` implementa un'interfaccia per la classe generata.</span><span class="sxs-lookup"><span data-stu-id="f53c0-218">[`@implements`](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="f53c0-219">Ad esempio: `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-219">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="f53c0-220">IdentityServer4 supporta l'autenticazione e l'autorizzazione per le API Web e le Spa</span><span class="sxs-lookup"><span data-stu-id="f53c0-220">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="f53c0-221">ASP.NET Core 3,0 offre l'autenticazione in app a pagina singola (Spa) usando il supporto per l'autorizzazione dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="f53c0-221">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="f53c0-222">ASP.NET Core identità per l'autenticazione e l'archiviazione degli utenti viene combinata con [IdentityServer4](https://identityserver.io/) per l'implementazione di Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="f53c0-222">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="f53c0-223">IdentityServer4 è un Framework di OpenID Connect e OAuth 2,0 per ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="f53c0-223">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="f53c0-224">Consente le seguenti funzionalità di sicurezza:</span><span class="sxs-lookup"><span data-stu-id="f53c0-224">It enables the following security features:</span></span>

* <span data-ttu-id="f53c0-225">Autenticazione come servizio (AaaS)</span><span class="sxs-lookup"><span data-stu-id="f53c0-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="f53c0-226">Single Sign-on/off (SSO) su più tipi di applicazione</span><span class="sxs-lookup"><span data-stu-id="f53c0-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="f53c0-227">Controllo di accesso per le API</span><span class="sxs-lookup"><span data-stu-id="f53c0-227">Access control for APIs</span></span>
* <span data-ttu-id="f53c0-228">Gateway federativo</span><span class="sxs-lookup"><span data-stu-id="f53c0-228">Federation Gateway</span></span>

<span data-ttu-id="f53c0-229">Per ulteriori informazioni, vedere [la documentazione di IdentityServer4](http://docs.identityserver.io/en/latest/index.html) o [l'autenticazione e l'autorizzazione per le Spa](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="f53c0-229">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="f53c0-230">Certificato e autenticazione Kerberos</span><span class="sxs-lookup"><span data-stu-id="f53c0-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="f53c0-231">L'autenticazione del certificato richiede:</span><span class="sxs-lookup"><span data-stu-id="f53c0-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="f53c0-232">Configurazione del server per l'accettazione dei certificati.</span><span class="sxs-lookup"><span data-stu-id="f53c0-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="f53c0-233">Aggiunta del middleware di autenticazione in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="f53c0-234">Aggiunta del servizio di autenticazione del certificato in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="f53c0-235">Le opzioni per l'autenticazione dei certificati includono la possibilità di:</span><span class="sxs-lookup"><span data-stu-id="f53c0-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="f53c0-236">Accettare certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="f53c0-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="f53c0-237">Controllare la revoca del certificato.</span><span class="sxs-lookup"><span data-stu-id="f53c0-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="f53c0-238">Verificare che nel certificato fornito siano presenti i flag di utilizzo corretti.</span><span class="sxs-lookup"><span data-stu-id="f53c0-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="f53c0-239">Un'entità utente predefinita viene costruita dalle proprietà del certificato.</span><span class="sxs-lookup"><span data-stu-id="f53c0-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="f53c0-240">L'entità utente contiene un evento che consente l'aggiunta o la sostituzione dell'entità.</span><span class="sxs-lookup"><span data-stu-id="f53c0-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="f53c0-241">Per altre informazioni, vedere <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="f53c0-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="f53c0-242">[L'autenticazione di Windows](/windows-server/security/windows-authentication/windows-authentication-overview) è stata estesa in Linux e MacOS.</span><span class="sxs-lookup"><span data-stu-id="f53c0-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="f53c0-243">Nelle versioni precedenti, l'autenticazione di Windows era limitata a [IIS](xref:host-and-deploy/iis/index) e a [HttpSys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="f53c0-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="f53c0-244">In ASP.NET Core 3,0, [gheppio](xref:fundamentals/servers/kestrel) è in grado di usare Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)e [NTLM in Windows](/windows-server/security/kerberos/ntlm-overview), Linux e MacOS per gli host aggiunti a un dominio Windows.</span><span class="sxs-lookup"><span data-stu-id="f53c0-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="f53c0-245">Il supporto di gheppio di questi schemi di autenticazione viene fornito dal pacchetto [NuGet Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) .</span><span class="sxs-lookup"><span data-stu-id="f53c0-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="f53c0-246">Come per gli altri servizi di autenticazione, configurare la larghezza dell'app di autenticazione, quindi configurare il servizio:</span><span class="sxs-lookup"><span data-stu-id="f53c0-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="f53c0-247">Requisiti host:</span><span class="sxs-lookup"><span data-stu-id="f53c0-247">Host requirements:</span></span>

* <span data-ttu-id="f53c0-248">Gli host Windows devono disporre di [nomi di entità servizio](/windows/win32/ad/service-principal-names) (SPN) aggiunti all'account utente che ospita l'app.</span><span class="sxs-lookup"><span data-stu-id="f53c0-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="f53c0-249">I computer Linux e macOS devono essere aggiunti al dominio.</span><span class="sxs-lookup"><span data-stu-id="f53c0-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="f53c0-250">È necessario creare nomi SPN per il processo Web.</span><span class="sxs-lookup"><span data-stu-id="f53c0-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="f53c0-251">[I file keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) devono essere generati e configurati nel computer host.</span><span class="sxs-lookup"><span data-stu-id="f53c0-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="f53c0-252">Per altre informazioni, vedere <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="f53c0-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="f53c0-253">Modifiche ai modelli</span><span class="sxs-lookup"><span data-stu-id="f53c0-253">Template changes</span></span>

<span data-ttu-id="f53c0-254">I modelli dell'interfaccia utente Web (Razor Pages, MVC con controller e visualizzazioni) hanno rimosso quanto segue:</span><span class="sxs-lookup"><span data-stu-id="f53c0-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="f53c0-255">L'interfaccia utente di consenso del cookie non è più inclusa.</span><span class="sxs-lookup"><span data-stu-id="f53c0-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="f53c0-256">Per abilitare la funzionalità di consenso dei cookie in un ASP.NET Core app generata da un modello 3,0, vedere <xref:security/gdpr>.</span><span class="sxs-lookup"><span data-stu-id="f53c0-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="f53c0-257">Agli script e agli asset statici correlati viene ora fatto riferimento come file locali invece di usare CDNs.</span><span class="sxs-lookup"><span data-stu-id="f53c0-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="f53c0-258">Per altre informazioni, vedere [gli script e gli asset statici correlati a cui viene ora fatto riferimento come file locali invece di usare CDNs in base all'ambiente corrente (ASPNET/AspNetCore. Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="f53c0-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="f53c0-259">Modello Angular aggiornato per l'utilizzo di Angular 8.</span><span class="sxs-lookup"><span data-stu-id="f53c0-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="f53c0-260">Per impostazione predefinita, il modello RCL (Razor Class Library) per impostazione predefinita è lo sviluppo di componenti Razor.</span><span class="sxs-lookup"><span data-stu-id="f53c0-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="f53c0-261">Una nuova opzione di modello in Visual Studio fornisce il supporto del modello per le pagine e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="f53c0-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="f53c0-262">Quando si crea un RCL dal modello in una shell dei comandi, passare l'opzione `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`).</span><span class="sxs-lookup"><span data-stu-id="f53c0-262">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="f53c0-263">Host generico</span><span class="sxs-lookup"><span data-stu-id="f53c0-263">Generic Host</span></span>

<span data-ttu-id="f53c0-264">I modelli ASP.NET Core 3,0 usano <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="f53c0-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="f53c0-265">Versioni precedenti utilizzate <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f53c0-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="f53c0-266">L'uso dell'host generico .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) offre una migliore integrazione delle app ASP.NET Core con altri scenari server che non sono specifici del Web.</span><span class="sxs-lookup"><span data-stu-id="f53c0-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="f53c0-267">Per ulteriori informazioni, vedere [HostBuilder sostituisce WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="f53c0-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="f53c0-268">Configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="f53c0-268">Host configuration</span></span>

<span data-ttu-id="f53c0-269">Prima del rilascio di ASP.NET Core 3,0, le variabili di ambiente con prefisso `ASPNETCORE_` sono state caricate per la configurazione host dell'host Web.</span><span class="sxs-lookup"><span data-stu-id="f53c0-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="f53c0-270">In 3,0 `AddEnvironmentVariables` viene usato per caricare le variabili di ambiente con il prefisso `DOTNET_` per la configurazione host con `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="f53c0-271">Modifiche all'inserimento del costruttore di avvio</span><span class="sxs-lookup"><span data-stu-id="f53c0-271">Changes to Startup constructor injection</span></span>

<span data-ttu-id="f53c0-272">L'host generico supporta solo i tipi seguenti per `Startup` injection del costruttore:</span><span class="sxs-lookup"><span data-stu-id="f53c0-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="f53c0-273">Tutti i servizi possono comunque essere inseriti direttamente come argomenti per il metodo `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="f53c0-274">Per ulteriori informazioni, vedere l' [host generico limita l'inserimento del costruttore di avvio (ASPNET/annunci #353)](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="f53c0-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="f53c0-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="f53c0-275">Kestrel</span></span>

* <span data-ttu-id="f53c0-276">La configurazione di Kestrel è stata aggiornata per la migrazione all'host generico.</span><span class="sxs-lookup"><span data-stu-id="f53c0-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="f53c0-277">In 3,0, Gheppio viene configurato nel generatore host Web fornito da `ConfigureWebHostDefaults`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="f53c0-278">Le schede di connessione sono state rimosse da Kestrel e sostituite con il middleware di connessione, che è simile al middleware HTTP nella pipeline ASP.NET Core ma per le connessioni di livello inferiore.</span><span class="sxs-lookup"><span data-stu-id="f53c0-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="f53c0-279">Il livello trasporto gheppio è stato esposto come interfaccia pubblica in `Connections.Abstractions`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="f53c0-280">L'ambiguità tra intestazioni e trailer è stata risolta spostando le intestazioni finali in una nuova raccolta.</span><span class="sxs-lookup"><span data-stu-id="f53c0-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="f53c0-281">Le API di i/o sincrone, ad esempio `HttpRequest.Body.Read`, rappresentano una fonte comune di inedia dei thread che causa arresti anomali dell'app.</span><span class="sxs-lookup"><span data-stu-id="f53c0-281">Synchronous IO APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="f53c0-282">In 3,0 `AllowSynchronousIO` è disabilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="f53c0-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="f53c0-283">Per altre informazioni, vedere <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="f53c0-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="f53c0-284">HTTP/2 abilitato per impostazione predefinita</span><span class="sxs-lookup"><span data-stu-id="f53c0-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="f53c0-285">HTTP/2 è abilitato per impostazione predefinita in gheppio per gli endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f53c0-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="f53c0-286">Il supporto HTTP/2 per IIS o HTTP. sys è abilitato quando supportato dal sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="f53c0-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="f53c0-287">EventCounters su richiesta</span><span class="sxs-lookup"><span data-stu-id="f53c0-287">EventCounters on request</span></span>

<span data-ttu-id="f53c0-288">Il EventSource di hosting, `Microsoft.AspNetCore.Hosting`, emette i nuovi tipi di <xref:System.Diagnostics.Tracing.EventCounter> seguenti correlati alle richieste in ingresso:</span><span class="sxs-lookup"><span data-stu-id="f53c0-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="f53c0-289">Routing degli endpoint</span><span class="sxs-lookup"><span data-stu-id="f53c0-289">Endpoint routing</span></span>

<span data-ttu-id="f53c0-290">Il routing degli endpoint, che consente ai Framework, ad esempio MVC, di funzionare correttamente con il middleware, viene migliorato:</span><span class="sxs-lookup"><span data-stu-id="f53c0-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="f53c0-291">L'ordine di middleware ed endpoint è configurabile nella pipeline di elaborazione delle richieste di `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="f53c0-292">Gli endpoint e il middleware si compongono bene con altre tecnologie basate su ASP.NET Core, ad esempio i controlli di integrità.</span><span class="sxs-lookup"><span data-stu-id="f53c0-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="f53c0-293">Gli endpoint possono implementare un criterio, ad esempio CORS o Authorization, sia in middleware che in MVC.</span><span class="sxs-lookup"><span data-stu-id="f53c0-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="f53c0-294">I filtri e gli attributi possono essere inseriti nei metodi dei controller.</span><span class="sxs-lookup"><span data-stu-id="f53c0-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="f53c0-295">Per altre informazioni, vedere <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="f53c0-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="f53c0-296">Controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="f53c0-296">Health Checks</span></span>

<span data-ttu-id="f53c0-297">I controlli di integrità usano il routing degli endpoint con l'host generico.</span><span class="sxs-lookup"><span data-stu-id="f53c0-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="f53c0-298">In `Startup.Configure`chiamare `MapHealthChecks` sul generatore di endpoint con l'URL dell'endpoint o il percorso relativo:</span><span class="sxs-lookup"><span data-stu-id="f53c0-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="f53c0-299">Gli endpoint di controllo integrità possono:</span><span class="sxs-lookup"><span data-stu-id="f53c0-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="f53c0-300">Specificare uno o più host o porte consentiti.</span><span class="sxs-lookup"><span data-stu-id="f53c0-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="f53c0-301">Richiedere l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="f53c0-301">Require authorization.</span></span>
* <span data-ttu-id="f53c0-302">Richiede CORS.</span><span class="sxs-lookup"><span data-stu-id="f53c0-302">Require CORS.</span></span>

<span data-ttu-id="f53c0-303">Per altre informazioni, vedere gli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="f53c0-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="f53c0-304">Pipe in HttpContext</span><span class="sxs-lookup"><span data-stu-id="f53c0-304">Pipes on HttpContext</span></span>

<span data-ttu-id="f53c0-305">È ora possibile leggere il corpo della richiesta e scrivere il corpo della risposta usando l'API <xref:System.IO.Pipelines>.</span><span class="sxs-lookup"><span data-stu-id="f53c0-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="f53c0-306">Il valore di</span><span class="sxs-lookup"><span data-stu-id="f53c0-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="f53c0-307">`HttpRequest.BodyReader` proprietà fornisce un <xref:System.IO.Pipelines.PipeReader> che può essere utilizzato per leggere il corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="f53c0-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="f53c0-308">Il valore di</span><span class="sxs-lookup"><span data-stu-id="f53c0-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="f53c0-309">`HttpResponse.BodyWriter` proprietà fornisce un <xref:System.IO.Pipelines.PipeWriter> che può essere utilizzato per scrivere il corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="f53c0-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="f53c0-310">`HttpRequest.BodyReader` è un analogo del flusso di `HttpRequest.Body`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="f53c0-311">`HttpResponse.BodyWriter` è un analogo del flusso di `HttpResponse.Body`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="f53c0-312">Segnalazione degli errori migliorata in IIS</span><span class="sxs-lookup"><span data-stu-id="f53c0-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="f53c0-313">Gli errori di avvio quando si ospitano app ASP.NET Core in IIS generano ora dati di diagnostica più completi.</span><span class="sxs-lookup"><span data-stu-id="f53c0-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="f53c0-314">Questi errori vengono segnalati al registro eventi di Windows con le tracce dello stack laddove applicabile.</span><span class="sxs-lookup"><span data-stu-id="f53c0-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="f53c0-315">Inoltre, tutti gli avvisi, gli errori e le eccezioni non gestite vengono registrati nel registro eventi di Windows.</span><span class="sxs-lookup"><span data-stu-id="f53c0-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="f53c0-316">Worker Service e worker SDK</span><span class="sxs-lookup"><span data-stu-id="f53c0-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="f53c0-317">.NET Core 3.0 introduce il nuovo modello di app del servizio Worker.</span><span class="sxs-lookup"><span data-stu-id="f53c0-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="f53c0-318">Questo modello offre un punto di partenza per la scrittura di servizi con esecuzione prolungata in .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f53c0-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="f53c0-319">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="f53c0-319">For more information, see:</span></span>

* [<span data-ttu-id="f53c0-320">Ruoli di lavoro di .NET Core come servizi Windows</span><span class="sxs-lookup"><span data-stu-id="f53c0-320">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="f53c0-321">Miglioramenti del middleware per intestazioni con inoltri</span><span class="sxs-lookup"><span data-stu-id="f53c0-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="f53c0-322">Nelle versioni precedenti di ASP.NET Core la chiamata di <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> e <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> è problematica quando è stata distribuita in Azure Linux o dietro qualsiasi proxy inverso diverso da IIS.</span><span class="sxs-lookup"><span data-stu-id="f53c0-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="f53c0-323">La correzione per le versioni precedenti è documentata in [inoltro dello schema per i proxy inversi di Linux e non IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span><span class="sxs-lookup"><span data-stu-id="f53c0-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="f53c0-324">Questo scenario è stato risolto in ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="f53c0-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="f53c0-325">L'host Abilita il [middleware delle intestazioni con inoltri](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) quando la variabile di ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` è impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="f53c0-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="f53c0-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` è impostato su `true` nelle immagini del contenitore.</span><span class="sxs-lookup"><span data-stu-id="f53c0-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="f53c0-327">Miglioramenti delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="f53c0-327">Performance improvements</span></span>

<span data-ttu-id="f53c0-328">ASP.NET Core 3.0 include numerosi miglioramenti che consentono di ridurre l'utilizzo della memoria e migliorare la velocità effettiva:</span><span class="sxs-lookup"><span data-stu-id="f53c0-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="f53c0-329">Riduzione dell'utilizzo della memoria quando si utilizza il contenitore incorporato di inserimento delle dipendenze per i servizi con ambito.</span><span class="sxs-lookup"><span data-stu-id="f53c0-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="f53c0-330">Riduzione delle allocazioni nel Framework, inclusi gli scenari middleware e il routing.</span><span class="sxs-lookup"><span data-stu-id="f53c0-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="f53c0-331">Riduzione dell'utilizzo della memoria per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="f53c0-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="f53c0-332">Miglioramenti della velocità effettiva e della riduzione della memoria per le connessioni HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f53c0-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="f53c0-333">Nuovo serializzatore JSON ottimizzato e completamente asincrono.</span><span class="sxs-lookup"><span data-stu-id="f53c0-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="f53c0-334">Riduzione dell'utilizzo della memoria e miglioramento della velocità effettiva nell'analisi dei moduli.</span><span class="sxs-lookup"><span data-stu-id="f53c0-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="f53c0-335">ASP.NET Core 3.0 viene eseguito solo in .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="f53c0-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="f53c0-336">A partire da ASP.NET Core 3,0, .NET Framework non è più un Framework di destinazione supportato.</span><span class="sxs-lookup"><span data-stu-id="f53c0-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="f53c0-337">I progetti destinati a .NET Framework possono continuare in modo completamente supportato con la [versione di .NET Core 2,1 LTS](https://www.microsoft.com/net/download/dotnet-core/2.1).</span><span class="sxs-lookup"><span data-stu-id="f53c0-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://www.microsoft.com/net/download/dotnet-core/2.1).</span></span> <span data-ttu-id="f53c0-338">La maggior parte dei pacchetti di ASP.NET Core 2.1. x saranno supportati per un tempo illimitato, oltre il periodo LTS di tre anni per .NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="f53c0-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="f53c0-339">Per informazioni sulla migrazione, vedere [trasferire il codice da .NET Framework a .NET Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="f53c0-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="f53c0-340">Usare il Framework condiviso ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f53c0-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="f53c0-341">Il Framework condiviso ASP.NET Core 3,0, contenuto nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), non richiede più un elemento `<PackageReference />` esplicito nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="f53c0-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="f53c0-342">Al Framework condiviso viene fatto automaticamente riferimento quando si usa l'SDK `Microsoft.NET.Sdk.Web` nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="f53c0-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="f53c0-343">Assembly rimossi dal Framework condiviso ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f53c0-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="f53c0-344">Gli assembly più rilevanti rimossi dal Framework condiviso ASP.NET Core 3.0 sono:</span><span class="sxs-lookup"><span data-stu-id="f53c0-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="f53c0-345">[Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.NET).</span><span class="sxs-lookup"><span data-stu-id="f53c0-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="f53c0-346">Per aggiungere Json.NET a ASP.NET Core 3,0, vedere [aggiungere il supporto per il formato JSON basato su Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="f53c0-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="f53c0-347">ASP.NET Core 3,0 introduce `System.Text.Json` per la lettura e la scrittura di JSON.</span><span class="sxs-lookup"><span data-stu-id="f53c0-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="f53c0-348">Per ulteriori informazioni, vedere la pagina relativa alla [nuova serializzazione JSON](#new-json-serialization) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="f53c0-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="f53c0-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f53c0-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="f53c0-350">Per un elenco completo degli assembly rimossi dal Framework condiviso, vedere [assembly rimossi da Microsoft. AspNetCore. App 3,0](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="f53c0-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="f53c0-351">Per altre informazioni sulla motivazione di questa modifica, vedere [modifiche di rilievo a Microsoft. AspNetCore. app in 3,0](https://github.com/aspnet/Announcements/issues/325) e [una prima occhiata alle modifiche apportate in ASP.NET Core 3,0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="f53c0-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
