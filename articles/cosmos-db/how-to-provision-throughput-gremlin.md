---
title: Door Voer in te richten op Azure Cosmos DB Gremlin API-resources
description: Meer informatie over het inrichten van de door Voer van containers, data bases en automatisch schalen in Azure Cosmos DB Gremlin API-resources. U gebruikt Azure Portal, CLI, Power shell en diverse andere Sdk's.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 3c2af7f33135a8c6621db233451231ffa89c2d64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93086156"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>Data Base, container of door Voer voor automatisch schalen inrichten op Azure Cosmos DB Gremlin API-resources
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

In dit artikel wordt uitgelegd hoe u de door Voer inricht in Azure Cosmos DB Gremlin-API. U kunt standaard (hand matig) of door Voer voor automatisch schalen inrichten voor een container of een Data Base en deze delen tussen de containers in de-data base. U kunt de door Voer inrichten met Azure Portal, Azure CLI of Azure Cosmos DB Sdk's.

Als u een andere API gebruikt, raadpleegt u [SQL API](how-to-provision-container-throughput.md), [Cassandra-API](how-to-provision-throughput-cassandra.md), [API for MongoDb](how-to-provision-throughput-mongodb.md) -artikelen om de door Voer in te richten.

## <a name="azure-portal"></a><a id="portal-gremlin"></a> Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-mongodb-dotnet.md#create-a-database-account)of selecteer een bestaand Azure Cosmos-account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe grafiek**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt. Selecteer de optie **doorvoer database inrichten** als u de door voer wilt inrichten op database niveau.
   * Voer een grafiek-id in.
   * Voer een waarde voor de partitiesleutel in (bijvoorbeeld `/ItemID`).
   * Voer een door Voer in die u wilt inrichten (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="Scherm opname van Data Explorer bij het maken van een nieuwe grafiek met data base-niveau doorvoer":::

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Gebruik de Cosmos Sdk's voor SQL API om de door Voer voor alle Azure Cosmos DB Api's in te richten, behalve de Cassandra-en MongoDB-API.

### <a name="provision-container-level-throughput"></a>Door Voer op container niveau inrichten

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

### <a name="provision-database-level-throughput"></a>Door Voer op database niveau inrichten

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager sjablonen kunnen worden gebruikt voor het inrichten van de door Voer voor automatisch schalen op Data Base-of container niveau-resources voor alle Azure Cosmos DB-Api's. Zie [Azure Resource Manager sjablonen voor Azure Cosmos DB](templates-samples-gremlin.md) voor voor beelden.

## <a name="azure-cli"></a>Azure CLI

Azure CLI kan worden gebruikt voor het inrichten van de door Voer voor automatisch schalen op een Data Base of op container niveau voor alle Azure Cosmos DB-Api's. Zie Azure CLI-voor beelden [voor Azure Cosmos DB](cli-samples-gremlin.md)voor meer informatie.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kan worden gebruikt voor het inrichten van de door Voer voor automatisch schalen op een Data Base of op container niveau resources voor alle Azure Cosmos DB-Api's. Zie [Azure PowerShell voor beelden voor Azure Cosmos DB](powershell-samples-gremlin.md)voor steek proeven.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het inrichten van doorvoer in Azure Cosmos DB:

* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)