---
title: Helper tag di script in ASP.NET Core
author: rick-anderson
ms.author: riande
description: Individuare gli attributi degli helper tag di script ASP.NET Core e il ruolo di ciascun attributo per estendere il comportamento del tag di script HTML.
ms.custom: mvc
ms.date: 12/02/2019
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: 8a90eb5a74ff3f8178a47c59ad7ba1b6a389ab87
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717377"
---
# <a name="script-tag-helper-in-aspnet-core"></a>Helper tag di script in ASP.NET Core

Di [Rick Anderson](https://twitter.com/RickAndMSFT)

L' [Helper tag script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) genera un collegamento a un file di script primario o di fallback. In genere il file di script primario si trova in una rete per la [distribuzione di contenuti](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

L'helper tag script consente di specificare una rete CDN per il file di script e un fallback quando la rete CDN non è disponibile. L'helper tag script offre il vantaggio in merito alle prestazioni di una rete CDN con l'affidabilità dell'hosting locale.

Il markup Razor seguente mostra un elemento `script` con un fallback:

```HTML
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

Non usare l'attributo [rinvia](https://developer.mozilla.org/docs/Web/HTML/Element/script) dell'elemento `<script>` per rinviare il caricamento dello script della rete CDN. L'helper tag script esegue il rendering di JavaScript che esegue immediatamente l'espressione [ASP-fallback-test](#asp-fallback-test) . L'espressione ha esito negativo se viene posticipato il caricamento dello script della rete CDN.

## <a name="commonly-used-script-tag-helper-attributes"></a>Attributi di helper tag di script di uso comune

Vedere [script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) per tutti gli attributi, le proprietà e i metodi dell'helper tag di script.

### <a name="asp-fallback-test"></a>ASP-fallback-test

Metodo di script definito nello script principale da usare per il test di fallback. Per ulteriori informazioni, vedere <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.

### <a name="asp-fallback-src"></a>ASP-fallback-src

L'URL di un tag di script di cui eseguire il fallback nel caso in cui si verifica l'errore primario. Per ulteriori informazioni, vedere <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
