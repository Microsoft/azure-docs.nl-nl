---
title: 'Quickstart: Een workloadclassificatie maken - T-SQL'
description: T-SQL gebruiken om een workloadclassificatie met hoge urgentie te maken.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e757c8047bf6d634ab6d7cbc8963087c0eccc46a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98677365"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Quickstart: Een workloadclassificatie maken met T-SQL

In deze quickstart maakt u snel een workloadclassificatie met hoge urgentie voor de CEO van uw organisatie. Met deze workloadclassificatie krijgen query’s van de CEO een hogere prioriteit dan query’s met een lagere urgentie in de wachtrij.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

> [!NOTE]
> Het maken van een instantie van een toegewezen SQL-pool in Azure Synapse Analytics kan resulteren in een nieuwe factureerbare service.  Zie [Prijzen voor Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) voor meer informatie.
>
>

## <a name="prerequisites"></a>Vereisten

In deze quickstart wordt ervan uitgegaan dat u al een toegewezen SQL-pool in Azure Synapse Analytics hebt ingericht, en dat u CONTROL DATABASE-rechten hebt. Gebruik [Maken en koppelen - portal](create-data-warehouse-portal.md) om een toegewezen SQL-pool met de naam **mySampleDataWarehouse** te maken als dat nodig is.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Aanmelding maken voor TheCEO

Maak een SQL Server-verificatieaanmelding in de `master` database met behulp van [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) voor 'TheCEO'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Gebruiker maken

[Maak gebruiker](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 'TheCEO' in mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Een werkbelastingclassficatie maken

Maak een [workloadclassificatie](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) met hoge urgentie voor ‘TheCEO’.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Bestaande classificaties weergeven

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Resources opschonen

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Er worden kosten in rekening gebracht voor datawarehouse-eenheden en gegevens die zijn opgeslagen in uw toegewezen SQL-pool. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in de opslag wilt houden, kunt u het berekenen onderbreken wanneer u de toegewezen SQL-pool niet gebruikt. Als u het berekenen onderbreekt, worden er alleen kosten in rekening gebracht voor de gegevensopslag. Wanneer u klaar bent om met de gegevens te werken, hervat u de berekening.
- Als u in de toekomst geen kosten meer wilt maken, kunt u de toegewezen SQL-pool verwijderen.

Volg deze stappen om de resources op te schonen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer uw toegewezen SQL-pool.

    ![Resources opschonen](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Als u het berekenen wilt onderbreken, selecteert u de knop **Onderbreken**. Wanneer de toegewezen SQL-pool is onderbroken, ziet u een knop **Starten**.  Als u de berekening wilt hervatten, selecteert u **Starten**.

3. Als u de toegewezen SQL-pool wilt verwijderen zodat er geen kosten in rekening worden gebracht voor berekenen of opslaan, selecteert u **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- U hebt nu een workloadclassificatie gemaakt. Voer enkele query's uit als TheCEO om te zien hoe ze presteren. Zie [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) om query's en de toegewezen urgentie weer te geven.
- Zie [Workloadurgentie](sql-data-warehouse-workload-importance.md) en [Workloadclassificatie](sql-data-warehouse-workload-classification.md)voor meer informatie over het beheer van de workload van toegewezen SQL-pools.
- Zie ook de artikelen over [Workloadurgentie configureren](sql-data-warehouse-how-to-configure-workload-importance.md) en [Workloadbeheer beheren en bewaken](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
