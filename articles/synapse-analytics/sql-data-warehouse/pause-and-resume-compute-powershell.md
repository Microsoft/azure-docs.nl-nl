---
title: 'Snelstartgids: de berekening onderbreken en hervatten in de toegewezen SQL-groep (voorheen SQL DW) met Azure PowerShell'
description: U kunt Azure PowerShell gebruiken om de toegewezen SQL-groep te onderbreken en hervatten (voorheen SQL DW). rekenresources.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 03/20/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-azurepowershell
ms.openlocfilehash: 74c30a843ef5edd54c7cd19f3fd49acfe782f488
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602176"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Snelstartgids: de berekening onderbreken en hervatten in de toegewezen SQL-groep (voorheen SQL DW) met Azure PowerShell

U kunt Azure PowerShell gebruiken om de toegewezen SQL-groep (voorheen SQL DW) reken resources te onderbreken en te hervatten.
Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In deze Quick Start wordt ervan uitgegaan dat u al een toegewezen SQL-groep hebt (voorheen SQL DW), die u kunt onderbreken en hervatten. Als u er een wilt maken, kunt u [maken en verbinden-Portal](create-data-warehouse-portal.md) gebruiken om een toegewezen SQL-groep (voorheen SQL DW) met de naam **mySampleDataWarehouse** te maken.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij uw Azure-abonnement met behulp van de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

Voer [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) uit om te zien welk abonnement u gebruikt.

```powershell
Get-AzSubscription
```

Als u een ander abonnement dan het standaardabonnement wilt gebruiken, voert u [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) uit.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-dedicated-sql-pool-formerly-sql-dw-information"></a>Informatie over de exclusieve SQL-groep (voorheen SQL DW) opzoeken

Zoek de database naam, de server naam en de resource groep voor de toegewezen SQL-groep (voorheen SQL DW) die u wilt onderbreken en hervatten.

Volg deze stappen om locatie gegevens te vinden voor uw toegewezen SQL-groep (voorheen SQL DW):

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linkerpagina van de Azure Portal.
1. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** . De SQL-pool wordt geopend.

    ![Servernaam en resourcegroep](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Noteer de naam van de toegewezen SQL-groep (voorheen SQL DW). Dit is de naam van de data base. Noteer ook de naam van de server en de resourcegroep.
1. Gebruik alleen het eerste deel van de servernaam in de PowerShell-cmdlets. In de voorgaande afbeelding is de volledige servernaam sqlpoolservername.database.windows.net. We gebruiken **sqlpoolservername** als de servernaam in de PowerShell-cmdlet.

## <a name="pause-compute"></a>Berekening onderbreken

Als u kosten wilt besparen, kunt u de rekenresources op aanvraag onderbreken en hervatten. Als u de database bijvoorbeeld ‘s nachts en in het weekend niet gebruikt, kunt u deze dan onderbreken en gedurende de dag hervatten.

>[!NOTE]
>Er worden geen kosten in rekening gebracht voor rekenresources terwijl de database is onderbroken. Er worden echter nog steeds kosten in rekening gebracht voor opslag.

Gebruik de cmdlet [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om een database te onderbreken. In het volgende voorbeeld wordt een SQL-pool onderbroken met de naam **mySampleDataWarehouse**, die wordt gehost op een server met de naam **sqlpoolservername**. De server bevindt zich in een Azure-resourcegroep met de naam **myResourceGroup**.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

In het volgende voorbeeld wordt de database opgehaald in het $database-object. Vervolgens wordt het object doorgesluisd naar [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). De resultaten worden opgeslagen in het object resultDatabase. Met de laatste opdracht worden de resultaten weergegeven.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Berekening hervatten

Gebruik de cmdlet [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om een database te starten. In het volgende voorbeeld wordt een database gestart met de naam **mySampleDataWarehouse**, die wordt gehost op een server met de naam **sqlpoolservername**. De server bevindt zich in een Azure-resourcegroep met de naam **myResourceGroup**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

In het volgende voorbeeld wordt de database opgehaald in het $database-object. Vervolgens wordt het object doorgesluisd naar [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) en worden de resultaten opgeslagen in $resultDatabase. Met de laatste opdracht worden de resultaten weergegeven.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>De status van de SQL-pool controleren

Als u de status van uw toegewezen SQL-groep (voorheen SQL DW) wilt controleren, gebruikt u de cmdlet [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Resources opschonen

Er worden kosten in rekening gebracht voor Data Warehouse-eenheden en gegevens die zijn opgeslagen in uw toegewezen SQL-groep (voorheen SQL DW). Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in de opslag wilt houden, moet u het berekenen onderbreken.
- Als u in de toekomst geen kosten meer wilt hebben, kunt u de SQL-pool verwijderen.

Volg deze stappen om de resources op te schonen zoals gewenst.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en klik op uw SQL-pool.

    ![Resources opschonen](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Als u het berekenen wilt onderbreken, klikt u op de knop **Onderbreken**. Wanneer de SQL-pool is onderbroken, ziet u een knop **Starten**.  Als u het berekenen wilt hervatten, klikt u op **Start**.

3. Als u de SQL-pool wilt verwijderen zodat er geen kosten in rekening worden gebracht voor berekenen of opslaan, klikt u op **Verwijderen**.

4. Als u de door u gemaakte SQL-server wilt verwijderen, klikt u op **sqlpoolservername.database.windows.net** en vervolgens op **Verwijderen**.  Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de resourcegroep wilt verwijderen, klikt u op **myResourceGroup**. Klik vervolgens op **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over SQL-pool gaat u naar het artikel [gegevens laden in toegewezen SQL-groep (voorheen SQL DW)](./load-data-from-azure-blob-storage-using-copy.md) . Zie het artikel [Overzicht rekencapaciteit beheren](sql-data-warehouse-manage-compute-overview.md) voor meer informatie over het beheren van rekencapaciteit.