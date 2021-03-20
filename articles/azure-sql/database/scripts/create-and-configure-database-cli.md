---
title: 'De Azure CLI: Een individuele database maken'
description: Gebruik dit Azure CLI-voorbeeldscript om een individuele database te maken.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: dc17ab55cb473036d8c40bd1e533bcc20c4c6259
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92742266"
---
# <a name="use-the-azure-cli-to-create-a-single-database-and-configure-a-firewall-rule"></a>De Azure CLI gebruiken om een individuele database te maken en een firewallregel te configureren

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Met dit Azure CLI-script wordt een individuele database gemaakt in Azure SQL Database en een regel voor een firewall op serverniveau geconfigureerd. Nadat het script is uitgevoerd, is de database toegankelijk via alle Azure-services en het geconfigureerde IP-adres.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Beschrijving |
|---|---|
| [az sql server](/cli/azure/sql/server#az-sql-server-create) | Serveropdrachten |
| [az sql server firewall](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Opdrachten voor serverfirewall. |
| [az sql db](/cli/azure/sql/db#az-sql-db-create) | Databaseopdrachten. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../az-cli-script-samples-content-guide.md).
