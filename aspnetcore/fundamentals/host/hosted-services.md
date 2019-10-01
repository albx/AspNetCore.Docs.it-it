---
title: Attività in background con servizi ospitati in ASP.NET Core
author: guardrex
description: Informazioni su come implementare attività in background con servizi ospitati in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 0eaa3a62370c1e413840bb65f597dc664adafc38
ms.sourcegitcommit: fe88748b762525cb490f7e39089a4760f6a73a24
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71688111"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Attività in background con servizi ospitati in ASP.NET Core

Di [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

In ASP.NET Core le attività in background possono essere implementate come *servizi ospitati*. Un servizio ospitato è una classe con logica di attività in background che implementa l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>. In questo argomento vengono forniti tre esempi di servizio ospitato:

* Attività in background eseguita su un timer.
* Servizio ospitato che attiva un [servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes). Il servizio con ambito può usare l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).
* Attività in background in coda che vengono eseguite in sequenza.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

L'app di esempio è disponibile in due versioni:

* Host Web &ndash; L'host Web è utile per l'hosting di app Web. Il codice di esempio in questo argomento è tratto dalla versione host Web dell'esempio. Per altre informazioni, vedere l'argomento [Host Web](xref:fundamentals/host/web-host).
* Host generico &ndash; L'host generico è una novità di ASP.NET Core 2.1. Per altre informazioni, vedere l'argomento [Host generico](xref:fundamentals/host/generic-host).

## <a name="worker-service-template"></a>Modello di servizio di ruolo di lavoro

Il modello di servizio di ruolo di lavoro di ASP.NET Core rappresenta un punto di partenza per la scrittura di app di servizi a esecuzione prolungata. Per usare il modello come base per un'app di servizi ospitati:

[!INCLUDE[](~/includes/worker-template-instructions.md)]

---

## <a name="package"></a>Pacchetto

Un riferimento al pacchetto [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) viene aggiunto in modo implicito per le app ASP.NET Core.

## <a name="ihostedservice-interface"></a>Interfaccia IHostedService

L' <xref:Microsoft.Extensions.Hosting.IHostedService> interfaccia definisce due metodi per gli oggetti gestiti dall'host:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contiene la logica per avviare l'attività in background. `StartAsync`viene chiamato *prima*di:

  * La pipeline di elaborazione delle richieste dell'app è`Startup.Configure`configurata ().
  * Il server viene avviato e viene attivato [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .

  Il comportamento predefinito può essere modificato in modo che il servizio `StartAsync` ospitato venga eseguito dopo che la pipeline dell'app è stata configurata e `ApplicationStarted` chiamata. Per modificare il comportamento predefinito, aggiungere il servizio ospitato (`VideosWatcher` nell'esempio seguente) dopo la chiamata `ConfigureWebHostDefaults`a:

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Attivato quando l'host sta eseguendo un arresto normale. `StopAsync` contiene la logica per terminare l'attività in background. Implementare <xref:System.IDisposable> e i [finalizzatori (distruttori)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) per eliminare tutte le risorse non gestite.

  Il token di annullamento ha un timeout predefinito di cinque secondi che indica che il processo di arresto non è più normale. Quando viene richiesto l'annullamento sul token:

  * Tutte le operazioni in background rimanenti che sta eseguendo l'app devono essere interrotte.
  * Tutti i metodi eventuali chiamati in `StopAsync` devono essere completati rapidamente.

  Tuttavia, dopo la richiesta di annullamento le attività non vengono abbandonate: il chiamante attende il completamento di tutte le attività.

  Se l'app si arresta in modo imprevisto, ad esempio, il processo dell'app ha esito negativo, il metodo `StopAsync` potrebbe non essere chiamato. Pertanto è possibile che i metodi chiamati o le operazioni effettuate in `StopAsync` non vengano eseguiti.

  Per estendere il timeout di arresto predefinito di cinque secondi, impostare:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> quando si usa l'host generico. Per altre informazioni, vedere <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Impostazione di configurazione dell'host del timeout di arresto quando si usa l'host Web. Per altre informazioni, vedere <xref:fundamentals/host/web-host#shutdown-timeout>.

Il servizio ospitato viene attivato una volta all'avvio dell'app e arrestato normalmente all'arresto dell'applicazione. Se viene generato un errore durante l'esecuzione dell'attività in background, deve essere chiamato `Dispose` anche se `StopAsync` non viene chiamato.

## <a name="backgroundservice"></a>BackgroundService

`BackgroundService`è una classe di base per l'implementazione di <xref:Microsoft.Extensions.Hosting.IHostedService>un oggetto a esecuzione prolungata. `BackgroundService`fornisce il `ExecuteAsync(CancellationToken stoppingToken)` metodo astratto per contenere la logica del servizio. Viene attivato quando viene chiamato [IHostedService. StopAsync.](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) `stoppingToken` L'implementazione di questo metodo restituisce un `Task` oggetto che rappresenta l'intera durata del servizio in background.

