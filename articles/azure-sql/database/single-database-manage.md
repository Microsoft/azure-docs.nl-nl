---
title: Servers en individuele data bases maken & beheren
description: Meer informatie over het maken en beheren van servers en individuele data bases in Azure SQL Database met behulp van de Azure Portal, Power shell, de Azure CLI, Transact-SQL (T-SQL) en rest-API.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: e50cce74f7291a6673e5d43f3485a1c63c81d827
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93319274"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Servers en individuele data bases maken en beheren in Azure SQL Database

U kunt servers en individuele data bases maken en beheren in Azure SQL Database met behulp van de Azure Portal, Power shell, de Azure CLI, REST API en Transact-SQL.

## <a name="the-azure-portal"></a>Azure Portal

U kunt de resource groep voor Azure SQL Database van tevoren of tijdens het maken van de server maken.

### <a name="create-a-server"></a>Een server maken

Als u een server wilt maken met behulp van de [Azure Portal](https://portal.azure.com), maakt u een nieuwe [Server](logical-servers.md) resource vanuit Azure Marketplace. U kunt de server ook maken wanneer u een Azure SQL Database implementeert.

  ![server maken](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Een lege of voorbeeld database maken

Als u één Azure SQL Database wilt maken met behulp van de [Azure Portal](https://portal.azure.com), kiest u de Azure SQL database resource in azure Marketplace. U kunt de resource groep en de server van tevoren maken of tijdens het maken van de afzonderlijke data base zelf. U kunt een lege data base maken of een voorbeeld database maken op basis van Adventure Works LT.

  ![database-1 maken](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> Zie voor meer informatie over het selecteren van de prijs categorie voor uw Data Base op [DTU gebaseerd inkoop model](service-tiers-dtu.md) en [op vCore gebaseerd aankoop model](service-tiers-vcore.md).

## <a name="manage-an-existing-server"></a>Een bestaande server beheren

Als u een bestaande server wilt beheren, gaat u naar de-server met behulp van een aantal methoden, bijvoorbeeld vanaf een specifieke database pagina, de pagina **SQL-servers** of de pagina **alle resources** .

Als u een bestaande Data Base wilt beheren, gaat u naar de pagina **SQL-data bases** en selecteert u de data base die u wilt beheren. In de volgende scherm afbeelding ziet u hoe u een firewall op server niveau instelt voor een Data Base op de pagina **overzicht** voor een Data Base.

   ![serverfirewallregel](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Zie op [DTU gebaseerd inkoop model](service-tiers-dtu.md) en op [vCore gebaseerd inkoop model](service-tiers-vcore.md)voor informatie over het configureren van prestatie-eigenschappen voor een Data Base.
> [!TIP]
> Zie [een Data Base maken in SQL database in de Azure Portal](single-database-create-quickstart.md)voor een Azure Portal Snelstartgids.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De module PowerShell Azure Resource Manager wordt nog steeds ondersteund in Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

Gebruik de volgende Power shell-cmdlets om servers, afzonderlijke en gegroepeerde Data bases en firewalls op server niveau met Azure PowerShell te maken en te beheren. Als u Power shell wilt installeren of upgraden, raadpleegt u [Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

> [!TIP]
> Zie Power [shell gebruiken om een Data Base te maken in SQL database en een firewall regel op server niveau te configureren](scripts/create-and-configure-database-powershell.md) en een [data base in SQL database te controleren en te schalen met behulp van Power](scripts/monitor-and-scale-database-powershell.md)shell voor voorbeeld scripts voor Power shell.

| Cmdlet | Beschrijving |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Hiermee maakt u een Data Base |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hiermee worden een of meer data bases opgehaald|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Eigenschappen instellen voor een Data Base of een bestaande data base verplaatsen naar een elastische pool|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Hiermee verwijdert u een Data Base|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Hiermee maakt u een resource groep|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Hiermee maakt u een server|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Retourneert informatie over servers|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Hiermee wijzigt u de eigenschappen van een server|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Hiermee wordt een server verwijderd|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Hiermee maakt u een firewall regel op server niveau |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Hiermee worden de firewall regels voor een server opgehaald|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Hiermee wijzigt u een firewall regel in een server|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Hiermee verwijdert u een firewall regel van een server.|
| New-AzSqlServerVirtualNetworkRule | Hiermee maakt u een regel voor het [*virtuele netwerk*](vnet-service-endpoint-rule-overview.md), op basis van een subnet dat Virtual Network Service-eind punt is. |

## <a name="the-azure-cli"></a>De Azure CLI

Gebruik de volgende [Azure cli](/cli/azure/sql/db) -opdrachten om de servers, data bases en firewalls te maken en te beheren met [de Azure cli](/cli/azure). Gebruik de [Cloud Shell](../../cloud-shell/overview.md) om de CLI in uw browser uit te voeren of [installeer](/cli/azure/install-azure-cli) de CLI op macOS, Linux of Windows. Zie [elastische Pools](elastic-pool-overview.md)voor het maken en beheren van elastische Pools.

> [!TIP]
> Zie [een enkele Azure SQL database maken met behulp van de Azure cli](az-cli-script-samples-content-guide.md)voor een Snelstartgids van Azure cli. Zie voor voor beelden van Azure CLI-scripts [CLI gebruiken om een Data Base te maken in Azure SQL database en een SQL database firewall regel configureren](scripts/create-and-configure-database-cli.md) en [CLI gebruiken om een data base in Azure SQL database te controleren en te schalen](scripts/monitor-and-scale-database-cli.md).
>

| Cmdlet | Beschrijving |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Hiermee maakt u een Data Base|
|[AZ SQL DB List](/cli/azure/sql/db#az-sql-db-list)|Een lijst met alle data bases en data warehouses in een server, of alle data bases in een elastische pool|
|[AZ SQL DB List-edities](/cli/azure/sql/db#az-sql-db-list-editions)|Een lijst met beschik bare service doelstellingen en opslag limieten|
|[AZ SQL DB List-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Retourneert database gebruik|
|[AZ SQL DB show](/cli/azure/sql/db#az-sql-db-show)|Hiermee wordt een Data Base of Data Warehouse opgehaald|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Hiermee wordt een Data Base bijgewerkt|
|[AZ SQL DB Delete](/cli/azure/sql/db#az-sql-db-delete)|Hiermee verwijdert u een Data Base|
|[az group create](/cli/azure/group#az-group-create)|Hiermee maakt u een resource groep|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Hiermee maakt u een server|
|[AZ SQL Server List](/cli/azure/sql/server#az-sql-server-list)|Servers weer geven|
|[AZ SQL Server List-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Retourneert server gebruik|
|[AZ SQL Server show](/cli/azure/sql/server#az-sql-server-show)|Hiermee wordt een server opgehaald|
|[AZ SQL Server Update](/cli/azure/sql/server#az-sql-server-update)|Hiermee wordt een server bijgewerkt|
|[AZ SQL Server Delete](/cli/azure/sql/server#az-sql-server-delete)|Hiermee wordt een server verwijderd|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Hiermee maakt u een server firewall regel|
|[AZ SQL Server firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Een lijst met firewall regels op een server|
|[AZ SQL Server firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Toont de details van een firewall regel|
|[AZ SQL Server firewall-Rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Hiermee wordt een firewall regel bijgewerkt|
|[AZ SQL Server firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Hiermee verwijdert u een firewall regel|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Gebruik de volgende T-SQL-opdrachten om de servers, data bases en firewalls te maken en te beheren met Transact-SQL. U kunt deze opdrachten geven met behulp van de Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio code](https://code.visualstudio.com/docs)of een ander programma dat verbinding kan maken met een server in SQL database en Transact-SQL-opdrachten kan passeren. Zie [elastische Pools](elastic-pool-overview.md)voor het beheren van elastische Pools.

> [!TIP]
> Voor een Snelstartgids met SQL Server Management Studio in micro soft Windows raadpleegt u [Azure SQL database: gebruik SQL Server Management Studio om verbinding te maken en gegevens op te vragen](connect-query-ssms.md). Zie [Azure SQL database: Visual Studio code gebruiken om verbinding te maken en gegevens op te vragen](connect-query-vscode.md)voor een Snelstartgids met Visual Studio code op het MacOS, Linux of Windows.
> [!IMPORTANT]
> U kunt geen server maken of verwijderen met behulp van Transact-SQL.

| Opdracht | Beschrijving |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)|Hiermee maakt u een nieuwe afzonderlijke data base. U moet zijn verbonden met de hoofd database om een nieuwe Data Base te kunnen maken.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Hiermee wijzigt u een Data Base of elastische pool. |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|Hiermee verwijdert u een Data Base.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retourneert de editie (service tier), de service doelstelling (prijs categorie) en de naam van de elastische groep, indien van toepassing, voor Azure SQL Database of een specifieke SQL-groep in azure Synapse Analytics. Als u bent aangemeld bij de hoofd database op een server in SQL Database, wordt er informatie weer gegeven over alle data bases. Voor Azure Synapse Analytics moet u verbinding hebben met de hoofd database.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Retourneert CPU, i/o en geheugen verbruik voor een data base in Azure SQL Database. Er bestaat één rij voor elke 15 seconden, zelfs als er geen activiteit in de data base is.|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Retourneert het CPU-gebruik en de opslag gegevens voor een data base in Azure SQL Database. De gegevens worden binnen een interval van vijf minuten verzameld en geaggregeerd.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Bevat statistieken voor SQL Database connectiviteits gebeurtenissen, met een overzicht van geslaagde en mislukte database verbindingen. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Hiermee worden geslaagde Azure SQL Database verbindingen, verbindings fouten en deadlocks geretourneerd. U kunt deze informatie gebruiken om uw database activiteit bij SQL Database bij te houden of problemen op te lossen.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Hiermee worden de firewall instellingen op server niveau voor uw server gemaakt of bijgewerkt. Deze opgeslagen procedure is alleen beschikbaar in de hoofd database voor de principal-aanmelding op server niveau. Een firewall regel op server niveau kan alleen worden gemaakt met behulp van Transact-SQL nadat de eerste firewall regel op server niveau is gemaakt door een gebruiker met machtigingen op Azure-niveau|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Retourneert informatie over de firewall instellingen op server niveau die zijn gekoppeld aan uw data base in Azure SQL Database.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Hiermee verwijdert u Firewall instellingen op server niveau van uw server. Deze opgeslagen procedure is alleen beschikbaar in de hoofd database voor de principal-aanmelding op server niveau.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Hiermee worden de firewall regels op database niveau voor uw data base in Azure SQL Database gemaakt of bijgewerkt. De firewall regels voor de data base kunnen worden geconfigureerd voor de hoofd database en voor gebruikers databases op SQL Database. Database firewall regels zijn handig wanneer u Inge sloten database gebruikers gebruikt. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Retourneert informatie over de firewall instellingen op database niveau die zijn gekoppeld aan uw data base in Azure SQL Database. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Hiermee verwijdert u de firewall-instelling op database niveau uit een Data Base. |

## <a name="rest-api"></a>REST-API

Gebruik deze REST API aanvragen om de servers, data bases en firewalls te maken en te beheren.

| Opdracht | Beschrijving |
| --- | --- |
|[Servers: maken of bijwerken](/rest/api/sql/servers/createorupdate)|Hiermee wordt een nieuwe server gemaakt of bijgewerkt.|
|[Servers-verwijderen](/rest/api/sql/servers/delete)|Hiermee verwijdert u een SQL-Server.|
|[Servers: ophalen](/rest/api/sql/servers/get)|Hiermee wordt een server opgehaald.|
|[Servers-lijst](/rest/api/sql/servers/list)|Retourneert een lijst met servers in een abonnement.|
|[Servers-lijst op resource groep](/rest/api/sql/servers/listbyresourcegroup)|Retourneert een lijst met servers in een resource groep.|
|[Servers-bijwerken](/rest/api/sql/servers/update)|Hiermee wordt een bestaande server bijgewerkt.|
|[Data bases: maken of bijwerken](/rest/api/sql/databases/createorupdate)|Hiermee maakt u een nieuwe data base of werkt u een bestaande data base bij.|
|[Data bases-verwijderen](/rest/api/sql/databases/delete)|Hiermee verwijdert u een Data Base.|
|[Data bases-ophalen](/rest/api/sql/databases/get)|Hiermee haalt u een Data Base.|
|[Data bases-lijst op elastische pool](/rest/api/sql/databases/listbyelasticpool)|Retourneert een lijst met data bases in een elastische pool.|
|[Data bases-lijst per server](/rest/api/sql/databases/listbyserver)|Retourneert een lijst met data bases op een server.|
|[Data bases-bijwerken](/rest/api/sql/databases/update)|Hiermee wordt een bestaande data base bijgewerkt.|
|[Firewall regels: maken of bijwerken](/rest/api/sql/firewallrules/createorupdate)|Hiermee wordt een firewall regel gemaakt of bijgewerkt.|
|[Firewall regels-verwijderen](/rest/api/sql/firewallrules/delete)|Hiermee verwijdert u een firewall regel.|
|[Firewall regels: ophalen](/rest/api/sql/firewallrules/get)|Hiermee wordt een firewall regel opgehaald.|
|[Firewall regels-lijst per server](/rest/api/sql/firewallrules/listbyserver)|Retourneert een lijst met firewall regels.|

## <a name="next-steps"></a>Volgende stappen

- Zie [migreren naar Azure SQL database](migrate-to-database-from-sql-server.md)voor meer informatie over het migreren van een SQL Server Data Base naar Azure.
- Zie [functies](features-comparison.md)voor meer informatie over ondersteunde functies.
 
