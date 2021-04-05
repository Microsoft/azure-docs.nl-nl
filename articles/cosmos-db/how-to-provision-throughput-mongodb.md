---
title: Door Voer in te richten op Azure Cosmos DB-API voor MongoDB-resources
description: Meer informatie over het inrichten van de door Voer van containers, data bases en automatisch schalen in Azure Cosmos DB-API voor MongoDB-resources. U gebruikt Azure Portal, CLI, Power shell en diverse andere Sdk's.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 25eaa4a66fb4a73f976edbd30e6f82015ce84f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93086122"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>Data Base, container of automatisch schalen door Voer in te richten op Azure Cosmos DB-API voor MongoDB-resources
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In dit artikel wordt uitgelegd hoe u de door Voer inricht in Azure Cosmos DB-API voor MongoDB. U kunt standaard (hand matig) of door Voer voor automatisch schalen inrichten voor een container of een Data Base en deze delen tussen de containers in de-data base. U kunt de door Voer inrichten met Azure Portal, Azure CLI of Azure Cosmos DB Sdk's.

Als u een andere API gebruikt, raadpleegt u [SQL API](how-to-provision-container-throughput.md), [CASSANDRA-API](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) -artikelen om de door Voer in te richten.

## <a name="azure-portal"></a><a id="portal-mongodb"></a> Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-mongodb-dotnet.md#create-a-database-account)of selecteer een bestaand Azure Cosmos-account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe verzameling**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt. Selecteer de optie **doorvoer database inrichten** als u de door voer wilt inrichten op database niveau.
   * Voer een verzamelings-id in.
   * Voer een waarde voor de partitiesleutel in (bijvoorbeeld `/ItemID`).
   * Voer een door Voer in die u wilt inrichten (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="Scherm opname van Data Explorer bij het maken van een nieuwe verzameling met een doorvoer capaciteit op database niveau":::

> [!Note]
> Als u de door Voer inricht voor een container in een Azure Cosmos-account dat is geconfigureerd met de Azure Cosmos DB-API voor MongoDB, gebruikt u `/myShardKey` voor het pad van de partitie sleutel.

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> .NET-SDK

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager sjablonen kunnen worden gebruikt voor het inrichten van de door Voer voor automatisch schalen op Data Base-of container niveau-resources voor alle Azure Cosmos DB-Api's. Zie [Azure Resource Manager sjablonen voor Azure Cosmos DB](templates-samples-mongodb.md) voor voor beelden.

## <a name="azure-cli"></a>Azure CLI

Azure CLI kan worden gebruikt voor het inrichten van de door Voer voor automatisch schalen op een Data Base of op container niveau voor alle Azure Cosmos DB-Api's. Zie Azure CLI-voor beelden [voor Azure Cosmos DB](cli-samples-mongodb.md)voor meer informatie.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kan worden gebruikt voor het inrichten van de door Voer voor automatisch schalen op een Data Base of op container niveau resources voor alle Azure Cosmos DB-Api's. Zie [Azure PowerShell voor beelden voor Azure Cosmos DB](powershell-samples-mongodb.md)voor steek proeven.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het inrichten van doorvoer in Azure Cosmos DB:

* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)