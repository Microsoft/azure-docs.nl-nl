---
title: Azure Cosmos DB core-API-resources beheren met Power shell
description: Beheer van Azure Cosmos DB core-API-resources met behulp van Power shell.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 2d99a760e3bf44febcee05bf9827479616bf7bd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333423"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-using-powershell"></a>Azure Cosmos DB core-API-resources (SQL) beheren met Power shell
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In de volgende hand leiding wordt beschreven hoe u Power shell gebruikt voor het uitvoeren van scripts en het automatiseren van het beheer van Azure Cosmos DB core-API-resources, waaronder het Cosmos-account, de data base, de container en de door voer. Zie voor PowerShell-cmdlets voor andere API's [PowerShell Samples for Cassandra](powershell-samples-cassandra.md), [PowerShell Samples for MongoDB API](powershell-samples-mongodb.md), [PowerShell Samples for Gremlin](powershell-samples-gremlin.md), [PowerShell Samples for Table](powershell-samples-table.md)

> [!NOTE]
> De voorbeelden in dit artikel maken gebruik van beheer-cmdlets voor [AZ. CosmosDB](/powershell/module/az.cosmosdb). Zie de referentiepagina van [Az.CosmosDB](/powershell/module/az.cosmosdb) API voor de nieuwste wijzigingen.

