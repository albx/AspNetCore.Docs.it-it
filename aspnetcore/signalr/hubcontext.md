---
title: SignalR HubContext
author: bradygaster
description: Informazioni su come usare il servizio ASP.NET Core SignalR HubContext per l'invio di notifiche ai client all'esterno di un hub.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2018
uid: signalr/hubcontext
ms.openlocfilehash: 7ec52d4711fc191dcb83120cf54b1dc28c41f947
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894478"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="0d213-103">Invio di messaggi provenienti dall'esterno di un hub</span><span class="sxs-lookup"><span data-stu-id="0d213-103">Send messages from outside a hub</span></span>

<span data-ttu-id="0d213-104">Da [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="0d213-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="0d213-105">L'hub SignalR è l'astrazione fondamentale per l'invio di messaggi ai client connessi al server di SignalR.</span><span class="sxs-lookup"><span data-stu-id="0d213-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="0d213-106">È anche possibile inviare messaggi da altre posizioni all'interno dell'app tramite il `IHubContext` servizio.</span><span class="sxs-lookup"><span data-stu-id="0d213-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="0d213-107">In questo articolo viene illustrato come accedere a un `IHubContext` di SignalR per inviare notifiche ai client all'esterno di un hub.</span><span class="sxs-lookup"><span data-stu-id="0d213-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="0d213-108">[Visualizzare o scaricare codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(come scaricare)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="0d213-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="0d213-109">Ottenere un'istanza di IHubContext</span><span class="sxs-lookup"><span data-stu-id="0d213-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="0d213-110">In ASP.NET Core SignalR, è possibile accedere a un'istanza di `IHubContext` tramite inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="0d213-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="0d213-111">È possibile inserire un'istanza di `IHubContext` in un controller, middleware o altro servizio di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="0d213-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="0d213-112">Usare l'istanza per inviare messaggi ai client.</span><span class="sxs-lookup"><span data-stu-id="0d213-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="0d213-113">Questo comportamento è diverso da ASP.NET 4.x SignalR che consente di fornire l'accesso a GlobalHost il `IHubContext`.</span><span class="sxs-lookup"><span data-stu-id="0d213-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="0d213-114">ASP.NET Core ha un framework di dependency injection che elimina la necessità di questo singleton globale.</span><span class="sxs-lookup"><span data-stu-id="0d213-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="0d213-115">Inserire un'istanza di IHubContext in un controller</span><span class="sxs-lookup"><span data-stu-id="0d213-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="0d213-116">È possibile inserire un'istanza di `IHubContext` in un controller, aggiungendola al costruttore eventi:</span><span class="sxs-lookup"><span data-stu-id="0d213-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="0d213-117">A questo punto, con l'accesso a un'istanza di `IHubContext`, è possibile chiamare i metodi dell'hub come se fossi nell'hub stesso.
</span><span class="sxs-lookup"><span data-stu-id="0d213-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="0d213-118">Ottenere un'istanza di IHubContext in middleware</span><span class="sxs-lookup"><span data-stu-id="0d213-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="0d213-119">Accesso di `IHubContext` all'interno della pipeline middleware come illustrato di seguito:</span><span class="sxs-lookup"><span data-stu-id="0d213-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<MyHub>>();
    //...
});
```

> [!NOTE]
> <span data-ttu-id="0d213-120">Quando i metodi dell'hub vengono chiamati all'esterno del `Hub` classe, non vi è alcun chiamante associati con la chiamata.</span><span class="sxs-lookup"><span data-stu-id="0d213-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="0d213-121">Pertanto, non è possibile accedere per il `ConnectionId`, `Caller`, e `Others` proprietà.</span><span class="sxs-lookup"><span data-stu-id="0d213-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="0d213-122">Inserire un HubContext fortemente tipizzati</span><span class="sxs-lookup"><span data-stu-id="0d213-122">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="0d213-123">Per inserire un HubContext fortemente tipizzato, assicurarsi che l'Hub eredita da `Hub<T>`.</span><span class="sxs-lookup"><span data-stu-id="0d213-123">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="0d213-124">Inserire usando il `IHubContext<THub, T>` interfaccia anziché `IHubContext<THub>`.</span><span class="sxs-lookup"><span data-stu-id="0d213-124">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(message);
    }
}
```

## <a name="related-resources"></a><span data-ttu-id="0d213-125">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="0d213-125">Related resources</span></span>

* [<span data-ttu-id="0d213-126">Introduzione</span><span class="sxs-lookup"><span data-stu-id="0d213-126">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0d213-127">Hub</span><span class="sxs-lookup"><span data-stu-id="0d213-127">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="0d213-128">Pubblicare in Azure</span><span class="sxs-lookup"><span data-stu-id="0d213-128">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
