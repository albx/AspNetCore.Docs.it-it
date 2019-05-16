---
title: Ospitare ASP.NET Core in una Web farm
author: guardrex
description: Informazioni su come ospitare più istanze di un'app ASP.NET Core con risorse condivise in un ambiente Web farm.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2018
uid: host-and-deploy/web-farm
ms.openlocfilehash: 4873665e6174a6acf885e1ebb41fb005d646bd1f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2019
ms.locfileid: "64884156"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>Ospitare ASP.NET Core in una Web farm

Di [Luke Latham](https://github.com/guardrex) e [Chris Ross](https://github.com/Tratcher)

Una *Web farm* è un gruppo di due o più server Web (o *nodi*) che ospita più istanze di un'app. Quando arrivano richieste dagli utenti per una Web farm, un servizio di *bilanciamento del carico* distribuisce le richieste ai nodi della Web farm. Le Web farm consentono di migliorare:

* **Affidabilità/disponibilità** &ndash; Quando uno o più nodi non funzionano, il servizio di bilanciamento del carico può indirizzare le richieste ad altri nodi funzionanti per continuare l'elaborazione delle richieste.
* **Capacità/prestazioni** &ndash; Più nodi possono elaborare più richieste rispetto a un singolo server. Il servizio di bilanciamento del carico consente di bilanciare il carico di lavoro distribuendo le richieste ai nodi.
* **Scalabilità** &ndash; Quando è necessaria più o meno capacità, il numero di nodi attivi può essere aumentato o ridotto in base al carico di lavoro. Le tecnologie della piattaforma per le Web farm, ad esempio [Servizio app di Azure](https://azure.microsoft.com/services/app-service/), possono aggiungere o rimuovere automaticamente nodi su richiesta dell'amministratore di sistema o automaticamente senza intervento umano.
* **Manutenibilità** &ndash; I nodi di una Web farm possono basarsi su un set di servizi condivisi, con conseguente maggiore facilità di gestione del sistema. Ad esempio, i nodi di una Web farm possono basarsi su un singolo server di database e un percorso di rete comune per le risorse statiche, ad esempio immagini e file scaricabili.

Questo argomento descrive la configurazione e le dipendenze per le app ASP.NET Core ospitate in una Web farm che si basano su risorse condivise.

## <a name="general-configuration"></a>Impostazioni di configurazione generali

<xref:host-and-deploy/index>  
Informazioni sulla configurazione degli ambienti host e sulla distribuzione delle app ASP.NET Core. Configurare un gestore di processi in ogni nodo della Web farm per automatizzare avvii e riavvii dell'app. Ogni nodo richiede il runtime di ASP.NET Core. Per altre informazioni, vedere gli argomenti nell'area [Ospitare e distribuire](xref:host-and-deploy/index) della documentazione.

<xref:host-and-deploy/proxy-load-balancer>  
Informazioni sulla configurazione per le app ospitate dietro server proxy e servizi di bilanciamento del carico, che spesso nascondono informazioni importanti sulle richieste.

<xref:host-and-deploy/azure-apps/index>  
Il [servizio app di Azure](https://azure.microsoft.com/services/app-service/) è un [servizio di piattaforma di cloud computing Microsoft](https://azure.microsoft.com/) per l'hosting di app Web, inclusa ASP.NET Core. Servizio app è una piattaforma completamente gestita che offre scalabilità automatica, bilanciamento del carico, applicazione di patch e distribuzione continua.

## <a name="app-data"></a>Dati dell'app

Quando un'app viene distribuita su più istanze, potrebbe essere necessario condividere informazioni sullo stato dell'app tra i nodi. Se lo stato è temporaneo, valutare la condivisione di una [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache). Se lo stato condiviso deve essere persistente, valutare la possibilità di archiviare lo stato condiviso in un database.

## <a name="required-configuration"></a>Configurazione richiesta

Occorre configurare la protezione dei dati e la memorizzazione nella cache per le app distribuite in una Web farm.

### <a name="data-protection"></a>Protezione dati

Il [sistema di protezione dei dati di ASP.NET Core](xref:security/data-protection/introduction) viene usato dalle app per proteggere i dati. La protezione dei dati si basa su un set di chiavi di crittografia archiviate in un *KeyRing*. Quando il sistema di protezione dei dati viene inizializzato, vengono applicate le [impostazioni predefinite](xref:security/data-protection/configuration/default-settings) che archiviano il KeyRing in locale. In base alla configurazione predefinita, viene archiviato un unico KeyRing in ogni nodo della Web farm. Di conseguenza, ogni nodo della Web farm non può decrittografare i dati crittografati da un'app su qualsiasi altro nodo. La configurazione predefinita non è in genere adatta per l'hosting di app in una Web farm. In alternativa all'implementazione di un KeyRing condiviso, è sempre possibile indirizzare le richieste utente allo stesso nodo. Per altre informazioni sulla configurazione del sistema di protezione dei dati per le distribuzioni di Web farm, vedere <xref:security/data-protection/configuration/overview>.

### <a name="caching"></a>Memorizzazione nella cache

In un ambiente Web farm, il meccanismo di memorizzazione nella cache deve condividere gli elementi memorizzati nella cache tra i nodi della Web farm. La memorizzazione nella cache deve basarsi su una cache Redis comune, un database di SQL Server condiviso o un'implementazione personalizzata di memorizzazione nella cache che condivide gli elementi memorizzati nella cache nella Web farm. Per ulteriori informazioni, vedere <xref:performance/caching/distributed>.

## <a name="dependent-components"></a>Componenti dipendenti

Gli scenari seguenti non richiedono configurazioni aggiuntive, ma dipendono da tecnologie che devono essere configurate per le Web farm.

| Scenario | Dipende da &hellip; |
| -------- | ------------------- |
| Autenticazione | Protezione dei dati (vedere <xref:security/data-protection/configuration/overview>).<br><br>Per altre informazioni, vedere <xref:security/authentication/cookie> e <xref:security/cookie-sharing>. |
| identità | Configurazione di autenticazione e database.<br><br>Per ulteriori informazioni, vedere <xref:security/authentication/identity>. |
| Sessione | Protezione dei dati (cookie crittografati) (vedere <xref:security/data-protection/configuration/overview>) e memorizzazione nella cache (vedere <xref:performance/caching/distributed>).<br><br>Per altre informazioni, vedere [Stato di sessioni e app: Stato sessione](xref:fundamentals/app-state#session-state). |
| TempData | Protezione dei dati (cookie crittografati) (vedere <xref:security/data-protection/configuration/overview>) o sessione (vedere [Stato di sessioni e app: Stato sessione](xref:fundamentals/app-state#session-state)).<br><br>Per altre informazioni, vedere [Stato di sessioni e app: TempData](xref:fundamentals/app-state#tempdata). |
| Antifalsificazione | Protezione dei dati (vedere <xref:security/data-protection/configuration/overview>).<br><br>Per ulteriori informazioni, vedere <xref:security/anti-request-forgery>. |

## <a name="troubleshoot"></a>Risolvere i problemi

### <a name="data-protection-and-caching"></a>Protezione dei dati e memorizzazione nella cache

Quando la protezione dei dati o la memorizzazione nella cache non è configurata per un ambiente Web farm, si verificano errori intermittenti durante l'elaborazione delle richieste. Ciò si verifica perché i nodi non condividono le stesse risorse e le richieste degli utenti non vengono sempre indirizzate allo stesso nodo.

Si consideri, ad esempio, un utente che accede all'app usando l'autenticazione basata su cookie. L'utente acceda all'app in un nodo della Web farm. Se la richiesta successiva dell'utente arriva nello stesso nodo in cui ha eseguito l'accesso, l'app è in grado di decrittografare il cookie di autenticazione e consente l'accesso alla risorsa dell'app. Se la richiesta successiva arriva a un nodo diverso, l'app non può decrittografare il cookie di autenticazione dal nodo in cui l'utente ha eseguito l'accesso e l'autorizzazione per la risorsa richiesta ha esito negativo.

Quando si verifica uno dei sintomi seguenti **in modo intermittente**, il problema è in genere da ricondurre a una configurazione non corretta della protezione dei dati o della memorizzazione nella cache per un ambiente Web farm:

* Problema di autenticazione &ndash; Il cookie di autenticazione non è configurato correttamente o non può essere decrittografato. Gli accessi OAuth (Facebook, Microsoft, Twitter) o OpenIdConnect non riescono con l'errore "Correlazione non riuscita."
* Problema di autenticazione &ndash; Identità persa.
* Perdita dei dati per lo stato della sessione.
* Gli elementi memorizzati nella cache scompaiono.
* Errore di TempData.
* Errori POST &ndash; Il controllo antifalsificazione non riesce.

Per altre informazioni sulla configurazione della protezione dei dati per le distribuzioni di Web farm, vedere <xref:security/data-protection/configuration/overview>. Per altre informazioni sulla configurazione della memorizzazione nella cache per le distribuzioni di Web farm, vedere <xref:performance/caching/distributed>.

## <a name="obtain-data-from-apps"></a>Ottenere dati dalle app

Se le app della Web farm sono in grado di rispondere alle richieste, è possibile ottenere dati sulle richieste, le connessioni e altri dati dalle app tramite middleware inline di terminale. Per altre informazioni e codice di esempio, vedere <xref:test/troubleshoot#obtain-data-from-an-app>.
