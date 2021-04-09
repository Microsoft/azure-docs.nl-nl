---
title: CLI-voorbeeld -Failover-groep - elastische pool van Azure SQL Database
description: Voorbeeldscript van Azure CLI om een elastische pool van Azure SQL Database te maken, deze toe te voegen aan een failover-groep en failover te testen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 07/16/2019
ms.openlocfilehash: a5814bfe3bd6ec2d97a068ea8ce71fa7ffea8ec0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323580"
---
# <a name="use-cli-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>CLI gebruiken om een elastische pool van Azure SQL Database toe te voegen aan een failover-groep

In dit Azure CLI-voorbeeldscript wordt een individuele database gemaakt, wordt deze toegevoegd aan een elastische pool, wordt er een failover-groep gemaakt en wordt de failover getest.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-cli.sh "Add elastic pool to a failover group")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die eraan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Beschrijving |
|---|---|
| [elastische pool voor az sql](/cli/azure/sql/elastic-pool) | Opdrachten voor elastische pools. |
| [failover-groep voor az sql](/cli/azure/sql/failover-group) | Opdrachten voor failover-groep. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure/overview) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database Azure CLI-scripts vindt u in de [Azure SQL Database Azure CLI-scripts](../../azure-sql/database/az-cli-script-samples-content-guide.md).
