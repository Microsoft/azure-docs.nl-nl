---
title: Azure CLI-scripts voor doorvoerbewerkingen (RU/s) voor Azure Cosmos DB Core (SQL) API-resources
description: Azure CLI-scripts voor doorvoerbewerkingen (RU/s) voor Azure Cosmos DB Core (SQL) API-resources
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 8310de5ce8fd3f90e422555a5111569fadcca982
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566385"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Doorvoerbewerkingen (RU/s) met Azure CLI voor een database of container voor de Azure Cosmos DB Core (SQL) API
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.12.1 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="sample-script"></a>Voorbeeldscript

Met dit script maakt u een Core (SQL) API-database met gedeelde doorvoer en een Core (SQL) API-container met toegewezen doorvoer, waarna de doorvoer voor zowel de database als de container wordt bijgewerkt. Met het script wordt de migratie van standaarddoorvoer naar doorvoer met automatische schaalaanpassing uitgevoerd en de waarde van doorvoer met automatische schaalaanpassing gelezen nadat de migratie is voltooid.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Throughput operations for a SQL database and container.")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Na het uitvoeren van het voorbeeldscript kan de volgende opdracht worden gebruikt om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Hiermee wordt een Azure Cosmos DB-account gemaakt. |
| [az cosmosdb sql database create](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Hiermee maakt u een Azure Cosmos Core (SQL)-database. |
| [az cosmosdb sql container create](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Hiermee maakt u een Azure Cosmos Core (SQL)-container. |
| [az cosmosdb sql database throughput update](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Hiermee wordt doorvoer bijgewerkt voor een Azure Cosmos Core (SQL)-database. |
| [az cosmosdb sql container throughput update](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Hiermee wordt doorvoer bijgewerkt voor een Azure Cosmos Core (SQL)-container. |
| [az cosmosdb sql database throughput migrate](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-migrate) | Hiermee wordt doorvoer gemigreerd voor een Azure Cosmos Core (SQL)-database. |
| [az cosmosdb sql container throughput migrate](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-migrate) | Hiermee wordt doorvoer gemigreerd voor een Azure Cosmos Core (SQL)-container. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure Cosmos DB CLI](/cli/azure/cosmosdb) voor meer informatie over de Azure Cosmos DB CLI.

Alle Azure Cosmos DB CLI-voorbeeldscripts vindt u in de [documentatie van Azure Cosmos DB CLI GitHub-opslagplaats](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
