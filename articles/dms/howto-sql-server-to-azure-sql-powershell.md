---
title: 'Power shell: SQL Server migreren naar SQL Database'
titleSuffix: Azure Database Migration Service
description: Meer informatie over het migreren van een Data Base van SQL Server naar Azure SQL Database met behulp van Azure PowerShell met de Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: a8f7e14500fb377b46f651b53e2704d8477aea7a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520656"
---
# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-azure-powershell"></a>Een SQL Server-Data Base migreren naar Azure SQL Database met behulp van Azure PowerShell

In dit artikel migreert u de **Adventureworks2012** -data base die is hersteld naar een on-premises exemplaar van SQL Server 2016 of hoger naar Azure SQL database met behulp van Microsoft Azure PowerShell. U kunt data bases van een SQL Server-exemplaar migreren naar Azure SQL Database met behulp van de `Az.DataMigration` module in Microsoft Azure PowerShell.

In dit artikel leert u het volgende:
> [!div class="checklist"]
>
> * Een resourcegroep maken.
> * De Azure-portal gebruiken om een Azure Database Migration Service-exemplaar te maken.
> * Een migratie project maken in een Azure Database Migration Service-exemplaar.
> * De migratie uitvoeren.

## <a name="prerequisites"></a>Vereisten

Als u deze stappen wilt uitvoeren, hebt u het volgende nodig:

