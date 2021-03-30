---
title: CLI-voor beeld-BYOK TDE-Azure SQL Managed instance inschakelen
description: Meer informatie over het configureren van een Azure SQL Managed instance om te beginnen met het gebruik van BYOK Transparent Data Encryption (TDE) voor versleuteling-at-rest met behulp van Power shell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto
ms.date: 11/05/2019
ms.openlocfilehash: 2b948161633569d629dfb048a7d7dee6a9946f43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323666"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transparent Data Encryption beheren in een beheerd exemplaar met uw eigen sleutel vanuit Azure Key Vault

In dit voor beeld van Azure CLI-script wordt Transparent Data Encryption (TDE) met door de klant beheerde sleutel geconfigureerd voor Azure SQL Managed instance, met behulp van een sleutel van Azure Key Vault. Dit wordt vaak een Bring Your Own Key scenario genoemd voor TDE. Zie [TDE Bring your own Key naar Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md)voor meer informatie over de TDe met door de klant beheerde sleutels.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="prerequisites"></a>Vereisten

Zie [Azure CLI gebruiken voor het maken van een beheerd exemplaar van Azure SQL](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Het script uitvoeren

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die eraan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Beschrijving |
|---|---|
| [az sql db](/cli/azure/sql/db) | Databaseopdrachten. |
| [az sql failovergroep](/cli/azure/sql/failover-group) | Opdrachten voor failovergroep. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
