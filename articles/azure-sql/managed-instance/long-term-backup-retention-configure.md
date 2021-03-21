---
title: 'Azure SQL Managed instance: lange termijn retentie van back-ups'
description: Meer informatie over het opslaan en herstellen van automatische back-ups op afzonderlijke Azure Blob Storage-containers voor een Azure SQL-beheerd exemplaar met behulp van Power shell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, sstein
ms.date: 02/25/2021
ms.openlocfilehash: f298f0f9d76750be932db79b5a08b6385e984f88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052015"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Lange termijn back-ups van Azure SQL Managed instance beheren (Power shell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In Azure SQL Managed Instance kunt u een langdurige versie van het Bewaar beleid (LTR) voor [back-ups](../database/long-term-retention-overview.md) configureren als een open bare preview-functie. Zo kunt u automatisch back-ups van data bases in afzonderlijke Azure Blob-opslag containers bewaren gedurende Maxi maal tien jaar. U kunt vervolgens een Data Base met behulp van deze back-ups herstellen met Power shell.

   > [!IMPORTANT]
   > LTR voor beheerde exemplaren is momenteel beschikbaar in open bare preview in open bare Azure-regio's. 

In de volgende secties ziet u hoe u Power shell kunt gebruiken voor het configureren van de lange termijn retentie van back-ups, het weer geven van back-ups in Azure SQL Storage en het herstellen van een back-up in Azure SQL Storage.


## <a name="using-the-azure-portal"></a>Azure Portal gebruiken

In de volgende secties ziet u hoe u de Azure Portal kunt gebruiken voor het instellen van een Bewaar beleid voor lange termijn, het beheren van beschik bare back-ups voor lange termijn retentie en het herstellen van een beschik bare back-up.

### <a name="configure-long-term-retention-policies"></a>Beleid voor lange termijn retentie configureren

U kunt een SQL Managed instance configureren voor het [bewaren van automatische back-ups](../database/long-term-retention-overview.md) gedurende een periode die langer is dan de retentie periode voor uw servicelaag.

