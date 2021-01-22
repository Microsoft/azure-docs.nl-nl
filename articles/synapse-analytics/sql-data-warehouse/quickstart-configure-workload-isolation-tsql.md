---
title: 'Quickstart: Isolatie van werkbelastingen configureren - T-SQL'
description: Gebruik T-SQL om isolatie van werkbelastingen te configureren.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9d69c1708e73ad7ce5610a0683835e9f304c3f54
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679751"
---
# <a name="quickstart-configure-workload-isolation-in-a-dedicated-sql-pool-using-t-sql"></a>Quickstart: Isolatie van werkbelastingen configureren in een toegewezen SQL-pool met behulp van T-SQL

In deze quickstart maakt u snel een werkbelastinggroep en classificatie voor het reserveren van resources voor het laden van gegevens. De werkbelastinggroep wijst 20% van de systeemresources toe aan de gegevensladingen.  De werkbelastingclassificatie wijst aanvragen toe aan de werkbelastinggroep voor de gegevensladingen.  Met een isolatie van 20% voor gegevensladingen, zijn er gegarandeerd resources om te voldoen aan de SLA's.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

> [!NOTE]
> Het maken van een Synapse SQL-exemplaar in Azure Synapse Analytics kan resulteren in een nieuwe factureerbare service.  Zie [Prijzen voor Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

In deze quickstart wordt ervan uitgegaan dat u al een Synapse SQL-exemplaar in Azure Synapse hebt, en dat u CONTROL DATABASE-rechten hebt. Gebruik [Maken en koppelen - portal](create-data-warehouse-portal.md) om een toegewezen SQL-pool met de naam **mySampleDataWarehouse** te maken als dat nodig is.

## <a name="create-login-for-dataloads"></a>Aanmelding maken voor DataLoads

Maak een SQL Server-verificatieaanmelding in de `master` database met behulp van [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) voor 'ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Gebruiker maken

[Maak gebruiker](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 'ELTLogin' in mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Een werkbelastinggroep maken

Maak een [werkbelastinggroep](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) voor DataLoads met een isolatie van 20%.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>Een werkbelastingclassficatie maken

Maak een [werkbelastingclassificatie](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) om ELTLogin toe te wijzen aan de werkbelastinggroep DataLoads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Bestaande werkbelastinggroepen en -classificaties en runtimewaarden weergeven

```sql
--Workload groups
SELECT * FROM
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Resources opschonen

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Er worden kosten in rekening gebracht voor datawarehouse-eenheden en gegevens die zijn opgeslagen in uw toegewezen SQL-pool. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in de opslag wilt houden, kunt u het berekenen onderbreken wanneer u de toegewezen SQL-pool niet gebruikt. Als u het berekenen onderbreekt, worden er alleen kosten in rekening gebracht voor de gegevensopslag. Wanneer u klaar bent om met de gegevens te werken, hervat u de berekening.
- Als u in de toekomst geen kosten meer wilt maken, kunt u de toegewezen SQL-pool verwijderen.

## <a name="next-steps"></a>Volgende stappen

- U hebt nu een werkbelastinggroep gemaakt. Voer enkele query's uit als ELTLogin om te zien hoe ze presteren. Zie [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) om query's en de toegewezen werkbelastinggroep weer te geven.
- Zie [Werkbelastingbeheer](sql-data-warehouse-workload-management.md) en [Werkbelastingisolatie](sql-data-warehouse-workload-isolation.md)voor meer informatie over Synapse SQL-werkbelastingbeheer.
