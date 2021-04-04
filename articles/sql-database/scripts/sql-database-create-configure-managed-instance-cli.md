---
title: 'Azure CLI: Een beheerd exemplaar maken'
description: Azure CLI-voorbeeldscript voor het maken van een beheerd exemplaar van Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 54e1aa993f177a4d3bc255287ae8c2fb14cf65af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87497270"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>CLI gebruiken om een beheerd exemplaar van Azure SQL te maken

In dit Azure CLI-voorbeeldscript wordt een beheerd exemplaar van Azure SQL gemaakt in een toegewezen subnet binnen een nieuw virtueel netwerk. Er wordt ook een routetabel en een netwerkbeveiligingsgroep voor het virtuele netwerk geconfigureerd. Zodra het script is uitgevoerd, kunt u toegang krijgen tot het beheerde exemplaar vanuit het virtuele netwerk of vanuit een on-premises omgeving. Zie [Azure VM configureren om verbinding te maken met een beheerd exemplaar van Azure SQL]../../azure-sql/managed-instance/connect-vm-instance-configure.md) en [On-premises een punt-naar-site-verbinding met een beheerd exemplaar van Azure SQL](../../azure-sql/managed-instance/point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Zie [ondersteunde regio's](../../azure-sql/managed-instance/resource-limits.md#supported-regions) en [ondersteunde abonnementstypen](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types) voor beperkingen.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die eraan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Beschrijving |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Opdrachten virtueel netwerk. |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Opdrachten virtueel netwerk subnet. |
| [az network routetabel](/cli/azure/network/route-table) | Opdrachten voor netwerkroutetabel. |
| [az sql mi](/cli/azure/sql/mi) | Opdrachten voor een met SQL beheerd exemplaar. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
