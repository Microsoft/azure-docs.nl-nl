---
title: PowerShell & REST API's voor toegewezen SQL-pool (voorheen SQL DW)
description: Belangrijkste PowerShell-cmdlets voor toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics waaronder het onderbreken en hervatten van een database.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 1f00f470fb0aa8ac98b431c6fc9428f501b553ed
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566440"
---
# <a name="powershell--rest-apis-for-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>PowerShell & REST API's voor toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics 

Veel toegewezen beheertaken voor SQL-pool kunnen worden beheerd met behulp Azure PowerShell cmdlets of REST API's.  Hieronder vindt u enkele voorbeelden van het gebruik van PowerShell-opdrachten voor het automatiseren van algemene taken in uw toegewezen SQL-pool (voorheen SQL DW).  Zie het artikel Schaalbaarheid beheren met REST voor enkele goede [REST-voorbeelden.](sql-data-warehouse-manage-compute-rest-api.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Aan de slag met Azure PowerShell-cmdlets

1. Open Windows PowerShell.
2. Voer bij de PowerShell-prompt deze opdrachten uit om u aan te melden bij de Azure Resource Manager selecteer uw abonnement.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Voorbeeld van een datawarehouse onderbreken

Pauzeer een database met de naam Database02 die wordt gehost op een server met de naam 'Server01'.  De server maakt deel uit van een Azure-resourcegroep met de naam ResourceGroup1.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Een variatie. In dit voorbeeld wordt het opgehaalde object doorgeslingerd naar [Suspend-AzSqlDatabase.](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)  Als gevolg hiervan wordt de database onderbroken. Met de laatste opdracht worden de resultaten weergegeven.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Voorbeeld van het starten van een datawarehouse

Hervat de werking van een database met de naam 'Database02' die wordt gehost op een server met de naam 'Server01'. De server is opgenomen in een resourcegroep met de naam ResourceGroup1.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Een variant, in dit voorbeeld, haalt een database met de naam 'Database02' op van een server met de naam 'Server01' die is opgenomen in een resourcegroep met de naam ResourceGroup1. Hiermee wordt het opgehaalde object doorgeslingerd [naar Resume-AzSqlDatabase.](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Als uw server niet foo.database.windows.net, gebruikt u 'foo' als de -ServerName in de PowerShell-cmdlets.

## <a name="other-supported-powershell-cmdlets"></a>Andere ondersteunde PowerShell-cmdlets

Deze PowerShell-cmdlets worden ondersteund met Azure Synapse Analytics datawarehouse.

* [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer PowerShell-voorbeelden:

* [Een datawarehouse maken met PowerShell](create-data-warehouse-powershell.md)
* [Database herstellen](sql-data-warehouse-restore-points.md)

Zie voor andere taken die kunnen worden geautomatiseerd met [PowerShell, Azure SQL Database cmdlets.](/powershell/module/az.sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Niet alle Azure SQL Database-cmdlets worden ondersteund voor Azure Synapse Analytics datawarehouse. Zie Bewerkingen voor Azure SQL Database voor een lijst met taken die kunnen worden geautomatiseerd [met REST.](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
