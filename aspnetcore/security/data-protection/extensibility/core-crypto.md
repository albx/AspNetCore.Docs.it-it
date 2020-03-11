---
title: Estendibilità della crittografia di base in ASP.NET Core
author: rick-anderson
description: Informazioni su IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer e sulla factory di primo livello.
ms.author: riande
ms.date: 08/11/2017
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: a5f651e3313cc579b995b45905826a5bffcc241c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663569"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="1b9ec-103">Estendibilità della crittografia di base in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b9ec-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="1b9ec-104">I tipi che implementano una delle interfacce seguenti devono essere thread-safe per i chiamanti di più.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="1b9ec-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="1b9ec-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="1b9ec-106">L'interfaccia **IAuthenticatedEncryptor** è il blocco predefinito di base del sottosistema crittografico.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="1b9ec-107">Esiste in genere un IAuthenticatedEncryptor per ogni chiave e l'istanza di IAuthenticatedEncryptor esegue il wrapping di tutte le informazioni sul materiale della chiave crittografica e sugli algoritmi necessari per eseguire operazioni di crittografia.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="1b9ec-108">Come suggerisce il nome, il tipo è responsabile di fornire servizi di crittografia e decrittografia autenticati.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="1b9ec-109">Espone le due API seguenti.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="1b9ec-110">Il metodo Encrypt restituisce un BLOB che include il testo non crittografato crittografato e un tag di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="1b9ec-111">Il tag di autenticazione deve includere i dati autenticati aggiuntivi (AAD), anche se non è necessario che AAD sia reversibile dal payload finale.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="1b9ec-112">Il metodo Decrypt convalida il tag di autenticazione e restituisce il payload decifrato.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="1b9ec-113">Tutti gli errori (ad eccezione di ArgumentNullException e simili) devono essere omogeneizzati a CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="1b9ec-114">L'istanza di IAuthenticatedEncryptor non deve contenere effettivamente il materiale della chiave.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="1b9ec-115">Ad esempio, l'implementazione può delegare a un modulo di protezione hardware per tutte le operazioni.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="1b9ec-116">Come creare un IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="1b9ec-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="1b9ec-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1b9ec-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1b9ec-118">L'interfaccia **IAuthenticatedEncryptorFactory** rappresenta un tipo che sa come creare un'istanza di [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) .</span><span class="sxs-lookup"><span data-stu-id="1b9ec-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="1b9ec-119">La relativa API è la seguente.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-119">Its API is as follows.</span></span>

* <span data-ttu-id="1b9ec-120">CreateEncryptorInstance (chiave IKey): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="1b9ec-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="1b9ec-121">Per ogni istanza di IKey specificata, tutti i crittografi autenticati creati dal relativo metodo CreateEncryptorInstance devono essere considerati equivalenti, come nell'esempio di codice riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="1b9ec-122">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1b9ec-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1b9ec-123">L'interfaccia **IAuthenticatedEncryptorDescriptor** rappresenta un tipo che sa come creare un'istanza di [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) .</span><span class="sxs-lookup"><span data-stu-id="1b9ec-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="1b9ec-124">La relativa API è la seguente.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-124">Its API is as follows.</span></span>

* <span data-ttu-id="1b9ec-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="1b9ec-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="1b9ec-126">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="1b9ec-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="1b9ec-127">Analogamente a IAuthenticatedEncryptor, si presuppone che un'istanza di IAuthenticatedEncryptorDescriptor esegua il wrapping di una chiave specifica.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="1b9ec-128">Ciò significa che per ogni istanza di IAuthenticatedEncryptorDescriptor specificata, tutti i crittografi autenticati creati dal relativo metodo CreateEncryptorInstance devono essere considerati equivalenti, come nell'esempio di codice riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="1b9ec-129">IAuthenticatedEncryptorDescriptor (solo ASP.NET Core 2. x)</span><span class="sxs-lookup"><span data-stu-id="1b9ec-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="1b9ec-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1b9ec-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1b9ec-131">L'interfaccia **IAuthenticatedEncryptorDescriptor** rappresenta un tipo che sa come esportarsi in XML.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="1b9ec-132">La relativa API è la seguente.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-132">Its API is as follows.</span></span>

