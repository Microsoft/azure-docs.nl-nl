---
title: Een container maken in Azure Cosmos DB Table-API
description: Meer informatie over het maken van een container in Azure Cosmos DB Table-API met behulp van Azure Portal, .NET, Java, Python, Node.js en andere Sdk's.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: fde3145e7bd7f4e53ae7a0c44f02e066c28ec785
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93101575"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>Een container maken in Azure Cosmos DB Table-API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

In dit artikel worden de verschillende manieren beschreven voor het maken van een container in Azure Cosmos DB Table-API. U ziet hoe u een container maakt met behulp van Azure Portal, Azure CLI, Power shell of ondersteunde Sdk's. In dit artikel ziet u hoe u een container maakt, de partitiesleutel opgeeft en doorvoer inricht.

In dit artikel worden de verschillende manieren beschreven voor het maken van een container in Azure Cosmos DB Table-API. Als u een andere API gebruikt, raadpleegt u [API voor MongoDb](how-to-create-container-mongodb.md), [CASSANDRA-API](how-to-create-container-cassandra.md), [Gremlin API](how-to-create-container-gremlin.md)en [SQL API](how-to-create-container.md) -artikelen om de container te maken.

> [!NOTE]
> Wanneer u containers maakt, moet u ervoor zorgen dat u niet twee containers maakt met dezelfde naam, maar met een ander hoofdletter gebruik. Dat komt omdat sommige onderdelen van het Azure-platform niet hoofdletter gevoelig zijn. Dit kan leiden tot Verwar ring/botsing van telemetriegegevens en acties op containers met dergelijke namen.

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>Maken met behulp van de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-table-dotnet.md#create-a-database-account)of selecteer een bestaand account.

1. Open het deel venster **Data Explorer** en selecteer **nieuwe tabel**. Geef de volgende gegevens op:

   * Voer een tabel-ID in.
   * Geef een door Voer op die moet worden ingericht (bijvoorbeeld 1000 RUs).
   * Selecteer **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Schermopname van Table-API, dialoogvenster Grafiek toevoegen":::

> [!Note]
> Voor de Table-API wordt de partitiesleutel opgegeven telkens wanneer u een nieuwe rij toevoegt.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Maken met behulp van Azure CLI

[Een Table-API tabel maken met Azure cli](./scripts/cli/table/create.md). Zie [Azure CLI-voor beelden voor Azure Cosmos DB](cli-samples.md)voor een overzicht van alle Azure CLI-voor beelden in alle Azure Cosmos DB api's.

## <a name="create-using-powershell"></a>Maken met PowerShell

[Maak een Table-API tabel met Power shell](./scripts/powershell/table/create.md). Zie [Power shell](powershell-samples.md) -voor beelden voor een lijst met alle Power shell-voor beelden in alle Azure Cosmos DB api's

## <a name="next-steps"></a>Volgende stappen

* [Partitionering in Azure Cosmos DB](partitioning-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Werken met een Azure Cosmos-account](./account-databases-containers-items.md)