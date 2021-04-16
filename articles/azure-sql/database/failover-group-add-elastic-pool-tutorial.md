---
title: 'Zelfstudie: Een elastische pool aan een failovergroep toevoegen'
description: Voeg een elastische Azure SQL Database-pool toe aan een failovergroep met behulp van de Azure-portal, PowerShell of Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/27/2019
ms.openlocfilehash: dd0532469cd4390d9a72900dcebc22994239325b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479148"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Zelfstudie: Een elastische pool van Azure SQL Database toevoegen aan een failovergroep
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Configureer een failovergroep voor een elastische Azure SQL Database-pool en test de failover met behulp van de Azure-portal.  In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Maak een individuele database.
> - Voeg de database aan een elastische pool toe.
> - Maak een [failovergroep](auto-failover-group-overview.md) voor twee elastische pools tussen twee servers.
> - Test de failover.

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

- Een Azure-abonnement. [Maak een gratis account aan](https://azure.microsoft.com/free/) als u er nog geen hebt.

## <a name="1---create-a-single-database"></a>1 - Maak een individuele database

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---add-the-database-to-an-elastic-pool"></a>2 - Voeg de database aan een elastische pool toe

In deze stap maakt u een elastische pool en voegt u er uw database aan toe.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Maak uw elastische pool met behulp van de Azure-portal.

1. Selecteer **Azure SQL** in het menu aan de linkerzijde van de Azure Portal. Als **Azure SQL** niet in de lijst staat, selecteert u **Alle services** en typt u 'Azure SQL' in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze te favoriseren en voeg deze als item in de linkernavigatiebalk toe.
1. Selecteer **+ Toevoegen** om de pagina **SQL-implementatieoptie selecteren** te openen. U kunt meer informatie over de verschillende databases bekijken door Details weergeven te selecteren in de tegel Databases.
1. Selecteer **Elastische pool** uit de vervolgkeuzelijst **Resourcetype** in de tegel **SQL-databases**. Selecteer **Maken** om uw elastische pool te maken.

    ![Elastische pool selecteren](./media/failover-group-add-elastic-pool-tutorial/select-azure-sql-elastic-pool.png)

1. Configureer uw elastische pool met de volgende waarden:
   - **Naam**: Geef een unieke naam op voor uw elastische pool, zoals `myElasticPool`.
   - **Abonnement**: Selecteer uw abonnement in de vervolgkeuzelijst.
   - **ResourceGroup**: Selecteer in de vervolgkeuzelijst `myResourceGroup`, de resourcegroep die u in gedeelte 1 hebt gemaakt.
   - **Server**: Selecteer in de vervolgkeuzelijst de server die u in gedeelte 1 hebt gemaakt.  

       ![Nieuwe server voor elastische pool maken](./media/failover-group-add-elastic-pool-tutorial/use-existing-server-for-elastic-pool.png)

   - **Compute en opslag**: Selecteer **Elastische pool configureren** om uw compute en opslag te configureren en om uw individuele database aan uw elastische pool toe te voegen. In het tabblad **Poolinstellingen** laat u de standaardwaarde Gen5 staan en kiest u voor 2 vCores en 32 GB.

1. Op de pagina **Configureren** selecteert u het tabblad **Databases** en kiest u vervolgens **Database toevoegen**. Kies de database die u in gedeelte 1 hebt gemaakt en selecteer vervolgens **Toepassen** om deze aan uw elastische pool toe te voegen. Selecteer opnieuw **Toepassen** om de instellingen voor de elastische pool toe te passen en sluit de pagina **Configureren**.

    ![Database aan elastische pool toevoegen](./media/failover-group-add-elastic-pool-tutorial/add-database-to-elastic-pool.png)

1. Selecteer **Controleren + maken** om uw instellingen voor de elastische groep te controleren en selecteer vervolgens **Maken** om uw elastische pool te maken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak uw elastische pools en secundaire server met behulp van PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Hiermee maakt u een elastische pool voor een Azure SQL Database.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Hiermee worden eigenschappen ingesteld voor een database of plaatst een bestaande database in een elastische pool. |

---

## <a name="3---create-the-failover-group"></a>3 - Maak de failovergroep

In deze stap maakt u een [failovergroep](auto-failover-group-overview.md) tussen een bestaande server en een nieuwe server in een andere regio. Voeg vervolgens de elastische pool aan de failovergroep toe.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Maak uw failovergroep met behulp van de Azure-portal.

1. Selecteer **Azure SQL** in het menu aan de linkerzijde van de [Azure-portal](https://portal.azure.com). Als **Azure SQL** niet in de lijst staat, selecteert u **Alle services** en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze te favoriseren en voeg deze als item in de linkernavigatiebalk toe.
1. Selecteer de elastische pool die u in het vorige gedeelte hebt gemaakt, zoals `myElasticPool`.
1. Selecteer in het deelvenster **Overzicht** de naam van de server onder **Servernaam** om de instellingen voor de server te openen.
  
    ![Server openen voor elastische pool](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Selecteer **Failovergroepen** onder het deelvenster **Instellingen** en selecteer vervolgens **Groep toevoegen** om een nieuwe failovergroep te maken.

    ![Nieuwe failovergroep toevoegen](./media/failover-group-add-elastic-pool-tutorial/elastic-pool-failover-group.png)

1. Voer op de pagina **Failovergroep maken** de volgende waarden in en selecteer **Maken**:
    - **Naam van failovergroep**: Typ een unieke naam voor de failovergroep, zoals `failovergrouptutorial`.
    - **Secundaire server**: Selecteer de optie *Vereiste instellingen configureren* en kies vervolgens **Een nieuwe server maken**. U kunt ook een reeds bestaande server als secundaire server kiezen. Nadat u de volgende waarden voor de nieuwe secundaire server hebt ingevoerd, selecteert u **Selecteren**.
        - **Servernaam**: Typ een unieke naam voor de secundaire server, zoals `mysqlsecondary`.
        - **Aanmeldgegevens van serverbeheerder**: Type `azureuser`
        - **Wachtwoord**: Typ een complex wachtwoord dat voldoet aan de wachtwoordvereisten.
        - **Locatie**: Kies een locatie uit de vervolgkeuzelijst, zoals `East US`. Deze locatie moet anders zijn dan de locatie van de primaire server.

       > [!NOTE]
       > De aanmeldings- en firewallinstellingen van de server moeten overeenkomen met die van de primaire server.

       ![Een secundaire server maken voor de failovergroep](./media/failover-group-add-elastic-pool-tutorial/create-secondary-failover-server.png)

1. Selecteer **Databases in de groep** en selecteer vervolgens de elastische pool die u in gedeelte 2 hebt gemaakt. Er wordt een waarschuwing weergegeven waarin u wordt gevraagd om een elastische pool te maken op de secundaire server. Selecteer de waarschuwing en selecteer vervolgens **OK** om de elastische pool te maken op de secundaire server. 

   ![Elastische pool aan failovergroep toevoegen](./media/failover-group-add-elastic-pool-tutorial/add-elastic-pool-to-failover-group.png)

1. Kies **Selecteren** om uw instellingen voor de elastische groep op de failovergroep toe te passen en selecteer vervolgens **Maken** om uw failovergroep te maken. Door de elastische pool aan de failovergroep toe te voegen, wordt het proces voor geo-replicatie automatisch gestart.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak uw failovergroep met behulp van PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary server =" $drServerName

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured"

   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Create a failover group between the servers
   Write-host "Creating failover group..."
   New-AzSqlDatabaseFailoverGroup `
     â€“ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      â€“FailoverGroupName $failoverGroupName `
      â€“FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully."

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...."
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..."
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases
   $failoverGroup
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een server die als host fungeert voor databases en elastische pools. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewallregel voor een server. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Hiermee maakt u een elastische pool voor een Azure SQL Database.|
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer Azure SQL-databases toe aan een failovergroep. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database-failovergroepen opgehaald of weergegeven. |

---

## <a name="4---test-failover"></a>4 - Test de failover

In deze stap voert u een failover van uw failovergroep naar de secundaire server uit en voert u vervolgens een failback uit met behulp van de Azure-portal.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Test de failover van uw failovergroep met behulp van de Azure-portal.

1. Selecteer **Azure SQL** in het menu aan de linkerzijde van de [Azure-portal](https://portal.azure.com). Als **Azure SQL** niet in de lijst staat, selecteert u **Alle services** en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze te favoriseren en voeg deze als item in de linkernavigatiebalk toe.
1. Selecteer de elastische pool die u in het vorige gedeelte hebt gemaakt, zoals `myElasticPool`.
1. Selecteer de naam van de server onder **Servernaam** om de instellingen voor de server te openen.

    ![Server openen voor elastische pool](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Selecteer **Failovergroepen** onder het deelvenster **Instellingen** en kies vervolgens de failovergroep die u in gedeelte 2 hebt gemaakt.
  
   ![De failovergroep in de portal selecteren](./media/failover-group-add-elastic-pool-tutorial/select-failover-group.png)

1. Controleer welke server primair en welke server secundair is.
1. Selecteer in het taakvenster **Failover** om een failover van uw failovergroep met elastische groepen uit te voeren.
1. Selecteer **Ja** in de waarschuwing die u laat weten dat TDS-sessies worden losgekoppeld.

   ![Failover uitvoeren van de failovergroep met uw database](./media/failover-group-add-elastic-pool-tutorial/failover-sql-db.png)

1. Controleer welke server primair en welke server secundair is. Als de failover is geslaagd, moeten de twee servers van rol zijn gewisseld.
1. Selecteer opnieuw **Failover** om de failovergroep te herstellen naar de oorspronkelijke instellingen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Test de failover van uw failovergroep met behulp van PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName
   ```

Voer een failover van uw failovergroep naar de secundaire server uit en voer vervolgens een failback uit met PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary"
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database-failovergroepen opgehaald of weergegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover uitgevoerd van een Azure SQL Database-failovergroep. |

---

## <a name="clean-up-resources"></a>Resources opschonen

Schoon resources op door de resourcegroep te verwijderen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigeer in [Azure Portal](https://portal.azure.com) naar uw resourcegroep.
1. Selecteer **Resourcegroep verwijderen** om alle resources uit de groep en de resourcegroep zelf te verwijderen.
1. Typ de naam van de resourcegroep, `myResourceGroup`, in het tekstvak en selecteer vervolgens **Verwijderen** om de resourcegroep te verwijderen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Schoon uw resources op met PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

In dit gedeelte van de zelfstudie wordt gebruikgemaakt van de volgende PowerShell-cmdlet:

| Opdracht | Opmerkingen |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep |

---

> [!IMPORTANT]
> Als u de resourcegroep wilt behouden, maar de secundaire database wilt wissen, dan moet u deze uit de failovergroep verwijderen voordat u deze wist. Het wissen van een secundaire database voordat deze uit de failovergroep is verwijderd, kan onvoorspelbaar gedrag veroorzaken.

## <a name="full-script"></a>Volledige script

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een server die als host fungeert voor databases en elastische pools. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewallregel voor een server. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een database. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Hiermee maakt u een elastische pool voor een Azure SQL Database.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Hiermee worden eigenschappen ingesteld voor een database of plaatst een bestaande database in een elastische pool. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hiermee haalt u een of meer databases op in SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer Azure SQL-databases toe aan een failovergroep. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database-failovergroepen opgehaald of weergegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover uitgevoerd van een Azure SQL Database-failovergroep. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep |

# <a name="portal"></a>[Portal](#tab/azure-portal)

Er zijn geen scripts beschikbaar voor de Azure-portal.

---

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een elastische Azure SQL Database-pool aan een failovergroep toegevoegd en de failover getest. U hebt geleerd hoe u:

> [!div class="checklist"]
>
> - Maak een individuele database.
> - Voeg de database aan een elastische pool toe.
> - Maak een [failovergroep](auto-failover-group-overview.md) voor twee elastische pools tussen twee servers.
> - Test de failover.

Ga verder met de volgende zelfstudie over migreren met behulp van DMS.

> [!div class="nextstepaction"]
> [Zelfstudie: SQL Server naar een pooldatabase migreren met behulp van DMS](../../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
