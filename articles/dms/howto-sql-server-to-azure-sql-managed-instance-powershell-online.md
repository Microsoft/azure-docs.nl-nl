---
title: 'Power shell: migratie van SQL Server naar SQL Managed instance online'
titleSuffix: Azure Database Migration Service
description: Meer informatie over het online migreren van SQL Server naar Azure SQL Managed instance met behulp van Azure PowerShell en de Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 7f09db2e1f98d48e91dfea2642969ff4ca360967
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697795"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>SQL Server naar SQL Managed instance online migreren met Power shell-& Azure Database Migration Service

In dit artikel kunt u de **Adventureworks2016** -data base die is hersteld naar een on-premises exemplaar van SQL Server 2005 of hoger, migreren naar een door Azure SQL SQL beheerd exemplaar met behulp van Microsoft Azure PowerShell. U kunt data bases van een SQL Server-exemplaar migreren naar een SQL-beheerd exemplaar met behulp van de `Az.DataMigration` module in Microsoft Azure PowerShell.

In dit artikel leert u het volgende:
> [!div class="checklist"]
>
> * Een resourcegroep maken.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Een migratie project maken in een exemplaar van Azure Database Migration Service.
> * Voer de migratie online uit.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Dit artikel bevat stappen voor een online migratie, maar het is ook mogelijk om [offline](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)te migreren.


## <a name="prerequisites"></a>Vereisten

Als u deze stappen wilt uitvoeren, hebt u het volgende nodig:

