---
title: 'Zelfstudie: Een database aan een groep voor failover toevoegen'
description: Voeg een database in Azure SQL Database toe aan een groep voor automatische failover met behulp van de Azure-portal, PowerShell of de Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/19/2019
ms.openlocfilehash: 68fa089713c3dd89b4699011ded7d667bca6f73f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178067"
---
# <a name="tutorial-add-an-azure-sql-database-to-an-autofailover-group"></a>Zelfstudie: Een Azure SQL Database toevoegen aan een groep voor automatische failover
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Een [failovergroep](auto-failover-group-overview.md) is een declaratieve abstractielaag waarmee u meerdere geo-gerepliceerde databases kunt groeperen. Leer een failovergroep configureren voor een Azure SQL Database en test de failover met behulp van de Azure-portal, PowerShell of de Azure CLI.  In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Een database maken in Azure SQL Database
> - Een failovergroep voor de database tussen twee servers maken.
> - Failover testen.

## <a name="prerequisites"></a>Vereisten

# <a name="the-portal"></a>[De portal](#tab/azure-portal)

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Zorg dat u over het volgende beschikt om de zelfstudie te kunnen voltooien:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.
- [Azure PowerShell](/powershell/azure/)

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Zorg dat u over het volgende beschikt als u de zelfstudie wilt uitvoeren:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.
- De nieuwste versie van [de Azure CLI](/cli/azure/install-azure-cli).

---

## <a name="1---create-a-database"></a>1 - Een database maken

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - De failovergroep maken

In deze stap maakt u een [failovergroep](auto-failover-group-overview.md) tussen een bestaande server en een nieuwe server in een andere regio. Vervolgens voegt u de voorbeelddatabase toe aan de failovergroep.

# <a name="the-portal"></a>[De portal](#tab/azure-portal)

Maak uw failovergroep en voeg uw database toe met behulp van de Azure-portal.

