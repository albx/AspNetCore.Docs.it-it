---
title: Dettagli di crittografia autenticata in ASP.NET Core
author: rick-anderson
description: Scopri i dettagli di implementazione della crittografia autenticata la protezione dei dati di ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 9def03e6b27e19fc34a839e923d6152e086889db
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896628"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="bf817-103">Dettagli di crittografia autenticata in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf817-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="bf817-104">Le chiamate a IDataProtector.Protect sono operazioni di crittografia autenticata.</span><span class="sxs-lookup"><span data-stu-id="bf817-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="bf817-105">Il metodo Protect offre sia la riservatezza e l'autenticità e quest'ultima è associata alla catena scopo che è stata utilizzata per derivare questa particolare istanza di oggetto IDataProtector dalla radice IDataProtectionProvider.</span><span class="sxs-lookup"><span data-stu-id="bf817-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="bf817-106">IDataProtector.Protect accetta un parametro di testo normale byte [] e produce un payload protetto [] byte, il cui formato è descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="bf817-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="bf817-107">(È inoltre disponibile un overload del metodo di estensione che accetta un parametro di stringa in testo normale e restituisce un payload protetto di stringa.</span><span class="sxs-lookup"><span data-stu-id="bf817-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="bf817-108">Se questa API viene usata il formato di payload protetti avranno comunque la seguente struttura, ma sarà [con codifica base64url](https://tools.ietf.org/html/rfc4648#section-5).)</span><span class="sxs-lookup"><span data-stu-id="bf817-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="bf817-109">Formato di payload protetti</span><span class="sxs-lookup"><span data-stu-id="bf817-109">Protected payload format</span></span>

<span data-ttu-id="bf817-110">Il formato di payload protetti è costituito da tre componenti principali:</span><span class="sxs-lookup"><span data-stu-id="bf817-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="bf817-111">Un'intestazione magic a 32 bit che identifica la versione del sistema di protezione dati.</span><span class="sxs-lookup"><span data-stu-id="bf817-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="bf817-112">Id chiave a 128 bit che identifica la chiave utilizzata per proteggere questo payload specifico.</span><span class="sxs-lookup"><span data-stu-id="bf817-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="bf817-113">È il resto del payload protetto [specifiche per il componente di crittografia incapsulata da questa chiave](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="bf817-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="bf817-114">Nell'esempio seguente, la chiave rappresenta un AES-256-CBC + encryptor HMACSHA256, e il payload viene ulteriormente suddiviso come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="bf817-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="bf817-115">Modificatore di un chiave a 128 bit.</span><span class="sxs-lookup"><span data-stu-id="bf817-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="bf817-116">Un vettore di inizializzazione di 128 bit.</span><span class="sxs-lookup"><span data-stu-id="bf817-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="bf817-117">48 byte di output di AES-256-CBC.</span><span class="sxs-lookup"><span data-stu-id="bf817-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="bf817-118">Un tag di autenticazione HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="bf817-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="bf817-119">Di seguito è illustrato un esempio di payload protetti.</span><span class="sxs-lookup"><span data-stu-id="bf817-119">A sample protected payload is illustrated below.</span></span>

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

<span data-ttu-id="bf817-120">Dal formato di payload sopra i primi 32 bit o 4 byte rappresentano l'intestazione magic che identifica la versione (09 F0 C9 F0)</span><span class="sxs-lookup"><span data-stu-id="bf817-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="bf817-121">I successivi 128 bit o 16 byte è l'identificatore di chiave (80 9 81 C 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span><span class="sxs-lookup"><span data-stu-id="bf817-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="bf817-122">Il resto contiene il payload e specifico del formato utilizzato.</span><span class="sxs-lookup"><span data-stu-id="bf817-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="bf817-123">Tutti i payload protetti per una determinata chiave inizia con la stessa intestazione di 20 byte (valore magic, id chiave).</span><span class="sxs-lookup"><span data-stu-id="bf817-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="bf817-124">Gli amministratori possono utilizzare questo fatto per scopi diagnostici per approssimare quando è stato generato un payload.</span><span class="sxs-lookup"><span data-stu-id="bf817-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="bf817-125">Ad esempio, il payload precedente corrisponde alla chiave {0c819c80-6619-4019-9536-53f8aaffee57}.</span><span class="sxs-lookup"><span data-stu-id="bf817-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="bf817-126">Se dopo aver controllato l'archivio chiave che la data di attivazione della chiave specifico era 2015-01-01 e relativa data di scadenza cadeva 2015-03-01, quindi a diminuire è ragionevole presupporre che il payload (se non manomessi) è stato generato in tale intervallo, assegnare o eseguire un piccolo fattore di fudge su entrambi i lati.</span><span class="sxs-lookup"><span data-stu-id="bf817-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
