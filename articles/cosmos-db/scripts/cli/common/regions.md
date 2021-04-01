---
title: Regio's toevoegen, prioriteit van failover wijzigen en failover activeren voor een Azure Cosmos-account
description: Regio's toevoegen, prioriteit van failover wijzigen en failover activeren voor een Azure Cosmos-account
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 0ae724f9900eea713af7d295eba26bf03cdf471a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563219"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Regio's toevoegen, prioriteit van failover wijzigen en failover activeren voor een Azure Cosmos-account met behulp van Azure CLI
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.9.1 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="sample-script"></a>Voorbeeldscript

In dit script worden drie bewerkingen gedemonstreerd.

- Het toevoegen van een regio aan een bestaand Azure Cosmos-account
- De prioriteit van regionale failover wijzigen (is van toepassing op accounts met automatische failover)
- Een handmatige failover activeren van primaire naar secundaire regio's (van toepassing op accounts met handmatige failover)

> [!NOTE]
> Het toevoegen en verwijderen van regiobewerkingen voor een Cosmos-account kan niet worden uitgevoerd terwijl andere eigenschappen worden gewijzigd.

> [!NOTE]
> Dit voorbeeld laat zien hoe u een SQL (core) API-account gebruikt, maar deze bewerkingen zijn identiek voor alle database-API's in Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

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
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Hiermee wordt een Azure Cosmos DB-account bijgewerkt (regio toevoegen of verwijderen). |
| [az cosmosdb failover-priority-change](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Failover-prioriteit bijwerken of failover activeren voor een Azure Cosmos DB-account. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure Cosmos DB CLI](/cli/azure/cosmosdb) voor meer informatie over de Azure Cosmos DB CLI.

Alle Azure Cosmos DB CLI-voorbeeldscripts vindt u in de [documentatie van Azure Cosmos DB CLI GitHub-opslagplaats](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
