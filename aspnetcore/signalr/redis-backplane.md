---
title: Backplane Redis per ASP.NET Core SignalR con scalabilità orizzontale
author: bradygaster
description: Informazioni su come configurare un backplane Redis per abilitare la scalabilità orizzontale per un'app ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 0461fc6a212ba78111bc2054cca74951721c5820
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289030"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-opno-locsignalr-scale-out"></a>Configurare un backplane Redis per ASP.NET Core SignalR con scalabilità orizzontale

Di [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)e [Tom Dykstra](https://github.com/tdykstra),

Questo articolo SignalRillustra gli aspetti specifici della configurazione di un server [Redis](https://redis.io/) da usare per la scalabilità orizzontale di un'app ASP.NET Core SignalR.

## <a name="set-up-a-redis-backplane"></a>Configurare un backplane Redis

* Distribuire un server Redis.

  > [!IMPORTANT] 
  > Per l'uso in produzione, è consigliabile un backplane Redis solo quando viene eseguito nella stessa data center dell'app SignalR. In caso contrario, la latenza di rete comporta un peggioramento delle prestazioni. Se l'app SignalR è in esecuzione nel cloud di Azure, si consiglia il servizio Azure SignalR invece di un backplane Redis. È possibile usare il servizio cache Redis di Azure per gli ambienti di sviluppo e test.

  Per altre informazioni, vedere le seguenti risorse:

  * <xref:signalr/scale>
  * [Documentazione di redis](https://redis.io/)
  * [Documentazione di cache Redis di Azure](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* Nell'app SignalR installare il pacchetto NuGet `Microsoft.AspNetCore.SignalR.Redis`.
* Nel metodo `Startup.ConfigureServices` chiamare `AddRedis` dopo `AddSignalR`:

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* Configurare le opzioni in base alle esigenze:
 
  La maggior parte delle opzioni può essere impostata nella stringa di connessione o nell'oggetto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Le opzioni specificate in `ConfigurationOptions` eseguono l'override di quelle impostate nella stringa di connessione.

  Nell'esempio seguente viene illustrato come impostare le opzioni nell'oggetto `ConfigurationOptions`. Questo esempio aggiunge un prefisso del canale in modo che più app possano condividere la stessa istanza di redis, come illustrato nel passaggio seguente.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  Nel codice precedente, `options.Configuration` viene inizializzato con tutto ciò che è stato specificato nella stringa di connessione.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Nell'app SignalR installare uno dei pacchetti NuGet seguenti:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`-dipende da StackExchange. Redis 2. X.X. Questo è il pacchetto consigliato per ASP.NET Core 2,2 e versioni successive.
  * `Microsoft.AspNetCore.SignalR.Redis`-dipende da StackExchange. Redis 1. X.X. Questo pacchetto non è incluso in ASP.NET Core 3,0 e versioni successive.

* Nel metodo `Startup.ConfigureServices` chiamare <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 Quando si utilizza `Microsoft.AspNetCore.SignalR.Redis`, chiamare <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.

* Configurare le opzioni in base alle esigenze:
 
  La maggior parte delle opzioni può essere impostata nella stringa di connessione o nell'oggetto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Le opzioni specificate in `ConfigurationOptions` eseguono l'override di quelle impostate nella stringa di connessione.

  Nell'esempio seguente viene illustrato come impostare le opzioni nell'oggetto `ConfigurationOptions`. Questo esempio aggiunge un prefisso del canale in modo che più app possano condividere la stessa istanza di redis, come illustrato nel passaggio seguente.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 Quando si utilizza `Microsoft.AspNetCore.SignalR.Redis`, chiamare <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.

  Nel codice precedente, `options.Configuration` viene inizializzato con tutto ciò che è stato specificato nella stringa di connessione.

  Per informazioni sulle opzioni di redis, vedere la [documentazione su Redis di stackexchange](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* Nell'app SignalR installare il pacchetto NuGet seguente:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* Nel metodo `Startup.ConfigureServices` chiamare <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* Configurare le opzioni in base alle esigenze:
 
  La maggior parte delle opzioni può essere impostata nella stringa di connessione o nell'oggetto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) . Le opzioni specificate in `ConfigurationOptions` eseguono l'override di quelle impostate nella stringa di connessione.

  Nell'esempio seguente viene illustrato come impostare le opzioni nell'oggetto `ConfigurationOptions`. Questo esempio aggiunge un prefisso del canale in modo che più app possano condividere la stessa istanza di redis, come illustrato nel passaggio seguente.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  Nel codice precedente, `options.Configuration` viene inizializzato con tutto ciò che è stato specificato nella stringa di connessione.

  Per informazioni sulle opzioni di redis, vedere la [documentazione su Redis di stackexchange](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

* Se si usa un server Redis per più app SignalR, usare un prefisso di canale diverso per ogni app SignalR.

  L'impostazione di un prefisso del canale isola una SignalR app da altre che usano prefissi di canale diversi. Se non si assegnano prefissi diversi, un messaggio inviato da un'app a tutti i relativi client passerà a tutti i client di tutte le app che usano il server Redis come backplane.

* Configurare il software di bilanciamento del carico server farm per le sessioni permanenti. Di seguito sono riportati alcuni esempi di documentazione su come eseguire questa operazione:

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Errori del server Redis

Quando un server Redis diventa inattivo, SignalR genera eccezioni che indicano che i messaggi non vengono recapitati. Alcuni messaggi di eccezione tipici:

* *Scrittura del messaggio non riuscita*
* *Non è stato possibile richiamare il metodo Hub ' MethodName '*
* *Connessione a Redis non riuscita*

SignalR non memorizza nel buffer i messaggi per inviarli quando viene eseguito il backup del server. Tutti i messaggi inviati durante il server Redis vengono persi.

SignalR si riconnette automaticamente quando il server Redis è nuovamente disponibile.

### <a name="custom-behavior-for-connection-failures"></a>Comportamento personalizzato per gli errori di connessione

Ecco un esempio che illustra come gestire gli eventi di errore di connessione Redis.

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a>Clustering di redis

Il [clustering di redis](https://redis.io/topics/cluster-spec) è un metodo per ottenere la disponibilità elevata usando più server Redis. Il clustering non è ufficialmente supportato, ma potrebbe funzionare.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* <xref:signalr/scale>
* [Documentazione di redis](https://redis.io/documentation)
* [Documentazione di StackExchange Redis](https://stackexchange.github.io/StackExchange.Redis/)
* [Documentazione di cache Redis di Azure](https://docs.microsoft.com/azure/redis-cache/)
