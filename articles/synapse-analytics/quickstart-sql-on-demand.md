---
title: 'Quickstart: Serverloze SQL-pools gebruiken'
description: In deze quickstart ziet en leert u hoe u eenvoudig een query kunt uitvoeren op verschillende bestandstypen, met behulp van een serverloze SQL-pool.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 8607355069bbae5983239ddbd3e8752143f31497
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676334"
---
# <a name="quickstart-use-serverless-sql-pool"></a>Quickstart: Serverloze SQL-pools gebruiken

Een serverloze Synapse SQL-pool is een serverloze queryservice waarmee u SQL-query's kunt uitvoeren op bestanden die zich bevinden in Azure Storage. In deze quickstart leert u hoe u een query kunt uitvoeren op verschillende typen bestanden, met behulp van een serverloze SQL-pool. De ondersteunde indelingen worden weergegeven in [OPENROWSET](sql/develop-openrowset.md).

In deze quickstart leert u hoe u query's uitvoert op: CSV-, Apache Parquet- en JSON-bestanden.

## <a name="prerequisites"></a>Vereisten

Kies een SQL-client waarmee u query's wilt uitvoeren:

- [Azure Synapse Studio](./get-started-create-workspace.md) is een webhulpprogramma waarmee u kunt zoeken naar bestanden in opslag en SQL-query's kunt maken.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) is een clienthulpprogramma waarmee u SQL-query's en notebooks kunt uitvoeren op uw on-demand database.
- [SQL Server Management Studio](sql/get-started-ssms.md) is een clienthulpprogramma waarmee u SQL-query's kunt uitvoeren op uw on-demand database.

Parameters voor deze quickstart:

| Parameter                                 | Beschrijving                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| eindpuntadres van de service voor serverloze SQL-pools    | Gebruikt als servernaam                                   |
| eindpuntregio van de service voor serverloze SQL-pools     | Wordt gebruikt om te bepalen welke opslag wordt gebruikt in voorbeelden |
| Gebruikersnaam en wachtwoord voor eindpunttoegang | Gebruikt voor toegang tot het eindpunt                               |
| De database die wordt gebruikt voor het maken van weergaven         | De database die wordt gebruikt als uitgangspunt in voorbeelden       |

## <a name="first-time-setup"></a>De eerste installatie

Voordat u de voorbeelden gaat gebruiken, moet u het volgende doen:

- Maak een database voor uw weergaven (wanneer u gebruik wilt maken van weergaven)
- Referenties maken die een serverloze SQL-pool kan gebruiken voor toegang tot bestanden in opslag

### <a name="create-database"></a>Database maken

Maak uw eigen database voor demonstratiedoeleinden. U gebruikt deze database om uw weergaven te maken en voor de voorbeeldquery's in dit artikel.

> [!NOTE]
> De databases worden alleen gebruikt voor het weergeven van metagegevens, niet voor de werkelijke gegevens.
>Noteer de naam van de database die u verderop in de quickstart gaat gebruiken.

Gebruik de volgende query om `mydbname` te wijzigen in een naam van uw keuze:

```sql
CREATE DATABASE mydbname
```

### <a name="create-data-source"></a>Gegevensbron maken

Als u query's wilt uitvoeren met behulp van een serverloze SQL-pool, maakt u een gegevensbron die de serverloze SQL-pool kan gebruiken voor toegang tot bestanden in de opslag.
Voer het volgende codefragment uit om de gegevensbron te maken die in de voorbeelden in deze sectie wordt gebruikt:

```sql
-- create master key that will protect the credentials:
CREATE MASTER KEY ENCRYPTION BY PASSWORD = <enter very strong password here>

-- create credentials for containers in our demo storage account
CREATE DATABASE SCOPED CREDENTIAL sqlondemand
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

## <a name="query-csv-files"></a>Query uitvoeren op CSV-bestanden

De volgende afbeelding is een voorbeeld van het bestand waarop een query moet worden uitgevoerd:

![Eerste tien rijen van het CSV-bestand zonder koptekst, nieuwe regel in Windows-stijl.](./sql/media/query-single-csv-file/population.png)

De volgende query laat zien hoe u een CSV-bestand leest dat geen headerrij bevat, met een nieuwe regel in Windows-stijl en door komma's gescheiden kolommen:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'csv/population/*.csv',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT = 'CSV', PARSER_VERSION = '2.0'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

U kunt het schema opgeven bij het compileren van de query.
Zie [Query uitvoeren op CSV-bestand](sql/query-single-csv-file.md)voor meer voorbeelden.

## <a name="query-parquet-files"></a>Query uitvoeren op Parquet-bestanden

In het volgende voorbeeld ziet u de mogelijkheden voor het automatisch afleiden van schema's voor het uitvoeren van query's op Parquet-bestanden. Hierbij wordt het aantal rijen in september 2017 geretourneerd zonder een schema op te geven.

> [!NOTE]
> U hoeft geen kolommen op te geven in de component `OPENROWSET WITH` bij het lezen van Parquet-bestanden. In dit geval maakt de serverloze SQL-pool gebruik van metagegevens in het Parquet-bestand, en worden kolommen gekoppeld op naam.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'parquet/taxi/year=2017/month=9/*.parquet',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT='PARQUET'
  ) AS nyc
```

Zie [Query uitvoeren op Parquet-bestanden](sql/query-parquet-files.md) voor meer informatie.

## <a name="query-json-files"></a>Query uitvoeren op JSON-bestanden

### <a name="json-sample-file"></a>JSON-voorbeeldbestand

Bestanden worden opgeslagen in de *json*-container en de map *boeken* en bevatten één boekvermelding met de volgende structuur:

```json
{  
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[  
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

### <a name="query-json-files"></a>Query uitvoeren op JSON-bestanden

De volgende query laat zien hoe u [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) kunt gebruiken om scalaire waarden (titel, uitgever) op te halen uit een boek met de titel *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected articles*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'json/books/*.json',
      DATA_SOURCE = 'SqlOnDemandDemo'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> Het gehele JSON-bestand wordt als één rij/kolom gelezen. FIELDTERMINATOR, FIELDQUOTE en ROWTERMINATOR zijn daarom ingesteld op 0x0B, omdat deze naar verwachting niet in het bestand worden gevonden.

## <a name="next-steps"></a>Volgende stappen

U kunt nu doorgaan met de volgende artikelen:

- [Query's uitvoeren op één CSV-bestand](sql/query-single-csv-file.md)
- [Query's uitvoeren op mappen en meerdere CSV-bestanden](sql/query-folders-multiple-csv-files.md)
- [Query's uitvoeren op specifieke bestanden](sql/query-specific-files.md)
- [Query's uitvoeren op Parquet-bestanden](sql/query-parquet-files.md)
- [Query uitvoeren op met Parquet geneste typen](sql/query-parquet-nested-types.md)
- [Query's uitvoeren op JSON-bestanden](sql/query-json-files.md)
- [Weergaven maken en gebruiken](sql/create-use-views.md)
- [Externe tabellen maken en gebruiken](sql/create-use-external-tables.md)
- [Queryresultaten behouden in Azure Storage](sql/create-external-table-as-select.md)
- [Query's uitvoeren op één CSV-bestand](sql/query-single-csv-file.md)