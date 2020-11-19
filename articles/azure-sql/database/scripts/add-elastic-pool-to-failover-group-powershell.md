---
title: 'PowerShell: Een elastische pool aan een automatische failovergroep toevoegen'
description: Azure PowerShell-voorbeeldscript om een elastische pool in Azure SQL Database te maken, deze toe te voegen aan een groep voor automatische failover en de failover te testen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.openlocfilehash: a8326249b48e98eccecb5f0c4953add26e167560
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594101"
---
# <a name="use-powershell-to-add-an-elastic-pool-to-a-failover-group"></a>Gebruik PowerShell om een elastische pool aan een failovergroep toe te voegen
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

In dit Azure PowerShell-scriptvoorbeeld wordt een database in Azure SQL Database gemaakt, wordt deze toegevoegd aan een elastische pool, wordt er een failovergroep gemaakt en wordt de failover getest.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze zelfstudie Az PowerShell 1.4.0 of hoger vereist. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-scripts"></a>Voorbeeldscripts

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die eraan zijn gekoppeld te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een server die als host fungeert voor databases en elastische pools. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewallregel voor een server. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een nieuwe database. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Hiermee maakt u een pool voor een elastische database.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Hiermee worden eigenschappen ingesteld voor een database of plaatst een bestaande database in een elastische pool. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hiermee haalt u een of meer databases op. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer databases toe aan een failovergroep. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden failovergroepen van de database opgehaald of weergegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover uitgevoerd van een database-failovergroep. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/) voor meer informatie over Azure PowerShell.

Aanvullende voorbeelden van SQL Database PowerShell-scripts vindt u in [Azure SQL Database PowerShell-scripts](../powershell-script-content-guide.md).
