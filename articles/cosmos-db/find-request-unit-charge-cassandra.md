---
title: De kosten van de aanvraag eenheid (RU) zoeken voor een Cassandra-API query in Azure Cosmos DB
description: Meer informatie over het vinden van de koppelings kosten van de aanvraag eenheid voor Cassandra query's die worden uitgevoerd op een Azure Cosmos-container. U kunt de Azure Portal-, .NET-en Java-Stuur Programma's gebruiken om de RU-kosten te vinden.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: b504702ad0c74ae9728c0a8b34fa94df26184f70
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200514"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>De kosten voor de aanvraag eenheid zoeken voor bewerkingen die zijn uitgevoerd in Azure Cosmos DB Cassandra-API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB ondersteunt veel Api's, zoals SQL, MongoDB, Cassandra, Gremlin en Table. Elke API heeft een eigen set database bewerkingen. Deze bewerkingen variëren van eenvoudig punt Lees-en schrijf bewerkingen naar complexe query's. Bij elke database bewerking worden systeem resources verbruikt op basis van de complexiteit van de bewerking.

De kosten van alle databasebewerkingen worden genormaliseerd door Azure Cosmos DB en uitgedrukt in aanvraageenheden (kortweg RU's). Kosten voor aanvragen zijn de aanvraag eenheden die worden verbruikt door alle database bewerkingen. U kunt RUs beschouwen als een prestatie valuta die de systeem bronnen aanabstractt, zoals CPU, IOPS en geheugen die nodig zijn om de database bewerkingen uit te voeren die door Azure Cosmos DB worden ondersteund. Ongeacht welke API u gebruikt om met uw Azure Cosmos-container te communiceren - de kosten worden altijd gemeten in RU's. Of de database bewerking een schrijven, een lees punt of een query is, worden de kosten altijd gemeten in RUs. Zie voor meer informatie het artikel [aanvraag eenheden en overwegingen](request-units.md) .

In dit artikel worden de verschillende manieren beschreven waarop u het verbruik van de [aanvraag eenheid](request-units.md) (ru) kunt vinden voor elke bewerking die wordt uitgevoerd op een container in azure Cosmos DB Cassandra-API. Als u een andere API gebruikt, raadpleegt u [API voor MongoDb](find-request-unit-charge-mongodb.md), [SQL API](find-request-unit-charge.md), [Gremlin-API](find-request-unit-charge-gremlin.md)en [Table-API](find-request-unit-charge-table.md) artikelen om de kosten voor de ru/s te vinden.

Wanneer u bewerkingen uitvoert op de Azure Cosmos DB Cassandra-API, wordt de RU-kosten geretourneerd in de binnenkomende nettolading als een veld met de naam `RequestCharge` . U hebt meerdere mogelijkheden voor het ophalen van de RU-kosten.

## <a name="use-the-net-sdk"></a>De .NET SDK gebruiken

Wanneer u de [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)gebruikt, kunt u de binnenkomende Payload ophalen onder de `Info` eigenschap van een `RowSet` object:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Zie [Quick Start: een Cassandra-app bouwen met behulp van de .NET SDK en Azure Cosmos DB](create-cassandra-dotnet.md)voor meer informatie.

## <a name="use-the-java-sdk"></a>De Java-SDK gebruiken

Wanneer u de [Java-SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)gebruikt, kunt u de binnenkomende Payload ophalen door de `getExecutionInfo()` methode aan te roepen voor een `ResultSet` object:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Zie [Quick Start: een Cassandra-app bouwen met behulp van de Java-SDK en Azure Cosmos DB](create-cassandra-java.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het optimaliseren van uw RU-verbruik:

* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)
* [Kosten voor ingerichte doorvoer optimaliseren in Azure Cosmos DB](optimize-cost-throughput.md)
* [Kosten van query's optimaliseren in Azure Cosmos DB](./optimize-cost-reads-writes.md)