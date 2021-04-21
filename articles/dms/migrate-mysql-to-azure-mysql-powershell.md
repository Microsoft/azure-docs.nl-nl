---
title: 'PowerShell: offlinemigratie uitvoeren van MySQL-database naar Azure Database for MySQL behulp van DMS'
titleSuffix: Azure Database Migration Service
description: Leer hoe u een on-premises MySQL-database migreert naar Azure Database for MySQL met behulp van Azure Database Migration Service PowerShell-script.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 424443288dddcb09d15b7d00ffe9a2840f602959
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819686"
---
# <a name="migrate-mysql-to-azure-database-for-mysql-offline-with-powershell--azure-database-migration-service"></a>MySQL migreren naar Azure Database for MySQL offline met PowerShell-& Azure Database Migration Service

In dit artikel migreert u een MySQL-database die is hersteld naar een on-premises exemplaar naar Azure Database for MySQL met behulp van de offlinemigratiemogelijkheid van Azure Database Migration Service via Microsoft Azure PowerShell. In het artikel wordt een verzameling PowerShell-scripts beschreven die opeenvolgend kunnen worden uitgevoerd om de offlinemigratie van de MySQL-database naar Azure uit te voeren.

> [!NOTE]
> Het is momenteel niet mogelijk om de volledige databasemigratie uit te voeren met behulp van de module Az.DataMigration. In de tussentijd wordt het PowerShell-voorbeeldscript 'as-is' aangeboden dat gebruikmaakt van de [DMS REST API](https://docs.microsoft.com/rest/api/datamigration/tasks/get) en waarmee u de migratie kunt automatiseren. Dit script wordt gewijzigd of afgeschaft zodra de officiële ondersteuning is toegevoegd in de Az.DataMigration-module en Azure CLI. 

> [!IMPORTANT]
> Het onlinemigratiescenario MySQL to Azure Database for MySQL wordt vervangen door een ge parallelliseerde, zeer goed presterende offlinemigratiescenario vanaf 1 juni 2021. Voor onlinemigraties kunt u deze nieuwe aanbieding gebruiken samen met [replicatie van binnenkomende gegevens.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) U kunt ook opensource-hulpprogramma's zoals [MyDumper/MyLoader](https://centminmod.com/mydumper.html) gebruiken met replicatie van binnenkomende gegevens voor onlinemigraties. 

Het artikel helpt bij het automatiseren van het scenario waarin de namen van de bron- en doeldatabase hetzelfde of verschillend kunnen zijn. Als onderdeel van de migratie moeten alle of enkele tabellen in de doeldatabase worden gemigreerd die dezelfde naam en tabelstructuur hebben. Hoewel in de artikelen wordt aangenomen dat de bron een MySQL-database-exemplaar en -doel Azure Database for MySQL is, kan deze worden gebruikt om van het ene naar het andere Azure Database for MySQL te migreren door alleen de naam en referenties van de bronserver te wijzigen. Migratie van MySQL-servers van lagere versies (v5.6 en hoger) naar hogere versies wordt ook ondersteund.

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]

In dit artikel leert u het volgende:
> [!div class="checklist"]
>
> * Databaseschema migreren.
> * Een resourcegroep maken.
> * De Azure-portal gebruiken om een Azure Database Migration Service-exemplaar te maken.
> * Maak een migratieproject in een Azure Database Migration Service-exemplaar.
> * Configureer het migratieproject voor het gebruik van de offlinemigratiemogelijkheid voor MySQL.
> * De migratie uitvoeren.

## <a name="prerequisites"></a>Vereisten

Als u deze stappen wilt voltooien, hebt u het volgende nodig:

