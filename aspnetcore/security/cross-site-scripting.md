---
title: Impedisci l'esecuzione di script tra siti (XSS) in ASP.NET Core
author: rick-anderson
description: Informazioni sulle tecniche e sugli script tra siti (XSS) per risolvere questa vulnerabilità in un'app ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 1d6f605dc336d8768b8a47e4995f119d198a61af
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172640"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>Impedisci l'esecuzione di script tra siti (XSS) in ASP.NET Core

Di [Rick Anderson](https://twitter.com/RickAndMSFT)

Il cross-site scripting (XSS) è una vulnerabilità della sicurezza che consente a un utente malintenzionato di inserire gli script sul lato client (in genere JavaScript) nelle pagine Web. Quando gli altri utenti caricano pagine interessate, gli script dell'utente malintenzionato vengono eseguiti, consentendo all'autore dell'attacco di rubare cookie e token di sessione, modificare il contenuto della pagina Web tramite la manipolazione DOM o reindirizzare il browser a un'altra pagina. In genere, le vulnerabilità XSS si verificano quando un'applicazione accetta l'input dell'utente e la restituisce a una pagina senza convalidare, codificare o eseguire l'escape.

## <a name="protecting-your-application-against-xss"></a>Protezione dell'applicazione da XSS

A un livello di base XSS funziona facendo in modo che l'applicazione inserisca un tag `<script>` nella pagina di cui è stato eseguito il rendering oppure inserendo un evento `On*` in un elemento. Per evitare di introdurre XSS nella propria applicazione, gli sviluppatori devono utilizzare i seguenti passaggi di prevenzione.

1. Non inserire mai i dati non attendibili nell'input HTML, a meno che non si segua il resto dei passaggi riportati di seguito. I dati non attendibili sono i dati che possono essere controllati da un utente malintenzionato, input di form HTML, stringhe di query, intestazioni HTTP, persino dati originati da un database, perché un utente malintenzionato potrebbe violare il database anche se non è in grado di violare l'applicazione.

2. Prima di inserire dati non attendibili all'interno di un elemento HTML, assicurarsi che sia codificato in HTML. La codifica HTML accetta caratteri come &lt; e li modifica in un formato sicuro come &amp;lt;

3. Prima di inserire dati non attendibili in un attributo HTML, assicurarsi che sia codificato in HTML. La codifica dell'attributo HTML è un superset della codifica HTML e codifica caratteri aggiuntivi come "and".

4. Prima di inserire dati non attendibili in JavaScript, inserire i dati in un elemento HTML il cui contenuto viene recuperato in fase di esecuzione. Se ciò non è possibile, assicurarsi che i dati siano codificati in JavaScript. La codifica JavaScript accetta caratteri pericolosi per JavaScript e li sostituisce con la relativa esadecimale, ad esempio &lt; verrebbe codificata come `\u003C`.

5. Prima di inserire dati non attendibili in una stringa di query URL, assicurarsi che l'URL sia codificato.

## <a name="html-encoding-using-razor"></a>Codifica HTML con Razor

Il motore Razor usato in MVC codifica automaticamente tutto l'output originato dalle variabili, a meno che non si lavori molto duramente per impedirlo. Usa le regole di codifica degli attributi HTML quando si usa la direttiva *@* . Poiché la codifica degli attributi HTML è un superset della codifica HTML, questo significa che non è necessario preoccuparsi se è necessario usare la codifica HTML o la codifica degli attributi HTML. È necessario assicurarsi di usare @ solo in un contesto HTML, non quando si tenta di inserire input non attendibile direttamente in JavaScript. Gli helper Tag codificano inoltre l'input usato nei parametri tag.

Prendere in considerazione la visualizzazione Razor seguente:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

Questa visualizzazione restituisce il contenuto della variabile *untrustedInput* . Questa variabile include alcuni caratteri usati negli attacchi XSS, in particolare &lt;"e &gt;. L'analisi dell'origine Mostra l'output sottoposto a rendering codificato come:

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC fornisce una classe `HtmlString` che non viene codificata automaticamente al momento dell'output. Questa operazione non deve mai essere utilizzata in combinazione con un input non attendibile, in quanto verrà esposta una vulnerabilità XSS.

## <a name="javascript-encoding-using-razor"></a>Codifica JavaScript con Razor

In alcuni casi potrebbe essere necessario inserire un valore in JavaScript da elaborare nella visualizzazione. Questa operazione può essere eseguita in due modi. Il modo più sicuro per inserire valori consiste nell'inserire il valore in un attributo di dati di un tag e recuperarlo nel codice JavaScript. Ad esempio,

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

Verrà generato il codice HTML seguente:

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

Che, quando viene eseguito, eseguirà il rendering degli elementi seguenti:

```
<"123">
   <"123">
```

È anche possibile chiamare direttamente il codificatore JavaScript:

```cshtml
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
```

Verrà eseguito il rendering nel browser nel modo seguente:

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> Non concatenare input non attendibile in JavaScript per creare elementi DOM. È consigliabile utilizzare `createElement()` e assegnare i valori di proprietà in modo appropriato, ad esempio `node.TextContent=`, oppure utilizzare `element.SetAttribute()`/`element[attribute]=` altrimenti si esporrà a XSS basati su DOM.

## <a name="accessing-encoders-in-code"></a>Accesso ai codificatori nel codice

I codificatori HTML, JavaScript e URL sono disponibili per il codice in due modi, è possibile inserirli tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection) oppure è possibile usare i codificatori predefiniti contenuti nello spazio dei nomi `System.Text.Encodings.Web`. Se si usano i codificatori predefiniti, qualsiasi utente applicato agli intervalli di caratteri per essere considerato sicuro non avrà effetto. i codificatori predefiniti utilizzano le regole di codifica più sicure possibili.

