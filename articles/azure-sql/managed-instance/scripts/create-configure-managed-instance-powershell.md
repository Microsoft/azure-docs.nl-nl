---
title: 'PowerShell: Een beheerd exemplaar maken'
titleSuffix: Azure SQL Managed Instance
description: Dit artikel bevat een Azure PowerShell-voorbeeldscript voor het maken van een beheerd exemplaar.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: fd091cedb67c07b7de2c7e8540e99c3e8daf7dcf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323699"
---
# <a name="use-powershell-to-create-a-managed-instance"></a>PowerShell gebruiken voor het maken van een beheerd exemplaar

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

In dit voorbeeld van een PowerShell-script wordt een beheerd exemplaar gemaakt in een toegewezen subnet binnen een nieuw virtueel netwerk. Ook worden er een routetabel en een netwerkbeveiligingsgroep voor het virtuele netwerk geconfigureerd. Zodra het script is uitgevoerd, kunt u toegang krijgen tot het beheerde exemplaar vanuit het virtuele netwerk of vanuit een on-premises omgeving. Zie [Azure VM configureren om verbinding te maken met een met Azure SQL Database beheerd exemplaar](../connect-vm-instance-configure.md) en [On-premises een punt-naar-site-verbinding met een met Azure SQL beheerd exemplaar configureren](../point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Zie [ondersteunde regio's](../resource-limits.md#supported-regions) en [ondersteunde abonnementstypen](../resource-limits.md#supported-subscription-types) voor beperkingen.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze zelfstudie Azure PowerShell 1.4.0 of hoger vereist. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden enkele van de volgende opdrachten gebruikt. Voor meer informatie over de gebruikte en andere opdrachten in de onderstaande tabel klikt u op de koppelingen naar documentatie over een specifieke opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Hiermee stelt u de doelstatus voor een virtueel netwerk in. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Hiermee haalt u een subnet in een virtueel netwerk op. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Hiermee configureert u de doelstatus voor een subnetconfiguratie in een virtueel netwerk. |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Hiermee maakt u een routetabel. |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Hiermee haalt u routetabellen op. |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Hiermee stelt u de doelstatus voor een routetabel in. |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Hiermee maakt u een beheerd exemplaar. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep, met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/) voor meer informatie over Azure PowerShell.

Extra voorbeelden van PowerShell-scripts voor een met Azure SQL beheerd exemplaar kunt u vinden in [ PowerShell-scripts voor met Azure SQL beheerd exemplaar](../../database/powershell-script-content-guide.md).
