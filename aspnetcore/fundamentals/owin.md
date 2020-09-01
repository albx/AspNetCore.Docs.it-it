---
title: Open Web Interface for .NET (OWIN) con ASP.NET Core
author: ardalis
description: Informazioni su come ASP.NET Core supporta Open Web Interface for .NET (OWIN) che consente ai server Web di disaccoppiare le app Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/owin
ms.openlocfilehash: 817eb652f4feedf19dd60873b480917c320272a3
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102757"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="0a494-103">Open Web Interface for .NET (OWIN) con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a494-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="0a494-104">Di [Steve Smith](https://ardalis.com/) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0a494-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0a494-105">ASP.NET Core supporta Open Web Interface for .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="0a494-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="0a494-106">OWIN consente alle app Web di essere disaccoppiate dai server Web.</span><span class="sxs-lookup"><span data-stu-id="0a494-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="0a494-107">Definisce un modo standard per usare il middleware in una pipeline per gestire le richieste e le risposte associate.</span><span class="sxs-lookup"><span data-stu-id="0a494-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="0a494-108">Le applicazioni ASP.NET Core e il middleware possono interagire con middleware, server e applicazioni basati su OWIN.</span><span class="sxs-lookup"><span data-stu-id="0a494-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="0a494-109">OWIN specifica un livello di disaccoppiamento che consente di usare contemporaneamente due framework con modelli a oggetti diversi.</span><span class="sxs-lookup"><span data-stu-id="0a494-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="0a494-110">Il pacchetto `Microsoft.AspNetCore.Owin` offre due implementazioni dell'adattatore:</span><span class="sxs-lookup"><span data-stu-id="0a494-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="0a494-111">Da ASP.NET Core a OWIN</span><span class="sxs-lookup"><span data-stu-id="0a494-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="0a494-112">Da OWIN a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a494-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="0a494-113">In questo modo ASP.NET Core può essere ospitato in un server/host compatibile con OWIN o altri componenti compatibili con OWIN possono essere eseguiti su ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a494-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="0a494-114">L'uso di questi adattatori comporta una riduzione delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="0a494-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="0a494-115">Le app che usano solo componenti di ASP.NET Core non devono usare il pacchetto o gli adattatori `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="0a494-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="0a494-116">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0a494-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="0a494-117">Esecuzione del middleware OWIN nella pipeline ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a494-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="0a494-118">Il supporto di OWIN per ASP.NET Core viene distribuito come parte del pacchetto `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="0a494-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="0a494-119">Per importare il supporto OWIN nel progetto è necessario installare il pacchetto.</span><span class="sxs-lookup"><span data-stu-id="0a494-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="0a494-120">Il middleware OWIN è conforme alle [specifiche OWIN](https://owin.org/spec/spec/owin-1.0.0.html), che richiedono l'interfaccia `Func<IDictionary<string, object>, Task>` e che siano impostate chiavi specifiche, come ad esempio `owin.ResponseBody`.</span><span class="sxs-lookup"><span data-stu-id="0a494-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="0a494-121">Il semplice middleware OWIN illustrato di seguito visualizza "Hello World":</span><span class="sxs-lookup"><span data-stu-id="0a494-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="0a494-122">La firma di esempio restituisce un oggetto `Task` e accetta un oggetto `IDictionary<string, object>` come richiesto da OWIN.</span><span class="sxs-lookup"><span data-stu-id="0a494-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="0a494-123">Il codice seguente illustra come aggiungere il middleware `OwinHello`, illustrato in precedenza, alla pipeline ASP.NET Core con il metodo di estensione `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="0a494-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="0a494-124">È possibile configurare altre azioni da eseguire all'interno della pipeline OWIN.</span><span class="sxs-lookup"><span data-stu-id="0a494-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="0a494-125">Le intestazioni di risposta devono essere modificate solo prima della prima scrittura nel flusso di risposta.</span><span class="sxs-lookup"><span data-stu-id="0a494-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="0a494-126">Le chiamate multiple al metodo `UseOwin` sono sconsigliate per non compromettere le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="0a494-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="0a494-127">I componenti OWIN funzionano meglio se raggruppati insieme.</span><span class="sxs-lookup"><span data-stu-id="0a494-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="0a494-128">Eseguire ASP.NET Core in un server basato su OWIN e usare il supporto di WebSocket</span><span class="sxs-lookup"><span data-stu-id="0a494-128">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="0a494-129">Un altro esempio di funzionalità dei server basati su OWIN che può essere sfruttata da ASP.NET Core è l'accesso a funzionalità quali i WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0a494-129">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="0a494-130">Il server Web OWIN per .NET usato nell'esempio precedente ha il supporto per i WebSocket incorporato che può essere usato da un'applicazione ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a494-130">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="0a494-131">L'esempio seguente illustra una semplice app Web che supporta i WebSocket e restituisce tutto quanto inviato al server tramite WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0a494-131">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a><span data-ttu-id="0a494-132">Ambiente OWIN</span><span class="sxs-lookup"><span data-stu-id="0a494-132">OWIN environment</span></span>

<span data-ttu-id="0a494-133">È possibile creare un ambiente OWIN tramite `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="0a494-133">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="0a494-134">Chiavi OWIN</span><span class="sxs-lookup"><span data-stu-id="0a494-134">OWIN keys</span></span>

<span data-ttu-id="0a494-135">OWIN dipende da un oggetto `IDictionary<string,object>` per comunicare informazioni attraverso uno scambio di richiesta/risposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="0a494-135">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="0a494-136">ASP.NET Core implementa le chiavi elencate di seguito.</span><span class="sxs-lookup"><span data-stu-id="0a494-136">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="0a494-137">Vedere le [specifiche principali e le estensioni](https://owin.org/#spec), nonché la pagina [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html) (Linee guida sulle chiavi OWIN e chiavi comuni).</span><span class="sxs-lookup"><span data-stu-id="0a494-137">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="0a494-138">Dati della richiesta (OWIN versione 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="0a494-138">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="0a494-139">Chiave</span><span class="sxs-lookup"><span data-stu-id="0a494-139">Key</span></span>               | <span data-ttu-id="0a494-140">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="0a494-140">Value (type)</span></span> | <span data-ttu-id="0a494-141">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0a494-141">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="0a494-142">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="0a494-142">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="0a494-143">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="0a494-143">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="0a494-144">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="0a494-144">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="0a494-145">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="0a494-145">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="0a494-146">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="0a494-146">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="0a494-147">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="0a494-147">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="0a494-148">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="0a494-148">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="0a494-149">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="0a494-149">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="0a494-150">Dati della richiesta (OWIN versione 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="0a494-150">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="0a494-151">Chiave</span><span class="sxs-lookup"><span data-stu-id="0a494-151">Key</span></span>               | <span data-ttu-id="0a494-152">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="0a494-152">Value (type)</span></span> | <span data-ttu-id="0a494-153">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0a494-153">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="0a494-154">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="0a494-154">owin.RequestId</span></span> | `String` | <span data-ttu-id="0a494-155">Facoltativo</span><span class="sxs-lookup"><span data-stu-id="0a494-155">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="0a494-156">Dati della risposta (OWIN versione 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="0a494-156">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="0a494-157">Chiave</span><span class="sxs-lookup"><span data-stu-id="0a494-157">Key</span></span>               | <span data-ttu-id="0a494-158">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="0a494-158">Value (type)</span></span> | <span data-ttu-id="0a494-159">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0a494-159">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="0a494-160">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="0a494-160">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="0a494-161">Facoltativo</span><span class="sxs-lookup"><span data-stu-id="0a494-161">Optional</span></span> |
| <span data-ttu-id="0a494-162">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="0a494-162">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="0a494-163">Facoltativo</span><span class="sxs-lookup"><span data-stu-id="0a494-163">Optional</span></span> |
| <span data-ttu-id="0a494-164">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="0a494-164">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="0a494-165">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="0a494-165">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="0a494-166">Altri dati (OWIN versione 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="0a494-166">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="0a494-167">Chiave</span><span class="sxs-lookup"><span data-stu-id="0a494-167">Key</span></span>               | <span data-ttu-id="0a494-168">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="0a494-168">Value (type)</span></span> | <span data-ttu-id="0a494-169">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0a494-169">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="0a494-170">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="0a494-170">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="0a494-171">owin.Version</span><span class="sxs-lookup"><span data-stu-id="0a494-171">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="0a494-172">Chiavi comuni</span><span class="sxs-lookup"><span data-stu-id="0a494-172">Common keys</span></span>

| <span data-ttu-id="0a494-173">Chiave</span><span class="sxs-lookup"><span data-stu-id="0a494-173">Key</span></span>               | <span data-ttu-id="0a494-174">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="0a494-174">Value (type)</span></span> | <span data-ttu-id="0a494-175">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0a494-175">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="0a494-176">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="0a494-176">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="0a494-177">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="0a494-177">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="0a494-178">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="0a494-178">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="0a494-179">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="0a494-179">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="0a494-180">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="0a494-180">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="0a494-181">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="0a494-181">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="0a494-182">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="0a494-182">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="0a494-183">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="0a494-183">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="0a494-184">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="0a494-184">SendFiles v0.3.0</span></span>

| <span data-ttu-id="0a494-185">Chiave</span><span class="sxs-lookup"><span data-stu-id="0a494-185">Key</span></span>               | <span data-ttu-id="0a494-186">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="0a494-186">Value (type)</span></span> | <span data-ttu-id="0a494-187">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0a494-187">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="0a494-188">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="0a494-188">sendfile.SendAsync</span></span> | <span data-ttu-id="0a494-189">Vedere [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Firma delegato)</span><span class="sxs-lookup"><span data-stu-id="0a494-189">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="0a494-190">Per richiesta</span><span class="sxs-lookup"><span data-stu-id="0a494-190">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="0a494-191">Opaque v0.3.0</span><span class="sxs-lookup"><span data-stu-id="0a494-191">Opaque v0.3.0</span></span>

| <span data-ttu-id="0a494-192">Chiave</span><span class="sxs-lookup"><span data-stu-id="0a494-192">Key</span></span>               | <span data-ttu-id="0a494-193">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="0a494-193">Value (type)</span></span> | <span data-ttu-id="0a494-194">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0a494-194">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="0a494-195">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="0a494-195">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="0a494-196">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="0a494-196">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="0a494-197">Vedere [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Firma delegato)</span><span class="sxs-lookup"><span data-stu-id="0a494-197">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="0a494-198">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="0a494-198">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="0a494-199">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="0a494-199">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="0a494-200">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="0a494-200">WebSocket v0.3.0</span></span>

| <span data-ttu-id="0a494-201">Chiave</span><span class="sxs-lookup"><span data-stu-id="0a494-201">Key</span></span>               | <span data-ttu-id="0a494-202">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="0a494-202">Value (type)</span></span> | <span data-ttu-id="0a494-203">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0a494-203">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="0a494-204">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="0a494-204">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="0a494-205">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="0a494-205">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="0a494-206">Vedere [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Firma delegato)</span><span class="sxs-lookup"><span data-stu-id="0a494-206">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="0a494-207">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="0a494-207">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="0a494-208">Non specificata</span><span class="sxs-lookup"><span data-stu-id="0a494-208">Non-spec</span></span> |
| <span data-ttu-id="0a494-209">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="0a494-209">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="0a494-210">Vedere [RFC6455 sezione 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) passaggio 5.5</span><span class="sxs-lookup"><span data-stu-id="0a494-210">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="0a494-211">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="0a494-211">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="0a494-212">Vedere [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Firma delegato)</span><span class="sxs-lookup"><span data-stu-id="0a494-212">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="0a494-213">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="0a494-213">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="0a494-214">Vedere [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Firma delegato)</span><span class="sxs-lookup"><span data-stu-id="0a494-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="0a494-215">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="0a494-215">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="0a494-216">Vedere [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Firma delegato)</span><span class="sxs-lookup"><span data-stu-id="0a494-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="0a494-217">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="0a494-217">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="0a494-218">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="0a494-218">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="0a494-219">Facoltativo</span><span class="sxs-lookup"><span data-stu-id="0a494-219">Optional</span></span> |
| <span data-ttu-id="0a494-220">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="0a494-220">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="0a494-221">Facoltativo</span><span class="sxs-lookup"><span data-stu-id="0a494-221">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="0a494-222">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0a494-222">Additional resources</span></span>

* [<span data-ttu-id="0a494-223">Middleware</span><span class="sxs-lookup"><span data-stu-id="0a494-223">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="0a494-224">Server</span><span class="sxs-lookup"><span data-stu-id="0a494-224">Servers</span></span>](xref:fundamentals/servers/index)
