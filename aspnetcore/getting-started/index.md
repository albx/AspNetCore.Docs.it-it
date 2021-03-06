---
title: Introduzione ad ASP.NET Core
author: rick-anderson
description: Un'esercitazione rapida per creare ed eseguire una semplice app Hello World usando ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: getting-started
ms.openlocfilehash: fef5ae525a7c01d0ea7733e990233f413aac61a7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057791"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Esercitazione: Introduzione ad ASP.NET Core

Questa esercitazione illustra come creare ed eseguire un'app Web ASP.NET Core usando il interfaccia della riga di comando di .NET Core.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un progetto di app Web.
> * Considerare attendibile il certificato di sviluppo.
> * Eseguire l'app.
> * Modificare una Razor pagina.

Al termine, si avrà un'app Web funzionante che viene eseguita nel computer locale.

![Home page dell'app Web](_static/home-page.png)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a>Creare un progetto di app Web

Aprire una shell dei comandi e immettere il comando seguente:

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

Il comando precedente:

* Crea una nuova app Web.  
* Il `-o aspnetcoreapp` parametro crea una directory denominata *aspnetcoreapp* con i file di origine per l'app.

### <a name="trust-the-development-certificate"></a>Considerare attendibile il certificato di sviluppo

Considerare attendibile il certificato di sviluppo HTTPS:

# <a name="windows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

Il comando precedente consente di visualizzare la finestra di dialogo seguente:

![Finestra di dialogo Avviso di sicurezza](~/getting-started/_static/cert.png)

Selezionare **Sì** se si accetta di considerare attendibile il certificato di sviluppo.

# <a name="macos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

Il comando precedente consente di visualizzare il messaggio seguente:

*È stato richiesto l'attendibilità del certificato di sviluppo HTTPS. Se il certificato non è già attendibile, verrà eseguito il comando seguente:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`

Questo comando potrebbe richiedere la password per installare il certificato nel keychain di sistema. Inserire la password se si accetta di considerare attendibile il certificato di sviluppo.

# <a name="linux"></a>[Linux](#tab/linux)

Vedere la documentazione della distribuzione di Linux su come rendere attendibile il certificato di sviluppo HTTPS.

---

Per altre informazioni, vedere [Considerare attendibile il certificato di sviluppo di ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)

## <a name="run-the-app"></a>Eseguire l'app

Eseguire i comandi seguenti:

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

Dopo che la shell dei comandi indica che l'app è stata avviata, passare a `https://localhost:5001`.

## <a name="edit-a-no-locrazor-page"></a>Modificare una Razor pagina

Aprire *pages/index. cshtml* e modificare e salvare la pagina con il markup evidenziato seguente:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Individuare `https://localhost:5001` , aggiornare la pagina e verificare che le modifiche vengano visualizzate.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un progetto di app Web.
> * Considerare attendibile il certificato di sviluppo.
> * Eseguire il progetto.
> * Apportare una modifica.

Per altre informazioni su ASP.NET Core, vedere il percorso di apprendimento consigliato nell'introduzione:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
