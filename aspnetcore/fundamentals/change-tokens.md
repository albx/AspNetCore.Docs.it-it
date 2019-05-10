---
title: Rilevare le modifiche apportate con i token di modifica in ASP.NET Core
author: guardrex
description: Informazioni su come usare i token di modifica per rilevare le modifiche.
ms.author: riande
ms.date: 11/10/2017
uid: fundamentals/change-tokens
ms.openlocfilehash: f7dad52fc19b6dc5c8668ad852e69acd65a72162
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886876"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="0bf79-103">Rilevare le modifiche apportate con i token di modifica in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0bf79-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="0bf79-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0bf79-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0bf79-105">Un *token di modifica* è un blocco predefinito di uso generico e di basso livello che viene usato per il rilevamento delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="0bf79-105">A *change token* is a general-purpose, low-level building block used to track changes.</span></span>

<span data-ttu-id="0bf79-106">[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0bf79-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="0bf79-107">Interfaccia IChangeToken</span><span class="sxs-lookup"><span data-stu-id="0bf79-107">IChangeToken interface</span></span>

<span data-ttu-id="0bf79-108">[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) propaga le notifiche relative a una modifica apportata.</span><span class="sxs-lookup"><span data-stu-id="0bf79-108">[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) propagates notifications that a change has occurred.</span></span> <span data-ttu-id="0bf79-109">`IChangeToken` risiede nello spazio dei nomi [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives).</span><span class="sxs-lookup"><span data-stu-id="0bf79-109">`IChangeToken` resides in the [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) namespace.</span></span> <span data-ttu-id="0bf79-110">Per le app che non usano il [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 o versioni successive), fare riferimento al pacchetto NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="0bf79-110">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), reference the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package in the project file.</span></span>

<span data-ttu-id="0bf79-111">`IChangeToken` ha due proprietà:</span><span class="sxs-lookup"><span data-stu-id="0bf79-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="0bf79-112">[ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks) indica se il token genera callback in modo proattivo.</span><span class="sxs-lookup"><span data-stu-id="0bf79-112">[ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks) indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="0bf79-113">Se `ActiveChangedCallbacks` è impostato su `false`, non viene mai chiamato alcun callback e l'app deve eseguire il polling di `HasChanged` per ottenere le modifiche.</span><span class="sxs-lookup"><span data-stu-id="0bf79-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="0bf79-114">È anche possibile che un token non venga mai annullato se non vengono apportate modifiche o se il listener di modifica sottostante viene eliminato o disabilitato.</span><span class="sxs-lookup"><span data-stu-id="0bf79-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="0bf79-115">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) ottiene un valore che indica se è stata apportata una modifica.</span><span class="sxs-lookup"><span data-stu-id="0bf79-115">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) gets a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="0bf79-116">L'interfaccia include un metodo, [RegisterChangeCallback(Action&lt;Object&gt;, Object)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback), che registra un callback che viene richiamato quando il token è stato modificato.</span><span class="sxs-lookup"><span data-stu-id="0bf79-116">The interface has one method, [RegisterChangeCallback(Action&lt;Object&gt;, Object)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback), which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="0bf79-117">Prima che il callback venga richiamato è necessario impostare `HasChanged`.</span><span class="sxs-lookup"><span data-stu-id="0bf79-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="0bf79-118">Classe ChangeToken</span><span class="sxs-lookup"><span data-stu-id="0bf79-118">ChangeToken class</span></span>

<span data-ttu-id="0bf79-119">`ChangeToken` è una classe statica usata per propagare le notifiche relative a una modifica che è stata apportata.</span><span class="sxs-lookup"><span data-stu-id="0bf79-119">`ChangeToken` is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="0bf79-120">`ChangeToken` risiede nello spazio dei nomi [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives).</span><span class="sxs-lookup"><span data-stu-id="0bf79-120">`ChangeToken` resides in the [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) namespace.</span></span> <span data-ttu-id="0bf79-121">Per le app che non usano il [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), fare riferimento al pacchetto NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="0bf79-121">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), reference the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package in the project file.</span></span>