Inoltre, *facoltativamente* , eseguire l'override dei metodi `IHostedService` definiti in per eseguire il codice di avvio e arresto per il servizio:

* `StopAsync(CancellationToken cancellationToken)`&ndash; vienechiamatoquandol'hostdell'applicazionesta`StopAsync` eseguendo un arresto normale. `cancellationToken` Viene segnalato quando l'host decide di terminare forzatamente il servizio. Se viene eseguito l'override di questo metodo, è **necessario** chiamare `await`(e) il metodo della classe di base per verificare che il servizio venga arrestato correttamente.
* `StartAsync(CancellationToken cancellationToken)`&ndash; vienechiamatoperavviare`StartAsync` il servizio in background. `cancellationToken` Viene segnalato se il processo di avvio viene interrotto. L'implementazione restituisce un `Task` oggetto che rappresenta il processo di avvio per il servizio. Non vengono avviati altri servizi fino `Task` a quando l'operazione non viene completata. Se viene eseguito l'override di questo metodo, è **necessario** chiamare `await`(e) il metodo della classe di base per verificare che il servizio venga avviato correttamente.

## <a name="timed-background-tasks"></a>Attività in background programmate

Un'attività programmata in background utilizza la classe [System.Threading.Timer](xref:System.Threading.Timer). Il timer attiva il metodo `DoWork` dell'attività. Il timer viene disabilitato con `StopAsync` ed eliminato quando il contenitore dei servizi è eliminato con `Dispose`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

Il servizio è registrato in `IHostBuilder.ConfigureServices` (*Program.cs*) con il `AddHostedService` metodo di estensione:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Utilizzo di un servizio con ambito in un'attività in background

