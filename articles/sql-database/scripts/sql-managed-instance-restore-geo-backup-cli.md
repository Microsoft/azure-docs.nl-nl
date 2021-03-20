---
title: CLI-voorbeeld back-up voor geografisch gebied terugzetten - Azure SQL Database
description: Azure CLI-voorbeeldscript voor het terugzetten van een beheerd exemplaar van Azure SQL Database uit een geografisch redundante back-up.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 07/03/2019
ms.openlocfilehash: 5468cfc733b037f6287e8536c8c85994f0840161
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86231250"
---
# <a name="use-cli-to-restore-a-managed-instance-database-to-another-geo-region"></a>Gebruik CLI om een beheerd-exemplaardatabase te herstellen naar een ander geografisch gebied

Met dit Azure CLI-scriptvoorbeeld wordt een beheerd exemplaar van Azure SQL Database vanuit een afgelegen geografisch gebied teruggezet (geo-restore).  

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Voorbeeldscript

### <a name="prerequisites"></a>Vereisten

Een bestaand paar beheerde exemplaren, zie [Azure CLI gebruiken om een beheerd exemplaar van Azure SQL te maken](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Het script uitvoeren

```azurepowershell-interactive
#!/bin/bash
$instance = "<instanceId>" # add instance here
$targetInstance = "<targetInstanceId>" # add target instance here
$resource = "<resourceId>" # add resource here

$randomIdentifier = $(Get-Random)
$managedDatabase = "managedDatabase-$randomIdentifier"

echo "Creating $($managedDatabase) on $($instance)..."
az sql midb create -g $resource --mi $instance -n $managedDatabase

echo "Restoring $($managedDatabase) to $($targetInstance)..."
az sql midb restore -g $resource --mi $instance -n $managedDatabase --dest-name $targetInstance --time "2018-05-20T05:34:22"
```

## <a name="sample-reference"></a>Voorbeeldverwijzing

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Script | Beschrijving |
|---|---|
| [az sql midb](/cli/azure/sql/midb) | Opdrachten voor beheerd-exemplaardatabase. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Aanvullende voorbeelden van SQL Database CLI-scripts vindt u in de [documentatie van Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
