---
title: Een container maken in Azure Cosmos DB SQL-API
description: Meer informatie over het maken van een container in Azure Cosmos DB SQL-API met behulp van Azure Portal, .NET, Java, Python, Node.js en andere Sdk's.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 302c5d6e8e523a11b8773f10bb6089e3bea09bdd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006845"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Een container maken in Azure Cosmos DB SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel worden de verschillende manieren beschreven voor het maken van een container in Azure Cosmos DB SQL-API. U ziet hoe u een container maakt met behulp van de Azure Portal, Azure CLI, Power shell of ondersteunde Sdk's. In dit artikel ziet u hoe u een container maakt, de partitiesleutel opgeeft en doorvoer inricht.

In dit artikel worden de verschillende manieren beschreven voor het maken van een container in Azure Cosmos DB SQL-API. Als u een andere API gebruikt, raadpleegt u [API voor MongoDb](how-to-create-container-mongodb.md), [CASSANDRA-API](how-to-create-container-cassandra.md), [Gremlin API](how-to-create-container-gremlin.md)en [Table-APIe](how-to-create-container-table.md) artikelen om de container te maken.

> [!NOTE]
> Wanneer u containers maakt, moet u ervoor zorgen dat u niet twee containers maakt met dezelfde naam, maar met een ander hoofdletter gebruik. Dat komt omdat sommige onderdelen van het Azure-platform niet hoofdletter gevoelig zijn. Dit kan leiden tot Verwar ring/botsing van telemetriegegevens en acties op containers met dergelijke namen.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Een container maken met behulp van de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account)of selecteer een bestaand account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe container**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt.
   * Voer een container-ID in.
   * Voer een partitiesleutel in.
   * Geef een door Voer op die moet worden ingericht (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Scherm afbeelding van Data Explorer deel venster, met nieuwe container gemarkeerd":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Een container maken met behulp van Azure CLI

[Maak een container met Azure cli](manage-with-cli.md#create-a-container). Zie [Azure CLI-voor beelden voor Azure Cosmos DB](cli-samples.md)voor een overzicht van alle Azure CLI-voor beelden in alle Azure Cosmos DB api's.

## <a name="create-a-container-using-powershell"></a>Een container maken met behulp van Power shell

[Maak een container met Power shell](manage-with-powershell.md#create-container). Zie [Power shell](powershell-samples.md) -voor beelden voor een lijst met alle Power shell-voor beelden in alle Azure Cosmos DB api's

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>Een container maken met behulp van .NET SDK

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

## <a name="next-steps"></a>Volgende stappen

* [Partitionering in Azure Cosmos DB](partitioning-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Werken met een Azure Cosmos-account](./account-databases-containers-items.md)