* U moet beschikken over een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free)
* Een on-premises MySQL-database met versie 5.6 of hoger. Als dat niet het beste is, downloadt en installeert u [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 of hoger.
* [Maak een exemplaar in Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Raadpleeg het artikel [MySQL Workbench](../mysql/connect-workbench.md) gebruiken om verbinding te maken en gegevens op te vragen voor meer informatie over het maken en verbinden van een database met behulp van de Workbench-toepassing. De Azure Database for MySQL versie moet gelijk zijn aan of hoger zijn dan de on-premises MySQL-versie . MySQL 5.7 kan bijvoorbeeld worden gemigreerd naar Azure Database for MySQL 5.7 of worden geüpgraded naar 8.  
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit biedt site-naar-site-connectiviteit met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [Documentatie over virtuele netwerken](../virtual-network/index.yml) en dan met name de quickstart-artikelen met stapsgewijze informatie.

    > [!NOTE]
    > Als u tijdens de installatie van het virtuele netwerknet ExpressRoute gebruikt [](../virtual-network/virtual-network-service-endpoints-overview.md) met netwerk peering naar Microsoft, voegt u het *Microsoft.Sql-service-eindpunt* toe aan het subnet waarin de service wordt ingericht. Deze configuratie is noodzakelijk omdat Azure Database Migration Service geen internetverbinding biedt.

* Zorg ervoor dat de regels voor de netwerkbeveiligingsgroep van uw virtuele netwerk de uitgaande poort 443 van ServiceTag voor Storage en AzureMonitor niet blokkeren. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* Open uw Windows-firewall om verbindingen vanaf Virtual Network voor Azure Database Migration Service toegang te geven tot de bronserver van MySQL, die standaard TCP-poort 3306 is.
* Wanneer u een firewallapparaat gebruikt vóór uw brondatabase(s), moet u mogelijk firewallregels toevoegen om verbindingen vanuit Virtual Network voor Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie.
* Maak een firewallregel op [serverniveau](../azure-sql/database/firewall-configure.md) of configureer [VNET-service-eindpunten](../mysql/howto-manage-vnet-using-portal.md) voor doel-Azure Database for MySQL zodat Virtual Network toegang Azure Database Migration Service tot de doeldatabases.
* De brondatabase van MySQL moet op een ondersteunde MySQL-communityversie zijn. Om de versie van het MySQL-exemplaar te bepalen, voert u in het MySQL-hulpprogramma of MySQL Workbench de volgende opdracht uit:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL ondersteunt alleen InnoDB-tabellen. Raadpleeg het artikel [Tabellen van MyISAM naar InnoDB converteren](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) om MyISAM-tabellen te converteren naar InnoDB.
* De gebruiker moet over de bevoegdheden beschikken om gegevens in de brondatabase te lezen.
* De handleiding maakt gebruik van PowerShell v7.1 met PSEdition Core, dat kan worden geïnstalleerd volgens de [installatiehandleiding](/powershell/scripting/install/installing-powershell?view=powershell-7.1&preserve-view=true)
* Download en installeer de volgende modules van de PowerShell Gallery met behulp [van de PowerShell-cmdlet Install-Module;](/powershell/module/powershellget/Install-Module) Zorg ervoor dat u het PowerShell-opdrachtvenster opent met Als administrator uitvoeren:
    * Az.Resources
    * Az.Network
    * Az.DataMigration

```powershell
Install-Module Az.Resources
Install-Module Az.Network
Install-Module Az.DataMigration
Import-Module Az.Resources
Import-Module Az.Network
Import-Module Az.DataMigration
```

## <a name="migrate-database-schema"></a>Databaseschema migreren

Als u alle databaseobjecten, zoals tabelschema's, indexen en opgeslagen procedures, wilt overdragen, moeten we het schema uit de brondatabase extraheren en toepassen op de doeldatabase. Om het schema te extraheren, kunt u mysqldump gebruiken met de parameter `--no-data`. Hiervoor hebt u een computer nodig die verbinding kan maken met zowel de mySQL-brondatabase als de doeldatabase Azure Database for MySQL.

Voer de volgende opdracht uit om het schema te exporteren met mysqldump:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Bijvoorbeeld:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Voer de volgende opdracht uit om het schema Azure Database for MySQL doel te importeren:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Bijvoorbeeld:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Als uw schema vreemde sleutels bevat, wordt de parallelle gegevensbelasting tijdens de migratie afgehandeld door de migratietaak. U hoeft geen vreemde sleutels te laten vallen tijdens de schemamigratie.

Als u triggers in de database hebt, wordt de gegevensintegriteit in het doel afgedwongen, voorafgaand aan de volledige gegevensmigratie van de bron. Het wordt aanbevolen triggers uit te schakelen voor alle tabellen in het doel tijdens de migratie en de triggers in te schakelen nadat de migratie is uitgevoerd.

Voer het volgende script uit in MySQL Workbench op de doeldatabase om het triggerscript voor neerzetten te extraheren en een triggerscript toe te voegen.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Voer de gegenereerde drop trigger-query (kolom DropQuery) uit in het resultaat om triggers in de doeldatabase te verwijderen. De triggerquery toevoegen kan worden opgeslagen om te worden gebruikt na voltooiing van de gegevensmigratie.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Meld u aan bij uw Microsoft Azure abonnement

Gebruik de [PowerShell-opdracht Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) om u aan te melden bij uw Azure-abonnement met behulp van PowerShell, volgens de aanwijzingen in het artikel Aanmelden [met Azure PowerShell](/powershell/azure/authenticate-azureps).

Met het volgende script stelt u het standaardabonnement voor de PowerShell-sessie in na aanmelding en maakt u een functie voor logboekregistratie van helper voor opgemaakte consolelogboeken.

```powershell
[string] $SubscriptionName = "mySubscription"
$ErrorActionPreference = "Stop";

Connect-AzAccount
Set-AzContext -Subscription $SubscriptionName
$global:currentSubscriptionId = (Get-AzContext).Subscription.Id;

function LogMessage([string] $Message, [bool] $IsProcessing = $false) {
    if ($IsProcessing) {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Yellow
    }
    else {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Green
    }    
}
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

Registratie van de resourceprovider hoeft slechts één keer te worden uitgevoerd voor elk Azure-abonnement. Zonder de registratie kunt u geen exemplaar van **Azure Database Migration Service.**

Registreer de resourceprovider met behulp van [de opdracht Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider) Met het volgende script wordt de resourceprovider geregistreerd die is vereist **voor Azure Database Migration Service**

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resourcegroep voordat u DMS-resources maakt.

Maak een resourcegroep met behulp van [de opdracht New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

In het volgende voorbeeld wordt een resourcegroep met de *naam myResourceGroup* gemaakt in de regio VS - west *2* onder het standaardabonnement *mySubscription*.

```powershell
# Get the details of resource group
[string] $Location = "westus2"
[string] $ResourceGroupName = "myResourceGroup"

$resourceGroup = Get-AzResourceGroup -Name $ResourceGroupName
if (-not($resourceGroup)) {
    LogMessage -Message "Creating resource group $ResourceGroupName..." -IsProcessing $true
    $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
    LogMessage -Message "Created resource group - $($resourceGroup.ResourceId)."
}
else { LogMessage -Message "Resource group $ResourceGroupName exists." }
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Een exemplaar maken van Azure Database Migration Service

U kunt een nieuw exemplaar van Azure Database Migration Service met behulp van de [opdracht New-AzDataMigrationService.](/powershell/module/az.datamigration/new-azdatamigrationservice) Met deze opdracht worden de volgende vereiste parameters verwacht:
* *Naam van Azure-resourcegroep.* U kunt de [opdracht New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) gebruiken om een Azure-resourcegroep te maken zoals eerder werd weergegeven en de naam op te geven als parameter.
* *Servicenaam*. Tekenreeks die overeenkomt met de gewenste unieke servicenaam voor Azure Database Migration Service 
* *Locatie*. Hiermee geeft u de locatie van de service. Geef een azure-datacentrumlocatie op, zoals VS - west of Azië - zuidoost
* *SKU*. Deze parameter komt overeen met de naam van de DMS-SKU. De momenteel ondersteunde *SKU-naam Standard_1vCore*, *Standard_2vCores*, *Standard_4vCores*, *Premium_4vCores*.
* *Virtuele subnet-id.* U kunt de [opdracht Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) gebruiken om de informatie van een subnet op te halen. 

Het volgende script verwacht dat het virtuele netwerk *myVirtualNetwork* bestaat met een subnet met de naam *default* en maakt vervolgens een Database Migration Service met de naam *myDmService* onder de resourcegroep die is gemaakt in stap **3** en in dezelfde regio.

```powershell
# Get a reference to the DMS service - Create if not exists
[string] $VirtualNetworkName = "myVirtualNetwork"
[string] $SubnetName = "default"
[string] $ServiceName = "myDmService"

$dmsServiceResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$ResourceGroupName/providers/Microsoft.DataMigration/services/$ServiceName"
$dmsService = Get-AzResource -ResourceId $dmsServiceResourceId -ErrorAction SilentlyContinue

# Create Azure DMS service if not existing
# Possible values for SKU currently are Standard_1vCore,Standard_2vCores,Standard_4vCores,Premium_4vCores
if (-not($dmsService)) {   
    $virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VirtualNetworkName
    if (-not ($virtualNetwork)) { throw "ERROR: Virtual Network $VirtualNetworkName does not exists" }

    $subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $virtualNetwork -Name $SubnetName
    if (-not ($subnet)) { throw "ERROR: Virtual Network $VirtualNetworkName does not contains Subnet $SubnetName" }

    LogMessage -Message "Creating Azure Data Migration Service $ServiceName..." -IsProcessing $true
    $dmsService = New-AzDataMigrationService `
        -ResourceGroupName $ResourceGroupName `
        -Name $ServiceName `
        -Location $resourceGroup.Location `
        -Sku Premium_4vCores `
        -VirtualSubnetId $Subnet.Id
    
    $dmsService = Get-AzResource -ResourceId $dmsServiceResourceId
    LogMessage -Message "Created Azure Data Migration Service - $($dmsService.ResourceId)."
}
else { LogMessage -Message "Azure Data Migration Service $ServiceName exists." }
```

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat u een Azure Database Migration Service hebt aanmaken, maakt u een migratieproject. Een migratieproject geeft het type migratie aan dat moet worden uitgevoerd.

Met het volgende script maakt u een migratieproject met de naam *myfirstmysqlofflineproject* voor offlinemigratie van MySQL naar Azure Database for MySQL onder het Database Migration Service-exemplaar dat is gemaakt in **stap 4** en in dezelfde regio.

```powershell
# Get a reference to the DMS project - Create if not exists
[string] $ProjectName = "myfirstmysqlofflineproject"

$dmsProjectResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($dmsService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($dmsService.Name)/projects/$projectName"
$dmsProject = Get-AzResource -ResourceId $dmsProjectResourceId -ErrorAction SilentlyContinue

# Create Azure DMS Project if not existing
if (-not($dmsProject)) {
    LogMessage -Message "Creating Azure DMS project $projectName for MySQL migration ..." -IsProcessing $true

    $newProjectProperties = @{"sourcePlatform" = "MySQL"; "targetPlatform" = "AzureDbForMySQL" }
    $dmsProject = New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $dmsService.Location `
        -ResourceId $dmsProjectResourceId `
        -Properties $newProjectProperties `
        -Force

    LogMessage -Message "Created Azure DMS project $projectName - $($dmsProject.ResourceId)."
}
else { LogMessage -Message "Azure DMS project $projectName exists." }
```

## <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Een databaseverbindingsgegevensobject maken voor de bron- en doelverbindingen

Nadat u het migratieproject hebt gemaakt, maakt u de verbindingsgegevens van de database. Deze verbindingsgegevens worden gebruikt om verbinding te maken met de bron- en doelservers tijdens het migratieproces.

Het volgende script gebruikt de servernaam, gebruikersnaam en het wachtwoord voor de bron- en doel-MySQL-exemplaren en maakt de verbindingsgegevensobjecten. Het script vraagt de gebruiker om het wachtwoord voor de bron- en doel-MySQL-exemplaren in te voeren. Voor stille scripts kunnen de referenties worden opgehaald uit Azure Key Vault. 

```powershell
# Initialize the source and target database server connections
[string] $SourceServerName = "13.66.136.192"
[string] $SourceUserName = "docadmin@mysqlserver"
[securestring] $SourcePassword = Read-Host "Enter MySQL Source Server Password" -AsSecureString

[string] $TargetServerName = "migdocdevwus2mysqlsstrgt.mysql.database.azure.com"
[string] $TargetUserName = "docadmin@migdocdevwus2mysqlsstrgt"
[securestring] $TargetPassword = Read-Host "Enter MySQL Target Server Password" -AsSecureString

function InitConnection(
    [string] $ServerName,
    [string] $UserName,
    [securestring] $Password) {
    $connectionInfo = @{
        "dataSource"             = "";
        "serverName"             = "";
        "port"                   = 3306;
        "userName"               = "";
        "password"               = "";
        "authentication"         = "SqlAuthentication";
        "encryptConnection"      = $true;
        "trustServerCertificate" = $true;
        "additionalSettings"     = "";
        "type"                   = "MySqlConnectionInfo" 
    }

    $connectionInfo.dataSource = $ServerName;
    $connectionInfo.serverName = $ServerName;
    $connectionInfo.userName = $UserName;
    $connectionInfo.password = (ConvertFrom-SecureString -AsPlainText $password).ToString();
    $connectionInfo;
}

# Initialize the source and target connections
LogMessage -Message "Initializing source and target connection objects ..." -IsProcessing $true
$sourceConnInfo = InitConnection `
    $SourceServerName `
    $SourceUserName `
    $SourcePassword;

$targetConnInfo = InitConnection `
    $TargetServerName `
    $TargetUserName `
    $TargetPassword;

LogMessage -Message "Source and target connection object initialization complete."
```

## <a name="extract-the-list-of-table-names-from-the-target-database"></a>De lijst met tabelnamen uit de doeldatabase extraheren

Een databasetabellijst kan worden geëxtraheerd met behulp van een migratietaak en verbindingsgegevens. De tabellijst wordt geëxtraheerd uit zowel de brondatabase als de doeldatabase, zodat de juiste toewijzing en validatie kan worden uitgevoerd. 

Het volgende script neemt de namen van de bron- en doeldatabases en extraheert vervolgens de tabellijst uit de databases met behulp van de *migratietaak GetUserTablesMySql.* 

```powershell
# Run scenario to get the tables from the target database to build
# the migration table mapping
[string] $TargetDatabaseName = "migtargetdb"
[string] $SourceDatabaseName = "migsourcedb"

function RunScenario([object] $MigrationService, 
    [object] $MigrationProject, 
    [string] $ScenarioTaskName, 
    [object] $TaskProperties, 
    [bool] $WaitForScenario = $true) {
    # Check if the scenario task already exists, if so remove it
    LogMessage -Message "Removing scenario if already exists..." -IsProcessing $true
    Remove-AzDataMigrationTask `
        -ResourceGroupName $MigrationService.ResourceGroupName `
        -ServiceName $MigrationService.Name `
        -ProjectName $MigrationProject.Name `
        -TaskName $ScenarioTaskName `
        -Force;

    # Start the new scenario task using the provided properties
    LogMessage -Message "Initializing scenario..." -IsProcessing $true
    New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $MigrationService.Location `
        -ResourceId "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($MigrationService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($MigrationService.Name)/projects/$($MigrationProject.Name)/tasks/$($ScenarioTaskName)" `
        -Properties $TaskProperties `
        -Force | Out-Null;
    
    LogMessage -Message "Waiting for $ScenarioTaskName scenario to complete..." -IsProcessing $true
    if ($WaitForScenario) {
        $progressCounter = 0;
        do {
            if ($null -ne $scenarioTask) {
                Start-Sleep 10;
            }

            # Get calls can time out and will return a cancellation exception in that case
            $scenarioTask = Get-AzDataMigrationTask `
                -ResourceGroupName $MigrationService.ResourceGroupName `
                -ServiceName $MigrationService.Name `
                -ProjectName $MigrationProject.Name `
                -TaskName $ScenarioTaskName `
                -Expand `
                -ErrorAction Ignore;

            Write-Progress -Activity "Scenario Run $ScenarioTaskName  (Marquee Progress Bar)" `
                -Status $scenarioTask.ProjectTask.Properties.State `
                -PercentComplete $progressCounter
            
            $progressCounter += 10;
            if ($progressCounter -gt 100) { $progressCounter = 10 }
        }
        while (($null -eq $scenarioTask) -or ($scenarioTask.ProjectTask.Properties.State -eq "Running") -or ($scenarioTask.ProjectTask.Properties.State -eq "Queued"))
    }
    Write-Progress -Activity "Scenario Run $ScenarioTaskName" `
        -Status $scenarioTask.ProjectTask.Properties.State `
        -Completed
                 
    # Now get it using REST APIs so we can expand the output  
    LogMessage -Message "Getting expanded task results ..." -IsProcessing $true  
    $psToken = (Get-AzAccessToken -ResourceUrl https://management.azure.com).Token;
    $token = ConvertTo-SecureString -String $psToken -AsPlainText -Force;
    $taskResource = Invoke-RestMethod `
        -Method GET `
        -Uri "https://management.azure.com$($scenarioTask.ProjectTask.Id)?api-version=2018-03-31-preview&`$expand=output" `
        -ContentType "application/json" `
        -Authentication Bearer `
        -Token $token;
    
    $taskResource.properties;
}

# create the get table task properties by initializing the connection and 
# database name
$getTablesTaskProperties = @{
    "input"    = @{
        "connectionInfo"    = $null;
        "selectedDatabases" = $null;
    };
    "taskType" = "GetUserTablesMySql";
};

LogMessage -Message "Running scenario to get the list of tables from the target database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $targetConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($TargetDatabaseName);
# Create a name for the task
$getTableTaskName = "$($TargetDatabaseName)GetUserTables"
# Get the list of tables from the source
$getTargetTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getTargetTablesTask)) { throw "ERROR: Could not get target database $TargetDatabaseName table information." }
LogMessage -Message "List of tables from the target database acquired."

