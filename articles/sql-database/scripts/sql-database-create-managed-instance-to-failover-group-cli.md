---
title: 'Azure CLI: Een beheerd exemplaar aan failover-groep toevoegen'
description: Leer twee beheerd exemplaren maken, ze toevoegen aan een failovergroep en vervolgens de failover testen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: afefc556bcac096d67051f3014c31e449dbbca32
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323563"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance-to-a-failover-group"></a>CLI gebruiken om een Azure SQL Managed Instance te maken in een failover-groep

Dit Azure CLI-voorbeeldscript maakt twee beheerde exemplaren, voegt ze toe aan een failover-groep en test de failover van het primaire beheerde exemplaar naar het secundaire beheerde exemplaar.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-scripts"></a>Voorbeeldscripts

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
| [az network nsg rule](/cli/azure/network/nsg/rule)| Opdrachten voor netwerkbeveiligingsregels. |
| [az network route-table](/cli/azure/network/route-table) | Opdrachten voor routetabel. |
| [az sql mi](/cli/azure/sql/mi) | Opdrachten voor een met SQL beheerd exemplaar. |
| [az network public-ip](/cli/azure/network/public-ip) | Opdrachten voor openbare IP-adressen van netwerk. |
| [az network vnet-gateway](/cli/azure/network/vnet-gateway) | Opdrachten voor virtuele-netwerkgateway. |
| [az sql instance-failover-group](/cli/azure/sql/instance-failover-group) | Opdrachten voor SQL Managed Instance-failovergroep. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
