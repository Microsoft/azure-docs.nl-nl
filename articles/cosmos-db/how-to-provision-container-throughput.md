---
title: Container doorvoer inrichten in Azure Cosmos DB SQL-API
description: Meer informatie over het inrichten van de door Voer op het niveau van de container in Azure Cosmos DB SQL-API met behulp van Azure Portal, CLI, Power shell en diverse andere Sdk's.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4caf43cb972b44dd1482b9e6e467e41cae294708
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100096"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>Standaard doorvoer (hand matig) door Voer voor een Azure Cosmos-container-SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel wordt uitgelegd hoe u de standaard doorvoer (hand matig) inricht voor een container in Azure Cosmos DB SQL-API. U kunt de door Voer voor één container inrichten of [door Voer in te richten op een Data Base](how-to-provision-database-throughput.md) en deze te delen tussen de containers in de data base. U kunt de door Voer voor een container inrichten met behulp van Azure Portal, Azure CLI of Azure Cosmos DB Sdk's.

Als u een andere API gebruikt, raadpleegt u [API voor MongoDb](how-to-provision-throughput-mongodb.md), [CASSANDRA-API](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) -artikelen om de door Voer in te richten.

## <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account)of selecteer een bestaand Azure Cosmos-account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe container**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt.
   * Voer een container-ID in.
   * Voer een waarde voor de partitiesleutel in (bijvoorbeeld `/ItemID`).
   * Voer een door Voer in die u wilt inrichten (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="Schermopname van Data Explorer met Nieuwe verzameling gemarkeerd":::

## <a name="azure-cli-or-powershell"></a>Azure CLI of Power shell

Als u een container met specifieke door voer wilt maken, raadpleegt u

* [Een container maken met behulp van Azure CLI](manage-with-cli.md#create-a-container)
* [Een container maken met behulp van Power shell](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Gebruik de Cosmos Sdk's voor SQL API om de door Voer voor alle Cosmos DB Api's in te richten, behalve de Cassandra-en MongoDB-API.

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het inrichten van doorvoer in Azure Cosmos DB:

* [Standaard doorvoer (hand matig) inrichten voor een Data Base](how-to-provision-database-throughput.md)
* [De door Voer van automatisch schalen inrichten voor een Data Base](how-to-provision-autoscale-throughput.md)
* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)
