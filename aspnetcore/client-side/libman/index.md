---
title: Acquisizione di una libreria lato client in ASP.NET Core con LibMan
author: scottaddie
description: Informazioni su come installare asset di una libreria lato client in un progetto ASP.NET Core tramite Gestione librerie (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: a6ff0cc3342cfac74739387aa17046ed5050232f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2019
ms.locfileid: "64882206"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>Acquisizione di una libreria lato client in ASP.NET Core con LibMan

Di [Scott Addie](https://twitter.com/Scott_Addie)

Gestione librerie (LibMan) è uno strumento per l'acquisizione di librerie lato client leggere. LibMan scarica librerie e framework popolari dal file system o da una [rete per la distribuzione di contenuti (CDN)](https://wikipedia.org/wiki/Content_delivery_network). Le reti CDN supportate includono [CDNJS](https://cdnjs.com/) e [unpkg](https://unpkg.com/#/). I file della libreria selezionata vengono recuperati e inseriti nella posizione appropriata all'interno del progetto ASP.NET Core.

## <a name="libman-use-cases"></a>Casi d'uso di LibMan

LibMan offre i vantaggi seguenti:

* Vengono scaricati solo i file di libreria necessari.
* Non sono necessari strumenti aggiuntivi, come [Node.js](https://nodejs.org), [npm](https://www.npmjs.com) e [WebPack](https://webpack.js.org) per acquisire un subset di file in una libreria.
* I file possono essere collocati in una posizione specifica senza dovere usare alcuna attività di compilazione o ricorrere alla copia dei file manuale.

Per altre informazioni sui vantaggi di LibMan, guardare [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s) (Sviluppo di Web front-end moderni con Visual Studio 2017: segmento LibMan).

LibMan non è un sistema di gestione pacchetti. Se si usa già una gestione pacchetti, ad esempio npm oppure [yarn](https://yarnpkg.com), continuare a farlo. LibMan non è stato sviluppato per sostituire questi strumenti.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [Repository GitHub di LibMan](https://github.com/aspnet/LibraryManager)
