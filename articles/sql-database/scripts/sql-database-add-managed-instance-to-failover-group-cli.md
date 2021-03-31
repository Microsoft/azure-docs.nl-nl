---
title: CLI-voorbeeld – Failovergroep – Azure SQL Managed Instance
description: Voorbeeldscript van Azure CLI om een Azure SQL Managed Instance te maken, deze toe te voegen aan een failovergroep en failover te testen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: f196db537ef0a64d14930ed6bc67696ee4614c23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86528897"
---
# <a name="use-cli-to-add-an-azure-sql-managed-instance-to-a-failover-group"></a>CLI gebruiken om een Azure SQL Managed Instance toe te voegen aan een failovergroep

Dit Azure CLI-voorbeeldscript maakt twee beheerde exemplaren, voegt ze toe aan een failover-groep en test de failover van het primaire beheerde exemplaar naar het secundaire beheerde exemplaar.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die eraan zijn gekoppeld te verwijderen. U moet de resourcegroep twee keer verwijderen. Als u de resourcegroep de eerste keer verwijdert, worden het beheerde exemplaar en de virtuele clusters verwijderd, maar daarna wordt het foutbericht `az group delete : Long running operation failed with status 'Conflict'.` weergegeven. Voer de opdracht az group delete een tweede keer uit om eventuele resterende resources en de resourcegroep te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Beschrijving |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Opdrachten virtueel netwerk.  |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Opdrachten virtueel netwerk subnet. |
| [az network nsg](/cli/azure/network/nsg) | Opdrachten voor netwerkbeveiligingsgroepen. |
| [az network route-table](/cli/azure/network/route-table) | Opdrachten voor routetabel. |
| [az sql mi](/cli/azure/sql/mi) | Opdrachten voor een met SQL beheerd exemplaar. |
| [az network public-ip](/cli/azure/network/public-ip) | Opdrachten voor openbare IP-adressen van netwerk. |
| [az network vnet-gateway](/cli/azure/network/vnet-gateway) | Opdrachten voor virtuele-netwerkgateway. |
| [az sql instance-failover-group](/cli/azure/sql/instance-failover-group) | Opdrachten voor SQL Managed Instance-failovergroep. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
