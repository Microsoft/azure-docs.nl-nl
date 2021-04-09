---
title: 'Quickstart: Gegevens bulksgewijs laden met behulp van één T-SQL-instructie'
description: Gegevens bulksgewijs laden met behulp van de instructie COPY
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 2f97c630189a0f037f1231bb2386fcb7226a9350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600034"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Quickstart: Gegevens bulksgewijs laden met behulp van de instructie COPY

In deze quickstart laadt u gegevens bulksgewijs in uw toegewezen SQL-pool met behulp van de eenvoudige en flexibele [COPY-instructie](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) voor gegevensopname met hoge doorvoer. De instructie COPY is het aanbevolen hulpprogramma voor het laden omdat u er naadloos en flexibel gegevens mee kunt laden door middel van de volgende functionaliteit:

- Sta gebruikers met een beperkte bevoegdheid toe om gegevens te laden zonder dat er strikte CONTROL-machtigingen voor het datawarehouse nodig zijn
- Gebruik slechts één T-SQL-instructie zonder extra databaseobjecten te hoeven maken
- Maak gebruik van een nauwkeurig machtigingsmodel zonder opslagaccountsleutels weer te geven door Shared Access Signatures (SAS) te gebruiken
- Geef een ander opslagaccount op voor de locatie van de ERRORFILE (REJECTED_ROW_LOCATION)
- Pas standaardwaarden voor elke doelkolom aan en geef brongegevensvelden op die moeten worden geladen in specifieke doelkolommen
- Geef een aangepast eindteken voor rijen op voor CSV-bestanden
- Voeg een escape-teken toe voor scheidingstekens voor tekenreeksen, velden en rijen voor CSV-bestanden
- Gebruik SQL Server-datumnotaties voor CSV-bestanden
- Geef jokertekens en meerdere bestanden op in het pad naar de opslaglocatie

## <a name="prerequisites"></a>Vereisten

In deze snelstart wordt ervan uitgegaan dat u al een toegewezen SQL-pool hebt. Gebruik de quickstart [Maken en verbinding maken - portal](create-data-warehouse-portal.md) als er nog geen toegewezen SQL-pool is gemaakt.

## <a name="set-up-the-required-permissions"></a>De vereiste machtigingen instellen

```sql
-- List the permissions for your user
select  princ.name
,       princ.type_desc
,       perm.permission_name
,       perm.state_desc
,       perm.class_desc
,       object_name(perm.major_id)
from    sys.database_principals princ
left join
        sys.database_permissions perm
on      perm.grantee_principal_id = princ.principal_id
where name = '<yourusername>';

--Make sure your user has the permissions to CREATE tables in the [dbo] schema
GRANT CREATE TABLE TO <yourusername>;
GRANT ALTER ON SCHEMA::dbo TO <yourusername>;

--Make sure your user has ADMINISTER DATABASE BULK OPERATIONS permissions
GRANT ADMINISTER DATABASE BULK OPERATIONS TO <yourusername>

--Make sure your user has INSERT permissions on the target table
GRANT INSERT ON <yourtable> TO <yourusername>

```

## <a name="create-the-target-table"></a>De doeltabel maken

In dit voorbeeld worden gegevens uit de New York Taxi-gegevensset geladen. We laden de tabel met de naam Trip waarin taxiritten staan die gedurende één jaar werden gemaakt. Voer de volgende opdracht uit om de tabel te maken:

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>Voer de COPY-instructie uit

Voer de volgende COPY-instructie uit. Hierdoor worden gegevens uit het Azure Blob-opslagaccount in de tabel Trip geladen.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Het laden controleren

Controleer of het laden plaatsvindt door periodiek de volgende query uit te voeren:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Volgende stappen

- Zie [Aanbevolen procedures voor het laden van gegevens](./guidance-for-loading-data.md) voor aanbevolen procedures voor het laden van gegevens.
- Zie [Isolatie van workload](./quickstart-configure-workload-isolation-tsql.md) voor meer informatie over het beheren van de resources voor het laden van gegevens.