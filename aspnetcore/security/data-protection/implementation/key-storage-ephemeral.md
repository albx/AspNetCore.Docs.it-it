---
title: Provider di protezione dati temporanee in ASP.NET Core
author: rick-anderson
description: Scopri i dettagli di implementazione dei provider di protezione dati temporanei di ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: e4b0014ab3bdbf90b91383e8a33102f94faa8153
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895178"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="18749-103">Provider di protezione dati temporanee in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18749-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="18749-104">Esistono scenari in cui un'applicazione richiede un buttare via `IDataProtectionProvider`.</span><span class="sxs-lookup"><span data-stu-id="18749-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="18749-105">Ad esempio, lo sviluppatore può solo in fase di sperimentazione in un'applicazione console non standard, o l'applicazione stessa è temporaneo (viene inserito nello script o un progetto unit test).</span><span class="sxs-lookup"><span data-stu-id="18749-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="18749-106">Per supportare questi scenari la [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) pacchetto include un tipo `EphemeralDataProtectionProvider`.</span><span class="sxs-lookup"><span data-stu-id="18749-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="18749-107">Questo tipo fornisce un'implementazione di base di `IDataProtectionProvider` la cui chiave repository viene mantenuto attivo esclusivamente in memoria e non viene scritto nell'archivio di backup.</span><span class="sxs-lookup"><span data-stu-id="18749-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="18749-108">Ogni istanza di `EphemeralDataProtectionProvider` Usa la propria chiave master univoca.</span><span class="sxs-lookup"><span data-stu-id="18749-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="18749-109">Pertanto, se un' `IDataProtector` nodo radice è un `EphemeralDataProtectionProvider` genera un payload protetto, quel payload possono solo essere non protetto da un equivalente `IDataProtector` (quello specificato è uguale [scopo](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) catena) rooted allo stesso `EphemeralDataProtectionProvider` istanza.</span><span class="sxs-lookup"><span data-stu-id="18749-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="18749-110">L'esempio seguente viene illustrato come creare un'istanza di un `EphemeralDataProtectionProvider` e usandolo per proteggere e rimuovere la protezione dei dati.</span><span class="sxs-lookup"><span data-stu-id="18749-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