Per usare i codificatori configurabili tramite i, i costruttori devono prendere un parametro *HtmlEncoder*, *JavaScriptEncoder* e *UrlEncoder* in base alle esigenze. ad esempio:

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a>Parametri URL di codifica

Se si desidera compilare una stringa di query URL con input non attendibile come valore, utilizzare il `UrlEncoder` per codificare il valore. Ad esempio,

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Dopo la codifica, la variabile valore conterrà `%22Quoted%20Value%20with%20spaces%20and%20%26%22`. Spazi, virgolette, punteggiatura e altri caratteri unsafe verranno codificati in percentuale nel valore esadecimale. ad esempio, un carattere di spazio diventerà %20.

>[!WARNING]
> Non usare input non attendibile come parte di un percorso URL. Passare sempre un input non attendibile come valore stringa di query.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Personalizzazione dei codificatori

Per impostazione predefinita, i codificatori utilizzano un elenco sicuro limitato all'intervallo Unicode Basic Latin e codificano tutti i caratteri al di fuori di tale intervallo come equivalenti del codice carattere. Questo comportamento interessa anche Razor TagHelper e il rendering di HtmlHelper perché utilizzerà i codificatori per restituire le stringhe.

Il motivo è quello di proteggersi da bug sconosciuti o futuri (i bug del browser precedenti hanno attivato l'analisi in base all'elaborazione di caratteri non in lingua inglese). Se il sito Web USA in modo intensivo caratteri non latini, come il cinese, il cirillico o altri, probabilmente non è il comportamento desiderato.

È possibile personalizzare gli elenchi di sicurezza del codificatore in modo da includere gli intervalli Unicode appropriati per l'applicazione durante l'avvio, in `ConfigureServices()`.

Se ad esempio si usa la configurazione predefinita, è possibile usare un HtmlHelper Razor come segue;

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Quando si visualizza l'origine della pagina Web, si noterà che è stato eseguito il rendering come segue, con il testo cinese codificato;

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Per ampliare i caratteri considerati sicuri dal codificatore, inserire la riga seguente nel metodo `ConfigureServices()` in `startup.cs`;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

In questo esempio l'elenco Safe viene ampliato per includere l'intervallo Unicode CjkUnifiedIdeographs. L'output di cui è stato eseguito il rendering è ora

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Gli intervalli di elenchi sicuri vengono specificati come grafici di codice Unicode e non per le lingue. Lo [standard Unicode](https://unicode.org/) include un elenco di [grafici di codice](https://www.unicode.org/charts/index.html) che è possibile utilizzare per trovare il grafico contenente i caratteri. Ogni codificatore, HTML, JavaScript e URL, deve essere configurato separatamente.

> [!NOTE]
> La personalizzazione dell'elenco di sicurezza influiscono solo sui codificatori originati tramite l'inserimento DI dipendenze. Se si accede direttamente a un codificatore tramite `System.Text.Encodings.Web.*Encoder.Default` il valore predefinito, verrà utilizzato solo il tipo di sicurezza Basic Latin.

## <a name="where-should-encoding-take-place"></a>Dove dovrebbe avvenire la codifica?

La pratica generalmente accettata è che la codifica avvenga nel punto di output e che i valori codificati non devono mai essere archiviati in un database. La codifica nel punto di output consente di modificare l'utilizzo dei dati, ad esempio da HTML a un valore query string. Consente inoltre di cercare facilmente i dati senza dover codificare i valori prima di eseguire ricerca e di sfruttare i vantaggi di eventuali modifiche o correzioni di errori apportate agli encoder.

## <a name="validation-as-an-xss-prevention-technique"></a>Convalida come tecnica di prevenzione XSS

La convalida può essere uno strumento utile per limitare gli attacchi XSS. Ad esempio, una stringa numerica contenente solo i caratteri 0-9 non attiverà un attacco XSS. La convalida diventa più complessa quando si accetta codice HTML nell'input dell'utente. L'analisi dell'input HTML è difficile, se non è impossibile. Markdown, abbinato a un parser che rimuove il codice HTML incorporato, è un'opzione più sicura per l'accettazione di un input avanzato. Non utilizzare mai solo la convalida. Codificare sempre l'input non attendibile prima dell'output, indipendentemente dalla convalida o dalla purificazione eseguita.