LogMessage -Message "Running scenario to get the list of tables from the source database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $sourceConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($SourceDatabaseName);
# Create a name for the task
$getTableTaskName = "$($SourceDatabaseName)GetUserTables"
# Get the list of tables from the source
$getSourceTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getSourceTablesTask)) { throw "ERROR: Could not get source database $SourceDatabaseName table information." }
LogMessage -Message "List of tables from the source database acquired."

```

## <a name="build-table-mapping-based-on-user-configuration"></a>Tabeltoewijzing maken op basis van gebruikersconfiguratie

Als onderdeel van het configureren van de migratietaak maakt u een toewijzing tussen de bron- en doeltabellen. De toewijzing is op tabelnaamniveau, maar de veronderstelling is dat de tabelstructuur (aantal kolommen, kolomnamen, gegevenstypen enzovoort) van de toegewezen tabellen exact hetzelfde is.

Met het volgende script maakt u een toewijzing op basis van de doel- en brontabellijst die is geëxtraheerd in **stap 7.** Voor gedeeltelijke gegevensbelasting kan de gebruiker een lijst met tabellen verstrekken om de tabellen te filteren. Als er geen gebruikersinvoer wordt opgegeven, worden alle doeltabellen in kaart gebracht. Het script controleert ook of er al dan niet een tabel met dezelfde naam bestaat in de bron. Als de tabelnaam niet bestaat in de bron, wordt de doeltabel genegeerd voor migratie. 

```powershell
# Create the source to target table map
# Optional table settings
# DEFAULT: $IncludeTables = $null => include all tables for migration
# DEFAULT: $ExcludeTables = $null => exclude no tables from migration
# Exclude list has higher priority than include list
# Array of qualified source table names which should be migrated
[string[]] $IncludeTables = @("migsourcedb.coupons", "migsourcedb.daily_cash_sheets");
[string[]] $ExcludeTables = $null;