Voor platformoverschrijdend beheer van Azure Cosmos DB kunt u de cmdlets `Az` en `Az.CosmosDB` gebruiken met [platformoverschrijdend PowerShell](/powershell/scripting/install/installing-powershell) en ook [Azure CLI](manage-with-cli.md), de [REST API][rp-rest-api] of de [Azure Portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Aan de slag

Volg de instructies in [Azure PowerShell installeren en configureren][powershell-install-configure] om uw Azure-account in PowerShell te installeren en u aan te melden.

> [!IMPORTANT]
> De naam van Azure Cosmos DB resources kan niet worden gewijzigd, omdat dit inbreuk maakt op de manier waarop Azure Resource Manager met resource-Uri's werkt.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos DB-accounts

In de volgende secties ziet u hoe u het Azure Cosmos-account kunt beheren, met inbegrip van:

* [Een Azure Cosmos-account maken](#create-account)
* [Een Azure Cosmos-account bijwerken](#update-account)
* [Alle Azure Cosmos-accounts in een abonnement weergeven](#list-accounts)
* [Een Azure Cosmos-account ontvangen](#get-account)
* [Een Azure Cosmos-account verwijderen](#delete-account)
* [Tags voor een Azure Cosmos-account bijwerken](#update-tags)
* [Sleutels voor een Azure Cosmos-account weergeven](#list-keys)
* [Sleutels voor een Azure Cosmos-account regenereren](#regenerate-keys)
* [Verbindingsreeksen voor een Azure Cosmos-account weergeven](#list-connection-strings)
* [Failover-prioriteit voor een Azure Cosmos-account wijzigen](#modify-failover-priority)
* [Handmatige failover voor een Azure Cosmos-account activeren](#trigger-manual-failover)
* [Resourcevergrendelingen op een Azure Cosmos DB-account weergeven](#list-account-locks)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Een Azure Cosmos-account maken

Met deze opdracht wordt een Azure Cosmos DB-databaseaccount gemaakt met [meerdere regio's][distribute-data-globally], [automatische failover](how-to-manage-database-account.md#automatic-failover) en [consistentiebeleid](consistency-levels.md) met gebonden veroudering.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName` De Azure-resourcegroep waarin de Cosmos-account moet worden geïmplementeerd. Deze moet al bestaan.
* `$locations` De regio's voor het database account, de regio met, `FailoverPriority 0` is de schrijf regio.
* `$accountName` De naam voor het Azure Cosmos-account. Moet uniek zijn, met kleine letters, mag alleen alfanumerieke tekens en '-'-tekens bevatten en moet tussen 3 en 31 tekens lang zijn.
* `$apiKind` Het type Cosmos-account dat moet worden gemaakt. Zie [API's in Cosmos DB](introduction.md#simplified-application-development) voor meer informatie.
* `$consistencyPolicy`, `$maxStalenessInterval` en `$maxStalenessPrefix` Het standaardconsistentieniveau en de instellingen van het Azure Cosmos-account. Zie de [Consistentieniveaus in Azure Cosmos DB](consistency-levels.md) voor meer informatie.

Azure Cosmos-accounts kunnen worden geconfigureerd met IP-firewall, service-eindpunten voor virtueel netwerk- en privé-eindpunten. Zie [IP-firewall configureren](how-to-configure-firewall.md) voor meer informatie over het configureren van de IP-firewall voor Azure Cosmos DB. Zie [Toegang vanuit virtuele netwerken configureren](how-to-configure-vnet-service-endpoint.md) voor meer informatie over het inschakelen van service-eindpunten voor Azure Cosmos DB. Zie [Toegang vanuit privé-eindpunten configureren](how-to-configure-private-endpoints.md) voor meer informatie over het inschakelen van privé-eindpunten voor Azure Cosmos DB.

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a> Alle Azure Cosmos-accounts in een resourcegroep weergeven

Met deze opdracht worden alle Azure Cosmos-accounts in een resourcegroep weergegeven.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> De eigenschappen van een Azure Cosmos-account ophalen

Met deze opdracht kunt u de eigenschappen van een bestaand Azure Cosmos-account ophalen.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> Een Azure Cosmos-account bijwerken

Met deze opdracht kunt u de eigenschappen van uw Azure Cosmos DB-databaseaccount bijwerken. Eigenschappen die kunnen worden bijgewerkt zijn onder meer:

* Regio's toevoegen of verwijderen
* Standaardconsistentiebeleid wijzigen
* IP-bereikfilter wijzigen
* Configuraties van virtuele netwerken wijzigen
* Schrijf bewerkingen met meerdere regio's inschakelen

> [!NOTE]
> U kunt niet tegelijkertijd regio's (`locations`) toevoegen of verwijderen en andere eigenschappen voor een Azure Cosmos-account wijzigen. Het wijzigen van regio's moet afzonderlijk worden uitgevoerd van andere wijzigingen aan het account.
> [!NOTE]
> Met deze opdracht kunt u regio's toevoegen en verwijderen, maar niet de failover-prioriteit wijzigen of een handmatige failover activeren. Zie [Failover-prioriteit wijzigen](#modify-failover-priority) en [Handmatige failover activeren](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locationObject2 = @()
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 2 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove West US region from the account
$locationObject3 = @()
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 1 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```

### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-region-writes"></a> Meerdere schrijfregio's inschakelen voor een Azure Cosmos-account

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-region writes
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> Een Azure Cosmos-account verwijderen

Met deze opdracht wordt een bestaand Azure Cosmos-account verwijderd.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> Tags voor een Azure Cosmos-account bijwerken

Met deze opdracht worden de [Azure-resourcetags][azure-resource-tags] voor een Azure Cosmos-account ingesteld. Tags kunnen zowel worden ingesteld bij het maken van het account met `New-AzCosmosDBAccount` als bij het bijwerken van een account met `Update-AzCosmosDBAccount`.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> Accountsleutels weergeven

Wanneer u een Azure Cosmos-account maakt, genereert de service twee primaire toegangs sleutels die kunnen worden gebruikt voor verificatie wanneer het Azure Cosmos-account wordt geopend. Ook worden er alleen-lezensleutels voor verificatie van alleen-lezenbewerkingen gegenereerd.
Door twee toegangssleutels te gebruiken, kunt u in Azure Cosmos DB één sleutel per keer regenereren en roteren zonder onderbreking van uw Azure Cosmos-account.
Cosmos DB-accounts hebben twee lees-schrijfsleutels (primaire en secundaire) en twee alleen-lezensleutels (primaire en secundaire).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a> Verbindingsreeksen weergeven

Met de volgende opdracht worden verbindingsreeksen opgehaald om apps te verbinden met het Cosmos DB-account.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> Accountsleutels regenereren

Toegangssleutels tot een Azure Cosmos-account moeten regelmatig worden geregenereerd om de verbindingen te beveiligen. Er wordt een primaire en secundaire toegangssleutel toegewezen aan het account. Hierdoor kunnen clients toegang houden terwijl één sleutel per keer wordt geregenereerd.
Er zijn vier typen sleutels voor een Azure Cosmos-account (primaire, secundaire, primaryReadonly en SecondaryReadonly)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a> Automatische failover inschakelen

Met de volgende opdracht wordt een Cosmos DB-account ingesteld op automatische failover naar de secundaire regio als de primaire regio onbeschikbaar is.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-region writes - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> Failover-prioriteit wijzigen

Voor accounts die zijn geconfigureerd met Automatische failover kunt u de volgorde wijzigen waarin Cosmos secundaire replica's promoveert naar primaire als de primaire niet meer beschikbaar is.

In het onderstaande voorbeeld wordt ervan uitgegaan dat de huidige failover-prioriteit `West US 2 = 0`, `East US 2 = 1`, `South Central US = 2` is. Met deze opdracht wordt dat gewijzigd in `West US 2 = 0`, `South Central US = 1`, `East US 2 = 2`.

> [!CAUTION]
> Het wijzigen van de locatie voor `failoverPriority=0` zal de handmatige failover voor een Azure Cosmos-account activeren. Andere wijzigingen van de prioriteit zullen geen failover activeren.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a> Handmatige failover activeren

Voor accounts die zijn geconfigureerd met Handmatige failover kunt u failover uitvoeren en elke secundaire replica promoveren naar primaire door deze te wijzigen in `failoverPriority=0`. Deze opdracht kan worden gebruikt om een uitwijktest te activeren om de uitwijkplanning te testen.

In het voorbeeld hieronder wordt ervan uitgegaan dat het account een huidige failover-prioriteit van `West US 2 = 0` en `East US 2 = 1` heeft en de regio's spiegelt.

> [!CAUTION]
> `locationName` wijzigen in `failoverPriority=0` activeert een handmatige failover voor een Azure Cosmos-account. Een andere wijziging van de prioriteit zal geen failover activeren.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="list-resource-locks-on-an-azure-cosmos-db-account"></a><a id="list-account-locks"></a> Resourcevergrendelingen op een Azure Cosmos DB-account weergeven

Resourcevergrendelingen kunnen worden geplaatst op Azure Cosmos DB-resources, met inbegrip van databases en verzamelingen. In het volgende voorbeeld ziet u hoe u alle Azure-resourcevergrendelingen op een Azure Cosmos DB-account kunt weergeven.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceTypeAccount = "Microsoft.DocumentDB/databaseAccounts"
$accountName = "mycosmosaccount"

Get-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceTypeAccount `
    -ResourceName $accountName
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB-database

In de volgende secties ziet u hoe u de Azure Cosmos DB-database kunt beheren, met inbegrip van:

* [Een Azure Cosmos DB-database maken](#create-db)
* [Een Azure Cosmos DB-database met gedeelde doorvoer maken](#create-db-ru)
* [De doorvoer van een Azure Cosmos DB-database ophalen](#get-db-ru)
* [Data Base-door Voer migreren naar automatisch schalen](#migrate-db-ru)
* [Alle Azure Cosmos DB-databases in een account weergeven](#list-db)
* [ Eén Azure Cosmos DB-database ophalen](#get-db)
* [Een Azure Cosmos DB-database verwijderen](#delete-db)
* [Een resourcevergrendeling op een Azure Cosmos DB-database maken om te voorkomen dat deze wordt verwijderd](#create-db-lock)
* [Een resourcevergrendeling op een Azure Cosmos DB-database verwijderen](#remove-db-lock)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Een Azure Cosmos DB-database maken

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a> Een Azure Cosmos DB-database met gedeelde doorvoer maken

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>De doorvoer van een Azure Cosmos DB-database ophalen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="migrate-database-throughput-to-autoscale"></a><a id="migrate-db-ru"></a>Data Base-door Voer migreren naar automatisch schalen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Invoke-AzCosmosDBSqlDatabaseThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -ThroughputType Autoscale
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Alle Azure Cosmos DB-databases in een account ophalen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Eén Azure Cosmos DB-database ophalen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Een Azure Cosmos DB-database verwijderen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-a-resource-lock-on-an-azure-cosmos-db-database-to-prevent-delete"></a><a id="create-db-lock"></a>Een resourcevergrendeling op een Azure Cosmos DB-database maken om te voorkomen dat deze wordt verwijderd

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-database"></a><a id="remove-db-lock"></a>Een resourcevergrendeling op een Azure Cosmos DB-database verwijderen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB-container

In de volgende secties ziet u hoe u de Azure Cosmos DB-container kunt beheren, met inbegrip van:

* [Een Azure Cosmos DB-container maken](#create-container)
* [Een Azure Cosmos DB-container met automatische schaalaanpassing maken](#create-container-autoscale)
* [Een Azure Cosmos DB-container met een grote partitiesleutel maken](#create-container-big-pk)
* [De doorvoer van een Azure Cosmos DB-container ophalen](#get-container-ru)
* [Container doorvoer migreren naar automatisch schalen](#migrate-container-ru)
* [Een Azure Cosmos DB-container met aangepaste indexering maken](#create-container-custom-index)
* [Een Azure Cosmos DB-container met uitgeschakelde indexering maken](#create-container-no-index)
* [Een Azure Cosmos DB-container met unieke sleutel en TTL maken](#create-container-unique-key-ttl)
* [Een Azure Cosmos DB-container met conflictoplossing maken](#create-container-lww)
* [Alle Azure Cosmos DB-containers in een database weergeven](#list-containers)
* [Eén Azure Cosmos DB-container in een database ophalen](#get-container)
* [Een Azure Cosmos DB-container verwijderen](#delete-container)
* [Een resourcevergrendeling op een Azure Cosmos DB-container maken om te voorkomen dat deze wordt verwijderd](#create-container-lock)
* [Een resourcevergrendeling op een Azure Cosmos DB-container verwijderen](#remove-container-lock)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Een Azure Cosmos DB-container maken

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$throughput = 400 #minimum = 400

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -Throughput $throughput
```

### <a name="create-an-azure-cosmos-db-container-with-autoscale"></a><a id="create-container-autoscale"></a>Een Azure Cosmos DB-container met automatisch schaalaanpassing maken

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and autoscale throughput at 4000 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$autoscaleMaxThroughput = 4000 #minimum = 4000

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -AutoscaleMaxThroughput $autoscaleMaxThroughput
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Een Azure Cosmos DB-container met een grote partitiesleutelgrootte maken

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>De doorvoer van een Azure Cosmos DB-container ophalen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="migrate-container-throughput-to-autoscale"></a><a id="migrate-container-ru"></a>Container doorvoer migreren naar automatisch schalen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Invoke-AzCosmosDBSqlContainerThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -ThroughputType Autoscale
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Een Azure Cosmos DB-container met aangepast indexeringsbeleid maken

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Een Azure Cosmos DB-container met uitgeschakelde indexering maken

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Een Azure Cosmos DB-container met uniek sleutelbeleid en TTL maken

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Een Azure Cosmos DB-container met conflictoplossing maken

Geef `-Type "Custom" -Path ""` door om alle conflicten naar de ConflictsFeed te schrijven en ze afzonderlijk te behandelen.

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

Roep `New-AzCosmosDBSqlConflictResolutionPolicy` aan en geef de parameters `-Type` en `-ConflictResolutionProcedure` door om conflictoplossingsbeleid te maken om een opgeslagen procedure te gebruiken.

```azurepowershell-interactive
# Create container with custom conflict resolution policy using a stored procedure
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionSprocName = "mysproc"

$conflictResolutionSproc = "/dbs/$databaseName/colls/$containerName/sprocs/$conflictResolutionSprocName"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type Custom `
    -ConflictResolutionProcedure $conflictResolutionSproc

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```


### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Alle Azure Cosmos DB-containers in een database weergeven

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Eén Azure Cosmos DB-container in een database ophalen

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Een Azure Cosmos DB-container verwijderen

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```
### <a name="create-a-resource-lock-on-an-azure-cosmos-db-container-to-prevent-delete"></a><a id="create-container-lock"></a>Een resourcevergrendeling op een Azure Cosmos DB-container maken om te voorkomen dat deze wordt verwijderd

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-container"></a><a id="remove-container-lock"></a>Een resourcevergrendeling op een Azure Cosmos DB-container verwijderen

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="next-steps"></a>Volgende stappen

* [Alle PowerShell-voorbeelden](powershell-samples.md)
* [Een Azure Cosmos-account beheren](how-to-manage-database-account.md)
* [Een Azure Cosmos DB-container maken](how-to-create-container.md)
* [TTL (Time to Live) configureren in Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: /powershell/azure/
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: ../azure-resource-manager/management/overview.md#resource-groups
[azure-resource-tags]: ../azure-resource-manager/management/tag-resources.md
[rp-rest-api]: /rest/api/cosmos-db-resource-provider/