<span data-ttu-id="0bf79-122">Il metodo `ChangeToken` [OnChange(Func&lt;IChangeToken&gt;, Action)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_) registra un elemento `Action` da chiamare quando il token viene modificato:</span><span class="sxs-lookup"><span data-stu-id="0bf79-122">The `ChangeToken` [OnChange(Func&lt;IChangeToken&gt;, Action)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="0bf79-123">`Func<IChangeToken>` produce il token.</span><span class="sxs-lookup"><span data-stu-id="0bf79-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="0bf79-124">`Action` viene chiamato alla modifica del token.</span><span class="sxs-lookup"><span data-stu-id="0bf79-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="0bf79-125">`ChangeToken` ha un overload [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, Action&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_) che acquisisce un parametro `TState` aggiuntivo che viene passato nell'elemento `Action` consumer del token.</span><span class="sxs-lookup"><span data-stu-id="0bf79-125">`ChangeToken` has an [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, Action&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_) overload that takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="0bf79-126">`OnChange` restituisce un elemento [IDisposable](/dotnet/api/system.idisposable).</span><span class="sxs-lookup"><span data-stu-id="0bf79-126">`OnChange` returns an [IDisposable](/dotnet/api/system.idisposable).</span></span> <span data-ttu-id="0bf79-127">La chiamata a [Dispose](/dotnet/api/system.idisposable.dispose) interrompe l'ascolto di altre modifiche da parte del token e rilascia le risorse del token.</span><span class="sxs-lookup"><span data-stu-id="0bf79-127">Calling [Dispose](/dotnet/api/system.idisposable.dispose) stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="0bf79-128">Esempi di uso di token di modifica in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0bf79-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="0bf79-129">I token di modifica vengono usati nelle aree principali di ASP.NET Core per monitorare le modifiche apportate agli oggetti:</span><span class="sxs-lookup"><span data-stu-id="0bf79-129">Change tokens are used in prominent areas of ASP.NET Core monitoring changes to objects:</span></span>

* <span data-ttu-id="0bf79-130">Per monitorare le modifiche ai file, il metodo [Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) di [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) crea un elemento `IChangeToken` per le cartelle o i file specificati da controllare.</span><span class="sxs-lookup"><span data-stu-id="0bf79-130">For monitoring changes to files, [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)'s [Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="0bf79-131">È possibile aggiungere token `IChangeToken` alle voci della cache per attivare le eliminazioni dalla cache alla modifica.</span><span class="sxs-lookup"><span data-stu-id="0bf79-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="0bf79-132">Per le modifiche `TOptions`, l'implementazione [OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1) predefinita di [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) ha un overload che accetta una o più istanze di [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1).</span><span class="sxs-lookup"><span data-stu-id="0bf79-132">For `TOptions` changes, the default [OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1) implementation of [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) has an overload that accepts one or more [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1) instances.</span></span> <span data-ttu-id="0bf79-133">Ogni istanza restituisce un elemento `IChangeToken` per registrare un callback di notifica delle modifiche per il rilevamento delle modifiche apportate alle opzioni.</span><span class="sxs-lookup"><span data-stu-id="0bf79-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitoring-for-configuration-changes"></a><span data-ttu-id="0bf79-134">Monitoraggio delle modifiche alla configurazione</span><span class="sxs-lookup"><span data-stu-id="0bf79-134">Monitoring for configuration changes</span></span>

<span data-ttu-id="0bf79-135">Per impostazione predefinita, i modelli ASP.NET Core usano [file di configurazione JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* e *appsettings.Production.json*) per caricare le impostazioni di configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="0bf79-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="0bf79-136">Questi file vengono configurati usando il metodo di estensione [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_) su [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) che accetta un parametro `reloadOnChange` (ASP.NET Core 1.1 e versioni successive).</span><span class="sxs-lookup"><span data-stu-id="0bf79-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_) extension method on [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) that accepts a `reloadOnChange` parameter (ASP.NET Core 1.1 and later).</span></span> <span data-ttu-id="0bf79-137">`reloadOnChange` indica se la configurazione deve essere ricaricata alla modifica del file.</span><span class="sxs-lookup"><span data-stu-id="0bf79-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="0bf79-138">Vedere questa impostazione nel metodo pratico [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) di [WebHost](/dotnet/api/microsoft.aspnetcore.webhost):</span><span class="sxs-lookup"><span data-stu-id="0bf79-138">See this setting in the [WebHost](/dotnet/api/microsoft.aspnetcore.webhost) convenience method [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder):</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);
```

<span data-ttu-id="0bf79-139">La configurazione basata su file è rappresentata da [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource).</span><span class="sxs-lookup"><span data-stu-id="0bf79-139">File-based configuration is represented by [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource).</span></span> <span data-ttu-id="0bf79-140">`FileConfigurationSource` usa [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) per monitorare i file.</span><span class="sxs-lookup"><span data-stu-id="0bf79-140">`FileConfigurationSource` uses [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) to monitor files.</span></span>

<span data-ttu-id="0bf79-141">Per impostazione predefinita, `IFileMonitor` viene offerto da una classe [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) che usa [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) per monitorare le modifiche apportate al file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="0bf79-141">By default, the `IFileMonitor` is provided by a [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider), which uses [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) to monitor for configuration file changes.</span></span>

<span data-ttu-id="0bf79-142">L'app di esempio illustra due implementazioni per il monitoraggio delle modifiche alla configurazione.</span><span class="sxs-lookup"><span data-stu-id="0bf79-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="0bf79-143">In caso di modifiche al file *appsettings.json* o alla versione dell'ambiente del file, ogni implementazione esegue codice personalizzato.</span><span class="sxs-lookup"><span data-stu-id="0bf79-143">If either the *appsettings.json* file changes or the Environment version of the file changes, each implementation executes custom code.</span></span> <span data-ttu-id="0bf79-144">L'app di esempio scrive un messaggio nella console.</span><span class="sxs-lookup"><span data-stu-id="0bf79-144">The sample app writes a message to the console.</span></span>

<span data-ttu-id="0bf79-145">L'elemento `FileSystemWatcher` di un file di configurazione può attivare più callback del token per una singola modifica del file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="0bf79-145">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="0bf79-146">L'implementazione dell'esempio evita questo problema controllando gli hash dei file nei file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="0bf79-146">The sample's implementation guards against this problem by checking file hashes on the configuration files.</span></span> <span data-ttu-id="0bf79-147">Grazie al controllo degli hash dei file è possibile assicurarsi che almeno uno dei file di configurazione sia stato modificato prima di eseguire il codice personalizzato.</span><span class="sxs-lookup"><span data-stu-id="0bf79-147">Checking file hashes ensures that at least one of the configuration files has changed before running the custom code.</span></span> <span data-ttu-id="0bf79-148">Nell'esempio viene usato l'hash di file SHA1 (*Utilities/Utilities.cs*):</span><span class="sxs-lookup"><span data-stu-id="0bf79-148">The sample uses SHA1 file hashing (*Utilities/Utilities.cs*):</span></span>

   [!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet1)]

   <span data-ttu-id="0bf79-149">Viene implementato un nuovo tentativo con un'interruzione temporanea esponenziale.</span><span class="sxs-lookup"><span data-stu-id="0bf79-149">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="0bf79-150">Il nuovo tentativo è presente perché potrebbe verificarsi un blocco di file che impedisce temporaneamente l'elaborazione di un nuovo hash in uno dei file.</span><span class="sxs-lookup"><span data-stu-id="0bf79-150">The re-try is present because file locking may occur that temporarily prevents computing a new hash on one of the files.</span></span>

### <a name="simple-startup-change-token"></a><span data-ttu-id="0bf79-151">Semplice token di modifica dell'avvio</span><span class="sxs-lookup"><span data-stu-id="0bf79-151">Simple startup change token</span></span>

<span data-ttu-id="0bf79-152">Registrare un callback dell'elemento `Action` consumer del token per le notifiche di modifica al token di ricaricamento della configurazione (*Startup.cs*):</span><span class="sxs-lookup"><span data-stu-id="0bf79-152">Register a token consumer `Action` callback for change notifications to the configuration reload token (*Startup.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="0bf79-153">`config.GetReloadToken()` fornisce il token.</span><span class="sxs-lookup"><span data-stu-id="0bf79-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="0bf79-154">Il callback è il metodo `InvokeChanged`:</span><span class="sxs-lookup"><span data-stu-id="0bf79-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet3)]

<span data-ttu-id="0bf79-155">L'elemento `state` del callback viene usato per passare `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="0bf79-155">The `state` of the callback is used to pass in the `IHostingEnvironment`.</span></span> <span data-ttu-id="0bf79-156">Ciò risulta utile per determinare il file JSON di configurazione *appsettings* corretto da monitorare, *appsettings.&lt;Environment&gt;.json*.</span><span class="sxs-lookup"><span data-stu-id="0bf79-156">This is useful to determine the correct *appsettings* configuration JSON file to monitor, *appsettings.&lt;Environment&gt;.json*.</span></span> <span data-ttu-id="0bf79-157">Gli hash dei file vengono usati per impedire che l'istruzione `WriteConsole` venga eseguita più volte a causa di più callback del token quando il file di configurazione è stato modificato una sola volta.</span><span class="sxs-lookup"><span data-stu-id="0bf79-157">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="0bf79-158">Questo sistema rimane in esecuzione finché l'app è in esecuzione e non può essere disabilitato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="0bf79-158">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitoring-configuration-changes-as-a-service"></a><span data-ttu-id="0bf79-159">Monitoraggio delle modifiche alla configurazione come servizio</span><span class="sxs-lookup"><span data-stu-id="0bf79-159">Monitoring configuration changes as a service</span></span>

