---
title: 'De Azure CLI: Een database verplaatsen tussen elastische pools'
description: Gebruik een Azure CLI-voorbeeldscript om twee elastische pools te maken en een database in SQL Database van de ene elastische pool naar de andere te verplaatsen.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 9fcb23750137c8286efbc0e1fb7446e324c572dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747342"
---
# <a name="use-the-azure-cli-to-move-a-database-in-sql-database-in-a-sql-elastic-pool"></a>De Azure CLI gebruiken om een database in SQL Database te verplaatsen naar een elastische SQL-pool

In dit voorbeeld van een Azure CLI-script worden twee elastische groepen of pools gemaakt, wordt een pooldatabase in SQL Database van de ene elastische SQL-pool naar de andere elastische SQL pool verplaatst en wordt de pooldatabase vervolgens vanuit de elastische SQL-pool naar een individuele database in SQL Database verplaatst.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Beschrijving |
|---|---|
| [az sql server](/cli/azure/sql/server) | Serveropdrachten. |
| [az sql elastic-pools](/cli/azure/sql/elastic-pool) | Opdrachten voor elastische pools. |
| [az sql db](/cli/azure/sql/db) | Databaseopdrachten. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../az-cli-script-samples-content-guide.md).
