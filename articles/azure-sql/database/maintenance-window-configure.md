---
title: Onderhouds venster configureren (preview-versie)
description: Meer informatie over het instellen van de tijd waarop gepland onderhoud moet worden uitgevoerd op uw Azure SQL-data bases, elastische Pools en beheerde exemplaar databases.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/04/2021
ms.openlocfilehash: 210f0c52a2b27492bfa2181473043df3537157d2
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183196"
---
# <a name="configure-maintenance-window-preview"></a>Onderhouds venster configureren (preview-versie)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Configureer het [onderhouds venster (preview)](maintenance-window.md) voor een Azure SQL database, een elastische pool of een Azure SQL Managed instance-data base tijdens het maken van resources of op elk gewenst moment nadat een resource is gemaakt. 

Het *systeem standaard* onderhoud van de computer is tot 17:00 uur naar 8 a.m. Daily (lokale tijd van de Azure-regio waarin de resource zich bevindt) om te voor komen dat er pieken voor kantoor uren worden onderbroken. Als de *systeem standaard* onderhouds venster niet het beste is, selecteert u een van de andere beschik bare onderhouds Vensters.

De mogelijkheid om te wijzigen in een ander onderhouds venster is niet beschikbaar voor elk service niveau of in elke regio. Zie [Beschik baarheid van onderhouds venster](maintenance-window.md#availability)voor meer informatie over beschik baarheid.

> [!Important]
> Het configureren van het onderhouds venster is een langlopende asynchrone bewerking, vergelijkbaar met het wijzigen van de servicelaag van de Azure SQL-resource. De resource is beschikbaar tijdens de bewerking, met uitzonde ring van een korte failover die aan het einde van de bewerking plaatsvindt en doorgaans tot 8 seconden duurt, zelfs in het geval van langdurige langlopende trans acties. Als u de gevolgen van de failover wilt beperken, moet u de bewerking buiten de piek uren uitvoeren.

## <a name="configure-maintenance-window-during-database-creation"></a>Onderhouds venster configureren tijdens het maken van de data base 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Als u het onderhouds venster wilt configureren wanneer u een Data Base, een elastische pool of een beheerd exemplaar maakt, stelt u het gewenste **onderhouds venster** op de pagina **extra instellingen** in. 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Het onderhouds venster instellen tijdens het maken van één data base of elastische pool

Zie [een Azure SQL database afzonderlijke data base maken](single-database-create-quickstart.md)voor stapsgewijze informatie over het maken van een nieuwe data base of groep.

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Het tabblad Extra instellingen voor data base maken":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Het onderhouds venster instellen tijdens het maken van een beheerd exemplaar

Zie [een Azure SQL Managed instance maken](../managed-instance/instance-create-quickstart.md)voor stapsgewijze informatie over het maken van een nieuw beheerd exemplaar.

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Het tabblad Extra instellingen voor het beheerde exemplaar maken":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In de volgende voor beelden ziet u hoe u het onderhouds venster configureert met behulp van Azure PowerShell. U kunt [Azure PowerShell installeren](/powershell/azure/install-az-ps)of de Azure Cloud shell gebruiken.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.


## <a name="discover-available-maintenance-windows"></a>Beschik bare onderhouds Vensters detecteren

Wanneer u het onderhouds venster instelt, heeft elke regio een eigen onderhouds venster opties die overeenkomen met de tijd zone voor de regio waarin de data base of groep zich bevindt. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Onderhouds Vensters voor SQL Database en elastische Pools detecteren 

In het volgende voor beeld worden de beschik bare onderhouds Vensters voor de *eastus2* -regio geretourneerd met behulp van de cmdlet [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) . Stel voor-data bases en elastische Pools `MaintenanceScope` in op `SQLDB` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Onderhouds Vensters van SQL Managed instance detecteren 

In het volgende voor beeld worden de beschik bare onderhouds Vensters voor de *eastus2* -regio geretourneerd met behulp van de cmdlet [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) . Stel voor beheerde instanties `MaintenanceScope` in op `SQLManagedInstance` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Het onderhouds venster instellen tijdens het maken van één data base

In het volgende voor beeld wordt een nieuwe data base gemaakt en wordt het onderhouds venster ingesteld met behulp van de cmdlet [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) . De `-MaintenanceConfigurationId` moet worden ingesteld op een geldige waarde voor de regio van uw data base. Zie [Detecteer beschik bare onderhouds Vensters](#discover-available-maintenance-windows)om geldige waarden voor uw regio op te halen.


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



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Het onderhouds venster instellen tijdens het maken van een elastische pool

In het volgende voor beeld wordt een nieuwe elastische pool gemaakt en wordt het onderhouds venster ingesteld met behulp van de cmdlet [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) . Het onderhouds venster is ingesteld op de elastische pool, zodat alle data bases in de groep de planning van het onderhouds venster van de pool hebben. De `-MaintenanceConfigurationId` moet worden ingesteld op een geldige waarde voor de regio van uw pool. Zie [Detecteer beschik bare onderhouds Vensters](#discover-available-maintenance-windows)om geldige waarden voor uw regio op te halen.


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

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Het onderhouds venster instellen tijdens het maken van een beheerd exemplaar

In het volgende voor beeld wordt een nieuw beheerd exemplaar gemaakt en wordt het onderhouds venster ingesteld met behulp van de cmdlet [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) . Het onderhouds venster is ingesteld op het exemplaar, zodat alle data bases in het exemplaar het onderhouds venster schema van het exemplaar hebben. Voor `-MaintenanceConfigurationId` is de *MaintenanceConfigName* een geldige waarde voor de regio van uw exemplaar. Zie [Detecteer beschik bare onderhouds Vensters](#discover-available-maintenance-windows)om geldige waarden voor uw regio op te halen.


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

In de volgende voor beelden ziet u hoe u het onderhouds venster configureert met behulp van Azure CLI. U kunt [de Azure cli installeren](/cli/azure/install-azure-cli)of de Azure Cloud shell gebruiken. 

Het configureren van het onderhouds venster met de Azure CLI is alleen beschikbaar voor SQL Managed instance.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/cli](https://shell.azure.com/cli) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="discover-available-maintenance-windows"></a>Beschik bare onderhouds Vensters detecteren

Wanneer u het onderhouds venster instelt, heeft elke regio een eigen onderhouds venster opties die overeenkomen met de tijd zone voor de regio waarin de data base of groep zich bevindt.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Onderhouds Vensters voor SQL Database en elastische Pools detecteren

In het volgende voor beeld worden de beschik bare onderhouds Vensters voor de *eastus2* -regio geretourneerd met behulp van de opdracht [AZ Maintenance Public-Configuration List](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) . Stel voor-data bases en elastische Pools `maintenanceScope` in op `SQLDB` .

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Onderhouds Vensters van SQL Managed instance detecteren

In het volgende voor beeld worden de beschik bare onderhouds Vensters voor de *eastus2* -regio geretourneerd met behulp van de opdracht [AZ Maintenance Public-Configuration List](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) . Stel voor beheerde instanties `maintenanceScope` in op `SQLManagedInstance` .

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Het onderhouds venster instellen tijdens het maken van één data base

In het volgende voor beeld wordt een nieuwe data base gemaakt en wordt het onderhouds venster ingesteld met behulp van de opdracht [AZ SQL DB Create](/cli/azure/sql/db#az_sql_db_create) . De `--maint-config-id` (of `-m` ) moet worden ingesteld op een geldige waarde voor de regio van uw data base. Zie [Detecteer beschik bare onderhouds Vensters](#discover-available-maintenance-windows)om geldige waarden voor uw regio op te halen.


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

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Het onderhouds venster instellen tijdens het maken van een elastische pool

In het volgende voor beeld wordt een nieuwe elastische pool gemaakt en wordt het onderhouds venster ingesteld met de cmdlet [AZ SQL Elastic-pool Create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) . Het onderhouds venster is ingesteld op de elastische pool, zodat alle data bases in de groep de planning van het onderhouds venster van de pool hebben. De `--maint-config-id` (of `-m` ) moet worden ingesteld op een geldige waarde voor de regio van uw pool. Zie [Detecteer beschik bare onderhouds Vensters](#discover-available-maintenance-windows)om geldige waarden voor uw regio op te halen.


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

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Het onderhouds venster instellen tijdens het maken van een beheerd exemplaar

In het volgende voor beeld wordt een nieuw beheerd exemplaar gemaakt en wordt het onderhouds venster ingesteld met behulp van [AZ SQL mi Create](/cli/azure/sql/mi#az_sql_mi_create). Het onderhouds venster is ingesteld op het exemplaar, zodat alle data bases in het exemplaar het onderhouds venster schema van het exemplaar hebben. *MaintenanceConfigName* moet een geldige waarde zijn voor de regio van uw exemplaar. Zie [Detecteer beschik bare onderhouds Vensters](#discover-available-maintenance-windows)om geldige waarden voor uw regio op te halen.

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Onderhouds venster voor bestaande data bases configureren


Bij het Toep assen van een onderhouds venster selectie aan een Data Base kan een korte failover (enkele seconden) in sommige gevallen worden ervaren, omdat Azure de vereiste wijzigingen toepast.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Met de volgende stappen wordt het onderhouds venster voor een bestaande data base, elastische pool of een beheerd exemplaar ingesteld met behulp van de Azure Portal:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Het onderhouds venster voor een bestaande data base of elastische pool instellen

1. Navigeer naar het SQL database of de elastische pool waarvoor u het onderhouds venster wilt instellen.
1. Selecteer in het menu **instellingen** de optie **onderhoud** en selecteer vervolgens het gewenste onderhouds venster.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="SQL database onderhouds pagina":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Het onderhouds venster voor een bestaand beheerd exemplaar instellen

1. Navigeer naar het beheerde exemplaar waarvoor u het onderhouds venster wilt instellen.
1. Selecteer in het menu **instellingen** de optie **onderhoud** en selecteer vervolgens het gewenste onderhouds venster.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="Onderhouds pagina voor SQL Managed instance":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Het onderhouds venster voor een bestaande data base instellen

In het volgende voor beeld wordt het onderhouds venster voor een bestaande data base ingesteld met behulp van de cmdlet [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) . De `-MaintenanceConfigurationId` moet worden ingesteld op een geldige waarde voor de regio van uw data base. Zie [Detecteer beschik bare onderhouds Vensters](#discover-available-maintenance-windows)om geldige waarden voor uw regio op te halen.

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

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Het onderhouds venster instellen voor een bestaande elastische pool

In het volgende voor beeld wordt het onderhouds venster voor een bestaande elastische pool ingesteld met behulp van de cmdlet [set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) . Het is belang rijk om ervoor te zorgen dat de `$maintenanceConfig` waarde een geldige waarde is voor de regio van uw pool.  Zie [detectie van beschik bare onderhouds Vensters](#discover-available-maintenance-windows)voor geldige waarden voor een regio.

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



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Het onderhouds venster instellen voor een bestaand beheerd exemplaar

In het volgende voor beeld wordt het onderhouds venster voor een bestaand beheerd exemplaar ingesteld met behulp van de cmdlet [set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) . Het is belang rijk om ervoor te zorgen dat de `$maintenanceConfig` waarde een geldige waarde voor de regio van uw exemplaar moet zijn.  Zie [detectie van beschik bare onderhouds Vensters](#discover-available-maintenance-windows)voor geldige waarden voor een regio.


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

In de volgende voor beelden ziet u hoe u het onderhouds venster configureert met behulp van Azure CLI. U kunt [de Azure cli installeren](/cli/azure/install-azure-cli)of de Azure Cloud shell gebruiken.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Het onderhouds venster voor een bestaande data base instellen

In het volgende voor beeld wordt het onderhouds venster voor een bestaande data base ingesteld met behulp van de opdracht [AZ SQL DB Update](/cli/azure/sql/db#az_sql_db_update) . De `--maint-config-id` (of `-m` ) moet worden ingesteld op een geldige waarde voor de regio van uw data base. Zie [Detecteer beschik bare onderhouds Vensters](#discover-available-maintenance-windows)om geldige waarden voor uw regio op te halen.

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

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Het onderhouds venster instellen voor een bestaande elastische pool

In het volgende voor beeld wordt het onderhouds venster voor een bestaande elastische pool ingesteld met behulp van de opdracht [AZ SQL Elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) . Het is belang rijk om ervoor te zorgen dat de `maintenanceConfig` waarde een geldige waarde is voor de regio van uw pool.  Zie [detectie van beschik bare onderhouds Vensters](#discover-available-maintenance-windows)voor geldige waarden voor een regio.

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

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Het onderhouds venster instellen voor een bestaand beheerd exemplaar

In het volgende voor beeld wordt het onderhouds venster ingesteld met behulp van [AZ SQL mi update](/cli/azure/sql/mi#az_sql_mi_update). Het onderhouds venster is ingesteld op het exemplaar, zodat alle data bases in het exemplaar het onderhouds venster schema van het exemplaar hebben. Voor `-MaintenanceConfigurationId` is de *MaintenanceConfigName* een geldige waarde voor de regio van uw exemplaar. Zie [Detecteer beschik bare onderhouds Vensters](#discover-available-maintenance-windows)om geldige waarden voor uw regio op te halen.

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Resources opruimen

Zorg ervoor dat u overbodige resources verwijdert nadat u klaar bent met deze om onnodige kosten te voor komen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga naar de SQL database of elastische pool die u niet meer nodig hebt.
1. Selecteer in het menu **overzicht** de optie om de resource te verwijderen.


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

- Zie [onderhouds venster (preview-versie)](maintenance-window.md)voor meer informatie over onderhouds Vensters.
- Zie [Veelgestelde vragen over onderhouds Vensters](maintenance-window-faq.yml)voor meer informatie.
- Zie [bewaking en prestaties afstemmen in Azure SQL database en Azure SQL Managed instance](monitor-tune-overview.md)voor meer informatie over het optimaliseren van prestaties.
