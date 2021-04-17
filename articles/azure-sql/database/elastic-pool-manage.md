---
title: Pools voor Elastic Database beheren
description: Maak en beheer Azure SQL Database elastische pools met behulp van de Azure Portal, PowerShell, de Azure CLI, Transact-SQL (T-SQL) en rest API.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1, devx-track-azurecli
ms.openlocfilehash: dc2bb24880b77eae24e9bb2ef0baf70ac0b92ac7
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588629"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Elastische pools beheren in Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Met een elastische pool bepaalt u de hoeveelheid resources die de elastische pool nodig heeft voor het afhandelen van de workload van de databases en de hoeveelheid resources voor elke pooldatabase.

## <a name="azure-portal"></a>Azure Portal

Alle poolinstellingen zijn op één plek te vinden: de blade **Groep configureren.** Als u hier wilt komen, gaat u naar een elastische pool in de Azure Portal en klikt u op Groep **configureren** boven aan de blade of in het resourcemenu aan de linkerkant.

Hier kunt u een combinatie van de volgende wijzigingen aanbrengen en ze allemaal in één batch opslaan:

1. De servicelaag van de pool wijzigen
2. De prestaties (DTU of vCores) en opslag omhoog of omlaag schalen
3. Databases toevoegen aan of verwijderen uit de pool
4. Stel een minimum ( gegarandeerd) en maximale prestatielimiet in voor de databases in de pools
5. Bekijk het kostenoverzicht om eventuele wijzigingen in uw factuur weer te geven als gevolg van uw nieuwe selecties

