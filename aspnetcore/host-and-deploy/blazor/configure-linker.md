---
title: Configurare il linker per ASP.NET Core Blazor
author: guardrex
description: Informazioni su come controllare il linker del linguaggio intermedio (IL) quando si compila un'app Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 263b85a3213c1da233e4c96095faaf39d0a8e13f
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726768"
---
# <a name="configure-the-linker-for-aspnet-core-opno-locblazor"></a><span data-ttu-id="c9c9a-103">Configurare il linker per ASP.NET Core [!OP.NO-LOC(Blazor)]</span><span class="sxs-lookup"><span data-stu-id="c9c9a-103">Configure the Linker for ASP.NET Core [!OP.NO-LOC(Blazor)]</span></span>

<span data-ttu-id="c9c9a-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c9c9a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!OP.NO-LOC(Blazor)]<span data-ttu-id="c9c9a-105"> esegue il collegamento [Intermediate Language (il)](/dotnet/standard/managed-code#intermediate-language--execution) durante una compilazione per rimuovere il linguaggio intermedio non necessario dagli assembly di output dell'app.</span><span class="sxs-lookup"><span data-stu-id="c9c9a-105"> performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="c9c9a-106">Controllare il collegamento degli assembly adottando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="c9c9a-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="c9c9a-107">Disabilitare il collegamento a livello globale con una [proprietà di MSBuild](#disable-linking-with-a-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="c9c9a-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="c9c9a-108">Controllare il collegamento per ogni singolo assembly con un [file di configurazione](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="c9c9a-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="c9c9a-109">Disabilitare il collegamento con una proprietà di MSBuild</span><span class="sxs-lookup"><span data-stu-id="c9c9a-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="c9c9a-110">Il collegamento è abilitato per impostazione predefinita quando viene compilata un'app, che include la pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="c9c9a-110">Linking is enabled by default when an app is built, which includes publishing.</span></span> <span data-ttu-id="c9c9a-111">Per disabilitare il collegamento per tutti gli assembly, impostare la proprietà di MSBuild `BlazorLinkOnBuild` su `false` nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="c9c9a-111">To disable linking for all assemblies, set the `BlazorLinkOnBuild` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="c9c9a-112">Controllare il collegamento con un file di configurazione</span><span class="sxs-lookup"><span data-stu-id="c9c9a-112">Control linking with a configuration file</span></span>

<span data-ttu-id="c9c9a-113">Controllare il collegamento per ogni singolo assembly usando un file di configurazione XML e specificando il file come un elemento MSBuild nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="c9c9a-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="c9c9a-114">*Linker.xml*:</span><span class="sxs-lookup"><span data-stu-id="c9c9a-114">*Linker.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or [!OP.NO-LOC(Blazor)] packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="c9c9a-115">Per altre informazioni, vedere [il linker: sintassi del descrittore XML](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span><span class="sxs-lookup"><span data-stu-id="c9c9a-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="c9c9a-116">Configurare il linker per l'internazionalizzazione</span><span class="sxs-lookup"><span data-stu-id="c9c9a-116">Configure the linker for internationalization</span></span>

<span data-ttu-id="c9c9a-117">Per impostazione predefinita, la configurazione del linker [!OP.NO-LOC(Blazor)]per le app [!OP.NO-LOC(Blazor)] webassembly rimuove le informazioni di internazionalizzazione ad eccezione delle impostazioni locali richieste in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="c9c9a-117">By default, [!OP.NO-LOC(Blazor)]'s linker configuration for [!OP.NO-LOC(Blazor)] WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="c9c9a-118">La rimozione di questi assembly riduce al minimo le dimensioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="c9c9a-118">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="c9c9a-119">Per controllare quali assembly I18N vengono conservati, impostare la proprietà `<MonoLinkerI18NAssemblies>` MSBuild nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="c9c9a-119">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="c9c9a-120">Valore Region</span><span class="sxs-lookup"><span data-stu-id="c9c9a-120">Region Value</span></span>     | <span data-ttu-id="c9c9a-121">Assembly dell'area mono</span><span class="sxs-lookup"><span data-stu-id="c9c9a-121">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="c9c9a-122">Tutti gli assembly inclusi</span><span class="sxs-lookup"><span data-stu-id="c9c9a-122">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="c9c9a-123">*I18n. CJK. dll*</span><span class="sxs-lookup"><span data-stu-id="c9c9a-123">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="c9c9a-124">*I18n. . Dll medio*</span><span class="sxs-lookup"><span data-stu-id="c9c9a-124">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="c9c9a-125">`none` (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="c9c9a-125">`none` (default)</span></span> | <span data-ttu-id="c9c9a-126">None</span><span class="sxs-lookup"><span data-stu-id="c9c9a-126">None</span></span>                    |
| `other`          | <span data-ttu-id="c9c9a-127">*I18n. Other. dll*</span><span class="sxs-lookup"><span data-stu-id="c9c9a-127">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="c9c9a-128">*I18n. Rare. dll*</span><span class="sxs-lookup"><span data-stu-id="c9c9a-128">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="c9c9a-129">*I18n. Dll occidentale*</span><span class="sxs-lookup"><span data-stu-id="c9c9a-129">*I18N.West.dll*</span></span>         |

<span data-ttu-id="c9c9a-130">Usare una virgola per separare più valori, ad esempio `mideast,west`.</span><span class="sxs-lookup"><span data-stu-id="c9c9a-130">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="c9c9a-131">Per altre informazioni, vedere [i18n: Pnetlib internationalation Framework Library (repository GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="c9c9a-131">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