1. Selecteer **Azure SQL** in het menu aan de linkerkant van de [Azure-portal](https://portal.azure.com). Als **Azure SQL** niet in de lijst staat, selecteert u **Alle services** en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en toe te voegen als item in de linkernavigatiebalk.
1. Selecteer de database die is gemaakt in sectie 1, zoals `mySampleDatabase`.
1. Failovergroepen kunnen op serverniveau worden geconfigureerd. Selecteer de naam van de server onder **Servernaam** om de instellingen voor de server te openen.

   ![Server voor database openen](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Selecteer **Failovergroepen** onder het deelvenster **Instellingen** en selecteer vervolgens **Groep toevoegen** om een nieuwe failovergroep te maken.

   ![Nieuwe failovergroep toevoegen](./media/failover-group-add-single-database-tutorial/sqldb-add-new-failover-group.png)

1. Voer op de pagina **Failovergroep maken** de volgende waarden in en selecteer **Maken**:

   - **Naam van failovergroep**: Typ een unieke naam voor de failovergroep, zoals `failovergrouptutorial`.
   - **Secundaire server**: Selecteer de optie *Vereiste instellingen configureren* en kies vervolgens **Een nieuwe server maken**. U kunt ook een al bestaande server kiezen als secundaire server. Nadat u de volgende waarden hebt ingevoerd, selecteert u **Selecteren**.
      - **Servernaam**: Typ een unieke naam voor de secundaire server, zoals `mysqlsecondary`.
      - **Aanmeldgegevens van serverbeheerder**: Type `azureuser`
      - **Wachtwoord**: Typ een complex wachtwoord dat voldoet aan de wachtwoordvereisten.
      - **Locatie**: Kies een locatie in de vervolgkeuzelijst, zoals `East US`. Deze locatie mag niet dezelfde locatie zijn als die van uw primaire server.

     > [!NOTE]
     > De aanmeldings- en firewall-instellingen van de server moeten overeenkomen met die van de primaire server.

     ![Een secundaire server maken voor de failovergroep](./media/failover-group-add-single-database-tutorial/create-secondary-failover-server.png)

   - **Databases in de groep**: Wanneer een secundaire server is geselecteerd, wordt deze optie ontgrendeld. Selecteer deze optie om **Toe te voegen databases te selecteren** en kies de database die u in sectie 1 hebt gemaakt. Door de database aan de failovergroep toe te voegen, wordt het proces voor geo-replicatie automatisch gestart.

   ![SQL Database aan failovergroep toevoegen](./media/failover-group-add-single-database-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak uw failovergroep en voeg uw database toe met behulp van de PowerShell.

   > [!NOTE]
   > De aanmeldings- en firewall-instellingen van de server moeten overeenkomen met die van de primaire server.

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup

   # Add the database to the failover group
   Write-host "Adding the database to the failover group..."
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..."
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een server in Azure SQL Database die individuele databases en elastische pools host. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewallregel voor een server in Azure SQL Database. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u één database in Azure SQL Database. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep in Azure SQL Database. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hiermee haalt u een of meer databases op in Azure SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer databases toe aan een failovergroep in Azure SQL Database. |

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Maak uw failovergroep en voeg uw database toe met behulp van de Azure CLI.

   > [!NOTE]
   > De aanmeldings- en firewall-instellingen van de server moeten overeenkomen met die van de primaire server.

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password

   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

In dit gedeelte van de zelfstudie worden de volgende Azure CLI-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een server die als host fungeert voor databases en elastische pools. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Hiermee maakt u de firewallregels van een server. |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) | Hiermee maakt u een failovergroep. |

---

## <a name="3---test-failover"></a>3 - Failover testen

In deze stap voert u een failover van uw failovergroep naar de secundaire server uit en voert u vervolgens een failback uit met behulp van de Azure-portal.

# <a name="the-portal"></a>[De portal](#tab/azure-portal)

Test failover met behulp van de Azure-portal.

1. Selecteer **Azure SQL** in het menu aan de linkerkant van de [Azure-portal](https://portal.azure.com). Als **Azure SQL** niet in de lijst staat, selecteert u **Alle services** en typt u Azure SQL in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en toe te voegen als item in de linkernavigatiebalk.
1. Selecteer de database die is gemaakt in sectie 2, zoals `mySampleDatbase`.
1. Selecteer de naam van de server onder **Servernaam** om de instellingen voor de server te openen.

   ![Server voor database openen](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Selecteer **Failovergroepen** onder het deelvenster **Instellingen** en kies vervolgens de failovergroep die u in sectie 2 hebt gemaakt.
  
   ![De failovergroep in de portal selecteren](./media/failover-group-add-single-database-tutorial/select-failover-group.png)

1. Controleer welke server primair en welke server secundair is.
1. Selecteer in het taakvenster **Failover** om een failover van de failovergroep met uw voorbeelddatabase uit te voeren.
1. Selecteer **Ja** bij de waarschuwing die u laat weten dat TDS-sessies worden losgekoppeld.

   ![Failover uitvoeren van de failovergroep met uw database](./media/failover-group-add-single-database-tutorial/failover-sql-db.png)

1. Controleer welke server nu primair en welke server secundair is. Als de failover is geslaagd, moeten de twee servers van rol zijn gewisseld.
1. Selecteer opnieuw **Failover** om de oorspronkelijke rollen van de servers te herstellen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Test failover met behulp van PowerShell.

Controleer de rol van de secundaire replica:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Failover naar de secundaire server:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName
   ```

Herstel de failovergroep weer naar de primaire server:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden Azure SQL Database-failovergroepen opgehaald of weergegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover uitgevoerd van een Azure SQL Database-failovergroep. |

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Test failover met behulp van de Azure CLI.

Controleer welke server de secundaire is:

   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Failover naar de secundaire server:

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Herstel de failovergroep weer naar de primaire server:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

In dit gedeelte van de zelfstudie worden de volgende Azure CLI-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group#az-sql-failover-group-list) | Geeft een lijst van de failovergroepen op een server. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Stel de primaire server van de failovergroep in door het uitvoeren van een failover voor alle databases van de huidige primaire server. |

---

## <a name="clean-up-resources"></a>Resources opschonen

Schoon resources op door de resourcegroep te verwijderen.

# <a name="the-portal"></a>[De portal](#tab/azure-portal)

Verwijder de resourcegroep met behulp van de Azure-portal.

1. Navigeer in [Azure Portal](https://portal.azure.com) naar uw resourcegroep.
1. Selecteer **Resourcegroep verwijderen** om alle resources uit de groep en de resourcegroep zelf te verwijderen.
1. Typ de naam van de resourcegroep, `myResourceGroup`, in het tekstvak en selecteer vervolgens **Verwijderen** om de resourcegroep te verwijderen.  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwijder de resourcegroep met behulp van PowerShell.

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

In dit gedeelte van de zelfstudie worden de volgende PowerShell-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep |

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Verwijder de resourcegroep met behulp van de Azure CLI.

   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

In dit gedeelte van de zelfstudie worden de volgende Azure CLI-cmdlets gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

---

> [!IMPORTANT]
> Als u de resourcegroep wilt behouden, maar de secundaire database wilt wissen, dan moet u deze uit de failovergroep verwijderen voordat u deze wist. Het wissen van een secundaire database voordat deze uit de failovergroep is verwijderd, kan onvoorspelbaar gedrag veroorzaken.

## <a name="full-scripts"></a>Volledige scripts

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add database to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Hiermee maakt u een server waar individuele databases en elastische pools worden gehost in Azure SQL Database. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Hiermee maakt u een firewallregel voor een server in Azure SQL Database. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Hiermee maakt u een nieuwe database in Azure SQL Database. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Hiermee maakt u een nieuwe failovergroep in Azure SQL Database. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hiermee haalt u een of meer databases op in Azure SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Hiermee voegt u een of meer databases toe aan een failovergroep in Azure SQL Database. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee worden failovergroepen in Azure SQL Database opgehaald of weergegeven. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Hiermee wordt een failover van een failovergroep in Azure SQL Database uitgevoerd. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee wordt een resourcegroep in Azure SQL Database verwijderd.|

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add database to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [az account set](/cli/azure/account#az-account-set) | Hiermee stelt u een abonnement in als het huidige actieve abonnement. |
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Hiermee maakt u een server waar individuele databases en elastische pools worden gehost in Azure SQL Database. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Hiermee worden de IP-firewallregels op serverniveau in Azure SQL Database gemaakt. |
| [az sql db create](/cli/azure/sql/db) | Hiermee maakt u een database in Azure SQL Database. |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) | Hiermee maakt u een failovergroep in Azure SQL Database. |
| [az sql failover-group list](/cli/azure/sql/failover-group#az-sql-failover-group-list) | Hiermee worden de failovergroepen op een server in Azure SQL Database weergegeven. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Stel de primaire server van de failovergroep in door het uitvoeren van een failover voor alle databases van de huidige primaire server. |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

# <a name="the-portal"></a>[De portal](#tab/azure-portal)

Er zijn geen scripts beschikbaar voor de Azure-portal.

---

U kunt hier andere Azure SQL Database-scripts vinden: [Azure PowerShell](powershell-script-content-guide.md) en [Azure CLI](az-cli-script-samples-content-guide.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een database in Azure SQL Database aan een failovergroep toegevoegd en de failover getest. U hebt geleerd hoe u:

> [!div class="checklist"]
>
> - Een database maken in Azure SQL Database
> - Een failovergroep voor de database tussen twee servers maken.
> - Failover testen.

Ga verder met de volgende zelfstudie over het toevoegen van een elastische pool aan een failovergroep.

> [!div class="nextstepaction"]
> [Zelfstudie: Een elastische pool van Azure SQL Database toevoegen aan een failovergroep](failover-group-add-elastic-pool-tutorial.md)