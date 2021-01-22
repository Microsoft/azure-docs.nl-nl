---
title: 'Quickstart: Rekenkracht schalen in toegewezen SQL-pool (voorheen SQL DW) - T-SQL'
description: Schaal de rekenkracht in toegewezen SQL-pool (voorheen SQL DW) met behulp van T-SQL en SQL Server Management Studio (SSMS). De schaal van rekenkracht vergroten voor betere prestaties of de schaal juist verkleinen om kosten te besparen.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ffffeb38aeb9d1f01f376d58a52323bb7b84b306
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676320"
---
# <a name="quickstart-scale-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-t-sql"></a>Quickstart: Schaal de rekenkracht voor toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics met behulp van T-SQL

Schaal de rekenkracht in toegewezen SQL-pool (voorheen SQL DW) met behulp van T-SQL en SQL Server Management Studio (SSMS). [Vergroot de schaal van Compute](sql-data-warehouse-manage-compute-overview.md) voor betere prestaties of verklein de schaal juist om kosten te besparen.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Download en installeer de nieuwste versie van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SQL Server Management Studio).

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Een toegewezen SQL-pool (voorheen SQL DW) maken

Gebruik [Quickstart: maken en verbinden - portal](create-data-warehouse-portal.md) om een toegewezen SQL-pool (voorheen SQL DW) te maken met de naam **mySampleDataWarehouse**. Voltooi de quickstart om ervoor te zorgen dat u een firewallregel hebt en dat u vanuit SQL Server Management Studio verbinding kunt maken met uw toegewezen SQL-pool (voorheen SQL DW).

## <a name="connect-to-the-server-as-server-admin"></a>Als serverbeheerder verbinding maken met de server

In deze sectie wordt gebruikgemaakt van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SQL Server Management Studio) om een verbinding tot stand te brengen met de Azure SQL-server.

1. Open SQL Server Management Studio.

2. Voer in het dialoogvenster **Verbinding maken met server** de volgende informatie in:

   | Instelling       | Voorgestelde waarde | Beschrijving |
   | ------------ | ------------------ | ------------------------------------------------- |
   | Servertype | Database-engine | Deze waarde is verplicht |
   | Servernaam | De volledig gekwalificeerde servernaam | Hier volgt een voorbeeld: **mySampleDataWarehouseservername.database.windows.net**. |
   | Verificatie | SQL Server-verificatie | SQL-verificatie is het enige verificatietype dat in deze zelfstudie is geconfigureerd. |
   | Aanmelden | Het beheerdersaccount voor de server | Het account dat u hebt opgegeven tijdens het maken van de server. |
   | Wachtwoord | Het wachtwoord voor het beheerdersaccount voor de server | Het wachtwoord dat u hebt opgegeven tijdens het maken van de server. |

    ![Verbinding maken met server](./media/quickstart-scale-compute-tsql/connect-to-server.png)

3. Klik op **Verbinden**. Het venster Objectverkenner wordt geopend in SQL Server Management Studio.

4. Vouw **Databases** uit in Objectverkenner. Vouw vervolgens **mySampleDataWarehouse** uit om de objecten in uw nieuwe database weer te geven.

    ![Databaseobjecten](./media/quickstart-scale-compute-tsql/connected.png)

## <a name="view-service-objective"></a>Servicedoelstelling weergeven

De instelling voor de servicedoelstelling bevat het aantal DWU’s voor de toegewezen SQL-pool (voorheen SQL DW).

De huidige DWU’s voor uw toegewezen SQL-pool (voorheen SQL DW) bekijken:

1. Vouw onder de verbinding met **mySampleDataWarehouseservername.database.windows.net** de optie **Systeemdatabases** uit.
2. Klik met de rechtermuisknop op **master** en selecteer **Nieuwe query**. Een nieuwe queryvenster wordt geopend.
3. Voer de volgende query uit om een selectie te maken in de dynamische beheerweergave sys.database_service_objectives.

    ```sql
    SELECT
        db.name [Database]
    ,    ds.edition [Edition]
    ,    ds.service_objective [Service Objective]
    FROM
         sys.database_service_objectives ds
    JOIN
        sys.databases db ON ds.database_id = db.database_id
    WHERE
        db.name = 'mySampleDataWarehouse'
    ```

