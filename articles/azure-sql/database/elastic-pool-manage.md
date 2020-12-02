---
title: Pools voor Elastic Database beheren
description: Maak en beheer Azure SQL Database elastische Pools met behulp van de Azure Portal, Power shell, de Azure CLI, Transact-SQL (T-SQL) en rest-API.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1, devx-track-azurecli
ms.openlocfilehash: 9c9af6e3bc3dfd798f4b3f0cad9319aa573c425d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455991"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Elastische Pools beheren in Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Met een elastische pool bepaalt u de hoeveelheid resources die de elastische pool nodig heeft voor het afhandelen van de werk belasting van de data bases en de hoeveelheid resources voor elke gegroepeerde Data Base.

## <a name="azure-portal"></a>Azure Portal

Alle groeps instellingen vindt u op één locatie: de Blade **groep configureren** . Als u dit wilt doen, gaat u naar een elastische groep in de Azure Portal en klikt u op **groep configureren** van de bovenkant van de Blade of vanuit het menu resource aan de linkerkant.

Hier kunt u een combi natie van de volgende wijzigingen maken en deze allemaal in één batch opslaan:

1. De servicelaag van de pool wijzigen
2. De prestaties (DTU of vCores) en de opslag omhoog of omlaag schalen
3. Data bases toevoegen aan of verwijderen uit de groep
4. Stel een minimale (gegarandeerde) en maximale prestatie limiet voor de data bases in de Pools in
5. Bekijk het kosten overzicht om eventuele wijzigingen in uw factuur weer te geven als gevolg van uw nieuwe selecties