* [SQL Server 2016 of hoger](https://www.microsoft.com/sql-server/sql-server-downloads) (alle edities).
* Een lokale kopie van de **AdventureWorks2016** -data base, die [hier](/sql/samples/adventureworks-install-configure)kan worden gedownload.
* Om het TCP/IP-protocol in te scha kelen, dat standaard is uitgeschakeld bij SQL Server Express installatie. Schakel het TCP/IP-protocol in door het artikel [een server netwerk protocol in of uit](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)te scha kelen.
* Als u uw [Windows Firewall wilt configureren voor toegang tot de data base-engine](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een door SQL beheerd exemplaar. U kunt een SQL Managed instance maken door de details in het artikel [een beheerde ASQL-instantie maken](../azure-sql/managed-instance/instance-create-quickstart.md)te volgen.
* Om [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 of nieuwer te downloaden en te installeren.
* Een Microsoft Azure Virtual Network gemaakt met behulp van het Azure Resource Manager-implementatie model, waarmee u de Azure Database Migration Service met site-naar-site-verbinding met uw on-premises bron servers kunt maken met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Een voltooide evaluatie van uw on-premises data base en schema migratie met behulp van Data Migration Assistant, zoals beschreven in het artikel [een SQL Server migratie-evaluatie uitvoeren](/sql/dma/dma-assesssqlonprem).
* Voor het downloaden en installeren `Az.DataMigration` van de module (versie 0.7.2 of hoger) via de PowerShell Gallery met behulp van de [Power shell-cmdlet install-module](/powershell/module/powershellget/Install-Module).
* Om ervoor te zorgen dat de referenties die worden gebruikt om verbinding te maken met de bron SQL Server, de [controle server](/sql/t-sql/statements/grant-server-permissions-transact-sql) machtiging hebben.
* Om ervoor te zorgen dat de referenties die worden gebruikt om verbinding te maken met een doel-SQL Managed instance, de machtiging besturings beheer hebben voor de data bases van het beheerde exemplaar van SQL.

    > [!IMPORTANT]
    > Voor online migraties moet u uw Azure Active Directory referenties al hebben ingesteld. Zie voor meer informatie het artikel [de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resource groep met behulp van de [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) opdracht.

In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt in de regio *VS Oost* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>Een exemplaar van DMS maken

U kunt een nieuw exemplaar van Azure Database Migration Service maken met behulp van de- `New-AzDataMigrationService` cmdlet.
Voor deze cmdlet worden de volgende vereiste para meters verwacht:

* *Naam van de Azure-resource groep*. U kunt [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) de opdracht gebruiken om een Azure-resource groep te maken zoals eerder wordt weer gegeven en de naam opgeven als een para meter.
* *Service naam*. Teken reeks die overeenkomt met de gewenste unieke service naam voor Azure Database Migration Service.
* *Locatie*. Hiermee geeft u de locatie van de service. Geef een Azure Data Center-locatie op, zoals vs-West of Zuidoost-Azië.
* *SKU*. Deze para meter komt overeen met de naam van een DMS-SKU. Momenteel ondersteunde SKU-namen zijn *Basic_1vCore*, *Basic_2vCores* *GeneralPurpose_4vCores*.
* *Id van het virtuele subnet*. U kunt de cmdlet gebruiken [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) om een subnet te maken.

In het volgende voor beeld wordt een service gemaakt met de naam *MyDMS* in de resource groep *MyDMSResourceGroup* die zich bevindt in de regio *VS Oost* met behulp van een virtueel netwerk met de naam *MyVNET* en een subnet met de naam *MySubnet*.


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat u een Azure Database Migration Service-exemplaar hebt gemaakt, moet u een migratie project maken. Een Azure Database Migration Service project vereist verbindings gegevens voor zowel de bron-als doel instantie, evenals een lijst met data bases die u wilt migreren als onderdeel van het project.
Definieer de verbindings reeksen voor de bron-en doel verbinding.

Het volgende script definieert bron SQL Server verbindings Details: 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

Met het volgende script worden de verbindings gegevens van het doel-SQL Managed instance gedefinieerd: 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>Toewijzing van bron-en doel database definiëren

Data bases opgeven die in dit migratie project moeten worden gemigreerd

Met het volgende script wordt de bron database toegewezen aan de respectieve nieuwe Data Base op het doel-SQL Managed instance met de gegeven naam.

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

Voor meerdere data bases voegt u de lijst met data bases toe aan het bovenstaande script met de volgende indeling:

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>DMS-project maken

U kunt een Azure Database Migration Service project maken binnen het DMS-exemplaar.

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>Een backup file share-object maken

Maak nu een file share-object dat de lokale SMB-netwerk share vertegenwoordigt waarmee Azure Database Migration Service de back-ups van de bron database kunt maken met behulp van de New-AzDmsFileShare-cmdlet.

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>Definieer de Azure Storage 

Selecteer Azure Storage container die moet worden gebruikt voor de migratie: 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>Azure Active Directory-app configureren

Geef de vereiste gegevens voor Azure Active Directory op voor de migratie van een online SQL-beheerde instantie: 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>Een migratie taak maken en starten

Maak vervolgens een Azure Database Migration Service taak en start deze. Roep de bron en het doel aan met behulp van variabelen en vermeld de database tabellen die moeten worden gemigreerd: 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

In het volgende voor beeld wordt een online migratie taak gemaakt en gestart: 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

Zie [New-AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask)voor meer informatie.

## <a name="monitor-the-migration"></a>Bewaak de migratie

Voer de volgende taken uit om de migratie te controleren.

### <a name="check-the-status-of-task"></a>De status van de taak controleren

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

Gebruik het volgende om een lijst met fouten op te halen:-

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>De cutover uitvoeren 

Bij een online migratie wordt een volledige back-up en herstel van data bases uitgevoerd en vervolgens wordt de werk tijd van de transactie logboeken hersteld die zijn opgeslagen in de BackupFileShare.

Wanneer de data base in een Azure SQL Managed instance is bijgewerkt met de meest recente gegevens en is gesynchroniseerd met de bron database, kunt u een cutover uitvoeren.

In het volgende voor beeld wordt de cutover\migration. voltooid Gebruikers roepen deze opdracht op hun eigen keuze op.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Het exemplaar van Azure Database Migration Service verwijderen

Nadat de migratie is voltooid, kunt u de Azure Database Migration Service-instantie verwijderen:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Aanvullende bronnen

Zie de micro soft [Data Base Migration Guide (Engelstalig](https://datamigration.microsoft.com/)) voor meer informatie over aanvullende migratie scenario's (bron/doel paren).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Database Migration Service vindt u in het artikel [Wat is de Azure database Migration service?](./dms-overview.md).