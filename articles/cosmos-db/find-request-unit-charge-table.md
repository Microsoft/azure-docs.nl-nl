---
title: De kosten van de aanvraag eenheid (RU) zoeken voor een Table-API query's in Azure Cosmos DB
description: Meer informatie over het vinden van de kosten voor de aanvraag eenheid (RU) voor Table-API query's die worden uitgevoerd op een Azure Cosmos-container. U kunt de Azure Portal, .NET, Java, python en Node.js talen gebruiken om de RU-kosten te vinden.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 0c3f2495630358f48900a9cf27cbc0feb4192e1d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201315"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>De kosten voor de aanvraag eenheid zoeken voor bewerkingen die zijn uitgevoerd in Azure Cosmos DB Table-API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB ondersteunt veel Api's, zoals SQL, MongoDB, Cassandra, Gremlin en Table. Elke API heeft een eigen set database bewerkingen. Deze bewerkingen variëren van eenvoudig punt Lees-en schrijf bewerkingen naar complexe query's. Bij elke database bewerking worden systeem resources verbruikt op basis van de complexiteit van de bewerking.

De kosten van alle databasebewerkingen worden genormaliseerd door Azure Cosmos DB en uitgedrukt in aanvraageenheden (kortweg RU's). Kosten voor aanvragen zijn de aanvraag eenheden die worden verbruikt door alle database bewerkingen. U kunt RUs beschouwen als een prestatie valuta die de systeem bronnen aanabstractt, zoals CPU, IOPS en geheugen die nodig zijn om de database bewerkingen uit te voeren die door Azure Cosmos DB worden ondersteund. Ongeacht welke API u gebruikt om met uw Azure Cosmos-container te communiceren - de kosten worden altijd gemeten in RU's. Of de database bewerking een schrijven, een lees punt of een query is, worden de kosten altijd gemeten in RUs. Zie voor meer informatie het artikel [aanvraag eenheden en overwegingen](request-units.md) .

In dit artikel worden de verschillende manieren beschreven waarop u het verbruik van de [aanvraag eenheid](request-units.md) (ru) kunt vinden voor elke bewerking die wordt uitgevoerd op een container in azure Cosmos db table-API. Als u een andere API gebruikt, raadpleegt u [API voor MongoDb](find-request-unit-charge-mongodb.md), [CASSANDRA-API](find-request-unit-charge-cassandra.md), [Gremlin API](find-request-unit-charge-gremlin.md)en [SQL API](find-request-unit-charge.md) -artikelen om de kosten voor de ru/s te vinden.

## <a name="use-the-net-sdk"></a>De .NET SDK gebruiken

Op dit moment is de enige SDK die de RU-kosten voor tabel bewerkingen retourneert, de [.NET Standard-SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Het `TableResult` object beschrijft een `RequestCharge` eigenschap die door de SDK wordt ingevuld wanneer u deze gebruikt voor de Azure Cosmos db table-API:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Zie [Quick Start: een Table-API-app bouwen met behulp van de .NET SDK en Azure Cosmos DB](create-table-dotnet.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het optimaliseren van uw RU-verbruik:

* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)
* [Kosten voor ingerichte doorvoer optimaliseren in Azure Cosmos DB](optimize-cost-throughput.md)
* [Kosten van query's optimaliseren in Azure Cosmos DB](./optimize-cost-reads-writes.md)