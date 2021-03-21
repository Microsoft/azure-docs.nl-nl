---
title: 'Azure CLI: Een individuele database bewaken en schalen in Azure SQL Database'
description: Gebruik een Azure CLI-voorbeeldscript om een individuele database in Azure SQL Database te bewaken en te schalen.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: 0a72bea08052e5cd17502a21a98b41cb5582a3b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96493624"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>De Azure CLI gebruiken om een individuele database in Azure SQL Database te bewaken en te schalen

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

In dit Azure CLI-voorbeeldscript wordt een individuele database in Azure SQL Database naar een andere rekenkracht geschaald nadat er een query is gestuurd over de grootte van de database.

Als u ervoor kiest om de Azure CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> Gebruik [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list) voor een lijst met bewerkingen die worden uitgevoerd op de database en gebruik [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) om een update van de database te annuleren.

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die eraan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Script | Beschrijving |
|---|---|
| [az sql server](/cli/azure/sql/server) | Serveropdrachten. |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | Toont de gebruikte grootte voor een database. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende CLI-scriptvoorbeelden vindt u in [Azure CLI-voorbeeldscripts](../az-cli-script-samples-content-guide.md).
