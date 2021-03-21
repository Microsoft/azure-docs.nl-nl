---
title: Externe tabellen maken en gebruiken in een serverloze SQL-pool
description: In deze sectie leert u hoe u externe tabellen kunt maken en gebruiken in een serverloze SQL-pool.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 5dcd4b7b76752ca5396fc68afc8d4c8e4e1edca5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462307"
---
# <a name="create-and-use-external-tables-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Externe tabellen maken en gebruiken met behulp van een serverloze SQL-pool in Azure Synapse Analytics

In deze sectie leert u hoe u [externe tabellen](develop-tables-external-tables.md) kunt maken en gebruiken in een serverloze SQL-pool. Externe tabellen zijn handig als u de toegang tot externe gegevens in serverloze SQL-pools wilt beheren, en als u hulpprogramma's, zoals Power BI, wilt gebruiken in combinatie met een serverloze SQL-pool. Externe tabellen hebben toegang tot twee soorten opslag:
- Openbare opslag waar gebruikers toegang hebben tot openbare opslagbestanden.
- Beveiligde opslag waar gebruikers toegang hebben tot opslagbestanden met behulp van SAS-referenties, een Azure AD-identiteit of een beheerde identiteit van een Synapse-werkruimte.

## <a name="prerequisites"></a>Vereisten

De eerste stap bestaat uit het maken van een database waarin de tabellen worden gemaakt. Initialiseer vervolgens de objecten door een [installatiescript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op die database. Met dit installatiescript worden de volgende objecten gemaakt die in dit voorbeeld worden gebruikt:
- REFERENTIES VAN DATABASECONFIGURATIE `sqlondemand` waarmee toegang tot een met SAS beveiligde `https://sqlondemandstorage.blob.core.windows.net` Azure-opslagaccount mogelijk wordt.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- EXTERNE GEGEVENSBRON `sqlondemanddemo` die verwijst naar een demo-opslagaccount dat wordt beveiligd met een SAS-sleutel en EXTERNE GEGEVENSBRON `YellowTaxi` die verwijst naar een openbaar beschikbaar Azure-opslagaccount op locatie `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/`.

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- De bestandsindelingen `QuotedCSVWithHeaderFormat` en `ParquetFormat` die bestandstypen van het type CSV en Parquet beschrijven.

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

De query's in dit artikel worden uitgevoerd op uw voorbeelddatabase en gebruiken deze objecten. 

## <a name="create-an-external-table-on-protected-data"></a>Een externe tabel maken van beveiligde gegevens

U kunt externe tabellen maken die toegang hebben tot gegevens in een Azure-opslagaccount waarmee gebruikers met een bepaalde Azure AD-identiteit of SAS-sleutel toegang kunnen krijgen. U kunt externe tabellen op dezelfde manier maken als gewone externe tabellen van SQL Server. 

Met de volgende query maakt u een externe tabel die het bestand *population.csv* van het SynapseSQL Azure demo-opslagaccount leest waarnaar wordt verwezen met behulp van de gegevensbron `sqlondemanddemo` en die wordt beveiligd met databaseconfiguratiereferenties met de naam `sqlondemand`. 

De referenties voor de gegevensbron en de databaseconfiguratie zijn gemaakt in het [installatiescript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql).

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt. 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>Een externe tabel maken van openbare gegevens

U kunt externe tabellen maken waarmee gegevens worden gelezen van de bestanden die in openbaar beschikbare Azure-opslag zijn geplaatst. Met dit [installatiescript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) maakt u een openbare externe gegevensbron en Parquet-bestandsindelingsdefinitie die wordt gebruikt in de volgende query:

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```
## <a name="use-an-external-table"></a>Een externe tabel gebruiken

U kunt [externe tabellen](develop-tables-external-tables.md) in uw query's op dezelfde manier gebruiken als u deze in SQL Server-query's gebruikt.

Met de volgende query wordt dit gedemonstreerd met behulp van de externe tabel *population* die we in de vorige sectie hebben gemaakt. Deze tabel retourneert de namen van landen/regio's met hun populatie in 2019 in aflopende volgorde.

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het artikel [Queryresultaten opslaan in de opslag](../sql/create-external-table-as-select.md) voor informatie over het opslaan van resultaten van een query in de opslag.