LogMessage -Message "Creating the table map based on the user input and database table information ..." `
    -IsProcessing $true

$targetTables = $getTargetTablesTask.Output.DatabasesToTables."$TargetDatabaseName";
$sourceTables = $getSourceTablesTask.Output.DatabasesToTables."$SourceDatabaseName";
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]';

$schemaPrefixLength = $($SourceDatabaseName + ".").Length;
$tableMappingError = $false
foreach ($srcTable in $sourceTables) {
    # Removing the database name prefix from the table name so that comparison
    # can be done in cases where database name given are different
    $tableName = $srcTable.Name.Substring($schemaPrefixLength, `
            $srcTable.Name.Length - $schemaPrefixLength)

    # In case the table is part of exclusion list then ignore the table
    if ($null -ne $ExcludeTables -and $ExcludeTables -contains $srcTable.Name) {
        continue;
    }

    # Either the include list is null or the table is part of the include list then add it in the mapping
    if ($null -eq $IncludeTables -or $IncludeTables -contains $srcTable.Name) {
        # Check if the table exists in the target. If not then log TABLE MAPPING ERROR
        if (-not ($targetTables | Where-Object { $_.name -ieq "$($TargetDatabaseName).$tableName" })) {
            $tableMappingError = $true
            Write-Host "TABLE MAPPING ERROR: $($targetTables.name) does not exists in target." -ForegroundColor Red
            continue;
        }  

        $tableMap.Add("$($SourceDatabaseName).$tableName", "$($TargetDatabaseName).$tableName");
    }     
}

