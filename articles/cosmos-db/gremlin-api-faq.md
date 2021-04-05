---
title: Veelgestelde vragen over de Gremlin-API in Azure Cosmos DB
description: Krijg antwoorden op veelgestelde vragen over de Gremlin-API in Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 0374886482b43eefe6df5e107fd4da371633099d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359775"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Veelgestelde vragen over de Gremlin-API in Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

In dit artikel vindt u antwoorden op enkele veelgestelde vragen over de Gremlin-API in Azure Cosmos DB.

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>De efficiëntie van Gremlin-query's evalueren

De **executionProfile ()** preview-stap kan worden gebruikt om een analyse van het uitvoerings plan voor query's te bieden. Deze stap moet aan het einde van een Gremlin-query worden toegevoegd, zoals wordt geïllustreerd door het volgende voor beeld:

**Query voorbeeld**

```
g.V('mary').out('knows').executionProfile()
```

**Voorbeelduitvoer**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

In de uitvoer van het bovenstaande profiel ziet u hoeveel tijd wordt besteed aan het verkrijgen van de object Vertex objecten, de rand objecten en de grootte van de werk gegevensset. Dit heeft betrekking op de standaardkosten metingen voor Azure Cosmos DB query's.

## <a name="other-frequently-asked-questions"></a>Andere veelgestelde vragen

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Hoe worden RU/s in rekening gebracht bij het uitvoeren van query's op een grafiek database?

Alle grafiek objecten, hoek punten en randen worden weer gegeven als JSON-documenten in de back-end. Omdat een Gremlin-query een of meer grafiek objecten tegelijk kan wijzigen, zijn de kosten die eraan zijn gekoppeld, direct gerelateerd aan de objecten, de randen die door de query worden verwerkt. Dit is hetzelfde proces dat Azure Cosmos DB voor alle andere Api's gebruikt. Zie [Aanvraageenheden in Azure Cosmos DB](request-units.md) voor meer informatie.

De RU-kosten zijn gebaseerd op de werk gegevensset van het Trans Port en niet de resultatenset. Als een query bijvoorbeeld gericht is op het verkrijgen van één hoek punt, maar er meer dan één ander object op de manier moet worden door lopen, worden de kosten gebaseerd op alle grafiek objecten die nodig zijn voor het berekenen van het ene resultaat hoekpunt.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Wat is de maximale schaal die een grafiek database kan hebben in Azure Cosmos DB Gremlin-API?

Azure Cosmos DB maakt gebruik van [horizontale partitionering](partitioning-overview.md) om de toename van opslag-en doorvoer vereisten automatisch te verhelpen. De maximale door Voer en opslag capaciteit van een werk belasting wordt bepaald door het aantal partities dat is gekoppeld aan een bepaalde container. Een Gremlin API-container heeft echter een specifieke set richt lijnen om een goede prestatie ervaring op schaal te garanderen. Zie [partitioneren in azure Cosmos DB](partitioning-overview.md) artikel voor meer informatie over partitionering en aanbevolen procedures.

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Voor C#/.net-ontwikkeling.-ontwikkeling moet ik het pakket micro soft. Azure. graphs of Gremlin.NET gebruiken?

Azure Cosmos DB Gremlin API maakt gebruik van de open source-Stuur Programma's als de belangrijkste connectors voor de service. De aanbevolen optie is daarom het gebruik [van Stuur Programma's die worden ondersteund door Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Hoe kan ik beveiligen tegen ininjectie aanvallen met behulp van Gremlin-Stuur Programma's?

De meeste systeem eigen Apache Tinkerpop Gremlin-Stuur Programma's bieden de mogelijkheid om een woorden lijst met para meters op te geven voor het uitvoeren van query's. Dit is een voor beeld van hoe u dit doet in [Gremlin.net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) en in [Gremlin-java script](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/main/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Waarom krijg ik de fout ' Gremlin-query compilatie fout: kan geen van de volgende methoden vinden '?

Azure Cosmos DB Gremlin API implementeert een subset van de functionaliteit die is gedefinieerd in de Gremlin-surface area. Zie [Gremlin-ondersteunings](gremlin-support.md) artikel voor ondersteunde stappen en meer informatie.

De beste oplossing is om de vereiste Gremlin-stappen opnieuw te schrijven met de ondersteunde functionaliteit, omdat alle essentiële Gremlin-stappen door Azure Cosmos DB worden ondersteund.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Waarom krijg ik de fout ' WebSocketException: de server heeft de status code ' 200 ' geretourneerd wanneer de status code ' 101 ' werd verwacht '?

Deze fout wordt waarschijnlijk veroorzaakt wanneer het verkeerde eind punt wordt gebruikt. Het eind punt dat deze fout genereert, heeft het volgende patroon:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Dit is het documenten eindpunt voor uw Graph-data base.  Het juiste eind punt dat moet worden gebruikt, is het Gremlin-eind punt dat de volgende indeling heeft:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Waarom krijg ik de fout ' RequestRateIsTooLarge '?

Deze fout betekent dat de toegewezen aanvraag eenheden per seconde niet voldoende zijn voor het leveren van de query. Deze fout wordt meestal weer gegeven wanneer u een query uitvoert waarmee alle hoek punten worden opgehaald:

```
// Query example:
g.V()
```

Met deze query wordt geprobeerd alle hoek punten op te halen uit de grafiek. Dit betekent dat de kosten van deze query gelijk zijn aan ten minste het aantal hoek punten in de voor waarden van RUs. De RU/s-instelling moet worden aangepast om deze query te verhelpen.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Waarom worden de verbindingen van mijn Gremlin-stuur programma uiteindelijk verwijderd?

Een Gremlin-verbinding wordt gemaakt via een WebSocket-verbinding. Hoewel WebSocket-verbindingen geen specifieke time to Live hebben, zal Azure Cosmos DB Gremlin-API na 30 minuten inactiviteit niet-actieve verbindingen beëindigen.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Waarom kan ik geen fluent API-aanroepen gebruiken in de systeem eigen Gremlin-Stuur Programma's?

Fluent API-aanroepen worden nog niet ondersteund door de Azure Cosmos DB Gremlin API. Voor Fluent-API-aanroepen is een interne indelings functie vereist die ondersteuning biedt voor byte code die momenteel niet wordt ondersteund door Azure Cosmos DB Gremlin-API. Om dezelfde reden wordt het meest recente Gremlin-JavaScript-stuur programma momenteel ook niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

* [Ondersteuning voor Azure Cosmos DB Gremlin wire-protocol](gremlin-support.md)
* Een Azure Cosmos DB Graph-data base maken, doorzoeken en er een query op uitvoeren met behulp van de [Gremlin-console](create-graph-gremlin-console.md)
