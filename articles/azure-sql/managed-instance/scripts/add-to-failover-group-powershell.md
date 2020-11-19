---
title: 'PowerShell: Een beheerd exemplaar toevoegen aan een groep voor automatische failover'
titleSuffix: Azure SQL Managed Instance
description: Voorbeeldscript van Azure PowerShell om een beheerd exemplaar te maken, dit toe te voegen aan een groep voor automatische failover, en failover te testen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.openlocfilehash: af1d7110cccfd8b0617d6c79eb5373cc7c087159
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594008"
---
# <a name="use-powershell-to-add-a-managed-instance-to-a-failover-group"></a>PowerShell gebruiken om een beheerd exemplaar toe te voegen aan een failovergroep 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Dit PowerShell-voorbeeldscript maakt twee beheerde exemplaren, voegt ze toe aan een failovergroep, en test vervolgens failover van het primaire beheerde exemplaar naar het secundaire beheerde exemplaar. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze zelfstudie Azure PowerShell 1.4.0 of hoger vereist. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-scripts"></a>Voorbeeldscripts

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die eraan zijn gekoppeld te verwijderen. U moet de resourcegroep twee keer verwijderen. Als u de resourcegroep de eerste keer verwijdert, worden het beheerde exemplaar en de virtuele clusters verwijderd, maar daarna wordt het foutbericht `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` weergegeven. Voer de opdracht Remove-AzResourceGroup een tweede keer uit om eventuele resterende resources en de resourcegroep te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een Azure-resourcegroep.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee haalt u een subnet in een virtueel netwerk op. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Hiermee maakt u een netwerkbeveiligingsgroep. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Hiermee maakt u een routetabel. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee werkt u een virtueel netwerk bij.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hiermee haalt u een netwerkbeveiligingsgroep op. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u de configuratie van een netwerkbeveiligingsregel toe aan een netwerkbeveiligingsgroep. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Hiermee werkt u een netwerkbeveiligingsgroep bij.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Hiermee voegt u een route toe aan een routetabel. |
| [Get-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Hiermee werkt u een routetabel bij.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Hiermee maakt u een beheerd exemplaar.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Hiermee retourneert u informatie over een met Azure SQL beheerd exemplaar. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Hiermee maakt u een IP-configuratie voor een virtuele netwerkgateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Hiermee maakt u een virtuele netwerkgateway |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Hiermee maakt u een verbinding tussen de twee virtuele netwerkgateways.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Hiermee maakt u een nieuwe failovergroep voor Azure SQL Managed Instance.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hiermee haalt u failovergroepen voor Azure SQL Managed Instance op of vermeldt u deze.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Hiermee voert u een nieuwe failover uit voor een failovergroep voor SQL Managed Instance. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep. | 

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/) voor meer informatie over Azure PowerShell.

Extra PowerShell-voorbeeldscripts voor SQL Managed Instance vindt u in [PowerShell-scripts voor Azure SQL Managed Instance](../../database/powershell-script-content-guide.md).
