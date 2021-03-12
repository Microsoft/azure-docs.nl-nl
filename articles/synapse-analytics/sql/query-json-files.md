---
title: JSON-bestanden bevragen met serverloze SQL-groep
description: In deze sectie wordt uitgelegd hoe u JSON-bestanden kunt lezen met serverloze SQL-pool in azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 5fcf688bbe8a5be2fc10b70950990b7b6ca71df8
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225588"
---
# <a name="query-json-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Een query uitvoeren op JSON-bestanden met serverloze SQL-groep in azure Synapse Analytics

In dit artikel leert u hoe u een query schrijft met behulp van serverloze SQL-pool in azure Synapse Analytics. Het doel van de query is het lezen van JSON-bestanden met [OPENrowset](develop-openrowset.md). 
- Standaard JSON-bestanden waarin meerdere JSON-documenten worden opgeslagen als een JSON-matrix.
- Door line gescheiden JSON-bestanden, waarbij JSON-documenten worden gescheiden met een nieuwe-regel teken. Algemene uitbrei dingen voor deze typen bestanden zijn `jsonl` , `ldjson` en `ndjson` .

## <a name="read-json-documents"></a>JSON-documenten lezen

De eenvoudigste manier om de inhoud van uw JSON-bestand te bekijken, is door de URL van het bestand op te geven voor de `OPENROWSET` functie, CSV op te geven `FORMAT` en waarden `0x0b` in te stellen voor `fieldterminator` en `fieldquote` . Als u JSON-bestanden met regel scheiding wilt lezen, is dit voldoende. Als u een klassiek JSON-bestand hebt, moet u waarden instellen `0x0b` voor `rowterminator` . `OPENROWSET` de functie parseert JSON en retourneert elk document in de volgende indeling:

| documenten |
| --- |
|{"date_rep": "2020-07-24", "dag": 24, "maand": 7, "jaar": 2020, "cases": 3, "sterf": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-25", "dag": 25, "maand": 7, "jaar": 2020, "cases": 7, "sterf": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-26", "dag": 26, "maand": 7, "jaar": 2020, "cases": 4, "sterf": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-27", "dag": 27, "maand": 7, "jaar": 2020, "cases": 8, "sterf": 0, "geo_id": "AF"}|

Als het bestand openbaar beschikbaar is, of als uw Azure AD-identiteit toegang heeft tot dit bestand, ziet u de inhoud van het bestand met behulp van de query zoals die in de volgende voor beelden wordt weer gegeven.

### <a name="read-json-files"></a>JSON-bestanden lezen

Met de volgende voorbeeld query worden JSON-en met regels gescheiden JSON-bestanden gelezen en wordt elk document als een afzonderlijke rij geretourneerd.

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Het JSON-document in de voor gaande voorbeeld query bevat een matrix met objecten. De query retourneert elk object als een afzonderlijke rij in de resultatenset. Zorg ervoor dat u toegang tot dit bestand hebt. Als uw bestand is beveiligd met een SAS-sleutel of aangepaste identiteit, moet u de [referentie op server niveau voor SQL-aanmelding](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)instellen. 

### <a name="data-source-usage"></a>Gebruik van gegevens bronnen

In het vorige voor beeld wordt het volledige pad naar het bestand gebruikt. Als alternatief kunt u een externe gegevens bron maken met de locatie die verwijst naar de hoofdmap van de opslag en die gegevens bron en het relatieve pad naar het bestand in de `OPENROWSET` functie gebruiken:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Als een gegevens bron wordt beveiligd met een SAS-sleutel of aangepaste identiteit, kunt u de [gegevens bron configureren met de referentie data base-Scope](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

In de volgende secties ziet u hoe u verschillende typen JSON-bestanden kunt opvragen.

## <a name="parse-json-documents"></a>JSON-documenten parseren

De query's in de voor gaande voor beelden retour neren elk JSON-document als één teken reeks in een afzonderlijke rij van de resultatenset. U kunt functies gebruiken `JSON_VALUE` en `OPENJSON` de waarden in JSON-documenten parseren en retour neren als relationele waarden, zoals wordt weer gegeven in het volgende voor beeld:

| datum \_ rep | meldingen | Geo \_ -id |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>Voor beeld van JSON-document

De query voorbeelden lezen *JSON* -bestanden met documenten met de volgende structuur:

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> Als deze documenten worden opgeslagen als een door een regel gescheiden JSON, moet u instellen `FIELDTERMINATOR` en `FIELDQUOTE` 0x0ben. Als u een standaard JSON-indeling hebt, moet u deze instellen `ROWTERMINATOR` op 0x0B.

### <a name="query-json-files-using-json_value"></a>Een query uitvoeren op JSON-bestanden met JSON_VALUE

De onderstaande query laat zien hoe u [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) kunt gebruiken om scalaire waarden ( `date_rep` , `countries_and_territories` ,) op te halen `cases` uit een JSON-document:

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    CAST(JSON_VALUE(doc, '$.deaths') AS INT) as fatal,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

Als u JSON-eigenschappen uit een JSON-document hebt geëxtraheerd, kunt u kolom aliassen definiëren en eventueel de tekstuele waarde naar een bepaald type converteren.

### <a name="query-json-files-using-openjson"></a>Een query uitvoeren op JSON-bestanden met openjson

De volgende query maakt gebruik van [openjson](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest&preserve-view=true). Er worden COVID statistieken opgehaald die zijn gerapporteerd in Servië:

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```
De resultaten zijn functioneel hetzelfde als de resultaten die zijn geretourneerd met behulp van de `JSON_VALUE` functie. In sommige gevallen `OPENJSON` kan het voor deel zijn van `JSON_VALUE` :
- In de `WITH` component kunt u de kolom aliassen en de typen voor elke eigenschap expliciet instellen. U hoeft de `CAST` functie niet in elke kolom in de lijst te plaatsen `SELECT` .
- `OPENJSON` kan sneller zijn als u een groot aantal eigenschappen retourneert. Als u slechts 1-2 eigenschappen retourneert, is de `OPENJSON` functie mogelijk overhead.
- U moet de `OPENJSON` functie gebruiken als u de matrix van elk document wilt parseren en deze wilt koppelen aan de bovenliggende rij.

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen in deze reeks wordt uitgelegd hoe u:

- [Query's uitvoeren op mappen en meerdere bestanden](query-folders-multiple-csv-files.md)
- [Weergaven maken en gebruiken](create-use-views.md)