# In case of any table mapping errors identified, throw an error and stop the process
if ($tableMappingError) { throw "ERROR: One or more table mapping errors were identified. Please see previous messages." }
# In case no tables are in the mapping then throw error
if ($tableMap.Count -le 0) { throw "ERROR: Could not create table mapping." }
LogMessage -Message "Migration table mapping created for $($tableMap.Count) tables."
```

## <a name="create-and-configure-the-migration-task-inputs"></a>De invoer van de migratietaak maken en configureren

Nadat u de tabeltoewijzing hebt gemaakt, maakt u de invoer voor de migratietaak van het type *Migrate.MySql.AzureDbForMySql* en configureert u de eigenschappen.

Met het volgende script maakt u de migratietaak en stelt u de verbindingen, databasenamen en tabeltoewijzing in.

```powershell
# Create and configure the migration scenario based on the connections
# and the table mapping
$offlineMigTaskProperties = @{
    "input"    = @{
        "sourceConnectionInfo"  = $null;
        "targetConnectionInfo"  = $null;
        "selectedDatabases"     = $null;
        "optionalAgentSettings" = @{
            "EnableCacheBatchesInMemory"         = $true;
            "DisableIncrementalRowStatusUpdates" = $true;
        };
        "startedOn"             = $null;
    };
    "taskType" = "Migrate.MySql.AzureDbForMySql";
};
$offlineSelectedDatabase = @{
    "name"               = $null;
    "targetDatabaseName" = $null;
    "tableMap"           = $null;
};

