---
title: Resourcevergrendeling maken voor een Azure Cosmos DB Core (SQL) API-database en -container
description: Resourcevergrendeling maken voor een Azure Cosmos DB Core (SQL) API-database en -container
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: b14bc8f77c6f26fba36880762d12723888d845e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94565990"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-core-sql-api-database-and-container-using-azure-cli"></a>Resourcevergrendeling maken voor een Azure Cosmos DB Core (SQL) API-database en -container met behulp van Azure CLI
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.9.1 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

> [!IMPORTANT]
> Resourcevergrendeling werkt niet voor wijzigingen die zijn aangebracht door gebruikers die verbinding maken met behulp van een Cosmos DB SDK, hulpprogramma's die verbinding maken via accountsleutels of Azure Portal tenzij het Cosmos DB-account eerst is vergrendeld met de eigenschap `disableKeyBasedMetadataWriteAccess` ingeschakeld. Zie [Wijzigingen aan SDK's voorkomen](../../../role-based-access-control.md#prevent-sdk-changes) voor meer informatie over het inschakelen van deze eigenschap.

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/lock.sh "Create a resource lock for an Azure Cosmos DB Core (SQL) API database and container.")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | Hiermee wordt een vergrendeling gemaakt. |
| [az lock list](/cli/azure/lock#az-lock-list) | Hiermee worden vergrendelingsgegevens weergegeven. |
| [az lock show](/cli/azure/lock#az-lock-show) | Hiermee worden eigenschappen van een vergrendeling weergegeven. |
| [az lock delete](/cli/azure/lock#az-lock-delete) | Hiermee wordt een vergrendeling verwijderd. |

## <a name="next-steps"></a>Volgende stappen

- [Resources vergrendelen om onverwachte wijzigingen te voorkomen](../../../../azure-resource-manager/management/lock-resources.md)

- [Documentatie voor Azure Cosmos DB CLI](/cli/azure/cosmosdb).

- [Azure Cosmos DB CLI GitHub-opslagplaats](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
