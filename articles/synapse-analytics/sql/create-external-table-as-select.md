---
title: Queryresultaten uit een serverloze SQL-pool opslaan
description: In dit artikel leert u hoe u queryresultaten opslaat met behulp van een serverloze SQL-pool.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: dd989d5925da864728e944e84962086c0cfb08ea
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462327"
---
# <a name="store-query-results-to-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Queryresultaten opslaan met behulp van een serverloze SQL-pool in Azure Synapse Analytics

In dit artikel leert u hoe u queryresultaten opslaat met behulp van een serverloze SQL-pool.

## <a name="prerequisites"></a>Vereisten

De eerste stap bestaat uit het **maken van een database** waarin u de query's gaat uitvoeren. Initialiseer vervolgens de objecten door een [installatiescript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op die database. Met dit installatiescript worden de gegevensbronnen, referenties voor het databasebereik en externe bestandsindelingen gemaakt die worden gebruikt om gegevens in deze voorbeelden te lezen.

Volg de instructies in dit artikel om gegevensbronnen, referenties voor het databasebereik en externe bestandsindelingen die worden gebruikt om gegevens in de uitvoeropslag te schrijven.

## <a name="create-external-table-as-select"></a>Create external table as select

U kunt de instructie CREATE EXTERNAL TABLE AS SELECT (CETAS) gebruiken om de queryresultaten op te slaan in de opslag.

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt.

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-the-external-table"></a>De externe tabel gebruiken

U kunt de externe tabel die is gemaakt via CETAS gebruiken als een gewone externe tabel.

> [!NOTE]
> Wijzig de eerste regel in de query, d.w.z. [mydbname], zodat u de database gebruikt die u hebt gemaakt.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Volgende stappen

Bekijk voor meer informatie over het uitvoeren van een query op verschillende bestandstypen de artikelen [Query's uitvoeren op één CSV-bestand](query-single-csv-file.md), [Query uitvoeren op Parquet-bestanden](query-parquet-files.md)en [Query's uitvoeren op JSON-bestanden](query-json-files.md).