LogMessage -Message "Preparing migration scenario configuration ..." -IsProcessing $true

# Select the database to be migrated
$offlineSelectedDatabase.name = $SourceDatabaseName;
$offlineSelectedDatabase.tableMap = New-Object PSObject -Property $tableMap;
$offlineSelectedDatabase.targetDatabaseName = $TargetDatabaseName;

# Set connection info and the database mapping
$offlineMigTaskProperties.input.sourceConnectionInfo = $sourceConnInfo;
$offlineMigTaskProperties.input.targetConnectionInfo = $targetConnInfo;
$offlineMigTaskProperties.input.selectedDatabases = @($offlineSelectedDatabase);
$offlineMigTaskProperties.input.startedOn = [System.DateTimeOffset]::UtcNow.ToString("O");
```

## <a name="configure-performance-tuning-parameters"></a>Prestatieafstemmingsparameters configureren

Op basis van de PowerShell-module zijn er enkele optionele parameters beschikbaar die kunnen worden afgestemd op basis van de omgeving. Deze parameters kunnen worden gebruikt om de prestaties van de migratietaak te verbeteren. Al deze parameters zijn optioneel en de standaardwaarde is NULL.

> [!NOTE]
> De volgende prestatieconfiguraties hebben een toegenomen doorvoer weergegeven tijdens de migratie op Premium SKU.
> * WriteDataRangeBatchTaskCount = 12
> * DelayProgressUpdatesInStorageInterval = 30 seconden
> * ThrottleQueryTableDataRangeTaskAtBatchCount = 36

Het volgende script neemt de gebruikerswaarden van de parameters en stelt de parameters in de eigenschappen van de migratietaak in.

```powershell
# Setting optional parameters from fine tuning the data transfer rate during migration
# DEFAULT values for all the configurations is $null
LogMessage -Message "Adding optional migration performance tuning configuration ..." -IsProcessing $true
# Partitioning settings
# Optional setting that configures the maximum number of parallel reads on tables located on the source database.
[object] $DesiredRangesCount = 4
# Optional setting that configures that size of the largest batch that will be committed to the target server.
[object] $MaxBatchSizeKb = 4096
# Optional setting that configures the minimum number of rows in each batch written to the target.
[object] $MinBatchRows = $null
# Task count settings
# Optional setting that configures the number of databases that will be prepared for migration in parallel.
[object] $PrepareDatabaseForBulkImportTaskCount = $null
# Optional setting that configures the number of tables that will be prepared for migration in parallel.
[object] $PrepareTableForBulkImportTaskCount = $null
# Optional setting that configures the number of threads available to read ranges on the source.
[object] $QueryTableDataRangeTaskCount = 8
# Optional setting that configures the number of threads available to write batches to the target.
[object] $WriteDataRangeBatchTaskCount = 12
# Batch cache settings
# Optional setting that configures how much memory will be used to cache batches in memory before reads on the source are throttled.
[object] $MaxBatchCacheSizeMb = $null
# Optional setting that configures the amount of available memory at which point reads on the source will be throttled.
[object] $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb = $null
# Optional setting that configures the number of batches cached in memory that will trigger read throttling on the source.
[object] $ThrottleQueryTableDataRangeTaskAtBatchCount = 36
# Performance settings
# Optional setting that configures the delay between updates of result objects in Azure Table Storage.
[object] $DelayProgressUpdatesInStorageInterval = "00:00:30"