Per usare i [servizi con ambito](xref:fundamentals/dependency-injection#service-lifetimes) all' `BackgroundService`interno di un, creare un ambito. Non viene creato automaticamente alcun ambito per un servizio ospitato.

Il servizio dell'attività in background con ambito contiene la logica dell'attività in background. Nell'esempio seguente:

* Il servizio è asincrono. Il metodo `DoWork` restituisce un tipo `Task`. A scopo dimostrativo, nel `DoWork` metodo è atteso un ritardo di dieci secondi.
* Un <xref:Microsoft.Extensions.Logging.ILogger> oggetto viene inserito nel servizio.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Il servizio ospitato crea un ambito per risolvere il servizio attività in background con ambito per chiamare `DoWork` il relativo metodo. `DoWork`Restituisce un `Task`oggetto, che è atteso `ExecuteAsync`in:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

I servizi sono registrati in `IHostBuilder.ConfigureServices` (*Program.cs*). Il servizio ospitato viene registrato con il `AddHostedService` metodo di estensione:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Attività in background in coda

Una coda delle attività in background è basata su .NET 4. <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> x ([programma provvisoriamente per essere incorporato per ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

Nell'esempio seguente `QueueHostedService` :

* Il `BackgroundProcessing` metodo restituisce un `Task`oggetto, che è atteso `ExecuteAsync`in.
* Le attività in background nella coda vengono rimesse in coda ed `BackgroundProcessing`eseguite in.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28,39-40,44)]

Un `MonitorLoop` servizio gestisce le attività di Accodamento per il servizio ospitato ogni `w` volta che la chiave viene selezionata in un dispositivo di input:

* `IBackgroundTaskQueue` Viene inserito`MonitorLoop` nel servizio.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem`viene chiamato per accodare un elemento di lavoro.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

I servizi sono registrati in `IHostBuilder.ConfigureServices` (*Program.cs*). Il servizio ospitato viene registrato con il `AddHostedService` metodo di estensione:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In ASP.NET Core le attività in background possono essere implementate come *servizi ospitati*. Un servizio ospitato è una classe con logica di attività in background che implementa l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>. In questo argomento vengono forniti tre esempi di servizio ospitato:

* Attività in background eseguita su un timer.
* Servizio ospitato che attiva un [servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes). Il servizio con ambito può usare l' [inserimento di dipendenze](xref:fundamentals/dependency-injection)
* Attività in background in coda che vengono eseguite in sequenza.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

L'app di esempio è disponibile in due versioni:

* Host Web &ndash; L'host Web è utile per l'hosting di app Web. Il codice di esempio in questo argomento è tratto dalla versione host Web dell'esempio. Per altre informazioni, vedere l'argomento [Host Web](xref:fundamentals/host/web-host).
* Host generico &ndash; L'host generico è una novità di ASP.NET Core 2.1. Per altre informazioni, vedere l'argomento [Host generico](xref:fundamentals/host/generic-host).

## <a name="package"></a>Pacchetto

Fare riferimento al [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) oppure aggiungere un riferimento al pacchetto [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).

## <a name="ihostedservice-interface"></a>Interfaccia IHostedService

I servizi ospitati implementano l'interfaccia <xref:Microsoft.Extensions.Hosting.IHostedService>. L'interfaccia definisce due metodi riservati agli oggetti gestiti dall'host:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contiene la logica per avviare l'attività in background. Quando si usa l'[Host Web](xref:fundamentals/host/web-host), `StartAsync` viene chiamato dopo l'avvio del server e l'attivazione di [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*). Quando si usa l'[Host generico](xref:fundamentals/host/generic-host), `StartAsync` viene chiamato prima dell'attivazione di `ApplicationStarted`.

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Attivato quando l'host sta eseguendo un arresto normale. `StopAsync` contiene la logica per terminare l'attività in background. Implementare <xref:System.IDisposable> e i [finalizzatori (distruttori)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) per eliminare tutte le risorse non gestite.

  Il token di annullamento ha un timeout predefinito di cinque secondi che indica che il processo di arresto non è più normale. Quando viene richiesto l'annullamento sul token:

  * Tutte le operazioni in background rimanenti che sta eseguendo l'app devono essere interrotte.
  * Tutti i metodi eventuali chiamati in `StopAsync` devono essere completati rapidamente.

  Tuttavia, dopo la richiesta di annullamento le attività non vengono abbandonate: il chiamante attende il completamento di tutte le attività.

  Se l'app si arresta in modo imprevisto, ad esempio, il processo dell'app ha esito negativo, il metodo `StopAsync` potrebbe non essere chiamato. Pertanto è possibile che i metodi chiamati o le operazioni effettuate in `StopAsync` non vengano eseguiti.

  Per estendere il timeout di arresto predefinito di cinque secondi, impostare:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> quando si usa l'host generico. Per altre informazioni, vedere <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Impostazione di configurazione dell'host del timeout di arresto quando si usa l'host Web. Per altre informazioni, vedere <xref:fundamentals/host/web-host#shutdown-timeout>.

Il servizio ospitato viene attivato una volta all'avvio dell'app e arrestato normalmente all'arresto dell'applicazione. Se viene generato un errore durante l'esecuzione dell'attività in background, deve essere chiamato `Dispose` anche se `StopAsync` non viene chiamato.

## <a name="timed-background-tasks"></a>Attività in background programmate

Un'attività programmata in background utilizza la classe [System.Threading.Timer](xref:System.Threading.Timer). Il timer attiva il metodo `DoWork` dell'attività. Il timer viene disabilitato con `StopAsync` ed eliminato quando il contenitore dei servizi è eliminato con `Dispose`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

Il servizio registrato in `Startup.ConfigureServices` con il metodo di estensione `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Utilizzo di un servizio con ambito in un'attività in background

Per usare i [servizi con ambito](xref:fundamentals/dependency-injection#service-lifetimes) all'interno di un `IHostedService`, creare un ambito. Non viene creato automaticamente alcun ambito per un servizio ospitato.

Il servizio dell'attività in background con ambito contiene la logica dell'attività in background. Nell'esempio seguente, <xref:Microsoft.Extensions.Logging.ILogger> viene inserito nel servizio:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Il servizio ospitato crea un ambito per risolvere il servizio dell'attività in background con ambito e chiamare il relativo metodo `DoWork`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

I servizi vengono registrati in `Startup.ConfigureServices`. L'implementazione di `IHostedService`viene registrata con il metodo di estensione `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Attività in background in coda

Una coda delle attività in background è basata su .NET 4. <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> x ([programma provvisoriamente per essere incorporato per ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

In `QueueHostedService`, le attività in background in coda vengono rimosse dalla coda ed eseguite come <xref:Microsoft.Extensions.Hosting.BackgroundService>, ovvero una classe di base per l'implementazione di un `IHostedService` a esecuzione prolungata:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

I servizi vengono registrati in `Startup.ConfigureServices`. L'implementazione di `IHostedService`viene registrata con il metodo di estensione `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

Nella classe modello della pagina di indice:

* `IBackgroundTaskQueue` viene inserito nel costruttore e assegnato a `Queue`.
* Un'interfaccia <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> viene inserita e assegnata a `_serviceScopeFactory`. La factory viene usata per creare istanze di <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, che consente di creare servizi all'interno di un ambito. Viene creato un ambito per usare la classe `AppDbContext` dell'app (un [servizio con ambito](xref:fundamentals/dependency-injection#service-lifetimes)) per scrivere record di database in `IBackgroundTaskQueue` (un servizio singleton).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Quando si seleziona il pulsante **Aggiungi attività** nella pagina di indice, viene eseguito il metodo `OnPostAddTask`. `QueueBackgroundWorkItem`viene chiamato per accodare un elemento di lavoro:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* [Implementare attività in background in microservizi con IHostedService e la classe BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
