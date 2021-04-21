---
title: Wat is een server in Azure SQL Database en Azure Synapse Analytics?
titleSuffix: ''
description: Meer informatie over logische SQL-servers die worden gebruikt door Azure SQL Database en Azure Synapse Analytics en hoe u deze beheert.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: c76d3ae78bf2b9b4a71d9520f7f1c6c2c322483b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784511"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Wat is een logische SQL-server in Azure SQL Database en Azure Synapse?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

In Azure SQL Database en Azure Synapse Analytics is een server een logische constructie die fungeert als een centraal beheerpunt voor een verzameling databases. Op serverniveau kunt u [](logins-create-manage.md)aanmeldingen, [firewallregels,](firewall-configure.md) [controleregels,](../../azure-sql/database/auditing-overview.md) [](threat-detection-configure.md)beleidsregels voor detectie van bedreigingen en groepen voor automatische [failover beheren.](auto-failover-group-overview.md) Een server kan zich in een andere regio dan de resourcegroep ervan. De server moet bestaan voordat u een database kunt maken in Azure SQL Database of een datawarehouse-database in Azure Synapse Analytics. Alle databases die door één server worden beheerd, worden binnen dezelfde regio als de server gemaakt.

Deze server verschilt van een SQL Server-instantie die u mogelijk kent in de on-premises wereld. Er zijn met name geen garanties met betrekking tot de locatie van de databases of datawarehouse-database met betrekking tot de server die ze beheert. Bovendien zijn er geen Azure SQL Database Azure Synapse of functies op exemplaarniveau. De exemplaardatabases in een beheerd exemplaar bevinden zich daarentegen allemaal fysiek op dezelfde manier als u bekend bent met SQL Server in de wereld van on-premises of virtuele machines.

Wanneer u een server maakt, geeft u een aanmeldingsaccount en wachtwoord voor de server op met beheerdersrechten voor de hoofddatabase op die server en alle databases die op die server zijn gemaakt. Dit eerste account is een SQL-aanmeldingsaccount. Azure SQL Database en Azure Synapse Analytics ondersteuning voor SQL-verificatie en Azure Active Directory verificatie voor verificatie. Zie Databases en aanmeldingen beheren in Azure SQL Database voor meer informatie [over aanmeldingen Azure SQL Database.](logins-create-manage.md) Windows-verificatie wordt niet ondersteund.

Een server in SQL Database en Azure Synapse:

