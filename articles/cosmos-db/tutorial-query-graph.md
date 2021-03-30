---
title: Grafiekgegevens opvragen in Azure Cosmos DB
description: Meer informatie over het uitvoeren van query's op grafiekgegevens uit Azure Cosmos DB met behulp van Gremlin query's
author: christopheranderson
ms.author: chrande
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: tutorial
ms.date: 12/03/2018
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 545ffd303d2039a3c54088220c1fa74e742c750f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360765"
---
# <a name="tutorial-query-azure-cosmos-db-gremlin-api-by-using-gremlin"></a>Zelfstudie: Azure Cosmos DB Gremlin-API opvragen met Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

De Azure Cosmos DB [Gremlin-API](graph-introduction.md) ondersteunt [Gremlin](https://github.com/tinkerpop/gremlin/wiki)-query's. Dit artikel bevat voorbeelddocumenten en query's waarmee u direct aan de slag kunt. Een gedetailleerde Gremlin-verwijzing vindt u in het artikel [Gremlin-ondersteuning](gremlin-support.md).

Dit artikel behandelt de volgende taken: 

> [!div class="checklist"]
> * Gegevens opvragen met behulp van Gremlin

## <a name="prerequisites"></a>Vereisten

Deze query's werken alleen als u een Azure DB Cosmos DB-account hebt en een container met grafiekgegevens. Hebt u geen van beide? Voltooi de [Quickstart van 5 minuten](create-graph-dotnet.md) of de [zelfstudie voor ontwikkelaars](tutorial-query-graph.md) om een account te maken en uw database te vullen. U kunt de volgende query's uitvoeren met behulp van de [Gremlin-console](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) of uw favoriete Gremlin-stuurprogramma.

## <a name="count-vertices-in-the-graph"></a>Hoekpunten in de grafiek tellen

In het volgende fragment ziet u hoe u het aantal hoekpunten in de grafiek telt:

```
g.V().count()
```

## <a name="filters"></a>Filters

U kunt filters toepassen met behulp van de stappen `has` en `hasLabel` en deze combineren met `and`, `or` en `not` om complexere filters samen te stellen. Azure Cosmos DB biedt schema-agnostische indexering van alle eigenschappen in uw hoekpunten en graden voor snelle query's:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projectie

U kunt bepaalde eigenschappen in de queryresultaten projecteren met de stap `values`:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Verwante randen en hoekpunten zoeken

Tot nu toe hebt u alleen query's gezien die in elke database werken. Grafieken zijn snel en efficiënt voor overdrachtbewerkingen wanneer u naar gerelateerde randen en hoekpunten moet navigeren. We gaan alle vrienden van Thomas zoeken. Daarvoor gebruiken we stap `outE` van Gremlin om alle uitgaande randen van Thomas te zoeken en vervolgens vanuit die randen de inkomende hoekpunten te onderzoeken met behulp van stap `inV` van Gremlin:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

Met de volgende query worden twee hops uitgevoerd om alle 'vrienden van vrienden' van Thomas te vinden, door `outE` en `inV` twee keer aan te roepen. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

U kunt complexere query's maken en krachtige logica voor grafiekdoorkruising implementeren met Gremlin, met inbegrip van het combineren van filterexpressies, het uitvoeren van lusconstructies met stap `loop` en de implementatie van voorwaardelijke navigatie met stap `choose`. Meer informatie over wat u kunt doen kunt [Gremlin-ondersteuning](gremlin-support.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * U hebt geleerd hoe u een query uitvoert met behulp van Graph 

U kunt nu doorgaan naar de sectie Concepten voor meer informatie over Cosmos DB.

> [!div class="nextstepaction"]
> [Wereldwijde distributie](distribute-data-globally.md) 