function AddOptionalSetting($optionalAgentSettings, $settingName, $settingValue) {
    # If no value specified for the setting, don't bother adding it to the input
    if ($null -eq $settingValue) {
        return;
    }

    # Add a new property to the JSON object to capture the setting which will be customized
    $optionalAgentSettings | add-member -MemberType NoteProperty -Name $settingName -Value $settingValue
}

# Set any optional settings in the input based on parameters to this cmdlet
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DesiredRangesCount" $DesiredRangesCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchSizeKb" $MaxBatchSizeKb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MinBatchRows" $MinBatchRows;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareDatabaseForBulkImportTaskCount" $PrepareDatabaseForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareTableForBulkImportTaskCount" $PrepareTableForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "QueryTableDataRangeTaskCount" $QueryTableDataRangeTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "WriteDataRangeBatchTaskCount" $WriteDataRangeBatchTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchCacheSizeMb" $MaxBatchCacheSizeMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb" $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtBatchCount" $ThrottleQueryTableDataRangeTaskAtBatchCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DelayProgressUpdatesInStorageInterval" $DelayProgressUpdatesInStorageInterval;
```

## <a name="creating-and-running-the-migration-task"></a>De migratietaak maken en uitvoeren

Nadat de invoer voor de taak is geconfigureerd, wordt de taak gemaakt en uitgevoerd op de agent. Het script activeert de taakuitvoering en wacht tot de migratie is voltooid.

Met het volgende script wordt de geconfigureerde migratietaak aanroepen en wordt gewacht tot deze is voltooid.

```powershell
# Running the migration scenario
[string] $TaskName = "mysqlofflinemigrate"

