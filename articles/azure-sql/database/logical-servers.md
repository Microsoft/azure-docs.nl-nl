---
title: Wat is een server in Azure SQL Database en Azure Synapse Analytics?
titleSuffix: ''
description: Meer informatie over logische SQL-servers die worden gebruikt door Azure SQL Database en Azure Synapse Analytics en hoe u deze kunt beheren.
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
ms.openlocfilehash: e0736e58983d4c28b8f2f4a20241cd36ac84fdfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96445371"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Wat is een logische SQL-Server in Azure SQL Database en Azure Synapse?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

In Azure SQL Database en Azure Synapse Analytics is een server een logische construct die fungeert als een centraal beheer punt voor een verzameling data bases. Op server niveau kunt u [aanmeldingen](logins-create-manage.md), [firewall regels](firewall-configure.md), [controle regels](../../azure-sql/database/auditing-overview.md), beleid voor [detectie van dreigingen](threat-detection-configure.md)en [groepen voor automatische failover](auto-failover-group-overview.md)beheren. Een server kan zich in een andere regio bevinden dan de bijbehorende resource groep. De server moet bestaan voordat u een data base in Azure SQL Database of een Data Warehouse-data base in azure Synapse Analytics kunt maken. Alle data bases die worden beheerd door één server, worden in dezelfde regio gemaakt als de-server.

Deze server verschilt van een SQL Server exemplaar dat u mogelijk kent in de on-premises wereld. Er zijn met name geen garanties met betrekking tot de locatie van de data bases of Data Warehouse-data bases ten opzichte van de server die deze beheert. Bovendien geven Azure SQL Database noch Azure Synapse toegang tot of functies op exemplaar niveau toe. De instantie databases in een beheerd exemplaar zijn daarentegen allemaal fysiek op dezelfde manier als u bekend bent met SQL Server op de on-premises of virtuele machine wereld.

Wanneer u een server maakt, geeft u een aanmeldings account voor de server en het wacht woord op die beheerders rechten heeft voor de hoofd database op die server en alle data bases die op die server worden gemaakt. Dit eerste account is een SQL-aanmeldings account. Azure SQL Database en Azure Synapse Analytics bieden ondersteuning voor SQL-verificatie en Azure Active Directory verificatie voor verificatie. Zie [data bases en aanmeldingen beheren in Azure SQL database](logins-create-manage.md)voor meer informatie over aanmeldingen en verificatie. Windows-verificatie wordt niet ondersteund.

Een server in SQL Database en Azure Synapse:

- Wordt gemaakt binnen een Azure-abonnement, maar kan met ingesloten resources naar een ander abonnement worden verplaatst
- Is de bovenliggende resource voor databases, elastische groepen en datawarehouses
- Voorziet in een naam ruimte voor data bases, elastische groepen en Data Warehouse-data bases
- Is een logische container met een sterke levens duur semantiek: een server verwijderen en de data bases, elastische groepen en SQK-groepen worden verwijderd
- Maakt deel uit van op [rollen gebaseerd toegangs beheer (Azure RBAC) van Azure](../../role-based-access-control/overview.md) -data bases, elastische groepen en Data Warehouse-data bases binnen een server neemt de toegangs rechten van de server over
- Is een hoog element van de identiteit van de data bases, elastische Pools en Data Warehouse-data bases voor Azure Resource Management (Zie het URL-schema voor data bases en Pools)
- Groepeert resources in een regio
- Biedt een verbindingseindpunt voor databasetoegang (`<serverName>`.database.windows.net)
- Biedt toegang tot metagegevens van ingesloten resources via DMV's door verbinding te maken met een hoofddatabase
- Voorziet in het bereik voor beheer beleid dat van toepassing is op de data bases-aanmeldingen, firewall, controle, detectie van bedreigingen en dergelijke
- Wordt beperkt door een quotum binnen het bovenliggende abonnement (zes servers per abonnement standaard: [Zie de limieten voor abonnementen hier](../../azure-resource-manager/management/azure-subscription-service-limits.md))
- Voorziet in het bereik voor database quota en DTU-of vCore-quotum voor de resources die het bevat (zoals 45.000 DTU)
- Is het versie bereik voor mogelijkheden die zijn ingeschakeld in opgenomen resources
- Hoofdaanmeldingen op serverniveau kunnen alle databases op een server beheren
- Kan aanmeldingen bevatten die vergelijkbaar zijn met die in exemplaren van SQL Server in uw on-premises omgeving die toegang krijgen tot een of meer data bases op de server en waaraan beperkte beheerders rechten kunnen worden verleend. Zie [Aanmeldingen](logins-create-manage.md) voor meer informatie.
- De standaard sortering voor alle data bases die op een server worden gemaakt `SQL_LATIN1_GENERAL_CP1_CI_AS` , is als volgt: `LATIN1_GENERAL` engels (Verenigde Staten), is `CP1` code pagina 1252, `CI` is hoofdletter gevoelig en `AS` is accent gevoelig.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Servers, data bases en firewalls beheren met behulp van de Azure Portal