* <span data-ttu-id="1b9ec-133">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="1b9ec-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="1b9ec-134">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1b9ec-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="1b9ec-135">Serializzazione XML</span><span class="sxs-lookup"><span data-stu-id="1b9ec-135">XML Serialization</span></span>

<span data-ttu-id="1b9ec-136">La differenza principale tra IAuthenticatedEncryptor e IAuthenticatedEncryptorDescriptor è che il descrittore sa come creare il codificatore e fornire gli argomenti validi.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="1b9ec-137">Si consideri un IAuthenticatedEncryptor la cui implementazione si basa su SymmetricAlgorithm e KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="1b9ec-138">Il processo di crittografia prevede l'utilizzo di questi tipi, ma non necessariamente sa da dove provengono questi tipi, quindi non può scrivere una descrizione corretta di come ricrearsi se l'applicazione viene riavviata.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="1b9ec-139">Il descrittore funge da livello superiore.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="1b9ec-140">Poiché il descrittore sa come creare l'istanza di crittografia (ad esempio, sa come creare gli algoritmi necessari), può serializzare tali informazioni in formato XML in modo che l'istanza di crittografia possa essere ricreata dopo la reimpostazione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="1b9ec-141">Il descrittore può essere serializzato tramite la routine ExportToXml.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="1b9ec-142">Questa routine restituisce un XmlSerializedDescriptorInfo che contiene due proprietà: la rappresentazione XElement del descrittore e il tipo che rappresenta un [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) che può essere utilizzato per riportare questo descrittore in base all'oggetto XElement corrispondente.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="1b9ec-143">Il descrittore serializzato può contenere informazioni riservate, ad esempio il materiale della chiave crittografica.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="1b9ec-144">Il sistema di protezione dei dati offre supporto incorporato per la crittografia delle informazioni prima che questo venga salvato in modo permanente nell'archiviazione.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="1b9ec-145">Per trarre vantaggio da questo, il descrittore deve contrassegnare l'elemento che contiene informazioni riservate con il nome di attributo "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), il valore "true".</span><span class="sxs-lookup"><span data-stu-id="1b9ec-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="1b9ec-146">È disponibile un'API helper per l'impostazione di questo attributo.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="1b9ec-147">Chiamare il metodo di estensione XElement. MarkAsRequiresEncryption () che si trova nello spazio dei nomi Microsoft. AspNetCore. dataprotection. AuthenticatedEncryption. distribuita.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="1b9ec-148">Possono inoltre essere presenti casi in cui il descrittore serializzato non contiene informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="1b9ec-149">Considerare di nuovo il caso di una chiave crittografica archiviata in un modulo di protezione hardware.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="1b9ec-150">Il descrittore non può scrivere il materiale della chiave durante la serializzazione, perché il modulo di protezione hardware non espone il materiale in formato testo normale.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="1b9ec-151">Al contrario, il descrittore potrebbe scrivere la versione della chiave di cui è stato eseguito il wrapper (se il modulo HSM consente l'esportazione in questo modo) o l'identificatore univoco per la chiave del modulo di protezione hardware.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="1b9ec-152">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="1b9ec-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="1b9ec-153">L'interfaccia **IAuthenticatedEncryptorDescriptorDeserializer** rappresenta un tipo che sa come deserializzare un'istanza di IAuthenticatedEncryptorDescriptor da un XElement.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="1b9ec-154">Espone un solo metodo:</span><span class="sxs-lookup"><span data-stu-id="1b9ec-154">It exposes a single method:</span></span>

