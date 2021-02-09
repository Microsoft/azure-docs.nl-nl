---
title: 'Azure SQL Database: Bewaar periode voor lange termijn back-ups beheren'
description: Meer informatie over het opslaan en herstellen van automatische back-ups voor Azure SQL Database in azure Storage (Maxi maal tien jaar) met behulp van de Azure Portal en Power shell
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/16/2020
ms.openlocfilehash: a0653f24eeb0a96c28714d00f1d943dfc7d336db
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979688"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Azure SQL Database lange termijn retentie van back-ups beheren
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In Azure SQL Database kunt u een Data Base configureren met een [Bewaar beleid voor lange termijn back-ups](long-term-retention-overview.md) (LTR) om de back-ups van de data base automatisch in afzonderlijke Azure Blob-opslag containers te bewaren gedurende Maxi maal tien jaar. U kunt vervolgens met behulp van de Azure Portal of Power shell een Data Base herstellen met behulp van deze back-ups. U kunt ook lange termijn retentie voor een [Azure SQL Managed instance](../managed-instance/long-term-backup-retention-configure.md) configureren, maar dit is momenteel een beperkte open bare preview-versie.

## <a name="using-the-azure-portal"></a>Azure Portal gebruiken

In de volgende secties ziet u hoe u de Azure Portal kunt gebruiken voor het configureren van de lange termijn retentie, het weer geven van back-ups voor lange termijn retentie en het terugzetten van back-ups voor lange termijn retentie.

### <a name="configure-long-term-retention-policies"></a>Beleid voor lange termijn retentie configureren

U kunt SQL Database configureren om [automatische back-ups te bewaren](long-term-retention-overview.md) gedurende een periode die langer is dan de retentie periode voor uw servicelaag.

