---
title: Formato di archiviazione chiavi in ASP.NET Core
author: rick-anderson
description: Scopri i dettagli di implementazione del formato di archiviazione delle chiavi di protezione dei dati di ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 81df124f3dd0cadf8fd895ab55f66eec6415705f
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208018"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="41fed-103">Formato di archiviazione chiavi in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="41fed-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="41fed-104">Gli oggetti sono archiviati inattivi nella rappresentazione XML.</span><span class="sxs-lookup"><span data-stu-id="41fed-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="41fed-105">La directory predefinita per l'archiviazione delle chiavi è % LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span><span class="sxs-lookup"><span data-stu-id="41fed-105">The default directory for key storage is %LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span></span>

## <a name="the-key-element"></a><span data-ttu-id="41fed-106">Il \<chiave > elemento</span><span class="sxs-lookup"><span data-stu-id="41fed-106">The \<key> element</span></span>

<span data-ttu-id="41fed-107">Le chiavi esistono come oggetti di primo livello nel repository di chiave.</span><span class="sxs-lookup"><span data-stu-id="41fed-107">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="41fed-108">Per convenzione, le chiavi hanno il nome del file **key dal {guid}. XML**, dove {guid} è l'id della chiave.</span><span class="sxs-lookup"><span data-stu-id="41fed-108">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="41fed-109">Ciascuno di questi file contiene una singola chiave.</span><span class="sxs-lookup"><span data-stu-id="41fed-109">Each such file contains a single key.</span></span> <span data-ttu-id="41fed-110">Il formato del file è come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="41fed-110">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="41fed-111">Il \<chiave > elemento contiene elementi figlio e gli attributi seguenti:</span><span class="sxs-lookup"><span data-stu-id="41fed-111">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="41fed-112">Id della chiave. Questo valore viene trattato come autorevoli; il nome del file è semplicemente un nicety leggibile.</span><span class="sxs-lookup"><span data-stu-id="41fed-112">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="41fed-113">La versione del \<chiave > elemento, attualmente è fissato a 1.</span><span class="sxs-lookup"><span data-stu-id="41fed-113">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="41fed-114">Date di creazione, l'attivazione e la scadenza della chiave.</span><span class="sxs-lookup"><span data-stu-id="41fed-114">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="41fed-115">Oggetto \<descrittore > elemento che contiene le informazioni in base all'implementazione di crittografia autenticata contenuta all'interno di questa chiave.</span><span class="sxs-lookup"><span data-stu-id="41fed-115">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="41fed-116">Nell'esempio precedente, l'id della chiave è {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, è stato creato e attivato 19 marzo 2015, e ha una durata di 90 giorni.</span><span class="sxs-lookup"><span data-stu-id="41fed-116">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="41fed-117">(In alcuni casi la data di attivazione può essere leggermente prima della data di creazione come in questo esempio.</span><span class="sxs-lookup"><span data-stu-id="41fed-117">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="41fed-118">Ciò è dovuto un nit in modalità di lavoro e non provoca problemi nella pratica.)</span><span class="sxs-lookup"><span data-stu-id="41fed-118">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="41fed-119">Il \<descrittore > elemento</span><span class="sxs-lookup"><span data-stu-id="41fed-119">The \<descriptor> element</span></span>

<span data-ttu-id="41fed-120">L'outer \<descrittore > elemento contiene un deserializerType attributo, ovvero il nome qualificato dall'assembly di un tipo che implementa IAuthenticatedEncryptorDescriptorDeserializer.</span><span class="sxs-lookup"><span data-stu-id="41fed-120">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="41fed-121">Questo tipo è responsabile della lettura interna \<descrittore > elemento e analizzare le informazioni contenute all'interno.</span><span class="sxs-lookup"><span data-stu-id="41fed-121">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="41fed-122">Il formato particolare del \<descrittore > elemento dipende dall'implementazione del componente di crittografia autenticata incapsulato dalla chiave e ogni tipo deserializzatore prevede un formato leggermente diverso per questo oggetto.</span><span class="sxs-lookup"><span data-stu-id="41fed-122">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="41fed-123">In generale, tuttavia, questo elemento conterrà informazioni algoritmiche (i nomi di tipi, OID, o simile) e il materiale della chiave privato.</span><span class="sxs-lookup"><span data-stu-id="41fed-123">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="41fed-124">Nell'esempio precedente, il descrittore specifica che questa chiave esegue il wrapping di crittografia AES-256-CBC + convalida HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="41fed-124">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="41fed-125">Il \<encryptedSecret > elemento</span><span class="sxs-lookup"><span data-stu-id="41fed-125">The \<encryptedSecret> element</span></span>

<span data-ttu-id="41fed-126">Un' **&lt;encryptedSecret&gt;** elemento che contiene la forma crittografata del materiale della chiave privata possono essere presente se [è abilitata la crittografia dei segreti a riposo](xref:security/data-protection/implementation/key-encryption-at-rest).</span><span class="sxs-lookup"><span data-stu-id="41fed-126">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="41fed-127">L'attributo `decryptorType` è il nome qualificato dall'assembly di un tipo che implementa [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span><span class="sxs-lookup"><span data-stu-id="41fed-127">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="41fed-128">Questo tipo è responsabile della lettura interna **&lt;encryptedKey&gt;** elemento e ne esegue la decrittografia per recuperare il testo non crittografato originale.</span><span class="sxs-lookup"><span data-stu-id="41fed-128">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="41fed-129">Come per gli `<descriptor>`, il formato particolare del `<encryptedSecret>` elemento dipende dal meccanismo di crittografia dei dati inattivi in uso.</span><span class="sxs-lookup"><span data-stu-id="41fed-129">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="41fed-130">Nell'esempio precedente, la chiave master viene crittografata usando DPAPI di Windows per ogni commento.</span><span class="sxs-lookup"><span data-stu-id="41fed-130">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="41fed-131">Il \<revoca > elemento</span><span class="sxs-lookup"><span data-stu-id="41fed-131">The \<revocation> element</span></span>

<span data-ttu-id="41fed-132">Revoca esista come oggetti di primo livello nel repository di chiave.</span><span class="sxs-lookup"><span data-stu-id="41fed-132">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="41fed-133">Per convenzione revoche hanno il nome del file **revoca-{timestamp}. XML** (per la revoca tutte le chiavi entro una determinata data) o **revoca-{guid}. XML** (per la revoca di una chiave specifica).</span><span class="sxs-lookup"><span data-stu-id="41fed-133">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="41fed-134">Ogni file contiene un singolo \<revoca > elemento.</span><span class="sxs-lookup"><span data-stu-id="41fed-134">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="41fed-135">Per la revoca delle chiavi singole, il contenuto del file saranno come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="41fed-135">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="41fed-136">In questo caso, viene revocata solo la chiave specificata.</span><span class="sxs-lookup"><span data-stu-id="41fed-136">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="41fed-137">Se l'id chiave è "\*", tuttavia, come nell'esempio seguente vengono revocate tutte le chiavi la cui data di creazione viene prima della data di revoche di certificati specificato.</span><span class="sxs-lookup"><span data-stu-id="41fed-137">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="41fed-138">Il \<motivo > elemento non viene mai letto dal sistema.</span><span class="sxs-lookup"><span data-stu-id="41fed-138">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="41fed-139">È semplicemente un modo pratico per archiviare un motivo leggibile dall'utente per la revoca.</span><span class="sxs-lookup"><span data-stu-id="41fed-139">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