* [SQL Server 2016 of hoger](https://www.microsoft.com/sql-server/sql-server-downloads) (alle edities)
* Om het TCP/IP-protocol in te scha kelen, dat standaard is uitgeschakeld bij SQL Server Express installatie. Schakel het TCP/IP-protocol in door het artikel [een server netwerk protocol in of uit](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)te scha kelen.
* Als u uw [Windows Firewall wilt configureren voor toegang tot de data base-engine](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Een Azure SQL Database-exemplaar. U kunt een Azure SQL Database-exemplaar maken door de details te volgen in het artikel [een Data Base maken in Azure SQL database in de Azure Portal](../azure-sql/database/single-database-create-quickstart.md).
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 of hoger.
* Als u een Microsoft Azure Virtual Network wilt maken met behulp van het Azure Resource Manager-implementatie model, dat de Azure Database Migration Service met site-naar-site-verbinding met uw on-premises bron servers biedt met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Als u de evaluatie van uw on-premises data base en schema migratie met behulp van Data Migration Assistant wilt volt ooien, zoals beschreven in het artikel [een SQL Server migratie-evaluatie uitvoeren](/sql/dma/dma-assesssqlonprem)
* Voor het downloaden en installeren van de module AZ. DataMigration van de PowerShell Gallery met behulp van de [Power shell-cmdlet install-module](/powershell/module/powershellget/Install-Module); Zorg ervoor dat u het Power shell-opdracht venster opent met uitvoeren als Administrator.
* Om ervoor te zorgen dat de referenties die worden gebruikt om verbinding te maken met de bron SQL Server, de machtiging voor de [besturings server](/sql/t-sql/statements/grant-server-permissions-transact-sql) hebben.
* Om ervoor te zorgen dat de referenties die worden gebruikt om verbinding te maken met een Azure SQL DB-exemplaar, de machtiging voor het beheren van de data base hebben voor het doel Azure SQL Database data bases
* Een Azure-abonnement. Als u er nog geen hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Meld u aan bij uw Microsoft Azure-abonnement

Gebruik de instructies in het artikel [Meld u aan met Azure PowerShell](/powershell/azure/authenticate-azureps) om u aan te melden bij uw Azure-abonnement met behulp van Power shell.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resource groep voordat u een virtuele machine kunt maken.

Maak een resource groep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .

In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt in de regio *eastus* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Een exemplaar maken van Azure Database Migration Service

U kunt een nieuw exemplaar van Azure Database Migration Service maken met behulp van de- `New-AzDataMigrationService` cmdlet. Voor deze cmdlet worden de volgende vereiste para meters verwacht:

* *Naam van de Azure-resource groep*. U kunt de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) gebruiken om een Azure-resource groep te maken zoals eerder wordt weer gegeven en de naam opgeven als een para meter.
* *Service naam*. Teken reeks die overeenkomt met de gewenste unieke service naam voor Azure Database Migration Service 
* *Locatie*. Hiermee geeft u de locatie van de service. Een Azure Data Center-locatie opgeven, zoals vs-West of Zuidoost-Azië
* *SKU*. Deze para meter komt overeen met de naam van een DMS-SKU. De momenteel ondersteunde SKU-naam is *GeneralPurpose_4vCores*.
* *Id van het virtuele subnet*. U kunt de cmdlet [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) gebruiken om een subnet te maken. 

In het volgende voor beeld wordt een service gemaakt met de naam *MyDMS* in de resource groep *MyDMSResourceGroup* die zich bevindt in de regio *VS-Oost* met behulp van een virtueel netwerk met de naam *MyVNET* en subnet genaamd *MySubnet*.

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

U kunt een object data base-verbindings gegevens maken met behulp van de- `New-AzDmsConnInfo` cmdlet. Deze cmdlet verwacht de volgende para meters:

* *Server type*. Het type aangevraagde database verbinding, bijvoorbeeld SQL, Oracle of MySQL. Gebruik SQL voor SQL Server en Azure SQL.
* *Gegevens bron*. De naam of het IP-adres van een SQL Server exemplaar of Azure SQL Database.
* *AuthType*. Het verificatie type voor de verbinding. Dit kan SqlAuthentication of WindowsAuthentication zijn.
* Met de para meter *TrustServerCertificate* wordt een waarde ingesteld die aangeeft of het kanaal is versleuteld tijdens het passeren van de verificatie van de certificaat keten om de vertrouwens relatie te valideren. De waarde kan True of False zijn.

In het volgende voor beeld wordt een verbindings info-object gemaakt voor bron SQL Server met de naam MySourceSQLServer met behulp van SQL-verificatie:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Het volgende voor beeld toont het maken van verbindings gegevens voor een server met de naam SQLAzureTarget met behulp van SQL-verificatie:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Data bases voor het migratie project opgeven

Maak een lijst met `AzDataMigrationDatabaseInfo` objecten die data bases opgeven als onderdeel van het Azure data base Migration project dat kan worden opgegeven als para meter voor het maken van het project. De cmdlet `New-AzDataMigrationDatabaseInfo` kan worden gebruikt om AzDataMigrationDatabaseInfo te maken. 

In het volgende voor beeld wordt een `AzDataMigrationDatabaseInfo` project gemaakt voor de **AdventureWorks2016** -data base en wordt dit toegevoegd aan de lijst die als para meter voor het maken van het project moet worden aangeboden.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Een project-object maken

Ten slotte kunt u een Azure data base Migration-project maken met de naam *MyDMSProject* dat zich in *VS Oost* bevindt met `New-AzDataMigrationProject` en de eerder gemaakte bron-en doel verbindingen en de lijst met te migreren data bases toe te voegen.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Een migratie taak maken en starten

Maak tot slot een Azure data base-migratie taak en start deze. De Azure data base Migration-taak vereist verbindings referentie gegevens voor zowel de bron als het doel en de lijst met database tabellen die moeten worden gemigreerd, naast de informatie die al is gemaakt met het project dat als een vereiste vereist is.

### <a name="create-credential-parameters-for-source-and-target"></a>Referentie parameters maken voor bron en doel

Beveiligings referenties voor de verbinding kunnen worden gemaakt als een [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) -object.

In het volgende voor beeld ziet u het maken van *PSCredential* -objecten voor zowel de bron-als de doel verbindingen die wacht woorden als teken reeks variabelen *$sourcePassword* en *$targetPassword* geven.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Een tabel toewijzing maken en bron-en doel parameters selecteren voor migratie

Een andere para meter die nodig is voor de migratie is het koppelen van tabellen van de bron naar het doel om te worden gemigreerd. Maak een woorden lijst met tabellen die een toewijzing van de bron-en doel tabellen voor migratie bieden. In het volgende voor beeld ziet u de toewijzing tussen de bron-en doel tabellen Human Resources-schema voor de AdventureWorks 2016-data base.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

De volgende stap bestaat uit het selecteren van de bron-en doel databases en het opgeven van de tabel toewijzing als een para meter met behulp van de `New-AzDmsSelectedDB` cmdlet, zoals wordt weer gegeven in het volgende voor beeld:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>De migratie taak maken en starten

Gebruik de `New-AzDataMigrationTask` cmdlet om een migratie taak te maken en te starten. Deze cmdlet verwacht de volgende para meters:

* *TaskType*. Type migratie taak dat moet worden gemaakt voor SQL Server Azure SQL Database migratie type *MigrateSqlServerSqlDb* wordt verwacht. 
* *Naam van de resource groep*. De naam van de Azure-resource groep waarin de taak moet worden gemaakt.
* *ServiceName*. Azure Database Migration Service exemplaar waarin de taak moet worden gemaakt.
* *ProjectName*. De naam van het Azure Database Migration Service project waarin de taak moet worden gemaakt. 
* *Taaknaam*. De naam van de taak die moet worden gemaakt. 
* *SourceConnection*. AzDmsConnInfo-object dat de verbinding van de bron SQL Server vertegenwoordigt.
* *TargetConnection*. AzDmsConnInfo-object dat de verbinding met de doel Azure SQL Database vertegenwoordigt.
* *SourceCred*. [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) -object voor het maken van verbinding met de bron server.
* *TargetCred*. [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) -object om verbinding te maken met de doel server.
* *SelectedDatabase*. AzDataMigrationSelectedDB-object dat de toewijzing van de bron-en doel database vertegenwoordigt.
* *SchemaValidation*. (optioneel, Switch parameter) Na de migratie voert een vergelijking uit van de schema gegevens tussen de bron en het doel.
* *DataIntegrityValidation*. (optioneel, Switch parameter) Na de migratie voert een validatie van de gegevens integriteit op basis van een controlesom uit tussen de bron en het doel.
* *QueryAnalysisValidation*. (optioneel, Switch parameter) Na de migratie voert een snelle en intelligente query analyse uit door het ophalen van query's van de bron database en worden deze uitgevoerd in het doel.

In het volgende voor beeld wordt een migratie taak gemaakt en gestart met de naam myDMSTask:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
```

In het volgende voor beeld wordt dezelfde migratie taak als hierboven gemaakt en gestart, maar worden ook alle drie de validaties uitgevoerd:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -SchemaValidation `
  -DataIntegrityValidation `
  -QueryAnalysisValidation `
```

## <a name="monitor-the-migration"></a>Bewaak de migratie

U kunt de migratie taak die wordt uitgevoerd controleren door een query uit te voeren op de eigenschap State van de taak, zoals wordt weer gegeven in het volgende voor beeld:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Het DMS-exemplaar verwijderen

Nadat de migratie is voltooid, kunt u de Azure DMS-instantie verwijderen:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Volgende stap

* Bekijk de richt lijnen voor migratie in de micro soft- [Data Base-migratie handleiding](https://datamigration.microsoft.com/).