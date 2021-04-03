---
title: Een bestaande toegewezen SQL-groep herstellen (voorheen SQL DW)
description: Instructies voor het herstellen van een bestaande, specifieke SQL-groep in azure Synapse Analytics.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2ce552a13592c9d26ef70575f98b0b76ecc454ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97591987"
---
# <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Een bestaande toegewezen SQL-groep herstellen (voorheen SQL DW)

In dit artikel leert u hoe u een bestaande toegewezen SQL-groep (voorheen SQL DW) kunt herstellen met behulp van Azure Portal en Power shell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Controleer de DTU-capaciteit.** Elke pool wordt gehost door een [logische SQL-Server](../../azure-sql/database/logical-servers.md) (bijvoorbeeld MyServer.database.Windows.net) die een standaard DTU-quotum heeft. Controleer of de server voldoende resterende DTU-quota heeft voor de data base die wordt hersteld. Zie [een wijziging in een DTU-quotum aanvragen](sql-data-warehouse-get-started-create-support-ticket.md)voor meer informatie over het berekenen van de benodigde DTU of om meer DTU aan te vragen.

## <a name="before-you-begin"></a>Voordat u begint

1. Zorg ervoor dat u [Azure PowerShell installeert](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Een bestaand herstel punt hebben waarvan u wilt herstellen. Als u een nieuwe herstel bewerking wilt maken, raadpleegt u [de zelf studie voor het maken van een nieuw door de gebruiker gedefinieerd herstel punt](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-powershell"></a>Een bestaande toegewezen SQL-groep (voorheen SQL DW) herstellen via Power shell

Gebruik de cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Power shell om een bestaande toegewezen SQL-groep (voorheen SQL DW) te herstellen vanaf een herstel punt.

1. Open PowerShell.

2. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.

3. Selecteer het abonnement dat de Data Base bevat die u wilt herstellen.

4. De herstel punten voor de toegewezen SQL-groep (voorheen SQL DW) weer geven.

5. Kies het gewenste herstel punt met behulp van de RestorePointCreationDate.

6. Herstel de toegewezen SQL-groep (voorheen SQL DW) naar het gewenste herstel punt met de Power shell [-cmdlet Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

    1. Als u de toegewezen SQL-groep (voorheen SQL DW) naar een andere server wilt herstellen, moet u de andere server naam opgeven.  Deze server kan zich ook in een andere resource groep en regio bevinden.
    2. Als u wilt herstellen naar een ander abonnement, gebruikt u de knop verplaatsen om de server naar een ander abonnement te verplaatsen.

7. Controleer of de herstelde toegewezen SQL-groep (voorheen SQL DW) online is.

8. Nadat de herstel bewerking is voltooid, kunt u uw gereserveerde, toegewezen SQL-groep (voorheen SQL DW) configureren door [de data base na het herstel te configureren](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$TargetResourceGroupName = $Database.ResourceGroupName # for restoring to different server in same resourcegroup 
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-the-azure-portal"></a>Een bestaande toegewezen SQL-groep (voorheen SQL DW) herstellen via de Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Ga naar de specifieke configuratie waarvan u wilt herstellen.
3. Klik boven aan de Blade overzicht op **herstellen**.

    ![ Overzicht van Herstellen](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Selecteer **automatische herstel punten** of door de **gebruiker gedefinieerde herstel punten**. Als de toegewezen SQL-groep (voorheen SQL DW) geen automatische herstel punten heeft, wacht u enkele uren of maakt u een door de gebruiker gedefinieerd herstel punt voordat u dit herstelt. Voor User-Defined herstel punten selecteert u een bestaande en maakt u een nieuw item. Voor- **Server** kunt u een server in een andere resource groep en regio kiezen of een nieuwe maken. Nadat u alle para meters hebt opgegeven, klikt u op **bekijken + herstellen**.

    ![Automatische herstelpunten](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Volgende stappen

- [Een verwijderde toegewezen SQL-groep herstellen (voorheen SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
- [Herstellen vanuit een toegewezen SQL-groep met geo-back-ups (voorheen SQL DW)](sql-data-warehouse-restore-from-geo-backup.md)
