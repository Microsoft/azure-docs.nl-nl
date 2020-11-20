---
title: 'Azure SQL Managed instance: lange termijn retentie van back-ups (Power shell)'
description: Meer informatie over het opslaan en herstellen van automatische back-ups op afzonderlijke Azure Blob Storage-containers voor een Azure SQL-beheerd exemplaar met behulp van Power shell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 04/29/2020
ms.openlocfilehash: ec193eab02d937e9d93b8632fa171fec8227d6c2
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987487"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Lange termijn back-ups van Azure SQL Managed instance beheren (Power shell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In Azure SQL Managed Instance kunt u een [lange termijn voor het bewaren van back-ups](../database/long-term-retention-overview.md#sql-managed-instance-support) (LTR) configureren als een beperkte open bare preview-functie. Zo kunt u automatisch back-ups van data bases in afzonderlijke Azure Blob-opslag containers bewaren gedurende Maxi maal tien jaar. U kunt vervolgens een Data Base met behulp van deze back-ups herstellen met Power shell.

   > [!IMPORTANT]
   > LTR voor beheerde instanties is momenteel een beperkte preview-versie en is per geval beschikbaar voor EA-en CSP-abonnementen. Als u de inschrijving wilt aanvragen, moet u een [ondersteunings ticket voor Azure](https://azure.microsoft.com/support/create-ticket/)maken. Voor het probleem type SELECT Technical issue, voor service Kies SQL Database Managed instance en voor het probleem type Selecteer **back-up, herstel en bedrijfs continuïteit/lange termijn retentie van back-ups**. In uw aanvraag moet u aangeven dat u wilt worden inge schreven in de beperkte open bare preview van LTR voor een beheerd exemplaar.

In de volgende secties ziet u hoe u Power shell kunt gebruiken voor het configureren van de lange termijn retentie van back-ups, het weer geven van back-ups in Azure SQL Storage en het herstellen van een back-up in Azure SQL Storage.

## <a name="azure-roles-to-manage-long-term-retention"></a>Azure-functies voor het beheren van lange termijn retentie

Voor **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** en **Restore-AzSqlInstanceDatabase** moet u een van de volgende rollen hebben:

- Rol van abonnements eigenaar of
- Rol Inzender voor beheerde instanties of
- Aangepaste rol met de volgende machtigingen:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

Voor **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup** moet u een van de volgende rollen hebben:

- Rol van abonnements eigenaar of
- Aangepaste rol met de volgende machtiging:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> De rol Inzender voor beheerde instanties heeft geen machtiging voor het verwijderen van V.L.N.R.-back-ups.

Er kunnen Azure RBAC-machtigingen worden verleend in *abonnementen* of in het bereik van de *resource groep* . Voor toegang tot LTR-back-ups die deel uitmaken van een verwijderde instantie, moet de machtiging echter worden verleend in het *abonnements* bereik van dat exemplaar.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>Een LTR-beleid maken

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

## <a name="view-ltr-policies"></a>LTR-beleid weer geven

Dit voor beeld laat zien hoe u het LTR-beleid in een exemplaar kunt weer geven

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup
```

## <a name="clear-an-ltr-policy"></a>Een LTR-beleid wissen

Dit voor beeld laat zien hoe u een LTR-beleid uit een Data Base wist

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>LTR-back-ups weer geven

Dit voor beeld laat zien hoe u de LTR-back-ups binnen een exemplaar kunt weer geven.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName

# get the LTR backups for a specific database from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -OnlyLatestPerDatabase
```

## <a name="delete-ltr-backups"></a>LTR-back-ups verwijderen

In dit voor beeld ziet u hoe u een LTR-back-up verwijdert uit de lijst met back-ups.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> LTR-back-up verwijderen is niet-omkeerbaar. Als u een LTR-back-up wilt verwijderen nadat het exemplaar is verwijderd, moet u een machtiging voor het abonnements bereik hebben. U kunt meldingen over elke verwijderings bewerking instellen in Azure Monitor door te filteren op een back-up van een lange termijn retentie verwijderen. Het activiteiten logboek bevat informatie over wie en wanneer de aanvraag is ingediend. Zie [waarschuwingen voor activiteiten logboeken maken](../../azure-monitor/platform/alerts-activity-log.md) voor gedetailleerde instructies.

## <a name="restore-from-ltr-backups"></a>Herstellen vanuit LTR-back-ups

In dit voor beeld ziet u hoe u een LTR-back-up herstelt. Opmerking: deze interface is niet gewijzigd, maar voor de resource-id-para meter is nu de bron-id LTR backup vereist.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> Als u een LTR-back-up wilt herstellen nadat het exemplaar is verwijderd, moet u machtigingen hebben die zijn afgestemd op het abonnement van het exemplaar en dat abonnement moet actief zijn. U moet ook de optionele para meter-ResourceGroupName weglaten.

> [!NOTE]
> Hier kunt u verbinding maken met de herstelde database met behulp van SQL Server Management Studio om noodzakelijke taken uit te voeren, zoals een deel van de gegevens uit de herstelde database extraheren om naar de bestaande database te kopiëren, of de bestaande database verwijderen en de naam van de herstelde database wijzigen in de naam van de bestaande database. Zie [herstel punt in tijd](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Volgende stappen

- Zie [Automatic backups](../database/automated-backups-overview.md) (Automatische back-ups) voor meer informatie over door de service gegenereerde automatische back-ups.
- Zie [Langetermijnretentie](../database/long-term-retention-overview.md) voor meer informatie over back-ups met langetermijnretentie.
