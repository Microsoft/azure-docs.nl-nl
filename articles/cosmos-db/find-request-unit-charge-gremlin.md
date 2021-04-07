---
title: De kosten voor de aanvraag eenheid (RU) zoeken voor Gremlin API-query's in Azure Cosmos DB
description: Meer informatie over het vinden van de koppelings kosten van de aanvraag eenheid voor Gremlin query's die worden uitgevoerd op een Azure Cosmos-container. U kunt de Azure Portal, .NET, Java-Stuur Programma's gebruiken om de RU-kosten te vinden.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 295ed3eb2312a5f614a4214b5b627f5657fba1eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201342"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>De toeslag voor aanvraag eenheden zoeken voor bewerkingen die zijn uitgevoerd in Azure Cosmos DB Gremlin-API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB ondersteunt veel Api's, zoals SQL, MongoDB, Cassandra, Gremlin en Table. Elke API heeft een eigen set database bewerkingen. Deze bewerkingen variëren van eenvoudig punt Lees-en schrijf bewerkingen naar complexe query's. Bij elke database bewerking worden systeem resources verbruikt op basis van de complexiteit van de bewerking.

De kosten van alle databasebewerkingen worden genormaliseerd door Azure Cosmos DB en uitgedrukt in aanvraageenheden (kortweg RU's). Kosten voor aanvragen zijn de aanvraag eenheden die worden verbruikt door alle database bewerkingen. U kunt RUs beschouwen als een prestatie valuta die de systeem bronnen aanabstractt, zoals CPU, IOPS en geheugen die nodig zijn om de database bewerkingen uit te voeren die door Azure Cosmos DB worden ondersteund. Ongeacht welke API u gebruikt om met uw Azure Cosmos-container te communiceren - de kosten worden altijd gemeten in RU's. Of de database bewerking een schrijven, een lees punt of een query is, worden de kosten altijd gemeten in RUs. Zie voor meer informatie het artikel [aanvraag eenheden en overwegingen](request-units.md) .

In dit artikel worden de verschillende manieren beschreven waarop u het gebruik van de [aanvraag eenheid](request-units.md) (ru) kunt vinden voor elke bewerking die wordt uitgevoerd op basis van een container in azure Cosmos DB GREMLIN-API. Als u een andere API gebruikt, raadpleegt u [API voor MongoDb](find-request-unit-charge-mongodb.md), [CASSANDRA-API](find-request-unit-charge-cassandra.md), [SQL API](find-request-unit-charge.md)en [Table-API](find-request-unit-charge-table.md) artikelen om de kosten voor de ru/s te vinden.

Headers die door de Gremlin-API worden geretourneerd, worden toegewezen aan aangepaste status kenmerken, die momenteel worden opgehaald door de Gremlin .NET en Java SDK. De aanvraag kosten zijn beschikbaar onder de- `x-ms-request-charge` sleutel. Wanneer u de Gremlin-API gebruikt, hebt u meerdere opties voor het vinden van het RU-verbruik voor een bewerking in een Azure Cosmos-container.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-graph-gremlin-console.md#create-a-database-account) en voer het in met gegevens of selecteer een bestaand account dat al gegevens bevat.

1. Ga naar het deel venster **Data Explorer** en selecteer vervolgens de container waaraan u wilt werken.

1. Geef een geldige query op en selecteer vervolgens **Gremlin query uitvoeren**.

1. Selecteer **query statistieken** om de werkelijke aanvraag kosten weer te geven voor de aanvraag die u hebt uitgevoerd.

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="Scherm afbeelding voor het verkrijgen van een Gremlin-query aanvraag kosten in de Azure Portal":::

## <a name="use-the-net-sdk-driver"></a>Het .NET SDK-stuur programma gebruiken

Wanneer u de [Gremlin.NET-SDK](https://www.nuget.org/packages/Gremlin.Net/)gebruikt, zijn status kenmerken beschikbaar onder de `StatusAttributes` eigenschap van `ResultSet<>` het object:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Zie [Quick Start: een .NET Framework-of kern toepassing bouwen met behulp van een Azure Cosmos DB Gremlin-API-account](create-graph-dotnet.md)voor meer informatie.

## <a name="use-the-java-sdk-driver"></a>Het Java SDK-stuur programma gebruiken

Wanneer u de [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)gebruikt, kunt u status kenmerken ophalen door de `statusAttributes()` methode aan te roepen voor het `ResultSet` object:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Zie [Quick Start: Create a Graph data base in azure Cosmos DB met behulp van de Java-SDK](create-graph-java.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het optimaliseren van uw RU-verbruik:

* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)
* [Kosten voor ingerichte doorvoer optimaliseren in Azure Cosmos DB](optimize-cost-throughput.md)
* [Kosten van query's optimaliseren in Azure Cosmos DB](./optimize-cost-reads-writes.md)