![Configuratieblade elastische pool](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De module PowerShell Azure Resource Manager wordt nog steeds ondersteund in Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

Gebruik de volgende PowerShell-cmdlets SQL Database elastische pools en pooldatabases met Azure PowerShell maken en beheren. Zie Install Azure PowerShell module (PowerShell installeren of upgraden) [als u PowerShell wilt installeren Azure PowerShell upgraden.](/powershell/azure/install-az-ps) Zie Servers maken en beheren voor het maken en beheren van de servers voor een [elastische pool.](logical-servers.md) Zie Firewallregels maken en beheren met PowerShell voor het maken en [beheren van firewallregels.](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules)

> [!TIP]
> Zie Voor PowerShell-voorbeeldscripts Elastische pools maken en databases verplaatsen tussen pools en uit een pool met behulp van [PowerShell](scripts/move-database-between-elastic-pools-powershell.md) en PowerShell gebruiken om een elastische [SQL-pool te](scripts/monitor-and-scale-pool-powershell.md)bewaken en te schalen in Azure SQL Database.
>

| Cmdlet | Beschrijving |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Hiermee maakt u een elastische pool.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Haalt elastische pools en hun eigenschapswaarden op.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Wijzigt eigenschappen van een elastische pool Gebruik bijvoorbeeld de eigenschap **StorageMB** om de maximale opslag van een elastische pool te wijzigen.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Hiermee verwijdert u een elastische pool.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Haalt de status van bewerkingen op een elastische pool op|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Hiermee maakt u een nieuwe database in een bestaande pool of als een individuele database. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hiermee haalt u een of meer databases op.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Hiermee stelt u eigenschappen in voor een database of verplaatst u een bestaande database naar, uit of tussen elastische pools.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Hiermee verwijdert u een database.|

> [!TIP]
> Het maken van veel databases in een elastische pool kan enige tijd duren wanneer u klaar bent met de portal of PowerShell-cmdlets die slechts één database tegelijk maken. Zie [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)als u het maken in een elastische pool wilt automatiseren.

## <a name="azure-cli"></a>Azure CLI

Als u een elastische SQL Database wilt maken en beheren met [de Azure CLI,](/cli/azure)gebruikt u de volgende [Azure CLI-SQL Database opdrachten.](/cli/azure/sql/db) Gebruik de [Cloud Shell](../../cloud-shell/overview.md) om de CLI in uw browser uit te voeren of [installeer](/cli/azure/install-azure-cli) de CLI op macOS, Linux of Windows.

> [!TIP]
> Zie CLI gebruiken om een database in SQL Database te verplaatsen in een elastische [SQL-pool](scripts/move-database-between-elastic-pools-cli.md) en Azure CLI gebruiken om een elastische [SQL-pool](scripts/scale-pool-cli.md)te schalen in Azure SQL Database voor voorbeeldscripts van Azure CLI.
>

| Cmdlet | Beschrijving |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Hiermee maakt u een elastische pool.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Retourneert een lijst met elastische pools op een server.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Retourneert een lijst met databases in een elastische pool.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Bevat ook beschikbare DTU-instellingen voor de groep, opslaglimieten en instellingen per database. Om de verbossing te verminderen, worden standaard extra opslaglimieten en per database-instellingen verborgen.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Werkt een elastische pool bij.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Hiermee verwijdert u de elastische pool.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Gebruik de volgende T-SQL-opdrachten om databases te maken en te verplaatsen binnen bestaande elastische pools of om informatie te retourneren over een SQL Database elastische pool met Transact-SQL. U kunt deze opdrachten uitvoeren met behulp van Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code of](https://code.visualstudio.com/docs)een ander programma dat verbinding kan maken met een server en Transact-SQL-opdrachten kan doorgeven. Zie Firewallregels beheren met Transact-SQL voor het maken en beheren van firewallregels met [T-SQL.](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules)

> [!IMPORTANT]
> U kunt een elastische pool niet maken, bijwerken Azure SQL Database verwijderen met behulp van Transact-SQL. U kunt databases toevoegen aan of verwijderen uit een elastische pool en u kunt DMV's gebruiken om informatie over bestaande elastische pools te retourneren.
>

| Opdracht | Beschrijving |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Hiermee maakt u een nieuwe database in een bestaande pool of als een individuele database. U moet zijn verbonden met de hoofddatabase om een nieuwe database te maken.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Verplaats een database naar, uit of tussen elastische pools.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Hiermee verwijdert u een database.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Retourneert statistieken over resourcegebruik voor alle elastische pools op een server. Voor elke elastische pool is er één rij voor elk rapportagevenster van 15 seconden (vier rijen per minuut). Dit omvat CPU-, IO-, logboek-, opslagverbruik en gelijktijdig aanvraag-/sessiegebruik door alle databases in de pool.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retourneert de editie (servicelaag), servicedoelstelling (prijscategorie) en de naam van de elastische pool, indien van gebruik, voor een database in SQL Database of Azure Synapse Analytics. Als u bent aangemeld bij de hoofddatabase op een server, retourneert informatie over alle databases. Voor Azure Synapse Analytics moet u zijn verbonden met de hoofddatabase.|

## <a name="rest-api"></a>REST-API

Gebruik deze SQL Database om elastische pools en pooldatabases te maken REST API beheren.

| Opdracht | Beschrijving |
| --- | --- |
|[Elastische pools : maken of bijwerken](/rest/api/sql/elasticpools/createorupdate)|Hiermee maakt u een nieuwe elastische pool of werkt u een bestaande elastische pool bij.|
|[Elastische pools - Verwijderen](/rest/api/sql/elasticpools/delete)|Hiermee verwijdert u de elastische pool.|
|[Elastische pools - Get](/rest/api/sql/elasticpools/get)|Haalt een elastische pool op.|
|[Elastische pools - Lijst per server](/rest/api/sql/elasticpools/listbyserver)|Retourneert een lijst met elastische pools in een server.|
|[Elastische pools - Bijwerken] (/rest/api/sql/2020-11-01-preview/elasticpools/update
)|Werkt een bestaande elastische pool bij.|
|[Activiteiten voor elastische pool](/rest/api/sql/elasticpoolactivities)|Retourneert elastische poolactiviteiten.|
|[Databaseactiviteiten voor elastische pool](/rest/api/sql/elasticpooldatabaseactivities)|Retourneert activiteit op databases in een elastische pool.|
|[Databases : maken of bijwerken](/rest/api/sql/databases/createorupdate)|Hiermee maakt u een nieuwe database of werkt u een bestaande database bij.|
|[Databases - Get](/rest/api/sql/databases/get)|Haalt een database op.|
|[Databases - Lijst per elastische pool](/rest/api/sql/databases/listbyelasticpool)|Retourneert een lijst met databases in een elastische pool.|
|[Databases - Lijst per server](/rest/api/sql/databases/listbyserver)|Retourneert een lijst met databases op een server.|
|[Databases - Bijwerken](/rest/api/sql/databases/update)|Werkt een bestaande database bij.|

## <a name="next-steps"></a>Volgende stappen

* Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](saas-tenancy-app-design-patterns.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische pools.
* Zie Inleiding tot de [Wingtip SaaS-toepassing](saas-dbpertenant-wingtip-app-overview.md)voor een SaaS-zelfstudie met elastische pools.
