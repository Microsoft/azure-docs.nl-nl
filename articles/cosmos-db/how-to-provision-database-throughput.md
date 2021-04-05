---
title: Data Base-door Voer inrichten in Azure Cosmos DB SQL-API
description: Meer informatie over het inrichten van de door Voer op database niveau in Azure Cosmos DB SQL-API met behulp van Azure Portal, CLI, Power shell en diverse andere Sdk's.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4ecbee2260da735cd6ba74d3b9ffb55b4a652e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341991"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db---sql-api"></a>Standaard doorvoer (hand matig) door Voer voor een data base in Azure Cosmos DB-SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel wordt uitgelegd hoe u de standaard doorvoer (hand matig) inricht voor een data base in Azure Cosmos DB SQL-API. U kunt de doorvoer inrichten voor één [container](how-to-provision-container-throughput.md) of inrichten voor een database en de doorvoer delen met de containers in de database. Zie het artikel [aanvragen voor het inrichten van de doorvoer snelheid voor containers en data bases](set-throughput.md) voor meer informatie over het gebruik van container niveau en door Voer op database niveau. U kunt doorvoer op databaseniveau inrichten met behulp van de Azure-portal of Azure Cosmos DB-SDK's.

Als u een andere API gebruikt, raadpleegt u [API voor MongoDb](how-to-provision-throughput-mongodb.md), [CASSANDRA-API](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) -artikelen om de door Voer in te richten.

## <a name="provision-throughput-using-azure-portal"></a>Doorvoer inrichten met behulp van de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account)of selecteer een bestaand Azure Cosmos-account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe data base**. Geef de volgende gegevens op:

   * Voer een database-id in.
   * Selecteer de optie **doorvoer database inrichten** .
   * Voer een doorvoer in, bijvoorbeeld 1000 RU's.
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-sql-api.png" alt-text="Schermafbeelding van het dialoogvenster Nieuwe database":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Door Voer in te richten met behulp van Azure CLI of Power shell

Als u een Data Base met gedeelde door voer wilt maken, raadpleegt u

* [Een Data Base maken met behulp van Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Een database maken met PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Doorvoer inrichten met behulp van .NET SDK

> [!Note]
> U kunt Azure Cosmos Sdk's voor SQL API gebruiken om de door Voer voor alle Api's in te richten. U kunt ook het volgende voorbeeld voor Cassandra-API gebruiken.

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

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over ingerichte door Voer in Azure Cosmos DB:

* [Wereldwijd schalen van ingerichte doorvoer](./request-units.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Standaard doorvoer (hand matig) voor een container inrichten](how-to-provision-container-throughput.md)
* [De door Voer van automatisch schalen inrichten voor een container](how-to-provision-autoscale-throughput.md)
* [Aanvraageenheden en doorvoer in Azure Cosmos DB](request-units.md)