4. In de volgende resultaten ziet u dat **mySampleDataWarehouse** een servicedoelstelling van DW400 heeft.

    ![iew-current-dwu](./media/quickstart-scale-compute-tsql/view-current-dwu.png)

## <a name="scale-compute"></a>De schaal van Compute aanpassen

In de toegewezen SQL-pool (voorheen SQL DW) kunt u het aantal rekenresources verhogen of verlagen door de datawarehouse-eenheden aan te passen. Met behulp van [Maken en verbinden - portal](create-data-warehouse-portal.md) is **mySampleDataWarehouse** gemaakt en vervolgens gestart met 400 DWU's. In de volgende stappen wordt het aantal DWU’s voor **mySampleDataWarehouse** aangepast.

DWU’s wijzigen:

1. Klik met de rechtermuisknop op **master** en selecteer **Nieuwe query**.
2. Gebruik de T-SQL-instructie [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) om de servicedoelstelling te wijzigen. Voer de volgende query uit om de servicedoelstelling te wijzigen in DW300.

    ```Sql
    ALTER DATABASE mySampleDataWarehouse
    MODIFY (SERVICE_OBJECTIVE = 'DW300c');
    ```

## <a name="monitor-scale-change-request"></a>Schaalaanpassingsverzoek controleren

Als u de voortgang van de eerdere aanpassingsaanvraag wilt bekijken, kunt u de `WAITFORDELAY` T-SQL syntax gebruiken om de dynamische beheerweergave (DMV) sys.dm_operation_status te peilen.

Op de volgende manier peilt u de aanpassingsstatus van het serviceobject:

1. Klik met de rechtermuisknop op **master** en selecteer **Nieuwe query**.
2. Voer de volgende query uit om de DMV sys.dm_operation_status te peilen.

    ```sql
    WHILE
    (
        SELECT TOP 1 state_desc
        FROM sys.dm_operation_status
        WHERE
            1=1
            AND resource_type_desc = 'Database'
            AND major_resource_id = 'mySampleDataWarehouse'
            AND operation = 'ALTER DATABASE'
        ORDER BY
            start_time DESC
    ) = 'IN_PROGRESS'
    BEGIN
        RAISERROR('Scale operation in progress',0,0) WITH NOWAIT;
        WAITFOR DELAY '00:00:05';
    END
    PRINT 'Complete';
    ```

3. In de resulterende uitvoer ziet u een logboek van de statuspeiling.

    ![Bewerkingsstatus](./media/quickstart-scale-compute-tsql/polling-output.png)

## <a name="check-dedicated-sql-pool-formerly-sql-dw-state"></a>De status van de toegewezen SQL-pool (voorheen SQL DW) controleren

Wanneer een toegewezen SQL-pool (voorheen SQL DW) wordt onderbroken, kunt u geen verbinding maken met T-SQL. Als u de huidige status van de toegewezen SQL-pool (voorheen SQL DW) wilt zien, kunt u een PowerShell-cmdlet gebruiken. Zie [De status van de toegewezen SQL-pool (voorheen SQL DW) controleren - Powershell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) voor een voorbeeld.

## <a name="check-operation-status"></a>Bewerkingsstatus controleren

Voer de volgende query uit in de DMV [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) om informatie te krijgen over de verschillende beheerbewerkingen in uw toegewezen SQL-pool (voorheen SQL DW). Zo wordt bijvoorbeeld de bewerking en de status van de bewerking (IN_PROGRESS of COMPLETED) geretourneerd.

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND
    major_resource_id = 'mySampleDataWarehouse'
```

## <a name="next-steps"></a>Volgende stappen

U hebt nu geleerd hoe u de rekenkracht voor uw toegewezen SQL-pool (voorheen SQL DW) kunt schalen. Voor meer informatie over Azure Synapse Analytics gaat u verder met de zelfstudie voor het laden van gegevens.

> [!div class="nextstepaction"]
>[Gegevens laden in een toegewezen SQL-pool](./load-data-from-azure-blob-storage-using-copy.md)