U kunt de resource groep voor een server van tevoren of tijdens het maken van de server maken. Er zijn meerdere methoden voor het ophalen van een nieuw SQL Server-formulier, hetzij door een nieuwe SQL Server te maken of als onderdeel van het maken van een nieuwe data base.

### <a name="create-a-blank-server"></a>Een lege server maken

Als u een server (zonder een Data Base, elastische pool of Data Warehouse-data base) wilt maken met behulp van de [Azure Portal](https://portal.azure.com), gaat u naar een leeg SQL Server-formulier (logische SQL-Server).

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Een lege of voorbeeld database maken in Azure SQL Database

Als u een data base in SQL Database wilt maken met behulp van de [Azure Portal](https://portal.azure.com), gaat u naar een leeg SQL database formulier en geeft u de gevraagde informatie op. U kunt de resource groep en de server van tevoren maken of tijdens het maken van de data base zelf. U kunt een lege data base maken of een voorbeeld database maken op basis van Adventure Works LT.

  ![database-1 maken](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> Zie voor meer informatie over het selecteren van de prijs categorie voor uw Data Base op [DTU gebaseerd inkoop model](service-tiers-dtu.md) en [op vCore gebaseerd aankoop model](service-tiers-vcore.md).

Zie [een beheerd exemplaar maken](../managed-instance/instance-create-quickstart.md) voor informatie over het maken van een beheerd exemplaar

### <a name="manage-an-existing-server"></a>Een bestaande server beheren

Als u een bestaande server wilt beheren, gaat u naar de-server met behulp van een aantal methoden, bijvoorbeeld van een specifieke database pagina, de pagina **SQL-servers** of de pagina **alle resources** .

Als u een bestaande Data Base wilt beheren, gaat u naar de pagina **SQL-data bases** en klikt u op de data base die u wilt beheren. In de volgende scherm afbeelding ziet u hoe u een firewall op server niveau instelt voor een Data Base op de pagina **overzicht** voor een Data Base.

   ![serverfirewallregel](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> Zie op [DTU gebaseerd inkoop model](service-tiers-dtu.md) en op [vCore gebaseerd inkoop model](service-tiers-vcore.md)voor informatie over het configureren van prestatie-eigenschappen voor een Data Base.
> [!TIP]
> Zie [een Data Base maken in SQL database in de Azure Portal](single-database-create-quickstart.md)voor een Azure Portal Snelstartgids.

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>Servers, data bases en firewalls beheren met Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

Als u servers, data bases en firewalls met Azure PowerShell wilt maken en beheren, gebruikt u de volgende Power shell-cmdlets. Als u Power shell wilt installeren of upgraden, raadpleegt u [Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Zie [elastische Pools](elastic-pool-overview.md)voor het maken en beheren van elastische Pools.

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

> [!TIP]
> Zie [een Data Base maken in Azure SQL database met behulp van Power shell](single-database-create-quickstart.md)voor een Power shell-Quick Start. Zie Power [shell gebruiken om een Data Base te maken in Azure SQL database en een firewall regel te configureren](scripts/create-and-configure-database-powershell.md) en [een data base in Azure SQL database te bewaken en te schalen met behulp van Power](scripts/monitor-and-scale-database-powershell.md)shell voor voorbeeld scripts voor Power shell.
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Servers, data bases en firewalls beheren met de Azure CLI

Als u servers, data bases en firewalls wilt maken en beheren met de [Azure cli](/cli/azure), gebruikt u de volgende [Azure cli SQL database](/cli/azure/sql/db) -opdrachten. Gebruik de [Cloud Shell](../../cloud-shell/overview.md) om de CLI in uw browser uit te voeren of [installeer](/cli/azure/install-azure-cli) de CLI op macOS, Linux of Windows. Zie [elastische Pools](elastic-pool-overview.md)voor het maken en beheren van elastische Pools.

| Cmdlet | Beschrijving |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Hiermee maakt u een Data Base|
|[AZ SQL DB List](/cli/azure/sql/db#az-sql-db-list)|Een lijst met alle data bases die worden beheerd door een server of alle data bases in een elastische pool|
|[AZ SQL DB List-edities](/cli/azure/sql/db#az-sql-db-list-editions)|Een lijst met beschik bare service doelstellingen en opslag limieten|
|[AZ SQL DB List-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Retourneert database gebruik|
|[AZ SQL DB show](/cli/azure/sql/db#az-sql-db-show)|Hiermee haalt u een Data Base
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

> [!TIP]
> Zie [een Data Base maken in Azure SQL database met behulp van de Azure cli](az-cli-script-samples-content-guide.md)voor een Snelstartgids van Azure cli. Zie [de CLI gebruiken voor het maken van een data base in Azure SQL database en een firewall regel configureren](scripts/create-and-configure-database-cli.md) en [de CLI gebruiken om een data base in Azure SQL database te bewaken en te schalen voor een](scripts/monitor-and-scale-database-cli.md)voor beeld van Azure CLI-scripts.
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Servers, data bases en firewalls beheren met behulp van Transact-SQL

Gebruik de volgende T-SQL-opdrachten om servers, data bases en firewalls te maken en te beheren met Transact-SQL. U kunt deze opdrachten uitgeven met de Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio code](https://code.visualstudio.com/docs)of een ander programma dat verbinding kan maken met een server en Transact-SQL-opdrachten kan passeren. Zie [elastische Pools](elastic-pool-overview.md)voor het beheren van elastische Pools.

> [!IMPORTANT]
> U kunt geen server maken of verwijderen met behulp van Transact-SQL.

| Opdracht | Beschrijving |
| --- | --- |
|[DATA base maken (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) | Hiermee maakt u een nieuwe database in Azure SQL Database. U moet zijn verbonden met de hoofd database om een nieuwe Data Base te kunnen maken.|
|[CREATE data base (Azure Synapse)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest) | Hiermee maakt u een nieuwe Data Warehouse-data base in azure Synapse. U moet zijn verbonden met de hoofd database om een nieuwe Data Base te kunnen maken.|
| [ALTER data base (Azure SQL Database)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Data Base of elastische pool wordt gewijzigd. |
|[ALTER data base (Azure Synapse Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=sql-server-ver15)|Hiermee wijzigt u een Data Warehouse-data base in azure Synapse.|
|[DROP data base (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Hiermee verwijdert u een Data Base.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retourneert de editie (service tier), de service doelstelling (prijs categorie) en de naam van de elastische groep, indien van toepassing, voor een Data Base. Als u bent aangemeld bij de hoofd database voor een-server, retourneert informatie over alle data bases. Voor Azure Synapse moet u verbinding hebben met de hoofd database.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Retourneert CPU, i/o en geheugen verbruik voor een data base in Azure SQL Database. Er bestaat één rij voor elke 15 seconden, zelfs als er geen activiteit in de data base is.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Retourneert het CPU-gebruik en de opslag gegevens voor een data base in Azure SQL Database. De gegevens worden binnen een interval van vijf minuten verzameld en geaggregeerd.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Bevat statistieken voor database connectiviteits gebeurtenissen voor Azure SQL Database, waarmee u een overzicht krijgt van geslaagde en mislukte database verbindingen. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Hiermee worden geslaagde Azure SQL Database database verbindingen, verbindings fouten en deadlocks voor Azure SQL Database geretourneerd. U kunt deze informatie gebruiken om de activiteit van uw data base bij te houden of problemen op te lossen.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Hiermee worden de firewall instellingen op server niveau voor uw server gemaakt of bijgewerkt. Deze opgeslagen procedure is alleen beschikbaar in de hoofd database voor de principal-aanmelding op server niveau. Een firewall regel op server niveau kan alleen worden gemaakt met behulp van Transact-SQL nadat de eerste firewall regel op server niveau is gemaakt door een gebruiker met machtigingen op Azure-niveau|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Retourneert informatie over de firewall instellingen op server niveau die zijn gekoppeld aan een server.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Hiermee verwijdert u Firewall instellingen op server niveau van een server. Deze opgeslagen procedure is alleen beschikbaar in de hoofd database voor de principal-aanmelding op server niveau.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Hiermee worden de firewall regels op database niveau voor een data base in Azure SQL Database gemaakt of bijgewerkt. Database firewall regels kunnen worden geconfigureerd voor de hoofd database en voor gebruikers databases in SQL Database. Database firewall regels zijn handig wanneer u Inge sloten database gebruikers gebruikt. De firewall regels voor de Data Base worden niet ondersteund in azure Synapse.|
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Retourneert informatie over de firewall instellingen op database niveau voor een data base in Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Hiermee verwijdert u de firewall-instelling op database niveau voor een Data Base van uw bedrijf in Azure SQL Database. |

> [!TIP]
> Voor een Snelstartgids met SQL Server Management Studio in micro soft Windows raadpleegt u [Azure SQL database: gebruik SQL Server Management Studio om verbinding te maken en gegevens op te vragen](connect-query-ssms.md). Zie [Azure SQL database: Visual Studio code gebruiken om verbinding te maken en gegevens op te vragen](connect-query-vscode.md)voor een Snelstartgids met Visual Studio code op het MacOS, Linux of Windows.

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>Servers, data bases en firewalls beheren met behulp van de REST API

Gebruik deze REST API aanvragen om servers, data bases en firewalls te maken en te beheren.

| Opdracht | Beschrijving |
| --- | --- |
|[Servers: maken of bijwerken](/rest/api/sql/servers/createorupdate)|Hiermee wordt een nieuwe server gemaakt of bijgewerkt.|
|[Servers-verwijderen](/rest/api/sql/servers/delete)|Hiermee wordt een server verwijderd.|
|[Servers: ophalen](/rest/api/sql/servers/get)|Hiermee wordt een server opgehaald.|
|[Servers-lijst](/rest/api/sql/servers/list)|Retourneert een lijst met servers.|
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

- Zie [migreren naar Azure SQL database](migrate-to-database-from-sql-server.md)voor meer informatie over het migreren van een SQL server-data base naar Azure SQL database.
- Zie [functies](features-comparison.md)voor meer informatie over ondersteunde functies.