<span data-ttu-id="0bf79-160">L'esempio implementa:</span><span class="sxs-lookup"><span data-stu-id="0bf79-160">The sample implements:</span></span>

* <span data-ttu-id="0bf79-161">Monitoraggio di base del token di avvio.</span><span class="sxs-lookup"><span data-stu-id="0bf79-161">Basic startup token monitoring.</span></span>
* <span data-ttu-id="0bf79-162">Monitoraggio come servizio.</span><span class="sxs-lookup"><span data-stu-id="0bf79-162">Monitoring as a service.</span></span>
* <span data-ttu-id="0bf79-163">Un meccanismo per abilitare e disabilitare il monitoraggio.</span><span class="sxs-lookup"><span data-stu-id="0bf79-163">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="0bf79-164">L'esempio stabilisce un'interfaccia `IConfigurationMonitor` (*Extensions/ConfigurationMonitor.cs*):</span><span class="sxs-lookup"><span data-stu-id="0bf79-164">The sample establishes an `IConfigurationMonitor` interface (*Extensions/ConfigurationMonitor.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="0bf79-165">Il costruttore della classe implementata, `ConfigurationMonitor`, registra un callback per le notifiche di modifica:</span><span class="sxs-lookup"><span data-stu-id="0bf79-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="0bf79-166">`config.GetReloadToken()` fornisce il token.</span><span class="sxs-lookup"><span data-stu-id="0bf79-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="0bf79-167">`InvokeChanged` è il metodo di callback.</span><span class="sxs-lookup"><span data-stu-id="0bf79-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="0bf79-168">L'elemento `state` in questa istanza è un riferimento all'istanza `IConfigurationMonitor` usata per accedere allo stato di monitoraggio.</span><span class="sxs-lookup"><span data-stu-id="0bf79-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that is used to access the monitoring state.</span></span> <span data-ttu-id="0bf79-169">Vengono usate due proprietà:</span><span class="sxs-lookup"><span data-stu-id="0bf79-169">Two properties are used:</span></span>

* <span data-ttu-id="0bf79-170">`MonitoringEnabled` indica se il callback deve eseguire il proprio codice personalizzato.</span><span class="sxs-lookup"><span data-stu-id="0bf79-170">`MonitoringEnabled` indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="0bf79-171">`CurrentState` descrive lo stato di monitoraggio corrente per l'uso nell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="0bf79-171">`CurrentState` describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="0bf79-172">Il metodo `InvokeChanged` è simile all'approccio precedente, ad eccezione del fatto che:</span><span class="sxs-lookup"><span data-stu-id="0bf79-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="0bf79-173">Non esegue il proprio codice a meno che `MonitoringEnabled` non sia impostato su `true`.</span><span class="sxs-lookup"><span data-stu-id="0bf79-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="0bf79-174">Annota l'elemento `state` corrente nell'output `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="0bf79-174">Notes the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="0bf79-175">Un'istanza di `ConfigurationMonitor` viene registrata come servizio in `ConfigureServices` di *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0bf79-175">An instance `ConfigurationMonitor` is registered as a service in `ConfigureServices` of *Startup.cs*:</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="0bf79-176">La pagina di indice offre all'utente il controllo sul monitoraggio della configurazione.</span><span class="sxs-lookup"><span data-stu-id="0bf79-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="0bf79-177">L'istanza di `IConfigurationMonitor` viene inserita in `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="0bf79-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`:</span></span>

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="0bf79-178">Un pulsante abilita e disabilita il monitoraggio:</span><span class="sxs-lookup"><span data-stu-id="0bf79-178">A button enables and disables monitoring:</span></span>

[!code-cshtml[](change-tokens/sample/Pages/Index.cshtml?range=35)]

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="0bf79-179">Quando viene attivato `OnPostStartMonitoring`, il monitoraggio è abilitato e lo stato corrente viene cancellato.</span><span class="sxs-lookup"><span data-stu-id="0bf79-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="0bf79-180">Quando viene attivato `OnPostStopMonitoring`, il monitoraggio è disabilitato e lo stato viene impostato in modo da indicare che il monitoraggio non viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="0bf79-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

## <a name="monitoring-cached-file-changes"></a><span data-ttu-id="0bf79-181">Monitoraggio delle modifiche al file nella cache</span><span class="sxs-lookup"><span data-stu-id="0bf79-181">Monitoring cached file changes</span></span>

<span data-ttu-id="0bf79-182">È possibile memorizzare il contenuto del file nella cache in memoria usando [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache).</span><span class="sxs-lookup"><span data-stu-id="0bf79-182">File content can be cached in-memory using [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache).</span></span> <span data-ttu-id="0bf79-183">La memorizzazione nella cache in memoria è descritta nell'argomento [Cache in memoria](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="0bf79-183">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="0bf79-184">Senza eseguire passaggi aggiuntivi, ad esempio l'implementazione descritta di seguito, la cache restituirà dati *non aggiornati* (obsoleti) se i dati di origine vengono modificati.</span><span class="sxs-lookup"><span data-stu-id="0bf79-184">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="0bf79-185">Se durante il rinnovo di un periodo di [scadenza variabile](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) non si tiene conto dello stato di un file di origine memorizzato nella cache, i dati nella cache risulteranno non aggiornati.</span><span class="sxs-lookup"><span data-stu-id="0bf79-185">Not taking into account the status of a cached source file when renewing a [sliding expiration](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) period leads to stale cache data.</span></span> <span data-ttu-id="0bf79-186">Ogni richiesta di dati rinnoverà il periodo di scadenza variabile, ma il file non verrà mai ricaricato nella cache.</span><span class="sxs-lookup"><span data-stu-id="0bf79-186">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="0bf79-187">Le funzionalità dell'app che usano il contenuto del file memorizzato nella cache sono soggette alla possibile ricezione di contenuto non aggiornato.</span><span class="sxs-lookup"><span data-stu-id="0bf79-187">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="0bf79-188">L'uso dei token di modifica in uno scenario di memorizzazione di file nella cache consente di evitare la presenza di contenuto non aggiornato nella cache.</span><span class="sxs-lookup"><span data-stu-id="0bf79-188">Using change tokens in a file caching scenario prevents stale file content in the cache.</span></span> <span data-ttu-id="0bf79-189">L'app di esempio illustra un'implementazione dell'approccio.</span><span class="sxs-lookup"><span data-stu-id="0bf79-189">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="0bf79-190">Nell'esempio viene usato `GetFileContent` per:</span><span class="sxs-lookup"><span data-stu-id="0bf79-190">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="0bf79-191">Restituire il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="0bf79-191">Return file content.</span></span>
* <span data-ttu-id="0bf79-192">Implementare un algoritmo di nuovi tentativi con interruzione temporanea esponenziale per i casi in cui un blocco di file impedisca temporaneamente la lettura di un file.</span><span class="sxs-lookup"><span data-stu-id="0bf79-192">Implement a retry algorithm with exponential back-off to cover cases where a file lock is temporarily preventing a file from being read.</span></span>

<span data-ttu-id="0bf79-193">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="0bf79-193">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="0bf79-194">Viene creato un elemento `FileService` per gestire le ricerche nel file memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="0bf79-194">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="0bf79-195">La chiamata del servizio al metodo `GetFileContent` prova a ottenere il contenuto del file dalla cache in memoria e a restituirlo al chiamante (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="0bf79-195">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="0bf79-196">Se non è possibile reperire il contenuto memorizzato nella cache usando la chiave della cache, verranno intraprese le azioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="0bf79-196">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="0bf79-197">Il contenuto del file viene ottenuto usando `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="0bf79-197">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="0bf79-198">Un token di modifica viene ottenuto dal provider di file con [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch).</span><span class="sxs-lookup"><span data-stu-id="0bf79-198">A change token is obtained from the file provider with [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch).</span></span> <span data-ttu-id="0bf79-199">Il callback del token viene attivato alla modifica del file.</span><span class="sxs-lookup"><span data-stu-id="0bf79-199">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="0bf79-200">Il contenuto del file viene memorizzato nella cache con un periodo di [scadenza variabile](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration).</span><span class="sxs-lookup"><span data-stu-id="0bf79-200">The file content is cached with a [sliding expiration](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) period.</span></span> <span data-ttu-id="0bf79-201">Al token di modifica viene associato [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) per eliminare la voce della cache se il file viene modificato mentre è memorizzato nella cache.</span><span class="sxs-lookup"><span data-stu-id="0bf79-201">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) to evict the cache entry if the file changes while it's cached.</span></span>

[!code-csharp[](change-tokens/sample/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="0bf79-202">`FileService` è registrato nel contenitore di servizi insieme al servizio di memorizzazione nella cache (*Startup.cs*):</span><span class="sxs-lookup"><span data-stu-id="0bf79-202">The `FileService` is registered in the service container along with the memory caching service (*Startup.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet4)]

<span data-ttu-id="0bf79-203">Il modello di pagina carica il contenuto del file usando il servizio (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="0bf79-203">The page model loads the file's content using the service (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="0bf79-204">Classe CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="0bf79-204">CompositeChangeToken class</span></span>

<span data-ttu-id="0bf79-205">Per rappresentare una o più istanze di `IChangeToken` in un singolo oggetto, usare la classe [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken).</span><span class="sxs-lookup"><span data-stu-id="0bf79-205">For representing one or more `IChangeToken` instances in a single object, use the [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken) class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        { 
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="0bf79-206">`HasChanged` nel token composito indica `true` se l'elemento `HasChanged` di qualsiasi token rappresentato è `true`.</span><span class="sxs-lookup"><span data-stu-id="0bf79-206">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="0bf79-207">`ActiveChangeCallbacks` nel token composito indica `true` se l'elemento `ActiveChangeCallbacks` di qualsiasi token rappresentato è `true`.</span><span class="sxs-lookup"><span data-stu-id="0bf79-207">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="0bf79-208">Se si verificano più eventi di modifica simultanei, il callback della modifica composita viene richiamato esattamente una volta.</span><span class="sxs-lookup"><span data-stu-id="0bf79-208">If multiple concurrent change events occur, the composite change callback is invoked exactly one time.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0bf79-209">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0bf79-209">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
