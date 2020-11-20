---
title: 'Power shell: SQL Server migreren naar een beheerd exemplaar van SQL'
titleSuffix: Azure Database Migration Service
description: Meer informatie over het migreren van SQL Server naar Azure SQL Managed instance met behulp van Azure PowerShell en de Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 9e1c45b99138a05ef78976b90f65f57304e676ff
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962770"
---
# <a name="migrate-sql-server-to-sql-managed-instance-with-powershell--azure-database-migration-service"></a>SQL Server migreren naar een beheerd exemplaar van SQL met Power shell-& Azure Database Migration Service

In dit artikel migreert u de **Adventureworks2016** -data base die is hersteld naar een on-premises exemplaar van SQL Server 2005 of hoger naar een door Azure SQL SQL beheerd exemplaar met behulp van Microsoft Azure PowerShell. U kunt data bases van een SQL Server-exemplaar migreren naar een SQL-beheerd exemplaar met behulp van de `Az.DataMigration` module in Microsoft Azure PowerShell.

In dit artikel leert u het volgende:
> [!div class="checklist"]
>
> * Een resourcegroep maken.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Een migratie project maken in een exemplaar van Azure Database Migration Service.
> * De migratie uitvoeren.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Dit artikel bevat gedetailleerde informatie over het uitvoeren van online-en offline migraties.

## <a name="prerequisites"></a>Vereisten

Als u deze stappen wilt uitvoeren, hebt u het volgende nodig:

