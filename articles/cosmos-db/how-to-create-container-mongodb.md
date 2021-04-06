---
title: Een container maken in Azure Cosmos DB-API voor MongoDB
description: Meer informatie over het maken van een container in Azure Cosmos DB-API voor MongoDB met behulp van Azure Portal, .NET, Java, Node.js en andere Sdk's.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a5669b15c041f663605a62ef8d02b206928d0c14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101592"
---
# <a name="create-a-container-in-azure-cosmos-db-api-for-mongodb"></a>Een container maken in Azure Cosmos DB-API voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In dit artikel worden de verschillende manieren beschreven voor het maken van een container in Azure Cosmos DB-API voor MongoDB. U ziet hoe u een container maakt met behulp van Azure Portal, Azure CLI, Power shell of ondersteunde Sdk's. In dit artikel ziet u hoe u een container maakt, de partitiesleutel opgeeft en doorvoer inricht.

In dit artikel worden de verschillende manieren beschreven voor het maken van een container in Azure Cosmos DB-API voor MongoDB. Als u een andere API gebruikt, raadpleegt u [SQL API](how-to-create-container.md), [CASSANDRA-API](how-to-create-container-cassandra.md), [Gremlin API](how-to-create-container-gremlin.md)en [Table-API](how-to-create-container-table.md) artikelen om de container te maken.

> [!NOTE]
> Wanneer u containers maakt, moet u ervoor zorgen dat u niet twee containers maakt met dezelfde naam, maar met een ander hoofdletter gebruik. Dat komt omdat sommige onderdelen van het Azure-platform niet hoofdletter gevoelig zijn. Dit kan leiden tot Verwar ring/botsing van telemetriegegevens en acties op containers met dergelijke namen.

## <a name="create-using-azure-portal"></a><a id="portal-mongodb"></a>Maken met behulp van de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-mongodb-dotnet.md#create-a-database-account)of selecteer een bestaand account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe container**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe database maakt of een bestaande database gebruikt.
   * Voer een container-ID in.
   * Voer een shardsleutel in.
   * Geef een door Voer op die moet worden ingericht (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Scherm opname van Azure Cosmos DB-API voor MongoDB, container toevoegen dialoog venster":::

## <a name="create-using-net-sdk"></a><a id="dotnet-mongodb"></a>Maken met behulp van .NET SDK

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Het MongoDB-draad protocol begrijpt het concept van [aanvraag eenheden](request-units.md)niet. Als u een nieuwe verzameling wilt maken met een ingerichte door Voer, gebruikt u de Azure Portal-of Cosmos DB Sdk's voor SQL API.

Als er een time-outuitzondering optreedt bij het maken van een verzameling, moet u een lees bewerking uitvoeren om te controleren of de verzameling is gemaakt. Met de Lees bewerking wordt een uitzonde ring gegenereerd totdat de bewerking voor het maken van de verzameling is voltooid. Zie de [HTTP-status codes voor Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artikel voor een lijst met status codes die door de maak bewerking worden ondersteund.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Maken met behulp van Azure CLI

[Maak een verzameling voor Azure Cosmos DB voor de MongoDb-API met Azure cli](./scripts/cli/mongodb/create.md). Zie [Azure CLI-voor beelden voor Azure Cosmos DB](cli-samples.md)voor een overzicht van alle Azure CLI-voor beelden in alle Azure Cosmos DB api's.

## <a name="create-using-powershell"></a>Maken met PowerShell

[Maak een verzameling voor Azure Cosmos DB voor de MongoDb-API met Power shell](./scripts/powershell/mongodb/create.md). Zie [Power shell](powershell-samples.md) -voor beelden voor een lijst met alle Power shell-voor beelden in alle Azure Cosmos DB api's

## <a name="create-a-container-using-azure-resource-manager-templates"></a>Een container maken met behulp van Azure Resource Manager sjablonen

[Maak een verzameling voor Azure Cosmos DB voor de MongoDb-API met de Resource Manager-sjabloon](./manage-with-templates.md#azure-cosmos-account-with-standard-provisioned-throughput).

## <a name="next-steps"></a>Volgende stappen

* [Partitionering in Azure Cosmos DB](partitioning-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Werken met een Azure Cosmos-account](./account-databases-containers-items.md)