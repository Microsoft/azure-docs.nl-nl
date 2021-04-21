---
title: Herstel naar een bepaald tijdstip (PITR)
titleSuffix: Azure SQL Managed Instance
description: Een database op een Azure SQL Managed Instance een eerder tijdstip herstellen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 4c116b378c72d87641157fc453d65e46be9f43ec
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787160"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Een database in Azure SQL Managed Instance herstellen naar een eerder tijdstip
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Gebruik herstel naar een bepaald tijdstip (PITR) om een database te maken als een kopie van een andere database van enige tijd in het verleden. In dit artikel wordt beschreven hoe u een database herstelt naar een bepaald tijdstip in Azure SQL Managed Instance.

Herstel naar een bepaald tijdstip is handig in herstelscenario's, zoals incidenten die worden veroorzaakt door fouten, onjuist geladen gegevens of het verwijderen van essentiële gegevens. U kunt deze ook gewoon gebruiken voor testen of controleren. Back-upbestanden worden 7 tot 35 dagen bewaard, afhankelijk van uw database-instellingen.

Herstel naar een bepaald tijdstip kan een database herstellen:

- uit een bestaande database.
- uit een verwijderde database.
- aan dezelfde SQL Managed Instance of aan een SQL Managed Instance. 

## <a name="limitations"></a>Beperkingen

Herstel naar een bepaald tijdstip naar SQL Managed Instance heeft de volgende beperkingen:

- Wanneer u herstelt van het ene SQL Managed Instance naar een andere, moeten beide exemplaren zich in hetzelfde abonnement en dezelfde regio hebben. Herstel naar andere regio's en abonnementen wordt momenteel niet ondersteund.
- Herstel naar een bepaald tijdstip van een geheel SQL Managed Instance is niet mogelijk. In dit artikel wordt alleen uitgelegd wat er mogelijk is: herstel naar een bepaald tijdstip van een database die wordt gehost op SQL Managed Instance.

> [!WARNING]
> Let op de opslaggrootte van uw SQL Managed Instance. Afhankelijk van de grootte van de gegevens die moeten worden hersteld, hebt u mogelijk geen exemplaaropslag meer. Als er onvoldoende ruimte is voor de herstelde gegevens, gebruikt u een andere benadering.

De volgende tabel bevat herstelscenario's voor herstel naar een bepaald SQL Managed Instance:

|           |Een bestaande database herstellen naar hetzelfde exemplaar van SQL Managed Instance| Bestaande database herstellen naar een andere SQL Managed Instance|Verwijderde database herstellen naar dezelfde SQL Managed Instance|Verwijderde database herstellen naar een andere SQL Managed Instance|
|:----------|:----------|:----------|:----------|:----------|
|**Azure-portal**| Ja|Nee |Ja|Nee|
|**Azure-CLI**|Ja |Ja |Nee|Nee|
|**PowerShell**| Ja|Ja |Ja|Ja|

## <a name="restore-an-existing-database"></a>Een bestaande database herstellen

Herstel een bestaande database naar dezelfde SQL Managed Instance met behulp van Azure Portal, PowerShell of de Azure CLI. Als u een database wilt herstellen naar een SQL Managed Instance, gebruikt u PowerShell of de Azure CLI, zodat u de eigenschappen voor de doelgroep SQL Managed Instance en resourcegroep kunt opgeven. Als u deze parameters niet opgeeft, wordt de database standaard hersteld naar SQL Managed Instance database. De Azure Portal biedt momenteel geen ondersteuning voor het herstellen naar een SQL Managed Instance.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
2. Ga naar uw SQL Managed Instance selecteer de database die u wilt herstellen.
3. Selecteer **Herstellen** op de databasepagina:

    ![Een database herstellen met behulp van de Azure Portal](./media/point-in-time-restore/restore-database-to-mi.png)

