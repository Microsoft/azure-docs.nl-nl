---
title: Servers & individuele databases beheren
description: Meer informatie over het maken en beheren van servers en individuele databases in Azure SQL Database met behulp van de Azure Portal, PowerShell, de Azure CLI, Transact-SQL (T-SQL) en rest-API.
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
ms.openlocfilehash: 4aaabdb3d21c41b973b21e6e52442be132796196
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781591"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Servers en individuele databases maken en beheren in Azure SQL Database

U kunt servers en individuele databases in Azure SQL Database maken en beheren met behulp van de Azure Portal, PowerShell, de Azure CLI, REST API en Transact-SQL.

## <a name="the-azure-portal"></a>Azure Portal

U kunt de resourcegroep voor Azure SQL Database maken of tijdens het maken van de server zelf.

### <a name="create-a-server"></a>Een server maken

Als u een server wilt maken met [behulp Azure Portal](https://portal.azure.com), maakt u een nieuwe [serverresource](logical-servers.md) op basis van Azure Marketplace. U kunt de server ook maken wanneer u een Azure SQL Database.

  ![server maken](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Een lege of voorbeelddatabase maken

Als u één resource wilt Azure SQL Database met behulp van [de Azure Portal,](https://portal.azure.com)kiest u Azure SQL Database resource in Azure Marketplace. U kunt de resourcegroep en server van tevoren of tijdens het maken van de individuele database zelf maken. U kunt een lege database maken of een voorbeelddatabase maken op basis van Adventure Works LT.

  ![database-1 maken](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> Zie Aankoopmodel op basis van [DTU](service-tiers-dtu.md) en aankoopmodel op basis van vCore voor informatie over het selecteren van de [prijscategorie voor uw database.](service-tiers-vcore.md)

## <a name="manage-an-existing-server"></a>Een bestaande server beheren

Als u een bestaande server wilt beheren, navigeert u naar de server met behulp van een aantal methoden, zoals vanaf een specifieke databasepagina, de **pagina SQL-servers** of de pagina **Alle resources.**

Als u een bestaande database wilt beheren, gaat u naar de **pagina SQL-databases** en selecteert u de database die u wilt beheren. In de volgende schermopname ziet u hoe u een firewall op serverniveau kunt instellen voor een database vanaf de **pagina** Overzicht voor een database.

   ![serverfirewallregel](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Zie Aankoopmodel op basis van [DTU](service-tiers-dtu.md) en aankoopmodel op basis van vCore om prestatie-eigenschappen voor een database [te configureren.](service-tiers-vcore.md)
> [!TIP]
> Zie Een Azure Portal maken in de SQL Database in de Azure Portal voor een [Azure Portal.](single-database-create-quickstart.md)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De module PowerShell Azure Resource Manager wordt nog steeds ondersteund in Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

Gebruik de volgende PowerShell-cmdlets om servers, individuele en pooldatabases en firewalls op serverniveau met Azure PowerShell maken en beheren. Zie Install Azure PowerShell module (PowerShell installeren of upgraden) [als u PowerShell wilt installeren Azure PowerShell upgraden.](/powershell/azure/install-az-ps)

> [!TIP]
> Zie PowerShell gebruiken om een database te maken in SQL Database en een firewallregel op [serverniveau](scripts/create-and-configure-database-powershell.md) te configureren en Een database in SQL Database bewaken en schalen voor [PowerShell voor powershell-voorbeeldscripts.](scripts/monitor-and-scale-database-powershell.md)

| Cmdlet | Beschrijving |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Hiermee maakt u een database |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Haalt een of meer databases op|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Hiermee stelt u eigenschappen in voor een database of verplaatst u een bestaande database naar een elastische pool|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Hiermee verwijdert u een database|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Hiermee maakt u een resourcegroep|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Hiermee maakt u een server|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Retourneert informatie over servers|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Wijzigt eigenschappen van een server|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Hiermee verwijdert u een server|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Hiermee maakt u een firewallregel op serverniveau |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Haalt firewallregels op voor een server|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Wijzigt een firewallregel in een server|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Hiermee verwijdert u een firewallregel van een server.|
| New-AzSqlServerVirtualNetworkRule | Hiermee maakt [*u een regel voor een virtueel*](vnet-service-endpoint-rule-overview.md)netwerk op basis van een subnet dat een Virtual Network service-eindpunt is. |

## <a name="the-azure-cli"></a>De Azure CLI

Gebruik de volgende Azure CLI-opdrachten om de servers, databases en firewalls te maken en beheren met [de Azure CLI.](/cli/azure/sql/db) [](/cli/azure) Gebruik de [Cloud Shell](../../cloud-shell/overview.md) om de CLI in uw browser uit te voeren of [installeer](/cli/azure/install-azure-cli) de CLI op macOS, Linux of Windows. Zie Elastische pools voor het maken en beheren [van elastische pools.](elastic-pool-overview.md)

> [!TIP]
> Zie Create a single Azure SQL Database using the Azure CLI (Een enkele app maken met behulp van de Azure CLI) voor een [Azure CLI-snelstart.](az-cli-script-samples-content-guide.md) Zie CLI gebruiken om een database in Azure SQL Database te maken en een [SQL Database-firewallregel](scripts/create-and-configure-database-cli.md) te configureren en CLI gebruiken om een database in Azure SQL Database voor voorbeeldscripts [te Azure SQL Database.](scripts/monitor-and-scale-database-cli.md)
>

| Cmdlet | Beschrijving |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Hiermee maakt u een database|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|Een lijst met alle databases en datawarehouses op een server of alle databases in een elastische pool|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|Een lijst met beschikbare servicedoelstellingen en opslaglimieten|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|Retourneert databasegebruik|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|Haalt een database of datawarehouse op|
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Een database wordt bijgewerkt|
|[az sql db delete](/cli/azure/sql/db#az_sql_db_delete)|Hiermee verwijdert u een database|
|[az group create](/cli/azure/group#az_group_create)|Hiermee maakt u een resourcegroep|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Hiermee maakt u een server|
|[az sql server list](/cli/azure/sql/server#az_sql_server_list)|Een lijst met servers|
|[az sql server list-usages](/cli/azure/sql/server#az_sql_server_list-usages)|Retourneert servergebruik|
|[az sql server show](/cli/azure/sql/server#az_sql_server_show)|Haalt een server op|
|[az sql server update](/cli/azure/sql/server#az_sql_server_update)|Een server wordt bijgewerkt|
|[az sql server delete](/cli/azure/sql/server#az_sql_server_delete)|Hiermee verwijdert u een server|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Hiermee maakt u een serverfirewallregel|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Een lijst met de firewallregels op een server|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Toont de details van een firewallregel|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Een firewallregel wordt bijgewerkt|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Hiermee verwijdert u een firewallregel|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Als u de servers, databases en firewalls wilt maken en beheren met Transact-SQL, gebruikt u de volgende T-SQL-opdrachten. U kunt deze opdrachten uitvoeren met behulp van Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code of](https://code.visualstudio.com/docs)een ander programma dat verbinding kan maken met een server in SQL Database en Transact-SQL-opdrachten kan doorgeven. Zie Elastische pools voor het beheren [van elastische pools.](elastic-pool-overview.md)

> [!TIP]
> Zie [Azure SQL Database:](connect-query-ssms.md)SQL Server Management Studio verbinding maken en query's uitvoeren voor een quickstart met behulp van SQL Server Management Studio in Microsoft Windows. Zie [Azure SQL Database:](connect-query-vscode.md)Visual Studio Code gebruiken om verbinding te maken en gegevens op te vragen voor een quickstart met behulp van Visual Studio Code in macOS, Linux of Windows.
> [!IMPORTANT]
> U kunt geen server maken of verwijderen met Transact-SQL.

| Opdracht | Beschrijving |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)|Hiermee maakt u een nieuwe individuele database. U moet zijn verbonden met de hoofddatabase om een nieuwe database te kunnen maken.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Wijzigt een database of elastische pool. |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|Hiermee verwijdert u een database.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retourneert de editie (servicelaag), servicedoelstelling (prijscategorie) en de naam van de elastische pool, indien van Azure SQL Database of een toegewezen SQL-pool in Azure Synapse Analytics. Als u bent aangemeld bij de hoofddatabase op een server in SQL Database, retourneert informatie over alle databases. Voor Azure Synapse Analytics moet u zijn verbonden met de hoofddatabase.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Retourneert HET CPU-, I/O- en geheugenverbruik voor een database in Azure SQL Database. Er bestaat één rij om de 15 seconden, zelfs als er geen activiteit in de database is.|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Retourneert CPU-gebruik en opslaggegevens voor een database in Azure SQL Database. De gegevens worden verzameld en geaggregeerd binnen een interval van vijf minuten.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Bevat statistieken voor SQL Database connectiviteitsgebeurtenissen, zodat u een overzicht krijgt van het slagen en mislukken van databaseverbindingen. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Retourneert geslaagde Azure SQL Database, verbindingsfouten en impasses. U kunt deze informatie gebruiken om uw databaseactiviteit bij te houden of problemen op te lossen met SQL Database.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Hiermee maakt of werkt u de firewallinstellingen op serverniveau voor uw server bij. Deze opgeslagen procedure is alleen beschikbaar in de hoofddatabase voor de principal-aanmelding op serverniveau. Een firewallregel op serverniveau kan alleen worden gemaakt met behulp van Transact-SQL nadat de eerste firewallregel op serverniveau is gemaakt door een gebruiker met machtigingen op Azure-niveau|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Retourneert informatie over de firewallinstellingen op serverniveau die zijn gekoppeld aan uw database in Azure SQL Database.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Hiermee verwijdert u firewallinstellingen op serverniveau van uw server. Deze opgeslagen procedure is alleen beschikbaar in de hoofddatabase voor de principal-aanmelding op serverniveau.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Hiermee maakt of werkt u de firewallregels op databaseniveau voor uw database in Azure SQL Database. Databasefirewallregels kunnen worden geconfigureerd voor de hoofddatabase en voor gebruikersdatabases op SQL Database. Databasefirewallregels zijn handig bij het gebruik van ingesloten databasegebruikers. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Retourneert informatie over de firewallinstellingen op databaseniveau die zijn gekoppeld aan uw database in Azure SQL Database. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Hiermee verwijdert u de firewallinstelling op databaseniveau uit een database. |

## <a name="rest-api"></a>REST-API

Als u de servers, databases en firewalls wilt maken en beheren, gebruikt u deze REST API aanvragen.

| Opdracht | Beschrijving |
| --- | --- |
|[Servers : maken of bijwerken](/rest/api/sql/servers/createorupdate)|Hiermee maakt of werkt u een nieuwe server bij.|
|[Servers - Verwijderen](/rest/api/sql/servers/delete)|Hiermee verwijdert u een SQL-server.|
|[Servers - Get](/rest/api/sql/servers/get)|Haalt een server op.|
|[Servers - Lijst](/rest/api/sql/servers/list)|Retourneert een lijst met servers in een abonnement.|
|[Servers - Lijst per resourcegroep](/rest/api/sql/servers/listbyresourcegroup)|Retourneert een lijst met servers in een resourcegroep.|
|[Servers - Bijwerken](/rest/api/sql/servers/update)|Werkt een bestaande server bij.|
|[Databases : maken of bijwerken](/rest/api/sql/databases/createorupdate)|Hiermee maakt u een nieuwe database of werkt u een bestaande database bij.|
|[Databases - Verwijderen](/rest/api/sql/databases/delete)|Hiermee verwijdert u een database.|
|[Databases - Get](/rest/api/sql/databases/get)|Haalt een database op.|
|[Databases - Lijst per elastische pool](/rest/api/sql/databases/listbyelasticpool)|Retourneert een lijst met databases in een elastische pool.|
|[Databases - Lijst per server](/rest/api/sql/databases/listbyserver)|Retourneert een lijst met databases op een server.|
|[Databases - Bijwerken](/rest/api/sql/databases/update)|Werkt een bestaande database bij.|
|[Firewallregels: maken of bijwerken](/rest/api/sql/firewallrules/createorupdate)|Hiermee maakt of werkt u een firewallregel bij.|
|[Firewallregels - Verwijderen](/rest/api/sql/firewallrules/delete)|Hiermee verwijdert u een firewallregel.|
|[Firewallregels - Get](/rest/api/sql/firewallrules/get)|Haalt een firewallregel op.|
|[Firewallregels - Lijst per server](/rest/api/sql/firewallrules/listbyserver)|Retourneert een lijst met firewallregels.|

## <a name="next-steps"></a>Volgende stappen

- Zie Migreren naar Azure SQL Database voor meer informatie over het SQL Server van een database [naar Azure.](migrate-to-database-from-sql-server.md)
- Zie Functies voor meer informatie over [ondersteunde functies.](features-comparison.md)