* <span data-ttu-id="1b9ec-155">ImportFromXml (elemento XElement): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="1b9ec-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="1b9ec-156">Il metodo ImportFromXml accetta il XElement restituito da [IAuthenticatedEncryptorDescriptor. ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) e crea un equivalente del IAuthenticatedEncryptorDescriptor originale.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="1b9ec-157">I tipi che implementano IAuthenticatedEncryptorDescriptorDeserializer devono avere uno dei due costruttori pubblici seguenti:</span><span class="sxs-lookup"><span data-stu-id="1b9ec-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="1b9ec-158">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="1b9ec-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="1b9ec-159">ctor)</span><span class="sxs-lookup"><span data-stu-id="1b9ec-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="1b9ec-160">L'oggetto IServiceProvider passato al costruttore può essere null.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="1b9ec-161">Factory di primo livello</span><span class="sxs-lookup"><span data-stu-id="1b9ec-161">The top-level factory</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="1b9ec-162">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="1b9ec-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1b9ec-163">La classe **AlgorithmConfiguration** rappresenta un tipo che sa come creare istanze di [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) .</span><span class="sxs-lookup"><span data-stu-id="1b9ec-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="1b9ec-164">Espone una singola API.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-164">It exposes a single API.</span></span>

* <span data-ttu-id="1b9ec-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="1b9ec-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="1b9ec-166">Si pensi a AlgorithmConfiguration come factory di primo livello.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="1b9ec-167">La configurazione funge da modello.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-167">The configuration serves as a template.</span></span> <span data-ttu-id="1b9ec-168">Esegue il wrapping delle informazioni algoritmiche (ad esempio, questa configurazione produce descrittori con una chiave master AES-128-GCM), ma non è ancora associata a una chiave specifica.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="1b9ec-169">Quando viene chiamato CreateNewDescriptor, il materiale della chiave aggiornata viene creato solo per questa chiamata e viene prodotto un nuovo IAuthenticatedEncryptorDescriptor che esegue il wrapping del materiale della chiave e le informazioni algoritmiche necessarie per utilizzare il materiale.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="1b9ec-170">Il materiale della chiave può essere creato nel software (e conservato in memoria), che può essere creato e mantenuto all'interno di un modulo di protezione hardware e così via.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="1b9ec-171">Il punto fondamentale è che le due chiamate a CreateNewDescriptor non devono mai creare istanze IAuthenticatedEncryptorDescriptor equivalenti.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="1b9ec-172">Il tipo AlgorithmConfiguration funge da punto di ingresso per le routine di creazione delle chiavi, ad esempio il [rollup automatico della chiave](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="1b9ec-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="1b9ec-173">Per modificare l'implementazione per tutte le chiavi future, impostare la proprietà AuthenticatedEncryptorConfiguration in KeyManagementOptions.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="1b9ec-174">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="1b9ec-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1b9ec-175">L'interfaccia **IAuthenticatedEncryptorConfiguration** rappresenta un tipo che sa come creare istanze di [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) .</span><span class="sxs-lookup"><span data-stu-id="1b9ec-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="1b9ec-176">Espone una singola API.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-176">It exposes a single API.</span></span>

* <span data-ttu-id="1b9ec-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="1b9ec-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="1b9ec-178">Si pensi a IAuthenticatedEncryptorConfiguration come factory di primo livello.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="1b9ec-179">La configurazione funge da modello.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-179">The configuration serves as a template.</span></span> <span data-ttu-id="1b9ec-180">Esegue il wrapping delle informazioni algoritmiche (ad esempio, questa configurazione produce descrittori con una chiave master AES-128-GCM), ma non è ancora associata a una chiave specifica.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="1b9ec-181">Quando viene chiamato CreateNewDescriptor, il materiale della chiave aggiornata viene creato solo per questa chiamata e viene prodotto un nuovo IAuthenticatedEncryptorDescriptor che esegue il wrapping del materiale della chiave e le informazioni algoritmiche necessarie per utilizzare il materiale.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="1b9ec-182">Il materiale della chiave può essere creato nel software (e conservato in memoria), che può essere creato e mantenuto all'interno di un modulo di protezione hardware e così via.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="1b9ec-183">Il punto fondamentale è che le due chiamate a CreateNewDescriptor non devono mai creare istanze IAuthenticatedEncryptorDescriptor equivalenti.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="1b9ec-184">Il tipo IAuthenticatedEncryptorConfiguration funge da punto di ingresso per le routine di creazione delle chiavi, ad esempio il [rollup automatico della chiave](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="1b9ec-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="1b9ec-185">Per modificare l'implementazione per tutte le chiavi future, registrare un IAuthenticatedEncryptorConfiguration singleton nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="1b9ec-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