1. Selecteer in de Azure Portal uw beheerde instantie en klik vervolgens op **back-ups**. Op het tabblad **Bewaar beleid** selecteert u de data base (s) waarop u het Bewaar beleid voor back-ups op lange termijn wilt instellen of wijzigen. Wijzigingen worden niet toegepast op data bases die niet zijn geselecteerd. 

   ![koppeling back-ups beheren](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. Geef in het deel venster **beleid configureren** de gewenste Bewaar periode op voor wekelijkse, maandelijkse of jaarlijkse back-ups. Kies een Bewaar periode van ' 0 ' om aan te geven dat het bewaren van back-ups op lange termijn niet moet worden ingesteld.

   ![beleid configureren](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. Wanneer u klaar bent, klikt u op **Toep assen**.

> [!IMPORTANT]
> Wanneer u een lange termijn beleid voor het bewaren van back-ups inschakelt, kan het tot zeven dagen duren voordat de eerste back-up zichtbaar is en beschikbaar is voor herstel. Zie [lange termijn retentie van back-ups](../database/long-term-retention-overview.md)voor meer informatie over de CADANCE voor LTR-back-ups.

### <a name="view-backups-and-restore-from-a-backup"></a>Back-ups weer geven en terugzetten vanuit een back-up

Bekijk de back-ups die worden bewaard voor een specifieke data base met een LTR-beleid en herstel van deze back-ups.

1. Selecteer in de Azure Portal uw beheerde instantie en klik vervolgens op **back-ups**. Selecteer de data base waarvoor u beschik bare back-ups wilt weer geven op het tabblad **beschik bare back-ups** . Klik op **Beheren**.

   ![data base selecteren](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. Controleer in het deel venster **back-ups beheren** de beschik bare back-ups.

   ![back-ups weer geven](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Selecteer de back-up van waaruit u wilt herstellen, klik op **herstellen** en geef op de pagina herstellen de nieuwe database naam op. De back-up en de bron worden vooraf ingevuld op deze pagina. 

   ![back-up selecteren voor herstellen](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)
   
   ![De pagina Restore](./media/long-term-backup-retention-configure/ltr-restore.png)

1. Klik op **beoordeling + maken** om de details van de herstel bewerking te controleren. Klik vervolgens op **maken** om de data base terug te zetten vanuit de gekozen back-up.

1. Klik op de werkbalk op het meldingspictogram om de status van de hersteltaak weer te geven.

   ![voortgang hersteltaak](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Wanneer de herstel taak is voltooid, opent u de overzichts pagina van het **beheerde exemplaar** om de zojuist herstelde data base weer te geven.

> [!NOTE]
> Hier kunt u verbinding maken met de herstelde database met behulp van SQL Server Management Studio om noodzakelijke taken uit te voeren, zoals [een deel van de gegevens uit de herstelde database extraheren om naar de bestaande database te kopiëren, of de bestaande database verwijderen en de naam van de herstelde database wijzigen in de naam van de bestaande database](../database/recovery-using-backups.md#point-in-time-restore).


## <a name="using-powershell"></a>PowerShell gebruiken
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar toekomstige ontwikkeling vindt plaats in de module AZ. SQL. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

In de volgende secties ziet u hoe u Power shell kunt gebruiken voor het configureren van de lange termijn retentie van back-ups, het weer geven van back-ups in azure Storage en het terugzetten van een back-up in azure Storage.

### <a name="azure-rbac-roles-to-manage-long-term-retention"></a>Azure RBAC-rollen voor het beheren van lange termijn retentie

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

### <a name="create-an-ltr-policy"></a>Een LTR-beleid maken

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
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-policies"></a>LTR-beleid weer geven

Dit voor beeld laat zien hoe u het LTR-beleid in een exemplaar voor één Data Base kunt weer geven

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

Dit voor beeld laat zien hoe u het LTR-beleid kunt weer geven voor alle data bases in een exemplaar

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

### <a name="clear-an-ltr-policy"></a>Een LTR-beleid wissen

Dit voor beeld laat zien hoe u een LTR-beleid uit een Data Base wist

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-backups"></a>LTR-back-ups weer geven

Dit voor beeld laat zien hoe u de LTR-back-ups binnen een exemplaar kunt weer geven.

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

### <a name="delete-ltr-backups"></a>LTR-back-ups verwijderen

In dit voor beeld ziet u hoe u een LTR-back-up verwijdert uit de lijst met back-ups.

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> LTR-back-up verwijderen is niet-omkeerbaar. Als u een LTR-back-up wilt verwijderen nadat het exemplaar is verwijderd, moet u een machtiging voor het abonnements bereik hebben. U kunt meldingen over elke verwijderings bewerking instellen in Azure Monitor door te filteren op een back-up van een lange termijn retentie verwijderen. Het activiteiten logboek bevat informatie over wie en wanneer de aanvraag is ingediend. Zie [waarschuwingen voor activiteiten logboeken maken](../../azure-monitor/alerts/alerts-activity-log.md) voor gedetailleerde instructies.

### <a name="restore-from-ltr-backups"></a>Herstellen vanuit LTR-back-ups

In dit voor beeld ziet u hoe u een LTR-back-up herstelt. Opmerking: deze interface is niet gewijzigd, maar voor de resource-ID-para meter is nu de bron-ID LTR backup vereist.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> Als u een LTR-back-up wilt herstellen nadat het exemplaar is verwijderd, moet u machtigingen hebben die zijn afgestemd op het abonnement van het exemplaar en dat abonnement moet actief zijn. U moet ook de optionele para meter-ResourceGroupName weglaten.

> [!NOTE]
> Hier kunt u verbinding maken met de herstelde database met behulp van SQL Server Management Studio om noodzakelijke taken uit te voeren, zoals een deel van de gegevens uit de herstelde database extraheren om naar de bestaande database te kopiëren, of de bestaande database verwijderen en de naam van de herstelde database wijzigen in de naam van de bestaande database. Zie [herstel punt in tijd](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Volgende stappen

- Zie [Automatic backups](../database/automated-backups-overview.md) (Automatische back-ups) voor meer informatie over door de service gegenereerde automatische back-ups.
- Zie [Langetermijnretentie](../database/long-term-retention-overview.md) voor meer informatie over back-ups met langetermijnretentie.
