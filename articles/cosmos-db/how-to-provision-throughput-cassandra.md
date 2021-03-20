---
title: Door Voer in te richten op Azure Cosmos DB Cassandra-API resources
description: Meer informatie over het inrichten van de door Voer van containers, data bases en automatisch schalen in Azure Cosmos DB Cassandra-API resources. U gebruikt Azure Portal, CLI, Power shell en diverse andere Sdk's.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 63b633ed67c03a006a154bc69a1aafb4cb4aa6d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93086275"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>De door Voer van data base, container of automatisch schalen op Azure Cosmos DB Cassandra-API resources inrichten
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

In dit artikel wordt uitgelegd hoe u de door Voer inricht in Azure Cosmos DB Cassandra-API. U kunt standaard (hand matig) of door Voer voor automatisch schalen inrichten voor een container of een Data Base en deze delen tussen de containers in de-data base. U kunt de door Voer inrichten met Azure Portal, Azure CLI of Azure Cosmos DB Sdk's.

Als u een andere API gebruikt, raadpleegt u [SQL API](how-to-provision-container-throughput.md), [API for MONGODB](how-to-provision-throughput-mongodb.md), [Gremlin API](how-to-provision-throughput-gremlin.md) articles (Engelstalig) om de door Voer in te richten.

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-mongodb-dotnet.md#create-a-database-account)of selecteer een bestaand Azure Cosmos-account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe tabel**. Geef de volgende gegevens op:

   * Geef aan of u een nieuwe spatie maakt of een bestaand gebruikt. Selecteer de optie **doorvoer database inrichten** als u de door voer wilt inrichten op het niveau van de opslag ruimte.
   * Voer de tabel-ID in de CQL-opdracht in.
   * Voer een waarde voor de primaire sleutel in (bijvoorbeeld `/userrID` ).
   * Voer een door Voer in die u wilt inrichten (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="Scherm opname van Data Explorer bij het maken van een nieuwe verzameling met een doorvoer capaciteit op database niveau":::

> [!Note]
> Als u de door Voer inricht voor een container in een Azure Cosmos-account dat is geconfigureerd met Cassandra-API, gebruikt u `/myPrimaryKey` voor het pad van de partitie sleutel.

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> .NET-SDK

### <a name="provision-throughput-for-a-cassandra-table"></a>Door Voer van de inrichting voor een Cassandra-tabel

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
Vergelijk bare opdrachten kunnen worden uitgegeven via elk CQL-compatibel stuur programma.

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>De door Voer voor een Cassandra-tabel wijzigen of wijzigen

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

Een vergelijk bare opdracht kan worden uitgevoerd via elk CQL-compatibel stuur programma.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager sjablonen kunnen worden gebruikt voor het inrichten van de door Voer voor automatisch schalen op Data Base-of container niveau-resources voor alle Azure Cosmos DB-Api's. Zie [Azure Resource Manager sjablonen voor Azure Cosmos DB](templates-samples-cassandra.md) voor voor beelden.

## <a name="azure-cli"></a>Azure CLI

Azure CLI kan worden gebruikt voor het inrichten van de door Voer voor automatisch schalen op een Data Base of op container niveau voor alle Azure Cosmos DB-Api's. Zie Azure CLI-voor beelden [voor Azure Cosmos DB](cli-samples-cassandra.md)voor meer informatie.

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kan worden gebruikt voor het inrichten van de door Voer voor automatisch schalen op een Data Base of op container niveau resources voor alle Azure Cosmos DB-Api's. Zie [Azure PowerShell voor beelden voor Azure Cosmos DB](powershell-samples-cassandra.md)voor steek proeven.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het inrichten van doorvoer in Azure Cosmos DB:

* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)