---
title: 'Quickstart: Een toegewezen SQL-pool (voorheen SQL DW) maken met Azure PowerShell'
description: Met een firewallregel op serverniveau en Azure PowerShell kunt u snel een toegewezen SQL-pool (voorheen SQL DW) maken.
services: synapse-analytics
author: XiaoyuMSFT
ms.author: xiaoyul
manager: craigg
ms.reviewer: igorstan
ms.date: 4/11/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 65bf509c8eb654a9f7712fdf7b94ff7fa26a3d32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537458"
---
# <a name="quickstart-create-a-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Quickstart: Een toegewezen SQL-pool (voorheen SQL DW) maken met Azure PowerShell

Maak een toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics met behulp van Azure PowerShell.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

> [!IMPORTANT]
> Het maken van een toegewezen SQL-pool (voorheen SQL DW) kan resulteren in een nieuwe factureerbare service.  Zie [Prijzen voor Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) voor meer informatie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met behulp van de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

Voer [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) uit om te zien welk abonnement u gebruikt.

```powershell
Get-AzSubscription
```

Als u een ander abonnement dan het standaardabonnement wilt gebruiken, voert u [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) uit.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="create-variables"></a>Variabelen maken

In deze snelstart leert u om variabelen te definiëren voor gebruik in de scripts.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Een resourcegroep is een container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt op de locatie `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>Een server maken

Maak een [logische SQL-server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) met behulp van de [opdracht New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Een server bevat een groep met databases die worden beheerd als groep. In het volgende voorbeeld wordt een server met een willekeurige naam gemaakt in de resourcegroep met een gebruiker met beheerdersrechten met de naam `ServerAdmin` en het wachtwoord `ChangeYourAdminPassword1`. U kunt deze vooraf gedefinieerde waarden vervangen.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Een serverfirewallregel configureren

Maak een [firewallregel op serverniveau](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) met de opdracht [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Een firewallregel op serverniveau kan een externe toepassing, zoals SQL Server Management Studio of het hulpprogramma SQLCMD, via de firewall van de toegewezen SQL-poolservice verbinding laten maken met een toegewezen SQL-pool (voorheen SQL DW).

In het volgende voorbeeld wordt de firewall alleen geopend voor andere Azure-resources. Voor externe connectiviteit wijzigt u het IP-adres in een correct adres voor uw omgeving. Als u alle IP-adressen wilt openen, gebruikt u 0.0.0.0 als beginadres en 255.255.255.255 als eindadres.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL-eindpunten communiceren via poort 1433. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 1433 mogelijk niet toegestaan door de firewall van uw netwerk. In dat geval kunt u alleen verbinding maken met uw server als uw IT-afdeling poort 1433 openstelt.
>

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Een toegewezen SQL-pool (voorheen SQL DW) maken

In het volgende voorbeeld wordt een toegewezen SQL-pool gemaakt met behulp van de eerder gedefinieerde variabelen.  De opgegeven servicedoelstelling is DW100c. Dit is een goedkoper startpunt voor uw toegewezen SQL-pool (voorheen SQL DW).

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

De vereiste parameters zijn:

* **RequestedServiceObjectiveName**: De hoeveelheid [datawarehouse-eenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md) die u aanvraagt. Als u deze waarde verhoogt, verhogen ook de rekenkosten. Zie [Geheugen- en gelijktijdigheidslimieten](memory-concurrency-limits.md) voor een lijst met ondersteunde waarden.
* **DatabaseName**: De naam van de toegewezen SQL-pool (voorheen SQL DW) die u maakt.
* **ServerName**: De naam van de server die u bij het maken gebruikt.
* **ResourceGroupName**: De resourcegroep die u gebruikt. Gebruik Get-AzureResource om beschikbare resourcegroepen in uw abonnement te zoeken.
* **Edition**: Moet 'DataWarehouse' zijn om een toegewezen SQL-pool (voorheen SQL DW) te kunnen maken.

De optionele parameters zijn:

* **CollationName**: Als hier niets wordt opgegeven, wordt de standaardsortering SQL_Latin1_General_CP1_CI_AS gebruikt. De sortering kan niet worden gewijzigd voor een database.
* **MaxSizeBytes**: De maximale grootte van een database is standaard 240 TB. De maximale grootte beperkt de rowstore-gegevens. Er is onbeperkte opslag voor kolomgegevens.

Zie [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) voor meer informatie over de parameteropties.

## <a name="clean-up-resources"></a>Resources opschonen

Andere zelfstudies in deze verzameling zijn gebaseerd op deze snelstart.

> [!TIP]
> Verwijder de resources die u in deze quickstart hebt gemaakt niet als u verder wilt gaan met volgende quickstart-zelfstudies. Als u niet van plan bent om verder te gaan, gebruikt u de volgende stappen om alle resources te verwijderen die tijdens deze quickstart in de Azure Portal zijn gemaakt.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Volgende stappen

U hebt nu een toegewezen SQL-pool (voorheen SQL DW) gemaakt, een firewallregel gemaakt en deze verbonden met uw toegewezen SQL-pool. Ga voor meer informatie naar het artikel [Gegevens in een toegewezen SQL-pool laden](./load-data-from-azure-blob-storage-using-copy.md).
