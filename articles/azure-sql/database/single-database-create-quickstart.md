---
title: Een individuele database maken
description: Maak één database in Azure SQL Database met de Azure-portal, PowerShell of Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/03/2020
ms.openlocfilehash: cb7a441a6d86cd64847300d1e597cf6f86067282
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740943"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Quickstart: Een individuele database maken in Azure SQL Database

In deze quickstart maakt u [één database](single-database-overview.md) in Azure SQL Database met behulp van Azure Portal, een PowerShell-script of een Azure CLI-script. Voer daarna in de Azure Portal een query voor de database uit met de **Query-editor**.



## <a name="prerequisite"></a>Vereiste

- Een actief Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account maken](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Een individuele database maken

In deze quickstart wordt één database gemaakt in de [laag voor serverloze computing](serverless-tier-overview.md).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Als u één database wilt maken in Azure Portal begint deze quickstart op de Azure SQL-pagina.

1. Browse naar de pagina [SQL-implementatieoptie selecteren](https://portal.azure.com/#create/Microsoft.AzureSQL).
1. Laat onder **SQL-databases** de optie **Resourcetype** ingesteld op **Eén database** en selecteer **Maken**.

   ![Toevoegen aan Azure SQL](./media/single-database-create-quickstart/select-deployment.png)

1. Selecteer op het tabblad **Basisinformatie** van het formulier **SQL-database maken**, onder **Projectgegevens**, het gewenste Azure-**abonnement**.
1. Selecteer voor **Resourcegroep** de optie **Nieuwe maken**, voer *myResourceGroup* in en selecteer **OK**.
1. Voer voor **Databasenaam** de tekst *mySampleDatabase* in.
1. Selecteer voor **Server** de optie **Nieuwe maken** en vul het formulier **Nieuwe server** in met de volgende waarden:
   - **Servernaam**: Voer *mysqlserverr* in en voeg tekens toe om de naam uniek te maken. We kunnen geen exacte servernaam geven die moet worden gebruikt omdat servernamen globaal uniek moeten zijn voor alle servers in Azure en niet alleen uniek moeten zijn binnen een abonnement. Voer daarom iets in als mysqlserver12345 en de portal laat u weten of de naam beschikbaar is of niet.
   - **Aanmeldgegevens van serverbeheerder**: Voer *azureuser* in.
   - **Wachtwoord**: Voer een wachtwoord in dat voldoet aan de vereisten en voer dit opnieuw in bij **Wachtwoord bevestigen**.
   - **Locatie**: Selecteer een locatie in de vervolgkeuzelijst.

   Selecteer **OK**.

1. Laat de instelling voor **Wilt u een elastische SQL-pool gebruiken?** ongewijzigd op **Nee**.
1. Selecteer onder **Computing en opslag** de optie **Database configureren**.
1. Deze quickstart maakt gebruik van een serverloze database, dus selecteer **Serverloos** en vervolgens **Toepassen**. 

      ![Serverloze database configureren](./media/single-database-create-quickstart/configure-database.png)

1. Selecteer **Volgende: Netwerken** onderaan de pagina.

   ![Nieuwe SQL-database - tabblad Basisinformatie](./media/single-database-create-quickstart/new-sql-database-basics.png)

1. Selecteer op het tabblad **Netwerken** voor **Verbindingsmethode** de optie **Openbaar eindpunt**.
1. Stel voor **Firewallregels** de optie **Huidig IP-adres van client toevoegen** in op **Ja**. Laat de instelling voor **Toestaan dat Azure-services en -resources toegang tot deze server krijgen** ongewijzigd op **Nee**.
1. Selecteer **Volgende: Aanvullende instellingen** onderaan de pagina.

   ![Tabblad Netwerken](./media/single-database-create-quickstart/networking.png)
  

1. Selecteer op het tabblad **Aanvullende instellingen**, in de sectie **Gegevensbron**, voor **Bestaande gegevens gebruiken**, de optie **Voorbeeld**. Hiermee maakt u een AdventureWorksLT-voorbeelddatabase, zodat er een aantal tabellen en gegevens zijn voor het uitvoeren van query's en het experimenteren hiermee, in tegenstelling tot een lege database.
1. Selecteer **Controleren en maken** onderaan de pagina:

   ![Tabblad Aanvullende instellingen](./media/single-database-create-quickstart/additional-settings.png)

1. Selecteer op de pagina **Controleren en maken** na het controleren de optie **Maken**.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com](https://shell.azure.com) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op **Enter** om de code uit te voeren.

## <a name="set-parameter-values"></a>Parameterwaarden instellen

De volgende waarden worden gebruikt in opeenvolgende opdrachten om de database en de vereiste resources te maken. Servernamen moeten globaal uniek zijn voor geheel Azure, dus wordt de functie $RANDOM gebruikt om de servernaam te maken. Vervang de 0.0.0.0-waarden in het IP-adresbereik zodat deze overeenkomt met uw specifieke omgeving.

```azurecli-interactive
# Set the resource group name and location for your server
resourceGroupName=myResourceGroup
location=eastus

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567!

# Set a server name that is unique to Azure DNS (<server_name>.database.windows.net)
serverName=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name $resourceGroupName --location $location
```

## <a name="create-a-server"></a>Een server maken

Maak een server met de opdracht [az sql server create](/cli/azure/sql/server).

```azurecli-interactive
az sql server create \
    --name $serverName \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password
```


## <a name="configure-a-firewall-rule-for-the-server"></a>Een firewallregel voor de server configureren

Maak een firewallregel met de opdracht [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule).

```azurecli-interactive
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $serverName \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip
```


## <a name="create-a-single-database-with-azure-cli"></a>Een individuele database maken met Azure CLI

Maak een database met de opdracht [az sql db create](/cli/azure/sql/db). Met de volgende code wordt gemaakt


```azurecli-interactive
az sql db create \
    --resource-group $resourceGroupName \
    --server $serverName \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --compute-model Serverless \
    --family Gen5 \
    --capacity 2
```


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt een resourcegroep, server en afzonderlijke database maken met behulp van Windows PowerShell.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com](https://shell.azure.com) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op **Enter** om de code uit te voeren.

## <a name="set-parameter-values"></a>Parameterwaarden instellen

De volgende waarden worden gebruikt in opeenvolgende opdrachten om de database en de vereiste resources te maken. Servernamen moeten globaal uniek zijn voor geheel Azure, dus wordt de cmdlet Get-Random gebruikt om de servernaam te maken. Vervang de 0.0.0.0-waarden in het IP-adresbereik zodat deze overeenkomt met uw specifieke omgeving.

```azurepowershell-interactive
   # Set variables for your server and database
   $resourceGroupName = "myResourceGroup"
   $location = "eastus"
   $adminLogin = "azureuser"
   $password = "Azure1234567!"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Server name is" $serverName
```


## <a name="create-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

```azurepowershell-interactive
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
```


## <a name="create-a-server"></a>Een server maken

Maak een server met de cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver).

```azurepowershell-interactive
  Write-host "Creating primary server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server
```

## <a name="create-a-firewall-rule"></a>Een firewallregel maken

Maak een serverfirewallregel met de cmdlet [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

```azurepowershell-interactive
   Write-host "Configuring server firewall rule..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule
```


## <a name="create-a-single-database-with-powershell"></a>Een individuele database maken met PowerShell

Maak één database met de cmdlet [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).

```azurepowershell-interactive
   Write-host "Creating a gen5 2 vCore serverless database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeModel Serverless `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
```

---



## <a name="query-the-database"></a>Een query uitvoeren op de database

Wanneer de database is gemaakt, kunt u de **Query-editor(preview)** in Azure Portal gebruiken om verbinding te maken met de database en query's uit te voeren op de gegevens.

1. In de portal zoekt en selecteert u **SQL-databases** en selecteert u vervolgens uw database in de lijst.
1. Selecteer op de pagina voor uw database in het linkermenu de optie **Query-editor (preview)** .
1. Voer uw aanmeldgegevens als serverbeheerder in en selecteer **OK**.

   ![Aanmelden bij query-editor](./media/single-database-create-quickstart/query-editor-login.png)

1. Voer de volgende query in het deelvenster **Query-editor** in.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Klik op **Uitvoeren** en bekijk de resultaten van de query in het deelvenster **Resultaten**.

   ![Resultaten query-editor](./media/single-database-create-quickstart/query-editor-results.png)

1. Sluit de pagina **Query-editor** en selecteer **OK** wanneer u wordt gevraagd om uw niet-opgeslagen bewerkingen te verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Bewaar de resourcegroep, server en individuele database als u verder wilt gaan met de volgende stappen en wilt leren hoe u op verschillende manieren verbinding kunt maken met uw database en query's kunt uitvoeren.

Wanneer u klaar bent met deze resources, kunt u de resourcegroep die u hebt gemaakt, verwijderen. U verwijdert daarmee ook de server en individuele database in die groep.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Zo verwijdert u **myResourceGroup** en alle bijbehorende resources met behulp van de Azure Portal:

1. Selecteer in de Azure Portal **Resourcegroepen** en selecteer **myResourceGroup** in de lijst.
1. Selecteer **Resourcegroep verwijderen** op de pagina van de resourcegroep.
1. Typ *myResourceGroup* onder **Typ de naam van de resourcegroep** en selecteer **Verwijderen**.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de resourcegroep inclusief alle resources wilt verwijderen, voert u de volgende Azure CLI-opdracht uit met de naam van uw resourcegroep:

```azurecli-interactive
az group delete --name $resourceGroupName
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de resourcegroep inclusief alle resources wilt verwijderen, voert u de PowerShell-cmdlet uit met de naam van de resourcegroep:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## <a name="next-steps"></a>Volgende stappen

Verschillende hulpprogramma's en talen gebruiken om [verbinding te maken met en query's uit te voeren voor](connect-query-content-reference-guide.md) de database:
> [!div class="nextstepaction"]
> [Verbinding maken en query's uitvoeren met behulp van SQL Server Management Studio](connect-query-ssms.md)
>
> [Verbinding maken en query's uitvoeren met behulp van Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)

Wilt u optimaliseren en op uw cloudverbruik besparen?

> [!div class="nextstepaction"]
> [Analyseer kosten met Cost Management](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)