1. Selecteer in de Azure Portal uw SQL Server-exemplaar en klik vervolgens op **back-ups beheren**. Schakel op het tabblad **beleid configureren** het selectie vakje in voor de data base waarvoor u het Bewaar beleid voor back-ups op lange termijn wilt instellen of wijzigen. Als het selectie vakje naast de data base niet is ingeschakeld, zijn de wijzigingen voor het beleid niet van toepassing op die data base.  

   ![koppeling back-ups beheren](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. Selecteer in het deel venster **beleid configureren** of u wekelijkse, maandelijkse of jaarlijkse back-ups wilt behouden en geef de Bewaar periode voor elk op.

   ![beleid configureren](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. Wanneer u klaar bent, klikt u op **Toep assen**.

> [!IMPORTANT]
> Wanneer u een lange termijn beleid voor het bewaren van back-ups inschakelt, kan het tot zeven dagen duren voordat de eerste back-up zichtbaar is en beschikbaar is voor herstel. Zie [lange termijn retentie van back-ups](long-term-retention-overview.md)voor meer informatie over de CADANCE voor LTR-back-ups.

### <a name="view-backups-and-restore-from-a-backup"></a>Back-ups weer geven en terugzetten vanuit een back-up

Bekijk de back-ups die worden bewaard voor een specifieke data base met een LTR-beleid en herstel van deze back-ups.

1. Selecteer uw server in de Azure Portal en klik vervolgens op **back-ups beheren**. Selecteer de data base waarvoor u beschik bare back-ups wilt weer geven op het tabblad **beschik bare back-ups** .

   ![data base selecteren](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. Bekijk de beschik bare back-ups in het deel venster **beschik bare back-ups** .

   ![back-ups weer geven](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Selecteer de back-up van waaruit u wilt herstellen en geef vervolgens de naam van de nieuwe Data Base op.

   ![De pagina Restore](./media/long-term-backup-retention-configure/ltr-restore.png)

1. Klik op **OK** om de data base terug te zetten van de back-up in azure Storage naar de nieuwe data base.

1. Klik op de werkbalk op het meldingspictogram om de status van de hersteltaak weer te geven.

   ![voortgang hersteltaak](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Wanneer de herstel taak is voltooid, opent u de pagina **SQL-data bases** om de zojuist herstelde data base weer te geven.

> [!NOTE]
> Hier kunt u verbinding maken met de herstelde database met behulp van SQL Server Management Studio om noodzakelijke taken uit te voeren, zoals [een deel van de gegevens uit de herstelde database extraheren om naar de bestaande database te kopiëren, of de bestaande database verwijderen en de naam van de herstelde database wijzigen in de naam van de bestaande database](recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>PowerShell gebruiken

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De module PowerShell Azure Resource Manager wordt nog steeds ondersteund in Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

In de volgende secties ziet u hoe u Power shell kunt gebruiken voor het configureren van de lange termijn retentie van back-ups, het weer geven van back-ups in azure Storage en het terugzetten van een back-up in azure Storage.

### <a name="azure-roles-to-manage-long-term-retention"></a>Azure-functies voor het beheren van lange termijn retentie

Voor **Get-AzSqlDatabaseLongTermRetentionBackup** en **Restore-AzSqlDatabase** moet u een van de volgende rollen hebben:

- Rol van abonnements eigenaar of
- SQL Server rol Inzender of
- Aangepaste rol met de volgende machtigingen:

   Micro soft. SQL/locations/longTermRetentionBackups/Read micro soft. SQL/locations/longTermRetentionServers/longTermRetentionBackups/Read micro soft. SQL/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/lezen

Voor **Remove-AzSqlDatabaseLongTermRetentionBackup** moet u een van de volgende rollen hebben:

- Rol van abonnements eigenaar of
- Aangepaste rol met de volgende machtiging:

   Micro soft. SQL/locaties/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/verwijderen

> [!NOTE]
> De rol SQL Server Inzender heeft geen machtiging voor het verwijderen van LTR-back-ups.

Er kunnen Azure RBAC-machtigingen worden verleend in *abonnementen* of in het bereik van de *resource groep* . Voor toegang tot LTR-back-ups die deel uitmaken van een verwijderde server, moet de machtiging echter worden verleend in het *abonnements* bereik van die server.

- Micro soft. SQL/locaties/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/verwijderen

### <a name="create-an-ltr-policy"></a>Een LTR-beleid maken

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>LTR-beleid weer geven

Dit voor beeld laat zien hoe u het LTR-beleid in een server kunt weer geven

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>Een LTR-beleid wissen

Dit voor beeld laat zien hoe u een LTR-beleid uit een Data Base wist

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR-back-ups weer geven

Dit voor beeld laat zien hoe u de LTR-back-ups in een server kunt weer geven.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR-back-ups verwijderen

In dit voor beeld ziet u hoe u een LTR-back-up verwijdert uit de lijst met back-ups.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> LTR-back-up verwijderen is niet-omkeerbaar. Als u een LTR-back-up wilt verwijderen nadat de server of resource groep is verwijderd, moet u een machtiging voor het abonnements bereik hebben. U kunt meldingen over elke verwijderings bewerking instellen in Azure Monitor door te filteren op een back-up van een lange termijn retentie verwijderen. Het activiteiten logboek bevat informatie over wie en wanneer de aanvraag is ingediend. Zie [waarschuwingen voor activiteiten logboeken maken](../../azure-monitor/platform/alerts-activity-log.md) voor gedetailleerde instructies.

### <a name="restore-from-ltr-backups"></a>Herstellen vanuit LTR-back-ups

In dit voor beeld ziet u hoe u een LTR-back-up herstelt. Opmerking: deze interface is niet gewijzigd, maar voor de resource-ID-para meter is nu de bron-ID LTR backup vereist.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Als u een LTR-back-up wilt herstellen nadat de server of resource groep is verwijderd, moet u machtigingen hebben die zijn afgestemd op het abonnement van de server en dat abonnement actief is. U moet ook de optionele para meter-ResourceGroupName weglaten.

> [!NOTE]
> Hier kunt u verbinding maken met de herstelde database met behulp van SQL Server Management Studio om noodzakelijke taken uit te voeren, zoals een deel van de gegevens uit de herstelde database extraheren om naar de bestaande database te kopiëren, of de bestaande database verwijderen en de naam van de herstelde database wijzigen in de naam van de bestaande database. Zie [herstel punt in tijd](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations"></a>Beperkingen
- Bij het herstellen van een LTR-back-up is de eigenschap Lees schaal uitgeschakeld. Als u de herstelde data base wilt inschakelen, moet u deze bijwerken door de data base bij te werken nadat deze is gemaakt.
- U moet het doel doelstelling van het service niveau opgeven bij het herstellen van een LTR-back-up die is gemaakt toen de data base zich in een elastische pool bevond. 

## <a name="next-steps"></a>Volgende stappen

- Zie [Automatic backups](automated-backups-overview.md) (Automatische back-ups) voor meer informatie over door de service gegenereerde automatische back-ups.
- Zie [Langetermijnretentie](long-term-retention-overview.md) voor meer informatie over back-ups met langetermijnretentie.
