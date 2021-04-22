---
title: Onderhoudsvenster configureren (preview)
description: Informatie over het instellen van de tijd waarop gepland onderhoud moet worden uitgevoerd op uw Azure SQL databases, elastische pools en databases van beheerde exemplaren.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/23/2021
ms.openlocfilehash: 9771c68dda6f457586f27ea45fbc52aa118e8006
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874779"
---
# <a name="configure-maintenance-window-preview"></a>Onderhoudsvenster configureren (preview)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Configureer [het onderhoudsvenster (preview)](maintenance-window.md) voor een Azure SQL-database, elastische pool of Azure SQL Managed Instance-database tijdens het maken van de resource, of op elk gewenst moment nadat een resource is gemaakt. 

Het *standaardonderhoudsvenster* van het systeem is dagelijks van 17:00 tot 8:00 uur (lokale tijd van de Azure-regio waar de resource zich bevindt) om piekonderbrekingen in bedrijfsuren te voorkomen. Als het *standaardonderhoudsvenster* van systeem niet de beste tijd is, selecteert u een van de andere beschikbare onderhoudsvensters.

De mogelijkheid om over te gaan naar een ander onderhoudsvenster is niet beschikbaar voor elk serviceniveau of in elke regio. Zie Beschikbaarheid van onderhoudsvenster voor [meer informatie over beschikbaarheid.](maintenance-window.md#availability)

> [!Important]
> Het configureren van het onderhoudsvenster is een langdurige asynchrone bewerking, vergelijkbaar met het wijzigen van de servicelaag van de Azure SQL resource. De resource is beschikbaar tijdens de bewerking, met uitzondering van een korte herconfiguratie die aan het einde van de bewerking wordt uitgevoerd en doorgaans maximaal 8 seconden duurt, zelfs in het geval van onderbroken langlopende transacties. Om de impact van de herconfiguratie te minimaliseren, moet u de bewerking uitvoeren buiten de piekuren.

## <a name="configure-maintenance-window-during-database-creation"></a>Onderhoudsvenster configureren tijdens het maken van de database 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Als u het onderhoudsvenster wilt configureren wanneer u een database, elastische pool of beheerd exemplaar maakt, stelt u het gewenste onderhoudsvenster **in** op **de pagina Aanvullende** instellingen. 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Het onderhoudsvenster instellen tijdens het maken van een individuele database of elastische pool

Zie Create an Azure SQL Database single database (Een database of pool maken) voor stapsgewijse informatie over het maken [van een nieuwe database of pool.](single-database-create-quickstart.md)

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Tabblad Extra database-instellingen maken":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Het onderhoudsvenster instellen tijdens het maken van een beheerd exemplaar

Zie Een nieuw beheerd exemplaar maken voor stapsgewijs informatie over het maken [van een Azure SQL Managed Instance.](../managed-instance/instance-create-quickstart.md)

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Tabblad Aanvullende instellingen voor beheerd exemplaar maken":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

De volgende voorbeelden laten zien hoe u het onderhoudsvenster configureert met behulp Azure PowerShell. U kunt [Azure PowerShell](/powershell/azure/install-az-ps)installeren of de Azure Cloud Shell.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.


## <a name="discover-available-maintenance-windows"></a>Beschikbare onderhoudsvensters ontdekken

Bij het instellen van het onderhoudsvenster heeft elke regio zijn eigen onderhoudsvensteropties die overeenkomen met de tijdzone voor de regio waarin de database of pool zich bevindt. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Onderhoudsvensters SQL Database elastische pool ontdekken 

In het volgende voorbeeld worden de beschikbare onderhoudsvensters voor de regio *eastus2* met behulp van de cmdlet [Get-AzMaintenancePublicConfiguration.](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) Voor databases en elastische pools stelt u `MaintenanceScope` in op `SQLDB` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Onderhoudsvensters SQL Managed Instance ontdekken 

In het volgende voorbeeld worden de beschikbare onderhoudsvensters voor de regio *eastus2* met behulp van de cmdlet [Get-AzMaintenancePublicConfiguration.](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) Voor beheerde exemplaren stelt u `MaintenanceScope` in op `SQLManagedInstance` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Het onderhoudsvenster instellen tijdens het maken van een individuele database

In het volgende voorbeeld wordt een nieuwe database gemaakt en wordt het onderhoudsvenster met behulp van de cmdlet [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) gemaakt. De `-MaintenanceConfigurationId` moet worden ingesteld op een geldige waarde voor de regio van uw database. Zie Beschikbare onderhoudsvensters ontdekken voor geldige waarden [voor uw regio.](#discover-available-maintenance-windows)


   ```powershell-interactive
    # Set variables for your database
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $databaseName = "your_db_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"

    Write-host "Creating a gen5 2 vCore database with maintenance window ${maintenanceConfig} ..."
    $database = New-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Het onderhoudsvenster instellen tijdens het maken van een elastische pool

In het volgende voorbeeld wordt een nieuwe elastische pool gemaakt en wordt het onderhoudsvenster met behulp van de cmdlet [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) gemaakt. Het onderhoudsvenster wordt ingesteld voor de elastische pool, zodat alle databases in de pool de planning voor het onderhoudsvenster van de pool hebben. De `-MaintenanceConfigurationId` moet worden ingesteld op een geldige waarde voor de regio van uw pool. Zie Beschikbare onderhoudsvensters ontdekken voor geldige waarden [voor uw regio.](#discover-available-maintenance-windows)


   ```powershell-interactive
    # Set variables for your pool
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $poolName = "your_pool_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Creating a Standard 50 pool with maintenance window ${maintenanceConfig} ..."
    $pool = New-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -Edition "Standard" `
      -Dtu 50 `
      -DatabaseDtuMin 10 `
      -DatabaseDtuMax 20 `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Het onderhoudsvenster instellen tijdens het maken van een beheerd exemplaar

In het volgende voorbeeld wordt een nieuw beheerd exemplaar gemaakt en wordt het onderhoudsvenster met behulp van de cmdlet [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) gemaakt. Het onderhoudsvenster is ingesteld op het exemplaar, zodat alle databases in het exemplaar de planning voor het onderhoudsvenster van het exemplaar hebben. Voor `-MaintenanceConfigurationId` moet *maintenanceConfigName* een geldige waarde zijn voor de regio van uw exemplaar. Zie Beschikbare onderhoudsvensters ontdekken voor geldige waarden [voor uw regio.](#discover-available-maintenance-windows)


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

De volgende voorbeelden laten zien hoe u het onderhoudsvenster configureert met behulp van Azure CLI. U kunt [de Azure CLI installeren](/cli/azure/install-azure-cli)of de Azure Cloud Shell. 

Het onderhoudsvenster configureren met de Azure CLI is alleen beschikbaar voor SQL Managed Instance.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/cli](https://shell.azure.com/cli) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="discover-available-maintenance-windows"></a>Beschikbare onderhoudsvensters ontdekken

Bij het instellen van het onderhoudsvenster heeft elke regio zijn eigen opties voor het onderhoudsvenster die overeenkomen met de tijdzone voor de regio waarin de database of pool zich bevindt.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Onderhoudsvensters SQL Database elastische pool ontdekken

In het volgende voorbeeld worden de beschikbare onderhoudsvensters voor de regio *eastus2* met behulp van de [opdracht az maintenance public-configuration list.](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) Stel voor databases en elastische pools in `maintenanceScope` op `SQLDB` .

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Onderhoudsvensters SQL Managed Instance ontdekken

In het volgende voorbeeld worden de beschikbare onderhoudsvensters voor de regio *eastus2* met behulp van de [opdracht az maintenance public-configuration list.](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) Stel voor beheerde exemplaren in `maintenanceScope` op `SQLManagedInstance` .

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Het onderhoudsvenster instellen tijdens het maken van een individuele database

In het volgende voorbeeld wordt een nieuwe database gemaakt en wordt het onderhoudsvenster met behulp van de [opdracht az sql db create](/cli/azure/sql/db#az_sql_db_create) gemaakt. De `--maint-config-id` (of `-m` ) moet worden ingesteld op een geldige waarde voor de regio van uw database. Zie Beschikbare onderhoudsvensters ontdekken voor geldige waarden [voor uw regio.](#discover-available-maintenance-windows)


   ```azurecli
    # Set variables for your database
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    databaseName="your_db_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Create database
    az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Het onderhoudsvenster instellen tijdens het maken van een elastische pool

In het volgende voorbeeld wordt een nieuwe elastische pool gemaakt en wordt het onderhoudsvenster met behulp van de cmdlet [az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) gemaakt. Het onderhoudsvenster wordt ingesteld voor de elastische pool, zodat alle databases in de pool de planning voor het onderhoudsvenster van de pool hebben. De `--maint-config-id` (of `-m` ) moet worden ingesteld op een geldige waarde voor de regio van uw pool. Zie Beschikbare onderhoudsvensters ontdekken voor geldige waarden [voor uw regio.](#discover-available-maintenance-windows)


   ```azurecli
    # Set variables for your pool
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    poolName="your_pool_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Create elastic pool
    az sql elastic-pool create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Het onderhoudsvenster instellen tijdens het maken van een beheerd exemplaar

In het volgende voorbeeld wordt een nieuw beheerd exemplaar gemaakt en wordt het onderhoudsvenster met [az sql mi create gemaakt.](/cli/azure/sql/mi#az_sql_mi_create) Het onderhoudsvenster wordt ingesteld op het exemplaar, zodat alle databases in het exemplaar de planning voor het onderhoudsvenster van het exemplaar hebben. *MaintenanceConfigName* moet een geldige waarde zijn voor de regio van uw exemplaar. Zie Beschikbare onderhoudsvensters ontdekken voor geldige waarden [voor uw regio.](#discover-available-maintenance-windows)

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Onderhoudsvenster configureren voor bestaande databases


Bij het toepassen van een selectie van een onderhoudsvenster op een database, kan er in sommige gevallen een korte herconfiguratie (enkele seconden) worden uitgevoerd, omdat Azure de vereiste wijzigingen moet toepassen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Met de volgende stappen stelt u het onderhoudsvenster in voor een bestaande database, elastische pool of beheerd exemplaar met behulp van de Azure Portal:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Het onderhoudsvenster voor een bestaande database of elastische pool instellen

1. Navigeer naar SQL database of elastische pool waar u het onderhoudsvenster voor wilt instellen.
1. Selecteer in **het** menu Instellingen **de optie Onderhoud** en selecteer vervolgens het gewenste onderhoudsvenster.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="SQL database onderhoudspagina":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Het onderhoudsvenster voor een bestaand beheerd exemplaar instellen

1. Navigeer naar het beheerde exemplaar waar u het onderhoudsvenster voor wilt instellen.
1. Selecteer in **het** menu Instellingen **de optie Onderhoud** en selecteer vervolgens het gewenste onderhoudsvenster.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="Onderhoudspagina voor SQL Managed Instance":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Het onderhoudsvenster voor een bestaande database instellen

In het volgende voorbeeld wordt het onderhoudsvenster voor een bestaande database ingesteld met behulp van de cmdlet [Set-AzSqlDatabase.](/powershell/module/az.sql/set-azsqldatabase) De `-MaintenanceConfigurationId` moet worden ingesteld op een geldige waarde voor de regio van uw database. Zie Beschikbare onderhoudsvensters ontdekken voor geldige waarden [voor uw regio.](#discover-available-maintenance-windows)

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Changing database maintenance window to ${maintenanceConfig} ..."
    $database = Set-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Het onderhoudsvenster voor een bestaande elastische pool instellen

In het volgende voorbeeld wordt het onderhoudsvenster voor een bestaande elastische pool ingesteld met behulp van de cmdlet [Set-AzSqlElasticPool.](/powershell/module/az.sql/set-azsqlelasticpool) Het is belangrijk om ervoor te zorgen dat de `$maintenanceConfig` waarde een geldige waarde is voor de regio van uw pool.  Zie Beschikbare onderhoudsvensters ontdekken voor geldige waarden [voor een regio.](#discover-available-maintenance-windows)

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"
    
    Write-host "Changing pool maintenance window to ${maintenanceConfig} ..."
    $pool = Set-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Het onderhoudsvenster instellen op een bestaand beheerd exemplaar

In het volgende voorbeeld wordt het onderhoudsvenster voor een bestaand beheerd exemplaar ingesteld met behulp van de cmdlet [Set-AzSqlInstance.](/powershell/module/az.sql/set-azsqlinstance) Het is belangrijk om ervoor te zorgen dat de waarde een geldige waarde moet `$maintenanceConfig` zijn voor de regio van uw exemplaar.  Zie Beschikbare onderhoudsvensters ontdekken voor geldige waarden [voor een regio.](#discover-available-maintenance-windows)


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

De volgende voorbeelden laten zien hoe u het onderhoudsvenster configureert met behulp van Azure CLI. U kunt [de Azure CLI installeren](/cli/azure/install-azure-cli)of de Azure Cloud Shell.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Het onderhoudsvenster voor een bestaande database instellen

In het volgende voorbeeld wordt het onderhoudsvenster voor een bestaande database met behulp van de [opdracht az sql db update.](/cli/azure/sql/db#az_sql_db_update) De `--maint-config-id` (of `-m` ) moet worden ingesteld op een geldige waarde voor de regio van uw database. Zie Beschikbare onderhoudsvensters ontdekken voor geldige waarden [voor uw regio.](#discover-available-maintenance-windows)

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Update database
    az sql db update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Het onderhoudsvenster voor een bestaande elastische pool instellen

In het volgende voorbeeld wordt het onderhoudsvenster voor een bestaande elastische pool met behulp van [de opdracht az sql elastic-pool update.](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) Het is belangrijk om ervoor te zorgen dat de `maintenanceConfig` waarde een geldige waarde is voor de regio van uw pool.  Zie Beschikbare onderhoudsvensters ontdekken voor geldige waarden [voor een regio.](#discover-available-maintenance-windows)

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Update pool
    az sql elastic-pool update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Het onderhoudsvenster instellen op een bestaand beheerd exemplaar

In het volgende voorbeeld wordt het onderhoudsvenster met [az sql mi update .](/cli/azure/sql/mi#az_sql_mi_update) Het onderhoudsvenster wordt ingesteld op het exemplaar, zodat alle databases in het exemplaar de planning voor het onderhoudsvenster van het exemplaar hebben. Voor `-MaintenanceConfigurationId` moet *MaintenanceConfigName* een geldige waarde zijn voor de regio van uw exemplaar. Zie Beschikbare onderhoudsvensters ontdekken voor geldige waarden [voor uw regio.](#discover-available-maintenance-windows)

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Resources opruimen

Zorg ervoor dat u overbodige resources verwijdert nadat u er klaar mee bent om onnodige kosten te voorkomen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigeer naar SQL database of elastische pool die u niet meer nodig hebt.
1. Selecteer in **het** menu Overzicht de optie om de resource te verwijderen.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   # Delete database
   Remove-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName

   # Delete elastic pool
   Remove-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

   ```azurecli
   az sql db delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName

   az sql elastic-pool delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName
   ```

-----

## <a name="next-steps"></a>Volgende stappen

- Zie onderhoudsvenster [(preview) voor meer informatie over het onderhoudsvenster.](maintenance-window.md)
- Zie Veelgestelde vragen over [onderhoudsvenster voor meer informatie.](maintenance-window-faq.yml)
- Zie Bewaking en prestaties afstemmen in Azure SQL Database en Azure SQL Managed Instance voor meer informatie over [het optimaliseren van Azure SQL Managed Instance.](monitor-tune-overview.md)
