---
title: Helper tag di cache in ASP.NET Core MVC
author: pkellner
description: Informazioni su come usare l'helper tag di cache.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: fb69584f6e9d4756e175bbd6f3deb1f413b80fc5
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890466"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>Helper tag di cache in ASP.NET Core MVC

Di [Peter Kellner](http://peterkellner.net) e [Luke Latham](https://github.com/guardrex) 

L'helper tag di cache consente di migliorare le prestazioni dell'app ASP.NET Core memorizzandone il contenuto nel provider di cache ASP.NET Core interno.

Per una panoramica degli helper tag, vedere <xref:mvc/views/tag-helpers/intro>.

Il seguente markup Razor memorizza nella cache la data corrente:

```cshtml
<cache>@DateTime.Now</cache>
```

La prima richiesta alla pagina contenente l'helper tag consente di visualizzare la data corrente. Le richieste aggiuntive mostrano il valore memorizzato nella cache fino alla scadenza della cache (per impostazione predefinita 20 minuti) o fino a quando la data memorizzata nella cache viene rimossa dalla cache.

## <a name="cache-tag-helper-attributes"></a>Attributi dell'helper tag di cache

### <a name="enabled"></a>enabled

| Tipo di attributo  | Esempi        | Impostazione predefinita |
| --------------- | --------------- | ------- |
| Booleano         | `true`, `false` | `true`  |

`enabled` determina se il contenuto incluso nell'helper tag di cache viene memorizzato nella cache. Il valore predefinito è `true`. Se impostato su `false`, l'output sottoposto a rendering **non** viene memorizzato nella cache.

Esempio:

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a>expires-on

| Tipo di attributo   | Esempio                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

`expires-on` imposta una data di scadenza assoluta per l'elemento memorizzato nella cache.

Il codice di esempio seguente memorizza nella cache il contenuto dell'helper tag di cache fino alle 17:02 del 29 gennaio 2025:

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a>expires-after

| Tipo di attributo | Esempio                      | Impostazione predefinita    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | 20 minuti |

`expires-after` imposta il tempo di memorizzazione del contenuto nella cache a partire dalla prima richiesta.

Esempio:

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Il motore di visualizzazione Razor imposta il valore predefinito di `expires-after` su venti minuti.

### <a name="expires-sliding"></a>expires-sliding

| Tipo di attributo | Esempio                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

Imposta il tempo trascorso il quale un elemento memorizzato nella cache viene rimosso se non è stato usato.

Esempio:

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a>vary-by-header

| Tipo di attributo | Esempi                                    |
| -------------- | ------------------------------------------- |
| Stringa         | `User-Agent`, `User-Agent,content-encoding` |

`vary-by-header` accetta un elenco delimitato da virgole di valori di intestazione che attivano un aggiornamento della cache quando vengono modificati.

L'esempio seguente esegue il monitoraggio del valore dell'intestazione `User-Agent`. Il codice dell'esempio memorizza nella cache il contenuto di ogni singolo elemento `User-Agent` presentato al server Web:

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a>vary-by-query

| Tipo di attributo | Esempi             |
| -------------- | -------------------- |
| Stringa         | `Make`, `Make,Model` |

`vary-by-query` accetta un elenco delimitato da virgole di <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in una stringa di query (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) che attiva un aggiornamento della cache quando cambia il valore di qualsiasi chiave inclusa nell'elenco.

L'esempio seguente esegue il monitoraggio dei valori di `Make` e `Model`. L'esempio memorizza nella cache il contenuto di ogni singolo valore `Make` e `Model` presentato al server Web:

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a>vary-by-route

| Tipo di attributo | Esempi             |
| -------------- | -------------------- |
| Stringa         | `Make`, `Make,Model` |

`vary-by-route` accetta un elenco delimitato da virgole di nomi di parametri di route che attivano un aggiornamento della cache quando cambia il valore del parametro dei dati di route.

Esempio:

*Startup.cs*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

*Index.cshtml*:

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a>vary-by-cookie

| Tipo di attributo | Esempi                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| Stringa         | `.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor` |

`vary-by-cookie` accetta un elenco delimitato da virgole di nomi di cookie che attivano un aggiornamento della cache quando cambiano i valori dei cookie.

L'esempio seguente gestisce il monitoraggio dei cookie associati ad ASP.NET Core Identity. Quando un utente viene autenticato, una modifica del cookie di Identity attiva un aggiornamento della cache:

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a>vary-by-user

| Tipo di attributo  | Esempi        | Impostazione predefinita |
| --------------- | --------------- | ------- |
| Booleano         | `true`, `false` | `true`  |

`vary-by-user` specifica se la cache deve essere o meno reimpostata quando cambia l'utente connesso (o l'entità di contesto connessa). L'utente corrente è detto anche entità di sicurezza del contesto della richiesta e può essere visualizzato in una visualizzazione Razor mediante riferimento a `@User.Identity.Name`.

L'esempio seguente esegue il monitoraggio dell'utente connesso corrente per attivare un aggiornamento della cache:

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Questo attributo consente di mantenere il contenuto nella cache durante un ciclo di accesso e disconnessione. Quando il valore è impostato su `true`, un ciclo di autenticazione invalida la cache per l'utente autenticato. La cache viene invalidata perché viene generato un nuovo valore di cookie univoco al momento dell'autenticazione di un utente. La cache viene mantenuta per lo stato anonimo se non è presente alcun cookie o il cookie è scaduto. Se l'utente **non** viene autenticato, la cache viene mantenuta.

### <a name="vary-by"></a>vary-by

| Tipo di attributo | Esempio  |
| -------------- | -------- |
| Stringa         | `@Model` |

`vary-by` consente di personalizzare quali dati vengono memorizzati nella cache. Quando l'oggetto al quale fa riferimento il valore stringa dell'attributo cambia, il contenuto dell'helper tag di cache viene aggiornato. Spesso a questo attributo viene assegnata una concatenazione stringa di valori del modello. In effetti, da ciò risulta uno scenario in cui un aggiornamento di uno qualsiasi dei valori concatenati invalida la cache.

L'esempio seguente presuppone che il metodo del controller che esegue il rendering della visualizzazione effettui la somma dei valori interi dei due parametri di route, `myParam1` e `myParam2`, e restituisca la somma come proprietà singola del modello. Quando questa somma cambia, il contenuto dell'helper tag di cache viene sottoposto a rendering e memorizzato di nuovo nella cache.  

Azione:

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

*Index.cshtml*:

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a>priority

| Tipo di attributo      | Esempi                               | Impostazione predefinita  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | `High`, `Low`, `NeverRemove`, `Normal` | `Normal` |

`priority` offre indicazioni per la rimozione dalla cache al provider di cache predefinito. In condizioni di utilizzo elevato della memoria, il server Web rimuove per prime le voci della cache `Low`.

Esempio:

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

L'attributo `priority` non garantisce un livello specifico di mantenimento nella cache. `CacheItemPriority` è un semplice suggerimento. L'impostazione di questo attributo su `NeverRemove` non garantisce che gli elementi memorizzati nella cache vengano sempre mantenuti. Per altre informazioni, vedere gli argomenti nella sezione [Risorse aggiuntive](#additional-resources).

L'helper tag di cache dipende dal [servizio cache in memoria](xref:performance/caching/memory). L'helper tag di cache aggiunge il servizio se non è stato aggiunto.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