4. Selecteer op **de** pagina Herstellen het punt voor de datum en tijd waarop u de database wilt herstellen.
5. Selecteer **Bevestigen om** uw database te herstellen. Met deze actie wordt het herstelproces gestart, waarbij een nieuwe database wordt gemaakt en deze op het opgegeven tijdstip wordt gevuld met gegevens uit de oorspronkelijke database. Zie Hersteltijd voor meer informatie over [het herstelproces.](../database/recovery-using-backups.md#recovery-time)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Zie Install the Azure PowerShell module (De module Azure PowerShell installeren) als u de Azure PowerShell [nog niet hebt geïnstalleerd.](/powershell/azure/install-az-ps)

Als u de database wilt herstellen met behulp van PowerShell, geeft u uw waarden voor de parameters op in de volgende opdracht. Voer vervolgens de opdracht uit:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Als u de database wilt herstellen naar SQL Managed Instance, geeft u ook de namen van de doelresourcegroep en doelgroep op SQL Managed Instance:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Zie [Restore-AzSqlInstanceDatabase voor meer informatie.](/powershell/module/az.sql/restore-azsqlinstancedatabase)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Zie Azure CLI installeren als u De Azure CLI nog [niet hebt geïnstalleerd.](/cli/azure/install-azure-cli)

Als u de database wilt herstellen met behulp van de Azure CLI, geeft u uw waarden voor de parameters op in de volgende opdracht. Voer vervolgens de opdracht uit:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Als u de database wilt herstellen naar een SQL Managed Instance, geeft u ook de namen op van de doelresourcegroep en SQL Managed Instance:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Zie de CLI-documentatie voor het herstellen van een database in een SQL Managed Instance voor een gedetailleerde [uitleg van de beschikbare parameters.](/cli/azure/sql/midb#az_sql_midb_restore)

---

## <a name="restore-a-deleted-database"></a>Een verwijderde database herstellen

U kunt een verwijderde database herstellen met behulp van PowerShell of Azure Portal. Als u een verwijderde database naar hetzelfde exemplaar wilt herstellen, gebruikt u de Azure Portal of PowerShell. Gebruik PowerShell om een verwijderde database te herstellen naar een ander exemplaar. 

### <a name="portal"></a>Portal 


Als u een beheerde database wilt herstellen met behulp Azure Portal, opent u SQL Managed Instance overzichtspagina en **selecteert u Verwijderde databases.** Kies een verwijderde database die u wilt herstellen en typ de naam voor de nieuwe database die wordt gemaakt met gegevens die vanuit de back-up worden hersteld.

  ![Schermopname van het herstellen van een Azure SQL exemplaardatabase](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

.. /.. /sql-database

### <a name="powershell"></a>PowerShell

Als u een database naar hetzelfde exemplaar wilt herstellen, moet u de parameterwaarden bijwerken en vervolgens de volgende PowerShell-opdracht uitvoeren: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Als u de database wilt herstellen naar SQL Managed Instance, geeft u ook de namen van de doelresourcegroep en doelgroep op SQL Managed Instance:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Een bestaande database overschrijven

Als u een bestaande database wilt overschrijven, moet u het volgende doen:

1. De bestaande database verwijderen die u wilt overschrijven.
2. Wijzig de naam van de herstelde database naar een bepaald tijdstip in de naam van de database die u hebt verwijderd.

### <a name="drop-the-original-database"></a>De oorspronkelijke database verwijderen

U kunt de database verwijderen met behulp van Azure Portal, PowerShell of de Azure CLI.

U kunt de database ook verwijderen door rechtstreeks verbinding te maken met de SQL Managed Instance, SQL Server Management Studio (SSMS) te starten en vervolgens de volgende Transact-SQL-opdracht (T-SQL) uit te voeren:

```sql
DROP DATABASE WorldWideImporters;
```

Gebruik een van de volgende methoden om verbinding te maken met uw database in SQL Managed Instance:

- [SSMS/Azure Data Studio via een virtuele Azure-machine](./connect-vm-instance-configure.md)
- [Punt-naar-site](./point-to-site-p2s-configure.md)
- [Openbaar eindpunt](./public-endpoint-configure.md)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer in Azure Portal de database in het SQL Managed Instance en selecteer **vervolgens Verwijderen.**

   ![Een database verwijderen met behulp van de Azure Portal](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de volgende PowerShell-opdracht om een bestaande database uit een SQL Managed Instance:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende Azure CLI-opdracht om een bestaande database uit een SQL Managed Instance:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Wijzig de naam van de nieuwe database zo dat deze overeenkomen met de oorspronkelijke databasenaam

Maak rechtstreeks verbinding met de SQL Managed Instance en start SQL Server Management Studio. Voer vervolgens de volgende Transact-SQL-query (T-SQL) uit. De query wijzigt de naam van de herstelde database in die van de verwijderde database die u wilt overschrijven.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Gebruik een van de volgende methoden om verbinding te maken met uw database in SQL Managed Instance:

- [Virtuele Azure-machine](./connect-vm-instance-configure.md)
- [Punt-naar-site](./point-to-site-p2s-configure.md)
- [Openbaar eindpunt](./public-endpoint-configure.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [automatische back-ups.](../database/automated-backups-overview.md)
