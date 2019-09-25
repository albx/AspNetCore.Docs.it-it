---
title: Confrontare i servizi gRPC con le API HTTP
author: jamesnk
description: Informazioni sul confronto tra gRPC e API HTTP e gli scenari consigliati.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/25/2019
uid: grpc/comparison
ms.openlocfilehash: 935078d890998fe6af366e3f6a7bf21f53c20cf7
ms.sourcegitcommit: a7813a776809a5029c94aa503ee71994f156231f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71267723"
---
# <a name="compare-grpc-services-with-http-apis"></a>Confrontare i servizi gRPC con le API HTTP

Di [James Newton-King](https://twitter.com/jamesnk)

Questo articolo illustra il confronto tra i [servizi di gRPC](https://grpc.io/docs/guides/) e le API HTTP (incluse ASP.NET Core [API Web](xref:web-api/index)). La tecnologia usata per fornire un'API per l'app è una scelta importante e gRPC offre vantaggi esclusivi rispetto alle API HTTP. Questo articolo illustra i punti di forza e i punti deboli di gRPC e consiglia scenari per l'uso di gRPC su altre tecnologie.

## <a name="high-level-comparison"></a>Confronto di alto livello

La tabella seguente offre un confronto di alto livello tra le funzionalità tra gRPC e le API HTTP con JSON.

| Funzionalità          | gRPC                                               | API HTTP con JSON           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Contratto         | Obbligatorio ( *. proto*)                                | Facoltativo (OpenAPI)            |
| Trasporto        | HTTP/2                                             | HTTP                          |
| Payload          | [Protobuf (Small, Binary)](#performance)           | JSON (grande, leggibile)  |
| Prescriptiveness | [Specifica Strict](#strict-specification)      | Sciolto. Qualsiasi HTTP è valido.      |
| Flusso        | [Client, server, bidirezionale](#streaming)       | Client, server                |
| Supporto browser  | [No (richiede grpc-Web)](#limited-browser-support) | Yes                           |
| Sicurezza         | Trasporto (HTTPS)                                  | Trasporto (HTTPS)             |
| Generazione di codice client | [Sì](#code-generation)                      | OpenAPI + strumenti di terze parti |

## <a name="grpc-strengths"></a>punti di forza di gRPC

### <a name="performance"></a>Prestazioni

i messaggi gRPC vengono serializzati tramite [protobuf](https://developers.google.com/protocol-buffers/docs/overview), un formato di messaggio binario efficiente. Protobuf serializza molto rapidamente sul server e sul client. La serializzazione protobuf produce payload di messaggi di piccole dimensioni, importanti in scenari con larghezza di banda limitata come le app per dispositivi mobili.

gRPC è progettato per HTTP/2, una revisione principale del protocollo HTTP che offre vantaggi significativi in merito alle prestazioni rispetto a HTTP 1. x:

* Framing e compressione binaria. Il protocollo HTTP/2 è compatto ed efficiente sia per l'invio sia per la ricezione.
* Multiplexing di più chiamate HTTP/2 su una singola connessione TCP. Il multiplexing Elimina il [blocco Head-of-line](https://en.wikipedia.org/wiki/Head-of-line_blocking).

### <a name="code-generation"></a>Generazione del codice

Tutti i Framework gRPC offrono supporto di prima classe per la generazione di codice. Un file principale per lo sviluppo gRPC è il [file *. proto* ](https://developers.google.com/protocol-buffers/docs/proto3), che definisce il contratto di servizi e messaggi di gRPC. Da questo file gRPC Frameworks codice genererà una classe di base del servizio, i messaggi e un client completo.

Condividendo il file con *estensione proto* tra il server e il client, i messaggi e il codice client possono essere generati da end-to-end. La generazione di codice del client elimina la duplicazione dei messaggi nel client e nel server e crea automaticamente un client fortemente tipizzato. Non è necessario scrivere un client per risparmiare tempo di sviluppo significativo nelle applicazioni con molti servizi.

### <a name="strict-specification"></a>Specifica Strict

Una specifica formale per l'API HTTP con JSON non esiste. Gli sviluppatori discutono del formato migliore di URL, verbi HTTP e codici di risposta.

La [specifica gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) è prescrittiva sul formato che deve essere seguito da un servizio gRPC. gRPC Elimina il dibattito e Risparmia tempo per gli sviluppatori perché gPRC è coerente tra piattaforme e implementazioni.

### <a name="streaming"></a>Flusso

HTTP/2 fornisce una base per flussi di comunicazione di lunga durata e in tempo reale. gRPC fornisce il supporto di prima classe per lo streaming tramite HTTP/2.

Un servizio gRPC supporta tutte le combinazioni di streaming:

* Unario (nessun flusso)
* Streaming da server a client
* Streaming da client a server
* Streaming bidirezionale

### <a name="deadlinetimeouts-and-cancellation"></a>Scadenza/timeout e annullamento

gRPC consente ai client di specificare per quanto tempo sono disposti ad attendere il completamento di un RPC. La [scadenza](https://grpc.io/blog/deadlines) viene inviata al server e il server può decidere quale azione intraprendere se supera la scadenza. Ad esempio, il server potrebbe annullare le richieste gRPC/HTTP/database in corso in fase di timeout.

La propagazione della scadenza e dell'annullamento tramite chiamate gRPC figlio consente di applicare limiti di utilizzo delle risorse.

## <a name="grpc-recommended-scenarios"></a>scenari consigliati gRPC

gRPC è particolarmente adatto agli scenari seguenti:

* **Microservizi** &ndash; gRPC è progettato per la comunicazione con bassa latenza e velocità effettiva elevata. gRPC è ideale per i microservizi leggeri in cui l'efficienza è fondamentale.
* **Comunicazione in tempo reale da punto a punto** &ndash; gRPC offre un supporto eccellente per lo streaming bidirezionale. i servizi gRPC possono eseguire il push dei messaggi in tempo reale senza polling.
* **Ambienti poliglotti** &ndash; gli strumenti gRPC supportano tutti i linguaggi di sviluppo più diffusi, rendendo gRPC una scelta ottimale per gli ambienti multilingue.
* **Ambienti vincolati alla rete** &ndash; i messaggi gRPC vengono serializzati con protobuf, un formato di messaggio leggero. Un messaggio gRPC è sempre più piccolo di un messaggio JSON equivalente.

## <a name="grpc-weaknesses"></a>punti deboli gRPC

### <a name="limited-browser-support"></a>Supporto browser limitato

Attualmente non è possibile chiamare direttamente un servizio gRPC da un browser. gRPC USA in modo intensivo le funzionalità HTTP/2 e nessun browser fornisce il livello di controllo necessario per le richieste Web per supportare un client gRPC. I browser, ad esempio, non consentono a un chiamante di richiedere l'utilizzo di HTTP/2 o di fornire l'accesso ai frame HTTP/2 sottostanti.

[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) è una tecnologia aggiuntiva del team gRPC che fornisce un supporto limitato per gRPC nel browser. gRPC-Web è costituito da due parti: un client JavaScript che supporta tutti i browser moderni e un proxy gRPC-Web nel server. Il client gRPC-Web chiama il proxy e il proxy inoltrerà le richieste gRPC al server gRPC.

Non tutte le funzionalità di gRPC sono supportate da gRPC-Web. Il flusso client e bidirezionale non è supportato ed è disponibile un supporto limitato per lo streaming del server.

### <a name="not-human-readable"></a>Non leggibile

Le richieste API HTTP vengono inviate come testo e possono essere lette e create dagli utenti.

per impostazione predefinita, i messaggi gRPC vengono codificati con protobuf. Sebbene protobuf sia efficace per l'invio e la ricezione, il formato binario non è leggibile. Protobuf richiede la descrizione dell'interfaccia del messaggio specificata nel file *. proto* per la deserializzazione corretta. È necessario disporre di strumenti aggiuntivi per analizzare i payload protobuf in transito e comporre le richieste manualmente.

Sono disponibili funzionalità quali [Reflection server](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) e lo [strumento da riga di comando gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) per supportare i messaggi protobuf binari. Inoltre, i messaggi protobuf supportano la [conversione da e verso JSON](https://developers.google.com/protocol-buffers/docs/proto3#json). La conversione JSON incorporata consente di convertire in modo efficiente i messaggi protobuf da e verso il formato leggibile durante il debug.

## <a name="alternative-framework-scenarios"></a>Scenari di Framework alternativi

Gli altri Framework sono consigliati rispetto a gRPC negli scenari seguenti:

* **API accessibili dal browser** &ndash; gRPC non è completamente supportato nel browser. gRPC-Web può offrire il supporto del browser, ma presenta limitazioni e introduce un proxy server.
* **Trasmissione della comunicazione in tempo reale** &ndash; gRPC supporta la comunicazione in tempo reale tramite lo streaming, ma il concetto di trasmissione di un messaggio alle connessioni registrate non esiste. Ad esempio, in uno scenario di chat room in cui i nuovi messaggi di chat devono essere inviati a tutti i client nella chat room, ogni chiamata gRPC è necessaria per trasmettere singolarmente nuovi messaggi di chat al client. [SignalR](xref:signalr/introduction) è un Framework utile per questo scenario. SignalR ha il concetto di connessioni permanenti e il supporto incorporato per la trasmissione di messaggi.
* **Comunicazione tra processi** &ndash; Un processo deve ospitare un server http/2 per accettare le chiamate gRPC in ingresso. Per Windows, le [pipe](/dotnet/standard/io/pipe-operations) di comunicazione tra processi sono un metodo di comunicazione rapido e leggero.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
