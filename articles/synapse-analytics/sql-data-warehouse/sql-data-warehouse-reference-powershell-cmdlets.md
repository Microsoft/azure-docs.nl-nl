---
title: Power shell-& REST-Api's voor toegewezen SQL-groep (voorheen SQL DW)
description: Top Power shell-cmdlets voor een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics, inclusief het onderbreken en hervatten van een Data Base.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 83e6082025f068e91a3d531f052b746870ffd57a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584105"
---
# <a name="powershell--rest-apis-for-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Power shell-& REST-Api's voor voor exclusieve SQL-groep (voorheen SQL DW) in azure Synapse Analytics 

Veel specifieke beheer taken van de SQL-groep kunnen worden beheerd met behulp van Azure PowerShell-cmdlets of REST-Api's.  Hieronder ziet u enkele voor beelden van het gebruik van Power shell-opdrachten voor het automatiseren van algemene taken in uw toegewezen SQL-groep (voorheen SQL DW).  Zie het artikel [schaal baarheid met rest beheren](sql-data-warehouse-manage-compute-rest-api.md)voor een aantal goede rest-voor beelden.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Aan de slag met Azure PowerShell-cmdlets

1. Open Windows PowerShell.
2. Voer bij de Power shell-prompt deze opdrachten uit om u aan te melden bij de Azure Resource Manager en selecteer uw abonnement.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Voor beeld van data warehouse pauzeren

Een Data Base met de naam ' Database02 ' onderbreken die wordt gehost op een server met de naam ' Server01 '.  De server bevindt zich in een Azure-resource groep met de naam ' ResourceGroup1 '.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Een variant, in dit voor beeld wordt het opgehaalde object door sluizen naar [suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Als gevolg hiervan wordt de data base onderbroken. Met de laatste opdracht worden de resultaten weergegeven.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Start Data Warehouse-voor beeld

Hervat de bewerking van een Data Base met de naam ' Database02 ' die wordt gehost op een server met de naam ' Server01 '. De server bevindt zich in een resource groep met de naam ' ResourceGroup1 '.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

In dit voor beeld wordt een Data Base met de naam ' Database02 ' opgehaald van een server met de naam ' Server01 ' die is opgenomen in een resource groep met de naam ' ResourceGroup1 '. Dit pipet het opgehaalde object om [AzSqlDatabase te hervatten](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Als uw server foo.database.windows.net is, gebruikt u Foo als de-server naam in de Power shell-cmdlets.

## <a name="other-supported-powershell-cmdlets"></a>Andere ondersteunde Power shell-cmdlets

Deze Power shell-cmdlets worden ondersteund met Azure Synapse Analytics Data Warehouse.

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

Zie voor meer Power shell-voor beelden:

* [Een Data Warehouse maken met behulp van Power shell](create-data-warehouse-powershell.md)
* [Data base terugzetten](sql-data-warehouse-restore-points.md)

Zie [Azure SQL database-cmdlets](/powershell/module/az.sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)voor andere taken die kunnen worden geautomatiseerd met Power shell. Niet alle Azure SQL Database-cmdlets worden ondersteund voor Azure Synapse Analytics Data Warehouse. Zie [bewerkingen voor Azure SQL database](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)voor een lijst met taken die kunnen worden geautomatiseerd met rest.
