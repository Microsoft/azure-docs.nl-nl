---
title: Een container maken in Azure Cosmos DB Gremlin-API
description: Meer informatie over het maken van een container in Azure Cosmos DB Gremlin API met behulp van Azure Portal, .NET en andere Sdk's.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: f7e9de1f23ec46af08fe96b5db3170fac9a7eb2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101626"
---
# <a name="create-a-container-in-azure-cosmos-db-gremlin-api"></a>Een container maken in Azure Cosmos DB Gremlin-API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

In dit artikel worden de verschillende manieren beschreven voor het maken van een container in Azure Cosmos DB Gremlin API. U ziet hoe u een container maakt met behulp van Azure Portal, Azure CLI, Power shell of ondersteunde Sdk's. In dit artikel ziet u hoe u een container maakt, de partitiesleutel opgeeft en doorvoer inricht.

In dit artikel worden de verschillende manieren beschreven voor het maken van een container in Azure Cosmos DB Gremlin API. Als u een andere API gebruikt, raadpleegt u [API voor MongoDb](how-to-create-container-mongodb.md), [Cassandra-API](how-to-create-container-cassandra.md), [Table-API](how-to-create-container-table.md)en [SQL API](how-to-create-container.md) -artikelen om de container te maken.

> [!NOTE]
> Wanneer u containers maakt, moet u ervoor zorgen dat u niet twee containers maakt met dezelfde naam, maar met een ander hoofdletter gebruik. Dat komt omdat sommige onderdelen van het Azure-platform niet hoofdletter gevoelig zijn. Dit kan leiden tot Verwar ring/botsing van telemetriegegevens en acties op containers met dergelijke namen.

## <a name="create-using-azure-portal"></a><a id="portal-gremlin"></a>Maken met behulp van de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-graph-dotnet.md#create-a-database-account)of selecteer een bestaand account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe grafiek**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt.
   * Voer een grafiek-ID in.
   * Selecteer **onbeperkte** opslagcapaciteit.
   * Voer een partitiesleutel in voor hoekpunten.
   * Geef een door Voer op die moet worden ingericht (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Schermopname van Gremlin-API, dialoogvenster Grafiek toevoegen":::

## <a name="create-using-net-sdk"></a><a id="dotnet-sql-graph"></a>Maken met behulp van .NET SDK

Als er een time-outuitzondering optreedt bij het maken van een verzameling, moet u een lees bewerking uitvoeren om te controleren of de verzameling is gemaakt. Met de Lees bewerking wordt een uitzonde ring gegenereerd totdat de bewerking voor het maken van de verzameling is voltooid. Zie de [HTTP-status codes voor Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artikel voor een lijst met status codes die door de maak bewerking worden ondersteund.

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Maken met behulp van Azure CLI

[Maak een Gremlin-grafiek met Azure cli](./scripts/cli/gremlin/create.md). Zie [Azure CLI-voor beelden voor Azure Cosmos DB](cli-samples.md)voor een overzicht van alle Azure CLI-voor beelden in alle Azure Cosmos DB api's.

## <a name="create-using-powershell"></a>Maken met PowerShell

[Maak een Gremlin-grafiek met Power shell](./scripts/powershell/gremlin/create.md). Zie [Power shell](powershell-samples.md) -voor beelden voor een lijst met alle Power shell-voor beelden in alle Azure Cosmos DB api's

## <a name="next-steps"></a>Volgende stappen

* [Partitionering in Azure Cosmos DB](partitioning-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Werken met een Azure Cosmos-account](./account-databases-containers-items.md)