![Blade voor elastische groeps configuratie](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De module PowerShell Azure Resource Manager wordt nog steeds ondersteund in Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

Gebruik de volgende Power shell-cmdlets om SQL Database elastische Pools en gegroepeerde Data bases te maken en te beheren met Azure PowerShell. Als u Power shell wilt installeren of upgraden, raadpleegt u [Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Zie [servers maken en beheren](logical-servers.md)voor meer informatie over het maken en beheren van de servers voor een elastische pool. Zie [firewall regels maken en beheren met Power shell](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules)voor meer informatie over het maken en beheren van firewall regels.

> [!TIP]
> Zie voor voor beelden van Power shell-scripts [elastische Pools maken en data bases verplaatsen tussen Pools en uit een pool met Power shell](scripts/move-database-between-elastic-pools-powershell.md) en [Power shell gebruiken om een elastische SQL-groep in Azure SQL database te controleren en te schalen](scripts/monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Beschrijving |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Hiermee maakt u een elastische pool.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Hiermee haalt u elastische Pools en de bijbehorende eigenschaps waarden op.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Hiermee worden de eigenschappen van een elastische pool gewijzigd. Gebruik bijvoorbeeld de eigenschap **StorageMB** om de maximale opslag van een elastische pool te wijzigen.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Hiermee verwijdert u een elastische pool.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Hiermee wordt de status van bewerkingen in een elastische pool opgehaald|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Hiermee maakt u een nieuwe data base in een bestaande groep of als één data base. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hiermee haalt u een of meer databases op.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Hiermee stelt u de eigenschappen voor een data base in, of verplaatst u een bestaande Data Base naar, van of tussen elastische Pools.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Hiermee verwijdert u een database.|

> [!TIP]
> Het maken van een groot aantal data bases in een elastische pool kan enige tijd in beslag nemen met de portal-of Power shell-cmdlets waarmee slechts één data base tegelijk wordt gemaakt. Zie [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)voor het automatiseren van het maken van een elastische pool.

## <a name="azure-cli"></a>Azure CLI

Als u SQL Database elastische Pools wilt maken en beheren met de [Azure cli](/cli/azure), gebruikt u de volgende [Azure cli SQL database](/cli/azure/sql/db) -opdrachten. Gebruik de [Cloud Shell](../../cloud-shell/overview.md) om de CLI in uw browser uit te voeren of [installeer](/cli/azure/install-azure-cli) de CLI op macOS, Linux of Windows.

> [!TIP]
> Zie voor voor beelden van Azure CLI-scripts [CLI gebruiken om een data base in SQL database in een elastische SQL-pool te verplaatsen](scripts/move-database-between-elastic-pools-cli.md) en [gebruik Azure CLI om een elastische SQL-groep in Azure SQL database te schalen](scripts/scale-pool-cli.md).
>

| Cmdlet | Beschrijving |
| --- | --- |
|[AZ SQL Elastic-pool Create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Hiermee maakt u een elastische pool.|
|[AZ SQL Elastic-pool List](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Retourneert een lijst met elastische Pools in een server.|
|[AZ SQL Elastic-pool List-db's](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Retourneert een lijst met data bases in een elastische pool.|
|[AZ SQL Elastic-pool List-edities](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Bevat ook de beschik bare DTU-instellingen voor de groep, opslag limieten en instellingen per data base. Om uitgebreidere mogelijkheden te beperken, zijn extra opslag limieten en instellingen per data base standaard verborgen.|
|[AZ SQL Elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Hiermee werkt u een elastische pool bij.|
|[AZ SQL Elastic-pool Delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Hiermee verwijdert u de elastische pool.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Gebruik de volgende T-SQL-opdrachten om data bases te maken en te verplaatsen binnen bestaande elastische Pools of om informatie over een SQL Database elastische pool te retour neren met Transact-SQL. U kunt deze opdrachten uitgeven met de Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio code](https://code.visualstudio.com/docs)of een ander programma dat verbinding kan maken met een server en Transact-SQL-opdrachten kan passeren. Zie [firewall regels beheren met behulp van Transact-SQL](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules)om firewall regels te maken en beheren met behulp van T-SQL.

> [!IMPORTANT]
> Het is niet mogelijk om een Azure SQL Database elastische pool te maken, bij te werken of te verwijderen met behulp van Transact-SQL. U kunt data bases toevoegen aan of verwijderen uit een elastische pool en u kunt Dmv's gebruiken om informatie over bestaande elastische Pools te retour neren.
>

| Opdracht | Beschrijving |
| --- | --- |
|[DATA base maken (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Hiermee maakt u een nieuwe data base in een bestaande groep of als één data base. U moet zijn verbonden met de hoofd database om een nieuwe Data Base te kunnen maken.|
| [ALTER data base (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Een Data Base verplaatsen naar, uit of tussen elastische Pools.|
|[DROP data base (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Hiermee verwijdert u een Data Base.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Retourneert statistieken over het resource gebruik voor alle elastische Pools op een server. Voor elke elastische pool is er één rij voor elk venster van 15 seconden (vier rijen per minuut). Dit omvat CPU, IO, logboek, opslag verbruik en gelijktijdige aanvraag/sessie gebruik door alle data bases in de pool.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retourneert de editie (service tier), de service doelstelling (prijs categorie) en de naam van de elastische groep, indien van toepassing, voor een data base in SQL Database of Azure Synapse Analytics. Als u bent aangemeld bij de hoofd database op een server, wordt er informatie weer gegeven over alle data bases. Voor Azure Synapse Analytics moet u verbinding hebben met de hoofd database.|

## <a name="rest-api"></a>REST-API

Gebruik deze REST API aanvragen voor het maken en beheren van SQL Database elastische Pools en gepoolde data bases.

| Opdracht | Beschrijving |
| --- | --- |
|[Elastische Pools-maken of bijwerken](/rest/api/sql/elasticpools/createorupdate)|Hiermee maakt u een nieuwe elastische pool of werkt u een bestaande elastische pool bij.|
|[Elastische Pools-verwijderen](/rest/api/sql/elasticpools/delete)|Hiermee verwijdert u de elastische pool.|
|[Elastische Pools-ophalen](/rest/api/sql/elasticpools/get)|Hiermee haalt u een elastische pool op.|
|[Elastische Pools-lijst op server](/rest/api/sql/elasticpools/listbyserver)|Retourneert een lijst met elastische Pools in een server.|
|[Elastische Pools-bijwerken](/rest/api/sql/elasticpools/listbyserver)|Hiermee wordt een bestaande elastische pool bijgewerkt.|
|[Activiteiten voor elastische Pools](/rest/api/sql/elasticpoolactivities)|Hiermee worden activiteiten voor elastische Pools geretourneerd.|
|[Activiteiten voor Elastic pool-data bases](/rest/api/sql/elasticpooldatabaseactivities)|Hiermee wordt de activiteit in data bases binnen een elastische pool geretourneerd.|
|[Data bases: maken of bijwerken](/rest/api/sql/databases/createorupdate)|Hiermee maakt u een nieuwe data base of werkt u een bestaande data base bij.|
|[Data bases-ophalen](/rest/api/sql/databases/get)|Hiermee haalt u een Data Base.|
|[Data bases-lijst op elastische pool](/rest/api/sql/databases/listbyelasticpool)|Retourneert een lijst met data bases in een elastische pool.|
|[Data bases-lijst per server](/rest/api/sql/databases/listbyserver)|Retourneert een lijst met data bases op een server.|
|[Data bases-bijwerken](/rest/api/sql/databases/update)|Hiermee wordt een bestaande data base bijgewerkt.|

## <a name="next-steps"></a>Volgende stappen

* Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](saas-tenancy-app-design-patterns.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische pools.
* Zie [Introduction to the Wingtip SaaS Application](saas-dbpertenant-wingtip-app-overview.md)voor een SaaS-zelf studie over het gebruik van elastische Pools.