LogMessage -Message "Running data migration scenario ..." -IsProcessing $true
$summary = @{
    "SourceServer"   = $SourceServerName;
    "SourceDatabase" = $SourceDatabaseName;
    "TargetServer"   = $TargetServerName;
    "TargetDatabase" = $TargetDatabaseName;
    "TableCount"     = $tableMap.Count;
    "StartedOn"      = $offlineMigTaskProperties.input.startedOn;
}

Write-Host "Job Summary:" -ForegroundColor Yellow
Write-Host $(ConvertTo-Json $summary) -ForegroundColor Yellow

$migrationResult = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $TaskName `
    -TaskProperties $offlineMigTaskProperties

LogMessage -Message "Migration completed with status - $($migrationResult.state)"
#Checking for any errors or warnings captured by the task during migration
$dbLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "DatabaseLevelOutput" } 
$migrationLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "MigrationLevelOutput" }
if ($dbLevelResult.exceptionsAndWarnings) {
    Write-Host "Following database errors were captured: $($dbLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationLevelResult.exceptionsAndWarnings) {
    Write-Host "Following migration errors were captured: $($migrationLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationResult.errors.details) {
    Write-Host "Following task level migration errors were captured: $($migrationResult.errors.details)" -ForegroundColor Red
}
```

## <a name="deleting-the-database-migration-service"></a>De Database Migration Service

Dezelfde Database Migration Service kunnen worden gebruikt voor meerdere migraties, zodat het exemplaar dat eenmaal is gemaakt opnieuw kan worden gebruikt. Als u de Database Migration Service niet meer gaat gebruiken, kunt u de service verwijderen met de opdracht [Remove-AzDataMigrationService.](/powershell/module/az.datamigration/remove-azdatamigrationservice?)

Met het volgende script worden de Azure Database Migration Service en de bijbehorende projecten verwijderd.

```powershell
Remove-AzDataMigrationService -ResourceId $($dmsService.ResourceId)
```

## <a name="next-steps"></a>Volgende stappen

* Zie het artikel Algemene problemen - Azure Database Migration Service voor meer informatie over bekende problemen en beperkingen bij het uitvoeren [van migraties met behulp van DMS.](./known-issues-troubleshooting-dms.md)
* Zie het artikel Problemen met het verbinden van brondatabases voor het oplossen van verbindingsproblemen met de [brondatabase](./known-issues-troubleshooting-dms-source-connectivity.md)tijdens het gebruik van DMS.
* Zie het artikel [Wat is de Azure Database Migration Service?](./dms-overview.md) voor informatie over Azure Database Migration Service.
* Raadpleeg het artikel [Wat is Azure Database for MySQL?](../mysql/overview.md) voor informatie over Azure Database for MySQL.
* Voor zelfstudie over het gebruik van DMS via de portal, zie het [artikel Zelfstudie: MySQL](./tutorial-mysql-azure-mysql-offline-portal.md) migreren naar Azure Database for MySQL offline met behulp van DMS