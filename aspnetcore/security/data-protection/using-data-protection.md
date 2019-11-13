---
title: Iniziare a usare le API di protezione dei dati in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare le API di protezione dei dati ASP.NET Core per la protezione e la rimozione della protezione dei dati in un'app.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 8c3f3c7fb21434cf335591c41741f0ce868df33e
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963869"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="c5d8c-103">Iniziare a usare le API di protezione dei dati in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5d8c-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="c5d8c-104">Al più semplice, la protezione dei dati è costituita dai passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="c5d8c-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="c5d8c-105">Creare una protezione dati da un provider di protezione dati.</span><span class="sxs-lookup"><span data-stu-id="c5d8c-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="c5d8c-106">Chiamare il metodo `Protect` con i dati che si desidera proteggere.</span><span class="sxs-lookup"><span data-stu-id="c5d8c-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="c5d8c-107">Chiamare il metodo `Unprotect` con i dati di cui si vuole tornare in testo normale.</span><span class="sxs-lookup"><span data-stu-id="c5d8c-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="c5d8c-108">La maggior parte dei Framework e dei modelli di app, ad esempio ASP.NET Core o SignalR, configura già il sistema di protezione dei dati e lo aggiunge a un contenitore di servizi a cui si accede tramite l'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="c5d8c-108">Most frameworks and app models, such as ASP.NET Core or SignalR, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="c5d8c-109">Nell'esempio seguente viene illustrata la configurazione di un contenitore di servizi per l'inserimento di dipendenze e la registrazione dello stack DI protezione dei dati, la ricezione del provider DI protezione dati tramite DI, la creazione di un programma di protezione e la protezione dei dati.</span><span class="sxs-lookup"><span data-stu-id="c5d8c-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="c5d8c-110">Quando si crea una protezione, è necessario specificare una o più [stringhe per finalità](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="c5d8c-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="c5d8c-111">Una stringa di scopo fornisce l'isolamento tra i consumer.</span><span class="sxs-lookup"><span data-stu-id="c5d8c-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="c5d8c-112">Ad esempio, una protezione creata con una stringa di scopo "verde" non sarà in grado di rimuovere la protezione dei dati forniti da un programma di protezione con scopo "viola".</span><span class="sxs-lookup"><span data-stu-id="c5d8c-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="c5d8c-113">Le istanze di `IDataProtectionProvider` e `IDataProtector` sono thread-safe per più chiamanti.</span><span class="sxs-lookup"><span data-stu-id="c5d8c-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="c5d8c-114">Quando un componente ottiene un riferimento a un `IDataProtector` tramite una chiamata a `CreateProtector`, utilizzerà tale riferimento per più chiamate a `Protect` e `Unprotect`.</span><span class="sxs-lookup"><span data-stu-id="c5d8c-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="c5d8c-115">Una chiamata a `Unprotect` genererà CryptographicException se non è possibile verificare o decifrare il payload protetto.</span><span class="sxs-lookup"><span data-stu-id="c5d8c-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="c5d8c-116">Alcuni componenti potrebbero voler ignorare gli errori durante le operazioni di rimozione della protezione. un componente che legge i cookie di autenticazione potrebbe gestire questo errore e trattare la richiesta come se non fosse disponibile alcun cookie anziché interrompere la richiesta in modo non corretto.</span><span class="sxs-lookup"><span data-stu-id="c5d8c-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="c5d8c-117">I componenti che vogliono questo comportamento devono rilevare in modo specifico CryptographicException anziché ingoiare tutte le eccezioni.</span><span class="sxs-lookup"><span data-stu-id="c5d8c-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