* [SQL Server 2016 of hoger](https://www.microsoft.com/sql-server/sql-server-downloads) (alle edities).
* Een lokale kopie van de **AdventureWorks2016** -data base, die [hier](/sql/samples/adventureworks-install-configure?view=sql-server-2017)kan worden gedownload.
* Om het TCP/IP-protocol in te scha kelen, dat standaard is uitgeschakeld bij SQL Server Express installatie. Schakel het TCP/IP-protocol in door het artikel [een server netwerk protocol in of uit](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)te scha kelen.
* Als u uw [Windows Firewall wilt configureren voor toegang tot de data base-engine](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een door SQL beheerd exemplaar. U kunt een SQL Managed instance maken door de details in het artikel [een beheerde ASQL-instantie maken](../azure-sql/managed-instance/instance-create-quickstart.md)te volgen.
* Om [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 of nieuwer te downloaden en te installeren.
* Een Microsoft Azure Virtual Network gemaakt met behulp van het Azure Resource Manager-implementatie model, waarmee u de Azure Database Migration Service met site-naar-site-verbinding met uw on-premises bron servers kunt maken met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Een voltooide evaluatie van uw on-premises data base en schema migratie met behulp van Data Migration Assistant, zoals beschreven in het artikel [een SQL Server migratie-evaluatie uitvoeren](/sql/dma/dma-assesssqlonprem).
* Voor het downloaden en installeren `Az.DataMigration` van de module (versie 0.7.2 of hoger) via de PowerShell Gallery met behulp van de [Power shell-cmdlet install-module](/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Om ervoor te zorgen dat de referenties die worden gebruikt om verbinding te maken met de bron SQL Server, de [controle server](/sql/t-sql/statements/grant-server-permissions-transact-sql) machtiging hebben.
* Om ervoor te zorgen dat de referenties die worden gebruikt om verbinding te maken met een doel-SQL Managed instance, de machtiging besturings beheer hebben voor de data bases van het beheerde exemplaar van SQL.

    > [!IMPORTANT]
    > Voor online migraties moet u uw Azure Active Directory referenties al hebben ingesteld. Zie voor meer informatie het artikel [de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Aanmelden bij uw Microsoft Azure-abonnement

Meld u aan bij uw Azure-abonnement met behulp van Power shell. Zie het artikel [Aanmelden met Azure PowerShell](/powershell/azure/authenticate-azureps)voor meer informatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resource groep met behulp van de [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) opdracht.

In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt in de regio *VS Oost* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Een exemplaar maken van Azure Database Migration Service

U kunt een nieuw exemplaar van Azure Database Migration Service maken met behulp van de- `New-AzDataMigrationService` cmdlet.
Voor deze cmdlet worden de volgende vereiste para meters verwacht:

* *Naam van de Azure-resource groep*. U kunt [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) de opdracht gebruiken om een Azure-resource groep te maken zoals eerder wordt weer gegeven en de naam opgeven als een para meter.
* *Service naam*. Teken reeks die overeenkomt met de gewenste unieke service naam voor Azure Database Migration Service.
* *Locatie*. Hiermee geeft u de locatie van de service. Geef een Azure Data Center-locatie op, zoals vs-West of Zuidoost-Azië.
* *SKU*. Deze para meter komt overeen met de naam van een DMS-SKU. Momenteel ondersteunde SKU-namen zijn *Basic_1vCore*, *Basic_2vCores* *GeneralPurpose_4vCores*.
* *Id van het virtuele subnet*. U kunt de cmdlet gebruiken [`New-AzVirtualNetworkSubnetConfig`](//powershell/module/az.network/new-azvirtualnetworksubnetconfig) om een subnet te maken.

In het volgende voor beeld wordt een service gemaakt met de naam *MyDMS* in de resource groep *MyDMSResourceGroup* die zich bevindt in de regio *VS Oost* met behulp van een virtueel netwerk met de naam *MyVNET* en een subnet met de naam *MySubnet*.

> [!IMPORTANT]
> Het onderstaande code fragment is voor een offline migratie waarvoor geen instantie van Azure Database Migration Service op basis van een Premium-SKU is vereist. Voor een online migratie moet de waarde van de para meter-SKU een Premium-SKU bevatten.

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

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Een database verbindings info-object maken voor de bron-en doel verbindingen

U kunt een object data base-verbindings gegevens maken met behulp van de `New-AzDmsConnInfo` -cmdlet, die de volgende para meters verwacht:

* *Server type*. Het type aangevraagde database verbinding, bijvoorbeeld SQL, Oracle of MySQL. Gebruik SQL voor SQL Server en Azure SQL.
* *Gegevens bron*. De naam of het IP-adres van een SQL Server exemplaar of Azure SQL Database exemplaar.
* *AuthType*. Het verificatie type voor de verbinding. Dit kan SqlAuthentication of WindowsAuthentication zijn.
* *TrustServerCertificate*. Met deze para meter wordt een waarde ingesteld die aangeeft of het kanaal is versleuteld tijdens het passeren van het door lopen van de certificaat keten om de vertrouwens relatie te valideren. De waarde kan `$true` of zijn `$false` .

In het volgende voor beeld wordt een verbindings info-object gemaakt voor een bron SQL Server met de naam *MySourceSQLServer* met behulp van SQL-verificatie:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

In het volgende voor beeld ziet u het maken van verbindings gegevens voor een Azure SQL Managed instance met de naam ' targetmanagedinstance ':

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
```

### <a name="provide-databases-for-the-migration-project"></a>Data bases voor het migratie project opgeven

Maak een lijst met `AzDataMigrationDatabaseInfo` objecten die data bases opgeven als onderdeel van het Azure database Migration service project, die als para meter kan worden opgegeven voor het maken van het project. U kunt de cmdlet gebruiken `New-AzDataMigrationDatabaseInfo` om te maken `AzDataMigrationDatabaseInfo` .

In het volgende voor beeld wordt het `AzDataMigrationDatabaseInfo` project voor de **AdventureWorks2016** -data base gemaakt en toegevoegd aan de lijst die als para meter voor het maken van het project moet worden aangeboden.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Een project-object maken

Ten slotte kunt u een Azure Database Migration Service project maken met de naam *MyDMSProject* in *VS-Oost* , met behulp van `New-AzDataMigrationProject` en de eerder gemaakte bron-en doel verbindingen toevoegen en de lijst met te migreren data bases.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Een migratie taak maken en starten

Maak vervolgens een Azure Database Migration Service taak en start deze. Voor deze taak zijn verbindings referentie gegevens vereist voor zowel de bron als het doel, evenals de lijst met database tabellen die moeten worden gemigreerd en de informatie die al aan het project is toegevoegd als een vereiste.

### <a name="create-credential-parameters-for-source-and-target"></a>Referentie parameters maken voor bron en doel

Maak beveiligings referenties voor de verbinding als een [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) -object.

In het volgende voor beeld wordt het maken van *PSCredential* -objecten voor zowel de bron-als de doel verbinding weer gegeven, waarbij wacht woorden worden opgegeven als teken reeks variabelen *$sourcePassword* en *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Een backup file share-object maken

Maak nu een file share-object dat de lokale SMB-netwerk share vertegenwoordigt waarmee Azure Database Migration Service de back-ups van de bron database kunt maken met behulp van de `New-AzDmsFileShare` cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Geselecteerd database object maken

De volgende stap is het selecteren van de bron-en doel database met behulp van de- `New-AzDmsSelectedDB` cmdlet.

Het volgende voor beeld is het migreren van één data base van SQL Server naar een door Azure SQL beheerd exemplaar:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Als voor een hele SQL Server-instantie een lift-en-verschuiving in een Azure SQL Managed instance nodig is, wordt een lus voor het uitvoeren van alle data bases uit de bron weer gegeven. Geef in het volgende voor beeld voor $Server, $SourceUserName en $SourcePassword uw bron SQL Server Details op.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>SAS-URI voor Azure Storage-container

Maak een variabele met de SAS-URI die de Azure Database Migration Service biedt met toegang tot de container van het opslag account waarnaar de back-upbestanden worden geüpload.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

> [!NOTE]
> Azure Database Migration Service biedt geen ondersteuning voor het gebruik van een SAS-token op account niveau. U moet een SAS-URI gebruiken voor de container van het opslag account. [Lees hier meer over het verkrijgen van de SAS-URI voor de blob-container](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

### <a name="additional-configuration-requirements"></a>Aanvullende configuratie vereisten

Er zijn enkele aanvullende vereisten die u moet aanpakken, maar deze verschillen, afhankelijk van of u een offline-of online migratie uitvoert.

#### <a name="offline-migrations"></a>Offline migraties

Voer de volgende aanvullende configuratie taken uit voor offline migraties.

* **Selecteer aanmeldingen**. Maak een lijst met aanmeldingen die moeten worden gemigreerd, zoals wordt weer gegeven in het volgende voor beeld:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Momenteel biedt Azure Database Migration Service alleen ondersteuning voor het migreren van SQL-aanmeldingen.

* **Selecteer Agent taken**. Maak een lijst met Agent taken die moeten worden gemigreerd, zoals wordt weer gegeven in het volgende voor beeld:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Op dit moment ondersteunt Azure Database Migration Service alleen taken met de taak stappen van het subsysteem T-SQL.

#### <a name="online-migrations"></a>Online migraties

Voer de volgende aanvullende configuratie taken uit voor alleen online migraties.

* **Azure Active Directory-app configureren**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Opslag Resource configureren**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>De migratie taak maken en starten

Gebruik de `New-AzDataMigrationTask` cmdlet om een migratie taak te maken en te starten.

#### <a name="specify-parameters"></a>Para meters opgeven

##### <a name="common-parameters"></a>Algemene para meters

Ongeacht of u een offline-of online migratie uitvoert, `New-AzDataMigrationTask` verwacht de cmdlet de volgende para meters:

* *TaskType*. Type migratie taak dat moet worden gemaakt voor SQL Server naar het migratie type *MigrateSqlServerSqlDbMi* van Azure SQL Managed instance wordt verwacht. 
* *Naam van de resource groep*. De naam van de Azure-resource groep waarin de taak moet worden gemaakt.
* *ServiceName*. Azure Database Migration Service exemplaar waarin de taak moet worden gemaakt.
* *ProjectName*. De naam van het Azure Database Migration Service project waarin de taak moet worden gemaakt. 
* *Taaknaam*. De naam van de taak die moet worden gemaakt. 
* *SourceConnection*. AzDmsConnInfo-object dat de verbinding van de bron SQL Server vertegenwoordigt.
* *TargetConnection*. AzDmsConnInfo-object dat de doel verbinding van Azure SQL Managed instance vertegenwoordigt.
* *SourceCred*. [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) -object voor het maken van verbinding met de bron server.
* *TargetCred*. [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) -object om verbinding te maken met de doel server.
* *SelectedDatabase*. AzDataMigrationSelectedDB-object dat de toewijzing van de bron-en doel database vertegenwoordigt.
* *BackupFileShare*. File share-object dat de lokale netwerk share vertegenwoordigt waarnaar de Azure Database Migration Service de back-ups van de bron database kan maken.
* *BackupBlobSasUri*. De SAS-URI die de Azure Database Migration Service biedt met toegang tot de container van het opslag account waarnaar de back-upbestanden worden geüpload door de service. Lees hier meer over het verkrijgen van de SAS-URI voor de blob-container.
* *SelectedLogins*. Een lijst met geselecteerde aanmeldingen die moeten worden gemigreerd.
* *SelectedAgentJobs*. Lijst met geselecteerde Agent taken die moeten worden gemigreerd.

##### <a name="additional-parameters"></a>Aanvullende para meters

De `New-AzDataMigrationTask` cmdlet verwacht ook para meters die uniek zijn voor het type migratie, offline of online, dat u uitvoert.

* **Offline migraties**. Voor offline migraties verwacht de `New-AzDataMigrationTask` cmdlet ook de volgende para meters:

  * *SelectedLogins*. Een lijst met geselecteerde aanmeldingen die moeten worden gemigreerd.
  * *SelectedAgentJobs*. Lijst met geselecteerde Agent taken die moeten worden gemigreerd.

* **Online migraties**. Voor online migraties verwacht de `New-AzDataMigrationTask` cmdlet ook de volgende para meters.

* *AzureActiveDirectoryApp*. Active Directory-toepassing.
* *StorageResourceID*. De resource-ID van het opslag account.

#### <a name="create-and-start-an-offline-migration-task"></a>Een taak voor een offline migratie maken en starten

In het volgende voor beeld wordt een offline migratie taak gemaakt en gestart met de naam **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>Een online migratie taak maken en starten

In het volgende voor beeld wordt een online migratie taak gemaakt en gestart met de naam **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>Bewaak de migratie

Voer de volgende taken uit om de migratie te controleren.

1. Consolideer alle migratie Details in een variabele met de naam $CheckTask.

    Gebruik het volgende code fragment om migratie gegevens te combi neren, zoals eigenschappen, status en database gegevens die zijn gekoppeld aan de migratie:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Gebruik de `$CheckTask` variabele om de huidige status van de migratie taak op te halen.

    `$CheckTask`Als u de variabele wilt gebruiken om de huidige status van de migratie taak op te halen, kunt u de migratie taak bewaken die wordt uitgevoerd door een query uit te voeren op de eigenschap State van de taak, zoals wordt weer gegeven in het volgende voor beeld:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>De cutover uitvoeren (alleen online migraties)

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