---
title: Verschuiving van Express.js naar Azure Functions
description: Meer informatie over het refactorion Express.js-eind punten naar Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 266df5371ff5f47526fa9d6567c62e31d51ebb05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87810221"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>Verschuiving van Express.js naar Azure Functions

Express.js is een van de populairste Node.js frameworks voor webontwikkelaars en is een uitstekende keuze voor het bouwen van apps die API-eind punten verwerken.

Bij het migreren van code naar een serverloze architectuur, is het herstructureren van Express.js eind punten van invloed op de volgende gebieden:

- **Middleware**: Express.js biedt een robuuste verzameling middleware. Veel middleware-modules zijn niet meer vereist in het licht van de mogelijkheden van Azure Functions en [Azure API Management](../api-management/api-management-key-concepts.md) . Zorg ervoor dat u een door essentiële middleware verwerkte logica kunt repliceren of vervangen voordat u eind punten migreert.

- **Verschillende api's**: de API die wordt gebruikt voor het verwerken van beide aanvragen en antwoorden verschilt van Azure Functions en Express.js. In het volgende voor beeld worden de vereiste wijzigingen beschreven.

- **Standaard route**: standaard worden Azure functions-eind punten weer gegeven onder de `api` route. Routerings regels kunnen worden geconfigureerd via [ `routePrefix` in de _host.jsvoor_ het bestand](./functions-bindings-http-webhook-output.md#hostjson-settings).

- **Configuratie en conventies**: een functions-app gebruikt de _function.jsin_ het bestand voor het definiëren van http-termen, het definiëren van beveiligings beleid en het configureren van de [invoer en uitvoer](./functions-triggers-bindings.md)van de functie. Standaard wordt de naam van de map die de functie bestanden bevat de naam van het eind punt gedefinieerd, maar u kunt de naam wijzigen via de `route` eigenschap in de [function.jsin](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) het bestand.

> [!TIP]
> Meer informatie vindt u in de interactieve zelf studie [Node.js en snelle api's naar serverloze api's met Azure functions](/learn/modules/shift-nodejs-express-apis-serverless/).

## <a name="example"></a>Voorbeeld

### <a name="expressjs"></a>Express.js

In het volgende voor beeld ziet u een typisch Express.js- `GET` eind punt.

```javascript
// server.js
app.get('/hello', (req, res) => {
  try {
    res.send("Success!");
  } catch(error) {
    const err = JSON.stringify(error);
    res.status(500).send(`Request error. ${err}`);
  }
});
```

Wanneer een `GET` aanvraag wordt verzonden naar `/hello` , `HTTP 200` wordt een antwoord met de melding `Success` geretourneerd. Als er een fout optreedt op het eind punt, is het antwoord een `HTTP 500` met de fout Details.

### <a name="azure-functions"></a>Azure Functions

Azure Functions organiseert configuratie-en code bestanden in één map voor elke functie. De naam van de map bepaalt standaard de functie naam.

Bijvoorbeeld, een functie `hello` met de naam heeft een map met de volgende bestanden.

``` files
| - hello
|  - function.json
|  - index.js
```

In het volgende voor beeld wordt hetzelfde resultaat geïmplementeerd als in het bovenstaande Express.js eind punt, maar met Azure Functions.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// hello/index.js
module.exports = async function (context, req) {
  try {
    context.res = { body: "Success!" };
  } catch(error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`
    };
  }
};
```

# <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
// hello/index.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
  try {
    context.res = { body: "Success!" };
  } catch (error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`,
    };
  }
};

export default httpTrigger;
```

---

Wanneer u overstapt naar functions, worden de volgende wijzigingen aangebracht:

- **Module:** De functie code wordt geïmplementeerd als Java script-module.

- **Context-en antwoord object**: [`context`](./functions-reference-node.md#context-object) Hiermee kunt u communiceren met de runtime van de functie. Vanuit de context kunt u aanvraag gegevens lezen en de reactie van de functie instellen. Voor synchrone code moet u aanroepen `context.done()` om de uitvoering te volt ooien, terwijl `asyc` functies de aanvraag impliciet oplossen.

- **Naam Conventie**: de mapnaam die wordt gebruikt om de Azure functions-bestanden op te nemen, wordt standaard als de eindpunt naam gebruikt (dit kan worden overschreven in de [function.jsop](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)).

- **Configuratie**: u definieert de HTTP-woorden in de [function.jsop](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) bestand, zoals `POST` of `PUT` .

De volgende _function.jsin_ het bestand bevatten configuratie-informatie voor de functie.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

`get`Als u in de `methods` matrix definieert, is de functie beschikbaar voor http- `GET` aanvragen. Als u wilt dat uw API ondersteunings `POST` aanvragen accepteert, kunt u `post` deze ook toevoegen aan de matrix.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de interactieve zelf studie [Node.js en snelle api's naar serverloze api's met Azure functions](/learn/modules/shift-nodejs-express-apis-serverless/)