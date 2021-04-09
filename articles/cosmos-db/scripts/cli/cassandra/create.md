---
title: Een Cassandra-keyspace en -tabel maken voor Azure Cosmos DB
description: Een Cassandra-keyspace en -tabel maken voor Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 5078567b081f0546f26406ab07bd8bd78cd3a756
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566058"
---
# <a name="create-an-azure-cosmos-cassandra-api-account-keyspace-and-table-using-azure-cli"></a>Een Azure Cosmos Cassandra API-account, -keyspace en -tabel maken met behulp van Azure CLI
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.9.1 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/create.sh "Create an Azure Cosmos DB Cassandra API account, keyspace, and table.")]

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
| [az cosmosdb cassandra keyspace create](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Hiermee maakt u een Azure Cosmos Cassandra-keyspace. |
| [az cosmosdb cassandra table create](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Hiermee maakt u een Azure Cosmos Cassandra-tabel. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure Cosmos DB CLI](/cli/azure/cosmosdb) voor meer informatie over de Azure Cosmos DB CLI.

Alle Azure Cosmos DB CLI-voorbeeldscripts vindt u in de [documentatie van Azure Cosmos DB CLI GitHub-opslagplaats](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
