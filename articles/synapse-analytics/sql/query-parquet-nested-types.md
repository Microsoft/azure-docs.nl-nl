---
title: Parquet geneste typen doorzoeken met serverloze SQL-groep
description: In dit artikel leert u hoe u een query kunt uitvoeren op geneste Parquet-typen met behulp van serverloze SQL-pool.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 2ef547bea9ba84665ec7187a5adf2f7a7d87e0a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666327"
---
# <a name="query-nested-types-in-parquet-and-json-files-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Geneste typen in Parquet-en JSON-bestanden doorzoeken met serverloze SQL-groep in azure Synapse Analytics

In dit artikel leert u hoe u een query schrijft met behulp van serverloze SQL-pool in azure Synapse Analytics. Met de query worden geneste Parquet-typen gelezen.
Geneste typen zijn complexe structuren die objecten of matrices vertegenwoordigen. Geneste typen kunnen worden opgeslagen in: 
- [Parquet](query-parquet-files.md), waar u meerdere complexe kolommen kunt hebben die matrices en objecten bevatten.
- Hiërarchische [json-bestanden](query-json-files.md), waar u een complex JSON-document als één kolom kunt lezen.
- Azure Cosmos DB verzamelingen (momenteel in de open bare preview-fase), waarbij elk document complexe geneste eigenschappen kan bevatten.

Met serverloze SQL-pool worden alle geneste typen als JSON-objecten en-matrices opgemaakt. U kunt [complexe objecten dus extra heren of wijzigen met behulp van JSON-functies](/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) of JSON- [gegevens parseren met behulp van de openjson-functie](/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Hier volgt een voor beeld van een query waarmee scalaire en object waarden worden geëxtraheerd uit het JSON [-bestand COVID-19 open Research dataset](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) dat geneste objecten bevat: 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

De `JSON_VALUE` functie retourneert een scalaire waarde uit het veld op het opgegeven pad. De `JSON_QUERY` functie retourneert een object dat als JSON is opgemaakt vanuit het veld op het opgegeven pad.

> [!IMPORTANT]
> In dit voor beeld wordt een bestand gebruikt uit de open COVID-19-Zoek gegevensset. [Bekijk het certificaat en de structuur van de gegevens hier](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

## <a name="prerequisites"></a>Vereisten

De eerste stap is het maken van een Data Base waarin de gegevens bron wordt gemaakt. U initialiseert de objecten vervolgens door een [installatie script](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op de data base. Het installatie script maakt de gegevens bronnen, referenties voor database bereik en externe bestands indelingen die worden gebruikt in de voor beelden.

## <a name="project-nested-or-repeated-data"></a>Geneste of herhaalde gegevens projecteren

Een Parquet-bestand kan meerdere kolommen met complexe typen bevatten. De waarden van deze kolommen zijn opgemaakt als JSON-tekst en geretourneerd als VARCHAR kolommen. Met de volgende query leest u het bestand structExample. Parquet en ziet u hoe u de waarden van de geneste kolommen kunt lezen: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Met deze query wordt het volgende resultaat geretourneerd. De inhoud van elk genest object wordt geretourneerd als JSON-tekst.

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Datum": "2009-04-25"}| {"Tijd": "20:51:54.3598000"}|    {"Tijds tempel": "5501-04-08 12:13:57.4821000"}|    {"Decimaal": 11143412.25350}| {"Float": 0,5}|
|{"Datum": "1916-04-29"}| {"Tijd": "00:16:04.6778000"}|    {"Tijds tempel": "1990-06-30 20:50:52.6828000"}|    {"Decimaal": 1963545.62800}|  {"Float":-2,125}|

Met de volgende query wordt het bestand justSimpleArray. Parquet gelezen. Hiermee worden alle kolommen uit het Parquet-bestand, inclusief geneste en herhaalde gegevens, geprojecteerd.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Met deze query wordt het volgende resultaat geretourneerd:

|SimpleArray|
| --- |
|[11, 12, 13]|
|[21, 22, 23]|

## <a name="read-properties-from-nested-object-columns"></a>Eigenschappen van geneste object kolommen lezen

`JSON_VALUE`Met de functie kunt u waarden retour neren uit kolommen die zijn opgemaakt als JSON-tekst:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

Het resultaat wordt weer gegeven in de volgende tabel:

|titel  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Aanvullende informatie een eco-epidemiolo... | Julien   | -Afbeelding S1: Phylogeny van... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

In tegens telling tot JSON-bestanden, die in de meeste gevallen één kolom retour neren die een complex JSON-object bevat, kunnen Parquet-bestanden meerdere complexe kolommen hebben. U kunt de eigenschappen van geneste kolommen lezen met behulp `JSON_VALUE` van de functie voor elke kolom. `OPENROWSET` Hiermee kunt u de paden van de geneste eigenschappen in een `WITH` component rechtstreeks opgeven. U kunt de paden instellen als de naam van een kolom, maar u kunt ook een [JSON-padexpressie](/sql/relational-databases/json/json-path-expressions-sql-server) toevoegen achter het kolom Type.

Met de volgende query wordt het bestand structExample. Parquet gelezen en wordt weer gegeven hoe u de Opper vlakken van een geneste kolom laat zien. Er zijn twee manieren om te verwijzen naar een geneste waarde:
- Door de expressie pad naar geneste waarde op te geven na de type specificatie.
- Door de kolom naam als een genest pad op te maken, gebruikt u '. ' om naar de velden te verwijzen.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Elementen benaderen vanuit herhaalde kolommen

De volgende query leest het bestand justSimpleArray. Parquet en gebruikt [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) om een scalair element op te halen in een herhaalde kolom, zoals een matrix of kaart:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Dit is het resultaat:

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Onderliggende objecten openen vanuit complexe kolommen

Met de volgende query wordt het bestand mapExample. Parquet gelezen en wordt [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest&preserve-view=true) gebruikt om een niet-scalair element op te halen uit een herhaalde kolom, zoals een matrix of kaart:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

U kunt ook expliciet verwijzen naar de kolommen die u wilt retour neren in een `WITH` component:

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

De structuur `MapOfPersons` wordt geretourneerd als een varchar-kolom en opgemaakt als een JSON-teken reeks.

## <a name="project-values-from-repeated-columns"></a>Waarden van herhaalde kolommen projecteren

Als u een matrix met scalaire waarden (bijvoorbeeld `[1,2,3]` ) in sommige kolommen hebt, kunt u deze eenvoudig uitbreiden en samen voegen met de hoofd rij met behulp van dit script:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Volgende stappen

In het volgende artikel ziet u hoe u een [query uitvoert voor json-bestanden](query-json-files.md).