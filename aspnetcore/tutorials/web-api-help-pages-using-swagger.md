---
title: Pagine della Guida dell'API Web ASP.NET Core con Swagger/OpenAPI
author: RicoSuter
description: In questa esercitazione viene descritta una procedura dettagliata per aggiungere Swagger e generare la documentazione e le pagine della Guida di un'app API Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: a3b8efcdb1f196fc878e0ad29580d305bb24f50a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021978"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="b744f-103">Pagine della Guida dell'API Web ASP.NET Core con Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="b744f-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="b744f-104">Di [Christoph Nienaber](https://twitter.com/zuckerthoben) e [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="b744f-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="b744f-105">Quando si usa un'API Web, la comprensione dei diversi metodi può risultare complessa per uno sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="b744f-105">When consuming a web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="b744f-106">[Spavalderia](https://swagger.io/), noto anche come [openapi](https://www.openapis.org/), risolve il problema della generazione di documentazione e pagine della guida utili per le API Web.</span><span class="sxs-lookup"><span data-stu-id="b744f-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="b744f-107">Offre vantaggi quali la documentazione interattiva, la generazione di SDK client e l'individuabilità delle API.</span><span class="sxs-lookup"><span data-stu-id="b744f-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="b744f-108">Questo articolo illustra le implementazioni .NET di Swagger [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) e [NSwag](https://github.com/RicoSuter/NSwag):</span><span class="sxs-lookup"><span data-stu-id="b744f-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="b744f-109">**Swashbuckle.AspNetCore** è un progetto open source per la generazione di documenti Swagger per le API Web di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b744f-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="b744f-110">**NSwag** è un altro progetto open source per la generazione di documenti di Swagger e l'integrazione dell'[interfaccia utente di Swagger](https://swagger.io/swagger-ui/) o di [ReDoc](https://github.com/Rebilly/ReDoc) nelle API Web di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b744f-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="b744f-111">Inoltre, NSwag offre diversi modi per generare codice client C# e TypeScript per l'API.</span><span class="sxs-lookup"><span data-stu-id="b744f-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="b744f-112">Che cos'è Swagger/OpenAPI?</span><span class="sxs-lookup"><span data-stu-id="b744f-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="b744f-113">Swagger è una specifica indipendente dal linguaggio per la descrizione delle API [REST](https://en.wikipedia.org/wiki/Representational_state_transfer).</span><span class="sxs-lookup"><span data-stu-id="b744f-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="b744f-114">Il progetto Swagger è stato donato all'[iniziativa OpenAPI](https://www.openapis.org/), in cui è ora denominato OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="b744f-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="b744f-115">I nomi sono intercambiabili, ma è preferibile usare OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="b744f-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="b744f-116">Questo strumento consente a computer e utenti di comprendere le funzionalità di un servizio senza accedere direttamente all'implementazione (codice sorgente, accesso alla rete, documentazione).</span><span class="sxs-lookup"><span data-stu-id="b744f-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="b744f-117">Un obiettivo è la riduzione al minimo della quantità di lavoro necessaria per la connessione di servizi non associati.</span><span class="sxs-lookup"><span data-stu-id="b744f-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="b744f-118">Un altro obiettivo è la riduzione del tempo necessario per documentare in modo accurato un servizio.</span><span class="sxs-lookup"><span data-stu-id="b744f-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="b744f-119">Specifica OpenAPI (openapi.js)</span><span class="sxs-lookup"><span data-stu-id="b744f-119">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="b744f-120">Per impostazione predefinita, il nucleo del flusso OpenAPI è la specifica &mdash; , un documento denominato *openapi.json*.</span><span class="sxs-lookup"><span data-stu-id="b744f-120">The core to the OpenAPI flow is the specification&mdash;by default, a document named *openapi.json*.</span></span> <span data-ttu-id="b744f-121">Viene generato dalla catena di strumenti OpenAPI (o implementazioni di terze parti) in base al servizio.</span><span class="sxs-lookup"><span data-stu-id="b744f-121">It's generated by the OpenAPI tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="b744f-122">Descrive le funzionalità dell'API e come accedervi mediante HTTP.</span><span class="sxs-lookup"><span data-stu-id="b744f-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="b744f-123">Gestisce Swagger UI ed è usata dalla catena di strumenti per abilitare l'individuazione e la generazione del codice client.</span><span class="sxs-lookup"><span data-stu-id="b744f-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="b744f-124">Di seguito è riportato un esempio di una specifica OpenAPI, ridotta per brevità:</span><span class="sxs-lookup"><span data-stu-id="b744f-124">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a><span data-ttu-id="b744f-125">Interfaccia utente di Swagger</span><span class="sxs-lookup"><span data-stu-id="b744f-125">Swagger UI</span></span>

<span data-ttu-id="b744f-126">L' [interfaccia utente di spavalderia](https://swagger.io/swagger-ui/) offre un'interfaccia utente basata sul Web che fornisce informazioni sul servizio, usando la specifica openapi generata.</span><span class="sxs-lookup"><span data-stu-id="b744f-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="b744f-127">Sia Swashbuckle sia NSwag includono una versione incorporata di Swagger UI che può essere ospitata nell'applicazione ASP.NET Core dell'utente con una chiamata di registrazione middleware.</span><span class="sxs-lookup"><span data-stu-id="b744f-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="b744f-128">L'interfaccia utente Web è simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="b744f-128">The web UI looks like this:</span></span>

![Interfaccia utente di Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="b744f-130">Ogni metodo di azione pubblico nei controller può essere testato dall'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="b744f-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="b744f-131">Fare clic su un nome di metodo per espandere la sezione.</span><span class="sxs-lookup"><span data-stu-id="b744f-131">Click a method name to expand the section.</span></span> <span data-ttu-id="b744f-132">Aggiungere i parametri necessari e fare clic su **try it out**.</span><span class="sxs-lookup"><span data-stu-id="b744f-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![Esempio test GET di Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="b744f-134">La versione di Swagger UI usata per le schermate è la versione 2.</span><span class="sxs-lookup"><span data-stu-id="b744f-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="b744f-135">Per un esempio con la versione 3, vedere l'[esempio Petstore](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="b744f-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="b744f-136">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="b744f-136">Next steps</span></span>

* [<span data-ttu-id="b744f-137">Introduzione a Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="b744f-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="b744f-138">Introduzione a NSwag</span><span class="sxs-lookup"><span data-stu-id="b744f-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