- Wordt gemaakt binnen een Azure-abonnement, maar kan met ingesloten resources naar een ander abonnement worden verplaatst
- Is de bovenliggende resource voor databases, elastische groepen en datawarehouses
- Biedt een naamruimte voor databases, elastische pools en datawarehouse-databases
- Is een logische container met sterke levensduursemantiek: een server verwijderen en de databases, elastische pools en SQK-pools verwijderen
- Neemt deel aan op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../../role-based-access-control/overview.md) - databases, elastische pools en datawarehouse-database binnen een server nemen toegangsrechten over van de server
- Is een belangrijk element van de identiteit van databases, elastische pools en datawarehouse-databases voor Azure-resourcebeheerdoeleinden (zie het URL-schema voor databases en pools)
- Groepeert resources in een regio
- Biedt een verbindingseindpunt voor databasetoegang (`<serverName>`.database.windows.net)
- Biedt toegang tot metagegevens van ingesloten resources via DMV's door verbinding te maken met een hoofddatabase
- Biedt het bereik voor beheerbeleidsregels die van toepassing zijn op de databases: aanmeldingen, firewall, controle, detectie van bedreigingen, en dergelijke
- Wordt beperkt door een quotum binnen het bovenliggende abonnement (standaard zes servers per abonnement. Zie [Abonnementslimieten hier](../../azure-resource-manager/management/azure-subscription-service-limits.md))
- Biedt het bereik voor het databasequotum en het DTU- of vCore-quotum voor de resources die het bevat (zoals 45.000 DTU's)
- Is het versiebereik voor mogelijkheden die zijn ingeschakeld voor ingesloten resources
- Hoofdaanmeldingen op serverniveau kunnen alle databases op een server beheren
- Kan aanmeldingen bevatten die vergelijkbaar zijn met die in exemplaren van SQL Server in uw on-premises omgeving die toegang krijgen tot een of meer databases op de server en beperkte beheerdersrechten kunnen krijgen. Zie [Aanmeldingen](logins-create-manage.md) voor meer informatie.
- De standaardseratie voor alle databases die op een server zijn gemaakt, is , waarbij Engels `SQL_LATIN1_GENERAL_CP1_CI_AS` `LATIN1_GENERAL` (Verenigde Staten) `CP1` is, codepagina 1252 is, hoofd- en hoofd- en `CI` `AS` accentgevoelig is.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Servers, databases en firewalls beheren met behulp van de Azure Portal

U kunt de resourcegroep voor een server van tevoren of tijdens het maken van de server zelf maken. Er zijn meerdere methoden om naar een nieuw SQL Server-formulier te gaan, door een nieuwe SQL-server te maken of als onderdeel van het maken van een nieuwe database.

### <a name="create-a-blank-server"></a>Een lege server maken

Als u een server (zonder database, elastische pool of datawarehouse-database) wilt maken met behulp van [de Azure Portal,](https://portal.azure.com)gaat u naar een leeg SQL Server-formulier (logische SQL Server).

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Een lege of voorbeelddatabase maken in Azure SQL Database

Als u een database in SQL Database maakt met behulp van [de Azure Portal,](https://portal.azure.com)navigeert u naar een leeg SQL Database formulier en geeft u de gevraagde gegevens op. U kunt de resourcegroep en server van tevoren of tijdens het maken van de database zelf maken. U kunt een lege database maken of een voorbeelddatabase maken op basis van Adventure Works LT.

  ![database-1 maken](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> Zie Aankoopmodel op basis van [DTU](service-tiers-dtu.md) en aankoopmodel op basis van vCore voor informatie over het selecteren van de [prijscategorie voor uw database.](service-tiers-vcore.md)

Zie Een beheerd exemplaar maken voor het [maken van een beheerd exemplaar](../managed-instance/instance-create-quickstart.md)

### <a name="manage-an-existing-server"></a>Een bestaande server beheren

Als u een bestaande server wilt beheren, navigeert u naar de server met behulp van een aantal methoden, zoals vanaf een specifieke databasepagina, de **pagina SQL-servers** of de pagina **Alle resources.**

Als u een bestaande database wilt beheren, gaat u naar de **pagina SQL-databases en** klikt u op de database die u wilt beheren. In de volgende schermopname ziet u hoe u een firewall op serverniveau kunt instellen voor een database vanaf de **pagina** Overzicht voor een database.

   ![serverfirewallregel](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> Zie Aankoopmodel op basis van [DTU](service-tiers-dtu.md) en aankoopmodel op basis van vCore om prestatie-eigenschappen voor een database [te configureren.](service-tiers-vcore.md)
> [!TIP]
> Zie Een Azure Portal maken in de SQL Database in de Azure Portal voor een [Azure Portal.](single-database-create-quickstart.md)

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>Servers, databases en firewalls beheren met PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

Als u servers, databases en firewalls wilt maken en beheren met Azure PowerShell, gebruikt u de volgende PowerShell-cmdlets. Zie Install Azure PowerShell module (PowerShell installeren of upgraden) [als u PowerShell wilt installeren Azure PowerShell upgraden.](/powershell/azure/install-az-ps) Zie Elastische pools voor het maken en beheren [van elastische pools.](elastic-pool-overview.md)

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

> [!TIP]
> Zie Create a database [in Azure SQL Database using PowerShell (Een database maken in een powershell-Azure SQL Database powershell) voor een PowerShell-snelstart.](single-database-create-quickstart.md) Zie PowerShell gebruiken om een database in Azure SQL Database te maken en een [firewallregel](scripts/create-and-configure-database-powershell.md) te configureren en Een database in een database bewaken en schalen in Azure SQL Database met behulp van PowerShell voor PowerShell voor [powershell-voorbeeldscripts.](scripts/monitor-and-scale-database-powershell.md)
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Servers, databases en firewalls beheren met de Azure CLI

Als u servers, databases en firewalls wilt maken en beheren met [de Azure CLI,](/cli/azure)gebruikt u de volgende [Azure CLI SQL Database opdrachten.](/cli/azure/sql/db) Gebruik de [Cloud Shell](../../cloud-shell/overview.md) om de CLI in uw browser uit te voeren of [installeer](/cli/azure/install-azure-cli) de CLI op macOS, Linux of Windows. Zie Elastische pools voor het maken en beheren [van elastische pools.](elastic-pool-overview.md)

| Cmdlet | Beschrijving |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Hiermee maakt u een database|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|Een lijst met alle databases die worden beheerd door een server of alle databases in een elastische pool|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|Een lijst met beschikbare servicedoelstellingen en opslaglimieten|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|Retourneert databasegebruik|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|Haalt een database op
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

> [!TIP]
> Zie Create a database [in Azure SQL Database using the Azure CLI (Een database maken in Azure SQL Database azure CLI) voor een Azure CLI-snelstart.](az-cli-script-samples-content-guide.md) Zie Use the CLI to create a database in Azure SQL Database and configure a firewall rule (Cli gebruiken om een database in Azure SQL Database te maken en een [firewallregel](scripts/create-and-configure-database-cli.md) te configureren) en Use the CLI to monitor and scale a database in Azure SQL Database (De CLI gebruiken om een database te bewaken en te [schalen in Azure SQL Database).](scripts/monitor-and-scale-database-cli.md)
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Servers, databases en firewalls beheren met Transact-SQL

Als u servers, databases en firewalls wilt maken en beheren met Transact-SQL, gebruikt u de volgende T-SQL-opdrachten. U kunt deze opdrachten uitvoeren met behulp van Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code of](https://code.visualstudio.com/docs)een ander programma dat verbinding kan maken met een server en Transact-SQL-opdrachten kan doorgeven. Zie Elastische pools voor het beheren [van elastische pools.](elastic-pool-overview.md)

> [!IMPORTANT]
> U kunt geen server maken of verwijderen met Transact-SQL.

| Opdracht | Beschrijving |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true) | Hiermee maakt u een nieuwe database in Azure SQL Database. U moet zijn verbonden met de hoofddatabase om een nieuwe database te kunnen maken.|
|[CREATE DATABASE (Azure Synapse)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Hiermee maakt u een nieuwe datawarehouse-database in Azure Synapse. U moet zijn verbonden met de hoofddatabase om een nieuwe database te kunnen maken.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Wijzigt de database of elastische pool. |
|[ALTER DATABASE (Azure Synapse Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true&tabs=sqlpool)|Wijzigt een datawarehouse-database in Azure Synapse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Hiermee verwijdert u een database.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retourneert de editie (servicelaag), de servicedoelstelling (prijscategorie) en de naam van de elastische pool, indien van u, voor een database. Als u bent aangemeld bij de hoofddatabase voor een server, retourneert informatie over alle databases. Voor Azure Synapse moet u zijn verbonden met de hoofddatabase.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Retourneert HET CPU-, I/O- en geheugenverbruik voor een database in Azure SQL Database. Er bestaat één rij om de 15 seconden, zelfs als er geen activiteit in de database is.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Retourneert CPU-gebruik en opslaggegevens voor een database in Azure SQL Database. De gegevens worden verzameld en geaggregeerd binnen een interval van vijf minuten.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Bevat statistieken voor databaseconnectiviteitsgebeurtenissen voor Azure SQL Database, zodat u een overzicht krijgt van de geslaagden en mislukte databaseverbindingen. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Retourneert geslaagde Azure SQL Database databaseverbindingen, verbindingsfouten en impasses voor Azure SQL Database. U kunt deze informatie gebruiken om uw databaseactiviteit bij te houden of problemen op te lossen.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Hiermee maakt of werkt u de firewallinstellingen op serverniveau voor uw server bij. Deze opgeslagen procedure is alleen beschikbaar in de hoofddatabase voor de principal-aanmelding op serverniveau. Een firewallregel op serverniveau kan alleen worden gemaakt met behulp van Transact-SQL nadat de eerste firewallregel op serverniveau is gemaakt door een gebruiker met machtigingen op Azure-niveau|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Retourneert informatie over de firewallinstellingen op serverniveau die aan een server zijn gekoppeld.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Hiermee verwijdert u firewallinstellingen op serverniveau van een server. Deze opgeslagen procedure is alleen beschikbaar in de hoofddatabase voor de principal-aanmelding op serverniveau.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Hiermee worden de firewallregels op databaseniveau voor een database in Azure SQL Database. Databasefirewallregels kunnen worden geconfigureerd voor de hoofddatabase en voor gebruikersdatabases in SQL Database. Databasefirewallregels zijn handig bij het gebruik van ingesloten databasegebruikers. Databasefirewallregels worden niet ondersteund in Azure Synapse.|
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Retourneert informatie over de firewallinstellingen op databaseniveau voor een database in Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Hiermee verwijdert u de firewallinstelling op databaseniveau voor een database van u in Azure SQL Database. |

> [!TIP]
> Zie voor een quickstart met SQL Server Management Studio in Microsoft Windows de volgende Azure SQL Database: SQL Server Management Studio verbinding maken en [query's uitvoeren op gegevens.](connect-query-ssms.md) Zie [Azure SQL Database:](connect-query-vscode.md)Visual Studio Code gebruiken om verbinding te maken en gegevens op te vragen voor een quickstart met behulp van Visual Studio Code in macOS, Linux of Windows.

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>Servers, databases en firewalls beheren met behulp van de REST API

Als u servers, databases en firewalls wilt maken en beheren, gebruikt u deze REST API aanvragen.

| Opdracht | Beschrijving |
| --- | --- |
|[Servers : maken of bijwerken](/rest/api/sql/servers/createorupdate)|Hiermee maakt of werkt u een nieuwe server bij.|
|[Servers - Verwijderen](/rest/api/sql/servers/delete)|Hiermee verwijdert u een server.|
|[Servers - Get](/rest/api/sql/servers/get)|Haalt een server op.|
|[Servers - Lijst](/rest/api/sql/servers/list)|Retourneert een lijst met servers.|
|[Servers - Lijst op resourcegroep](/rest/api/sql/servers/listbyresourcegroup)|Retourneert een lijst met servers in een resourcegroep.|
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

- Zie Migreren naar SQL Server voor Azure SQL Database meer informatie over het [Azure SQL Database.](migrate-to-database-from-sql-server.md)
- Zie Functies voor meer informatie over [ondersteunde functies.](features-comparison.md)