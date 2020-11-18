---
title: Weergaven maken en gebruiken in een serverloze SQL-pool (preview-versie)
description: In deze sectie leert u hoe u weergaven kunt maken en gebruiken in om query's voor serverloze SQL-pools (preview-versie) te verpakken. Weergaven stellen u in staat deze query's opnieuw te gebruiken. Weergaven zijn ook nodig als u hulpprogramma’s, zoals Power BI, wilt gebruiken samen met een serverloze SQL-pool.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 302cf97db7c1d2ba489a84b6be912816d20f6091
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685559"
---
# <a name="create-and-use-views-using-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Weergaven maken en gebruiken met behulp van een serverloze SQL-pool (preview-versie) in Azure Synapse Analytics

In deze sectie leert u hoe u weergaven kunt maken en gebruiken in om query's voor serverloze SQL-pools (preview-versie) te verpakken. Weergaven stellen u in staat deze query's opnieuw te gebruiken. Weergaven zijn ook nodig als u hulpprogramma’s, zoals Power BI, wilt gebruiken samen met een serverloze SQL-pool.

## <a name="prerequisites"></a>Vereisten

De eerste stap bestaat uit het maken van een database waar de weergave wordt gemaakt, en het initialiseren van de objecten die nodig zijn voor verificatie in Azure Storage door [setup script](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren voor deze database. Alle query's in dit artikel worden uitgevoerd op uw voorbeelddatabase.

## <a name="create-a-view"></a>Een weergave maken

U kunt weergaven op dezelfde manier maken als normale SQL Server-weergaven. Met de volgende query wordt een weergave gemaakt die het *population.csv*-bestand leest.

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt.

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

De weergave in dit voorbeeld maakt gebruik van de functie `OPENROWSET`. Deze functie gebruikt het absolute pad naar de onderliggende bestanden. Als u `EXTERNAL DATA SOURCE` hebt met een basis-URL van uw opslag, kunt u `OPENROWSET` gebruiken met `DATA_SOURCE` en een relatief bestandspad:

```sql
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>Een weergave gebruiken

U kunt weergaven in uw query's op dezelfde manier gebruiken als u weergaven in SQL Server-query's gebruikt.

Met de volgende query demonstreert het gebruik van de weergave *population_csv* die we hebben gemaakt bij [Een weergave maken](#create-a-view). Deze tabel retourneert de namen van landen/regio's met hun populatie in 2019 in aflopende volgorde.

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor informatie over het uitvoeren van een query op verschillende bestandstypen de artikelen [Query's uitvoeren op één CSV-bestand](query-single-csv-file.md), [Query uitvoeren op Parquet-bestanden](query-parquet-files.md)en [Query's uitvoeren op JSON-bestanden](query-json-files.md).
