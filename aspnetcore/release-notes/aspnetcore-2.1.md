---
title: Novità di ASP.NET Core 2.1
author: isaac2004
description: Informazioni sulle nuove funzionalità in ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: d969b4caab44e3e50b3a0202b25864921d6d01dc
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880863"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="93ac2-103">Novità di ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="93ac2-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="93ac2-104">Questo articolo evidenzia le modifiche più significative apportate ad ASP.NET Core 2.1, con collegamenti alla relativa documentazione.</span><span class="sxs-lookup"><span data-stu-id="93ac2-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## SignalR

SignalR<span data-ttu-id="93ac2-105"> è stato riscritto per ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="93ac2-105"> has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="93ac2-106">ASP.NET Core SignalR include una serie di miglioramenti:</span><span class="sxs-lookup"><span data-stu-id="93ac2-106">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="93ac2-107">Un modello semplificato di scale-out.</span><span class="sxs-lookup"><span data-stu-id="93ac2-107">A simplified scale-out model.</span></span>
* <span data-ttu-id="93ac2-108">Un nuovo client JavaScript senza dipendenza jQuery.</span><span class="sxs-lookup"><span data-stu-id="93ac2-108">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="93ac2-109">Un nuovo protocollo binario compresso basato su MessagePack.</span><span class="sxs-lookup"><span data-stu-id="93ac2-109">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="93ac2-110">Supporto per protocolli personalizzati.</span><span class="sxs-lookup"><span data-stu-id="93ac2-110">Support for custom protocols.</span></span>
* <span data-ttu-id="93ac2-111">Un nuovo modello di risposta streaming.</span><span class="sxs-lookup"><span data-stu-id="93ac2-111">A new streaming response model.</span></span>
* <span data-ttu-id="93ac2-112">Supporto per client basati su WebSocket.</span><span class="sxs-lookup"><span data-stu-id="93ac2-112">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="93ac2-113">Per ulteriori informazioni, vedere [ASP.NET Core SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="93ac2-113">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="razor-class-libraries"></a><span data-ttu-id="93ac2-114">Librerie di classi Razor</span><span class="sxs-lookup"><span data-stu-id="93ac2-114">Razor class libraries</span></span>

<span data-ttu-id="93ac2-115">Con ASP.NET Core 2.1 è più semplice compilare e includere l'interfaccia utente basata su Razor in una libreria e condividerla tra più progetti.</span><span class="sxs-lookup"><span data-stu-id="93ac2-115">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="93ac2-116">Il nuovo Razor SDK consente di compilare file Razor in un progetto di libreria di classi che può essere incluso in un pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="93ac2-116">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="93ac2-117">Le visualizzazioni e le pagine delle librerie vengono individuate automaticamente e possono essere sostituite dall'app.</span><span class="sxs-lookup"><span data-stu-id="93ac2-117">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="93ac2-118">Integrando la compilazione Razor nella compilazione:</span><span class="sxs-lookup"><span data-stu-id="93ac2-118">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="93ac2-119">Si riduce notevolmente il tempo di avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="93ac2-119">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="93ac2-120">Gli aggiornamenti rapidi a visualizzazioni e pagine Razor in fase di esecuzione sono ancora disponibili come parte di un flusso di lavoro di sviluppo iterativo.</span><span class="sxs-lookup"><span data-stu-id="93ac2-120">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="93ac2-121">Per altre informazioni, vedere [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class) (Creare un'interfaccia utente riutilizzabile tramite il progetto di libreria di classi Razor).</span><span class="sxs-lookup"><span data-stu-id="93ac2-121">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="identity-ui-library--scaffolding"></a><span data-ttu-id="93ac2-122">Scaffolding e libreria dell'interfaccia utente di Identity</span><span class="sxs-lookup"><span data-stu-id="93ac2-122">Identity UI library & scaffolding</span></span>

<span data-ttu-id="93ac2-123">ASP.NET Core 2.1 offre [ASP.NET Identity Core](xref:security/authentication/identity) come [libreria di classi Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="93ac2-123">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="93ac2-124">Le app che includono Identity possono applicare il nuovo scaffolder di Identity per aggiungere in modo selettivo il codice sorgente incluso nella libreria di classi Razor per Identity.</span><span class="sxs-lookup"><span data-stu-id="93ac2-124">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="93ac2-125">Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="93ac2-125">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="93ac2-126">Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione.</span><span class="sxs-lookup"><span data-stu-id="93ac2-126">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="93ac2-127">Il codice generato ha la precedenza rispetto allo stesso codice nella libreria di classi Razor per Identity.</span><span class="sxs-lookup"><span data-stu-id="93ac2-127">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="93ac2-128">Le app che **non** includono l'autenticazione possono applicare lo scaffolder di Identity per aggiungere il pacchetto della libreria di classi Razor per Identity.</span><span class="sxs-lookup"><span data-stu-id="93ac2-128">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="93ac2-129">È possibile selezionare il codice Identity da generare.</span><span class="sxs-lookup"><span data-stu-id="93ac2-129">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="93ac2-130">Per altre informazioni, vedere [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity) (Scaffolding di Identity in progetti ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="93ac2-130">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="93ac2-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="93ac2-131">HTTPS</span></span>

<span data-ttu-id="93ac2-132">Vista la maggiore attenzione rivolta a sicurezza e privacy, è importante abilitare HTTPS per le app Web.</span><span class="sxs-lookup"><span data-stu-id="93ac2-132">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="93ac2-133">L'imposizione HTTPS sta diventando sempre più rigida sul Web.</span><span class="sxs-lookup"><span data-stu-id="93ac2-133">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="93ac2-134">I siti che non utilizzano HTTPS sono considerati non sicuri.</span><span class="sxs-lookup"><span data-stu-id="93ac2-134">Sites that don't use HTTPS are considered insecure.</span></span> <span data-ttu-id="93ac2-135">Alcuni browser come Chromium e Mozilla hanno iniziato a imporre che le funzionalità Web siano usate da un contesto protetto.</span><span class="sxs-lookup"><span data-stu-id="93ac2-135">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="93ac2-136">Il [Regolamento generale sulla protezione dei dati (GDPR) ](xref:security/gdpr) richiede l'uso di HTTPS per proteggere la privacy degli utenti.</span><span class="sxs-lookup"><span data-stu-id="93ac2-136">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="93ac2-137">Usare HTTPS nell'ambiente di produzione è una questione critica, usarlo nell'ambiente di sviluppo può invece prevenire problemi di distribuzione, ad esempio collegamenti non sicuri.</span><span class="sxs-lookup"><span data-stu-id="93ac2-137">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="93ac2-138">ASP.NET Core 2.1 include numerosi miglioramenti che semplificano l'uso di HTTPS nell'ambiente di sviluppo e la configurazione di HTTPS nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="93ac2-138">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="93ac2-139">Per altre informazioni, vedere [Imporre HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="93ac2-139">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="93ac2-140">Abilitazione predefinita</span><span class="sxs-lookup"><span data-stu-id="93ac2-140">On by default</span></span>

<span data-ttu-id="93ac2-141">Per semplificare lo sviluppo di siti Web sicuri, HTTPS è ora abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="93ac2-141">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="93ac2-142">A partire dalla versione 2.1, Kestrel è in ascolto su `https://localhost:5001` quando è disponibile un certificato di sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="93ac2-142">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="93ac2-143">Viene creato un certificato di sviluppo nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="93ac2-143">A development certificate is created:</span></span>

* <span data-ttu-id="93ac2-144">Come parte di completamento dell'installazione di .NET Core SDK, quando si usa SDK per la prima volta.</span><span class="sxs-lookup"><span data-stu-id="93ac2-144">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="93ac2-145">Manualmente tramite il nuovo strumento `dev-certs`.</span><span class="sxs-lookup"><span data-stu-id="93ac2-145">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="93ac2-146">Per rendere attendibile il certificato, eseguire `dotnet dev-certs https --trust`.</span><span class="sxs-lookup"><span data-stu-id="93ac2-146">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="93ac2-147">Imposizione e reindirizzamento HTTPS</span><span class="sxs-lookup"><span data-stu-id="93ac2-147">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="93ac2-148">Le app Web sono generalmente in ascolto sia su HTTP sia su HTTPS, ma reindirizzano poi tutto il traffico HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="93ac2-148">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="93ac2-149">Nella versione 2.1 è stato introdotto un middleware di reindirizzamento HTTPS specializzato che in modo intelligente reindirizza il traffico in base alla configurazione o all'associazione di porte server.</span><span class="sxs-lookup"><span data-stu-id="93ac2-149">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="93ac2-150">HTTPS può essere ulteriormente rafforzato tramite il [protocollo di sicurezza HSTS (sicurezza rigida per il trasporto di HTTP)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="93ac2-150">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="93ac2-151">Il protocollo HSTS indica al browser di accedere sempre al sito tramite HTTPS.</span><span class="sxs-lookup"><span data-stu-id="93ac2-151">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="93ac2-152">ASP.NET Core 2.1 aggiunge il middleware HSTS che supporta le opzioni per la validità massima, i sottodomini e l'elenco di precaricamento HSTS.</span><span class="sxs-lookup"><span data-stu-id="93ac2-152">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="93ac2-153">Configurazione per l'ambiente di produzione</span><span class="sxs-lookup"><span data-stu-id="93ac2-153">Configuration for production</span></span>

<span data-ttu-id="93ac2-154">Nell'ambiente di produzione, HTTPS deve essere configurato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="93ac2-154">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="93ac2-155">Nella versione 2.1 è stato aggiunto lo schema di configurazione predefinito per la configurazione di HTTPS per Kestrel.</span><span class="sxs-lookup"><span data-stu-id="93ac2-155">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="93ac2-156">È possibile configurare le app in modo che possano usare gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="93ac2-156">Apps can be configured to use:</span></span>

* <span data-ttu-id="93ac2-157">Più endpoint, inclusi gli URL.</span><span class="sxs-lookup"><span data-stu-id="93ac2-157">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="93ac2-158">Per altre informazioni, vedere [Implementazione del server Web Kestrel: Configurazione dell'endpoint](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="93ac2-158">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="93ac2-159">Il certificato da usare per HTTPS da un file su disco o da un archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="93ac2-159">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="93ac2-160">GDPR</span><span class="sxs-lookup"><span data-stu-id="93ac2-160">GDPR</span></span>

<span data-ttu-id="93ac2-161">ASP.NET Core offre API e modelli con cui è possibile soddisfare alcuni dei requisiti del [GDPR](https://www.eugdpr.org/).</span><span class="sxs-lookup"><span data-stu-id="93ac2-161">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="93ac2-162">Per altre informazioni, vedere [Supporto per il Regolamento generale sulla protezione dei dati in ASP.NET Core](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="93ac2-162">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="93ac2-163">L'[app di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) illustra come usare e testare molte delle API e dei punti di estensione del GDPR aggiunti nei modelli ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="93ac2-163">A [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="93ac2-164">Test di integrazione</span><span class="sxs-lookup"><span data-stu-id="93ac2-164">Integration tests</span></span>

<span data-ttu-id="93ac2-165">È stato introdotto un nuovo pacchetto che semplifica la creazione e l'esecuzione di test.</span><span class="sxs-lookup"><span data-stu-id="93ac2-165">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="93ac2-166">Con il pacchetto [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) è possibile gestire le attività seguenti:</span><span class="sxs-lookup"><span data-stu-id="93ac2-166">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="93ac2-167">Copiare il file di dipendenza (con estensione *\*deps*) dall'app testata nella cartella *bin* del progetto di test.</span><span class="sxs-lookup"><span data-stu-id="93ac2-167">Copies the dependency file (*\*.deps*) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="93ac2-168">Impostare la radice del contenuto sulla radice del progetto dell'app testata in modo che i file statici e le pagine/visualizzazioni siano rilevate durante l'esecuzione dei test.</span><span class="sxs-lookup"><span data-stu-id="93ac2-168">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="93ac2-169">Specificare la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) per semplificare il bootstrap dell'app testata con [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="93ac2-169">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="93ac2-170">Il test seguente usa [xUnit](https://xunit.github.io/) per verificare che la pagina di indice sia caricata con un codice di stato con esito positivo e con l'intestazione Content-Type corretta:</span><span class="sxs-lookup"><span data-stu-id="93ac2-170">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

<span data-ttu-id="93ac2-171">Per altre informazioni, vedere l'argomento [Test di integrazione](xref:test/integration-tests).</span><span class="sxs-lookup"><span data-stu-id="93ac2-171">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="93ac2-172">[ApiController], ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="93ac2-172">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="93ac2-173">ASP.NET Core 2.1 aggiunge nuove convenzioni di programmazione che rendono più semplice la compilazione di API Web descrittive e ordinate.</span><span class="sxs-lookup"><span data-stu-id="93ac2-173">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="93ac2-174">`ActionResult<T>` è una nuova convenzione aggiunta che consente a un'app di restituire un tipo di risposta o qualsiasi altro risultato dell'azione (come IActionResult) e al tempo stesso indicare il tipo di risposta.</span><span class="sxs-lookup"><span data-stu-id="93ac2-174">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="93ac2-175">`[ApiController]` è un attributo aggiunto per acconsentire esplicitamente a convenzioni e comportamenti specifici per API Web.</span><span class="sxs-lookup"><span data-stu-id="93ac2-175">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="93ac2-176">Per altre informazioni, vedere [Creare API Web con ASP.NET Core](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="93ac2-176">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="93ac2-177">IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="93ac2-177">IHttpClientFactory</span></span>

<span data-ttu-id="93ac2-178">ASP.NET Core 2.1 include un nuovo servizio `IHttpClientFactory` che semplifica la configurazione e l'uso di istanze di `HttpClient` nelle app.</span><span class="sxs-lookup"><span data-stu-id="93ac2-178">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="93ac2-179">`HttpClient` include già il concetto di delega di gestori concatenati per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="93ac2-179">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="93ac2-180">Il factory:</span><span class="sxs-lookup"><span data-stu-id="93ac2-180">The factory:</span></span>

* <span data-ttu-id="93ac2-181">Esegue la registrazione di istanze di `HttpClient` per ogni client denominato in modo più intuitivo.</span><span class="sxs-lookup"><span data-stu-id="93ac2-181">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="93ac2-182">Implementa un gestore Polly che consente di usare i criteri Polly per schemi Retry, CircuitBreakers e così via.</span><span class="sxs-lookup"><span data-stu-id="93ac2-182">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="93ac2-183">Per altre informazioni, vedere [Inizializzare richieste HTTP](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="93ac2-183">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="93ac2-184">Configurazione del trasporto di Kestrel</span><span class="sxs-lookup"><span data-stu-id="93ac2-184">Kestrel transport configuration</span></span>

<span data-ttu-id="93ac2-185">Con la versione ASP.NET Core 2.1, il trasporto predefinito di Kestrel non si basa più su Libuv, ma su socket gestiti.</span><span class="sxs-lookup"><span data-stu-id="93ac2-185">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="93ac2-186">Per altre informazioni, vedere [Implementazione del server Web Kestrel: Configurazione del trasporto](xref:fundamentals/servers/kestrel#transport-configuration).</span><span class="sxs-lookup"><span data-stu-id="93ac2-186">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="93ac2-187">Generatore di host generico</span><span class="sxs-lookup"><span data-stu-id="93ac2-187">Generic host builder</span></span>

<span data-ttu-id="93ac2-188">È stato introdotto il generatore di host generico (`HostBuilder`).</span><span class="sxs-lookup"><span data-stu-id="93ac2-188">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="93ac2-189">È possibile usare il generatore per le app che non elaborano richieste HTTP (messaggistica, attività in background e così via).</span><span class="sxs-lookup"><span data-stu-id="93ac2-189">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="93ac2-190">Per altre informazioni, vedere [Host generico .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="93ac2-190">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="93ac2-191">Modelli di applicazione a pagina singola aggiornati</span><span class="sxs-lookup"><span data-stu-id="93ac2-191">Updated SPA templates</span></span>

<span data-ttu-id="93ac2-192">Sono stati aggiornati i modelli di applicazione a pagina singola per Angular, React e React con Redux in modo da usare le strutture di progetto e i sistemi di compilazione standard per ogni framework.</span><span class="sxs-lookup"><span data-stu-id="93ac2-192">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="93ac2-193">Il modello Angular si basa sulla riga di comando di Angular mentre i modelli React su create-react-app.</span><span class="sxs-lookup"><span data-stu-id="93ac2-193">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="93ac2-194">Per altre informazioni, vedere:</span><span class="sxs-lookup"><span data-stu-id="93ac2-194">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a><span data-ttu-id="93ac2-195">Ricerca di asset Razor con Razor Pages</span><span class="sxs-lookup"><span data-stu-id="93ac2-195">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="93ac2-196">Nella versione 2.1 Razor Pages esegue la ricerca di asset Razor, ad esempio layout e parziali, nelle directory seguenti attendendosi all'ordine elencato:</span><span class="sxs-lookup"><span data-stu-id="93ac2-196">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="93ac2-197">Cartella Pages corrente.</span><span class="sxs-lookup"><span data-stu-id="93ac2-197">Current Pages folder.</span></span>
1. <span data-ttu-id="93ac2-198">*/Pages/Shared/*</span><span class="sxs-lookup"><span data-stu-id="93ac2-198">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="93ac2-199">*/Views/Shared/*</span><span class="sxs-lookup"><span data-stu-id="93ac2-199">*/Views/Shared/*</span></span>

## <a name="razor-pages-in-an-area"></a><span data-ttu-id="93ac2-200">Razor Pages in un'area</span><span class="sxs-lookup"><span data-stu-id="93ac2-200">Razor Pages in an area</span></span>

<span data-ttu-id="93ac2-201">Razor Pages ora supporta le [aree](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="93ac2-201">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="93ac2-202">Per visualizzare un esempio di area, creare una nuova app Web Razor Pages con singoli account utente.</span><span class="sxs-lookup"><span data-stu-id="93ac2-202">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="93ac2-203">Un'app Web Razor Pages con account utente individuali include */Areas/Identity/Pages*.</span><span class="sxs-lookup"><span data-stu-id="93ac2-203">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="93ac2-204">Versione di compatibilità MVC</span><span class="sxs-lookup"><span data-stu-id="93ac2-204">MVC compatibility version</span></span>

<span data-ttu-id="93ac2-205">Il metodo <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> consente a un'app di acconsentire o rifiutare esplicitamente modifiche potenzialmente importanti del comportamento introdotte in ASP.NET Core MVC 2.1 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="93ac2-205">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="93ac2-206">Per ulteriori informazioni, vedere <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="93ac2-206">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="93ac2-207">Eseguire la migrazione dalla versione 2.0 alla versione 2.1</span><span class="sxs-lookup"><span data-stu-id="93ac2-207">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="93ac2-208">Vedere [Eseguire la migrazione da ASP.NET Core 2.0 alla versione 2.1](xref:migration/20_21).</span><span class="sxs-lookup"><span data-stu-id="93ac2-208">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="93ac2-209">Informazioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="93ac2-209">Additional information</span></span>

<span data-ttu-id="93ac2-210">Per l'elenco completo delle modifiche, vedere le [note sulla versione di ASP.NET Core 2.1](https://github.com/aspnet/Home/releases/tag/2.1.0).</span><span class="sxs-lookup"><span data-stu-id="93ac2-210">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/aspnet/Home/releases/tag/2.1.0).</span></span>
