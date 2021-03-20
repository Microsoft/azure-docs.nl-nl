---
title: 'PowerShell: Verbinding maken met SQL Database'
description: Ontdek hoe u Azure PowerShell kunt gebruiken om implementatie en beheer van App Service kunt automatiseren. In dit voorbeeld ziet u hoe u een app verbindt met een SQL-database.
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 3b355a830703b51264d2f4b819d5be7f2627b4e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89077786"
---
# <a name="connect-an-app-service-app-to-sql-database"></a>Een App Service-app verbinden met SQL Database

In dit scenario leert u hoe u een database in Azure SQL Database en een App Service-app maakt. Vervolgens gaat u de database aan de app koppelen met behulp van app-instellingen.

Installeer zo nodig de Azure PowerShell volgens de instructies in de [Azure PowerShell handleiding](/powershell/azure/) en voer vervolgens `Connect-AzAccount` uit om verbinding te maken met Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to SQL Database")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het voorbeeldscript is uitgevoerd, kan de volgende opdracht worden gebruikt om de resourcegroep, App Service-app en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Hiermee maakt u een App Service-plan. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Hiermee maakt u een App Service-app. |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een server. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewallregel op serverniveau. |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een database of een elastische database. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Hiermee wijzigt u de configuratie van een App Service-app. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

Meer voorbeelden voor Azure Powershell voor Azure App Service vindt u in de [voorbeelden van Azure PowerShell](../samples-powershell.md).
