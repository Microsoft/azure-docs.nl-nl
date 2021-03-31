---
title: 'Quickstart: Uw eerste JavaScript-query'
description: In deze quickstart voert u de stappen uit om de Resource Graph-bibliotheek voor JavaScript in te schakelen en uw eerste query uit te voeren.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 97cbd9ddf65a4135f55304f6dd67c704c6fcac5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917518"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>Quickstart: Uw eerste Resource Graph-query uitvoeren met JavaScript

In deze quickstart doorloopt u het proces voor het toevoegen van de bibliotheken aan uw JavaScript-installatie. De eerste stap voor het gebruik van Azure Resource Graph bestaat uit het initialiseren van een JavaScript-toepassing met de vereiste bibliotheken.

Aan het einde van dit proces hebt u de bibliotheken toegevoegd aan uw JavaScript-installatie en hebt u uw eerste Resource Graph-query uitgevoerd.

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- **Node.js**: [Node.js](https://nodejs.org/)-versie 12 of hoger is vereist.

## <a name="application-initialization"></a>Initialisatie van toepassingen

Als u JavaScript wilt inschakelen om een query uit te voeren in Azure Resource Graph, moet de omgeving worden geconfigureerd. Deze instelling werkt overal waar JavaScript kan worden gebruikt, met inbegrip van [bash in Windows 10](/windows/wsl/install-win10).

1. Initialiseer een nieuw Node.js-project door de volgende opdracht uit te voeren.

   ```bash
   npm init -y
   ```

1. Voeg een verwijzing toe naar de yargs-module.

   ```bash
   npm install yargs
   ```

1. Voeg een verwijzing toe naar de Azure Resource Graph-module.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Voeg een verwijzing toe naar de Azure-verificatiebibliotheek.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Controleer in _package. json_ of `@azure/arm-resourcegraph` versie **2.0.0** of hoger is en of `@azure/ms-rest-nodeauth` versie **3.0.3** of hoger is.

## <a name="query-the-resource-graph"></a>Resource Graph doorzoeken

1. Maak een nieuw bestand met de naam _index. js_ en voer de volgende code in.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query && argv.subs) {
       const subscriptionList = argv.subs.split(",");

       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query,
                 subscriptions: subscriptionList,
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

1. Voer de volgende opdracht in de terminal in:

   ```bash
   node index.js --query "Resources | project name, type | limit 5" --subs <YOUR_SUBSCRIPTION_ID_LIST>
   ```

   Zorg ervoor dat u de tijdelijke aanduiding voor `<YOUR_SUBSCRIPTION_ID_LIST>` vervangt door de door komma's gescheiden lijst met Azure-abonnement-id's.

   > [!NOTE]
   > Omdat deze voorbeeldquery geen sorteermodificator geeft, bijvoorbeeld `order by`, zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd.

1. Wijzig de eerste parameter in `index.js` en wijzig de query om te `order by` op de eigenschap **Naam**. Vervang `<YOUR_SUBSCRIPTION_ID_LIST>` door uw abonnements-id:

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   Wanneer het script probeert te verifiëren, wordt er een bericht weergegeven dat vergelijkbaar is met het volgende bericht in de terminal:

   > Als u zich wilt aanmelden, opent u de pagina https://microsoft.com/devicelogin in een webbrowser. Voer de code FGB56WJUGK in om te verifiëren.

   Zodra u zich hebt geverifieerd in de browser, wordt het script verder uitgevoerd.

   > [!NOTE]
   > Net als bij de eerste query zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd. De volgorde van de queryopdrachten is belangrijk. In dit voorbeeld komt `order by` na `limit`. Met deze opdracht worden de queryresultaten eerst beperkt en vervolgens gerangschikt.

1. Wijzig de eerste parameter in `index.js` en wijzig de query om eerst te `order by` op de eigenschap **Naam** en daarna de resultaten van de top vijf te `limit`. Vervang `<YOUR_SUBSCRIPTION_ID_LIST>` door uw abonnements-id:

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

Wanneer de laatste query meerdere keren wordt uitgevoerd, ervan uitgaande dat niets in uw omgeving verandert, zijn de geretourneerde resultaten consistent en gesorteerd op de eigenschap **Naam**, maar nog steeds beperkt tot de top vijf.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de geïnstalleerde bibliotheken uit uw toepassing wilt verwijderen, kunt u dit doen met de volgende opdracht.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de Resource Graph-bibliotheken toegevoegd aan uw JavaScript-omgeving en uw eerste query uitgevoerd. Ga door naar de pagina met details van de querytaal voor meer informatie over de taal van Resource Graph.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](./concepts/query-language.md)