---
title: CSV-bestanden doorzoeken met serverloze SQL-groep
description: In dit artikel leert u hoe u met een serverloze SQL-groep een query kunt uitvoeren op enkelvoudige CSV-bestanden met verschillende bestands indelingen.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: f2f0cdf307e91fb40c55d4a98139bad1a5eca886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462587"
---
# <a name="query-csv-files"></a>Query uitvoeren op CSV-bestanden

In dit artikel leert u hoe u een query kunt uitvoeren op één CSV-bestand met serverloze SQL-groep in azure Synapse Analytics. CSV-bestanden hebben mogelijk verschillende indelingen: 

- Met en zonder een veldnamenrij
- Door komma's en tabs gescheiden waarden
- Windows-en UNIX-stijl regel eindigt
- Niet-geciteerde en niet-geciteerde waarden en Escape tekens

Alle bovenstaande variaties worden hieronder besproken.

## <a name="quickstart-example"></a>Quick start-voor beeld

`OPENROWSET` met de functie kunt u de inhoud van een CSV-bestand lezen door de URL naar uw bestand op te geven.

### <a name="read-a-csv-file"></a>Een CSV-bestand lezen

De eenvoudigste manier om de inhoud van uw bestand te bekijken `CSV` is door de bestands-URL te laten `OPENROWSET` functioneren, csv en 2,0 op te geven `FORMAT` `PARSER_VERSION` . Als het bestand openbaar beschikbaar is of als uw Azure AD-identiteit toegang heeft tot dit bestand, moet u de inhoud van het bestand kunnen zien met behulp van de query zoals die in het volgende voor beeld wordt weer gegeven:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    format = 'csv',
    parser_version = '2.0',
    firstrow = 2 ) as rows
```

Wordt `firstrow` gebruikt voor het overs laan van de eerste rij in het CSV-bestand dat de koptekst in dit geval vertegenwoordigt. Zorg ervoor dat u toegang tot dit bestand hebt. Als uw bestand is beveiligd met een SAS-sleutel of aangepaste identiteit, moet u de [referenties voor SQL-aanmelding op server niveau](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)instellen.

> [!IMPORTANT]
> Als uw CSV-bestand UTF-8-tekens bevat, moet u ervoor zorgen dat u een UTF-8-database sortering gebruikt (bijvoorbeeld `Latin1_General_100_CI_AS_SC_UTF8` ).
> Het verschil tussen tekst codering in het bestand en de sortering kan onverwachte conversie fouten tot gevolg hebben.
> U kunt eenvoudig de standaard sortering van de huidige data base wijzigen met behulp van de volgende T-SQL-instructie: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

### <a name="data-source-usage"></a>Gebruik van gegevens bronnen

In het vorige voor beeld wordt het volledige pad naar het bestand gebruikt. Als alternatief kunt u een externe gegevens bron maken met de locatie die verwijst naar de hoofdmap van de opslag:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
```

Zodra u een gegevens bron hebt gemaakt, kunt u deze gegevens bron en het relatieve pad naar het bestand in `OPENROWSET` functie gebruiken:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) as rows
```

Als een gegevens bron wordt beveiligd met een SAS-sleutel of aangepaste identiteit, kunt u de [gegevens bron configureren met de referentie data base-Scope](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Expliciet schema opgeven

`OPENROWSET` Hiermee kunt u expliciet opgeven welke kolommen u wilt lezen uit het bestand met behulp van de `WITH` component:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) with (
        date_rep date 1,
        cases int 5,
        geo_id varchar(6) 8
    ) as rows
```

De getallen na een gegevens type in de `WITH` component vertegenwoordigen kolom index in het CSV-bestand.

> [!IMPORTANT]
> Als uw CSV-bestand UTF-8-tekens bevat, moet u ervoor zorgen dat u explicilty een UTF-8-sortering opgeeft (bijvoorbeeld `Latin1_General_100_CI_AS_SC_UTF8` ) voor alle kolommen in- `WITH` component of een bepaalde UTF-8-sortering op database niveau instellen.
> De tekst codering in het bestand en de sortering kunnen onverwachte conversie fouten tot gevolg hebben.
> U kunt eenvoudig de standaard sortering van de huidige data base wijzigen met behulp van de volgende T-SQL-instructie: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`
> U kunt de sortering eenvoudig instellen voor de kolom typen met behulp van de volgende definitie: `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8 8`

In de volgende secties ziet u hoe u verschillende typen CSV-bestanden kunt opvragen.

## <a name="prerequisites"></a>Vereisten

De eerste stap is het **maken van een Data Base** waarin de tabellen worden gemaakt. Initialiseer vervolgens de objecten door een [installatiescript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op die database. Met dit installatie script worden de gegevens bronnen, referenties voor het data base-bereik en externe bestands indelingen gemaakt die in deze voor beelden worden gebruikt.

## <a name="windows-style-new-line"></a>Windows-stijl nieuwe regel

De volgende query laat zien hoe u een CSV-bestand zonder een koprij, met een nieuwe Windows-stijl en door komma's gescheiden kolommen kunt lezen.

Bestands voorbeeld:

![Eerste tien rijen van het CSV-bestand zonder koptekst, nieuwe regel in Windows-stijl.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>UNIX-stijl nieuwe regel

De volgende query laat zien hoe u een bestand zonder koprij, met een nieuwe UNIX-stijl en door komma's gescheiden kolommen kunt lezen. Let op de verschillende locatie van het bestand in vergelijking met de andere voor beelden.

Bestands voorbeeld:

![De eerste tien rijen van het CSV-bestand zonder veldnamenrij en met Unix-Style nieuwe regel.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Veldnamenrij

De volgende query laat zien hoe een bestand wordt gelezen met een veldnamenrij, met een nieuwe regel in UNIX-stijl en door komma's gescheiden kolommen. Let op de verschillende locatie van het bestand in vergelijking met de andere voor beelden.

Bestands voorbeeld:

![De eerste tien rijen van het CSV-bestand met veldnamenrij en met Unix-Style nieuwe regel.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Aangepast aanhalings teken

De volgende query laat zien hoe u een bestand met een veldnamenrij kunt lezen met een nieuwe UNIX-stijl, door komma's gescheiden kolommen en geciteerde waarden. Let op de verschillende locatie van het bestand in vergelijking met de andere voor beelden.

Bestands voorbeeld:

![Eerste tien rijen van het CSV-bestand met veldnamenrij en met Unix-Style nieuwe regel en geciteerde waarden.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Deze query retourneert dezelfde resultaten als u de para meter FIELDQUOTE hebt wegge laten, omdat de standaard waarde voor FIELDQUOTE een dubbele aanhalings tekens is.

## <a name="escape-characters"></a>Escape tekens

De volgende query laat zien hoe u een bestand met een veldnamenrij kunt lezen met een nieuwe UNIX-stijl, door komma's gescheiden kolommen en een escape teken dat wordt gebruikt voor het veld scheidings teken (komma) in waarden. Let op de verschillende locatie van het bestand in vergelijking met de andere voor beelden.

Bestands voorbeeld:

![Eerste tien rijen van het CSV-bestand met veldnamenrij en met Unix-Style nieuwe regel en escape-teken gebruikt voor veld scheidings tekens.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Deze query mislukt als ESCAPECHAR niet is opgegeven omdat de komma in "slov, enia" als veld scheidings teken wordt beschouwd in plaats van een deel van de naam van het land/de regio. "Slov, enia" worden beschouwd als twee kolommen. Daarom zou de betreffende rij een kolom meer hebben dan de andere rijen en één kolom meer dan u hebt gedefinieerd in de WITH-component.

### <a name="escape-quoting-characters"></a>Aanhalings tekens als escape-teken

De volgende query laat zien hoe u een bestand met een veldnamenrij kunt lezen met een nieuwe UNIX-stijl, door komma's gescheiden kolommen en een dubbele aanhalings tekens in waarden. Let op de verschillende locatie van het bestand in vergelijking met de andere voor beelden.

Bestands voorbeeld:

![De volgende query laat zien hoe u een bestand met een veldnamenrij kunt lezen met een nieuwe UNIX-stijl, door komma's gescheiden kolommen en een dubbele aanhalings tekens in waarden.](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Het aanhalingsteken moet worden a”gesloten door een ander aanhalingsteken. Een aanhalingsteken kan alleen in een kolomwaarde worden weer gegeven als de waarde tussen aanhalingstekens staat.

## <a name="tab-delimited-files"></a>Door tabs gescheiden bestanden

De volgende query laat zien hoe u een bestand met een veldnamenrij, met een nieuwe regel in UNIX-stijl en door tabs gescheiden kolommen kunt lezen. Let op de verschillende locatie van het bestand in vergelijking met de andere voor beelden.

Bestands voorbeeld:

![De eerste tien rijen van het CSV-bestand met veldnamenrij en met Unix-Style nieuwe regel en scheidings teken.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="return-a-subset-of-columns"></a>Een subset van kolommen retour neren

Tot nu toe hebt u het CSV-bestands schema opgegeven met behulp van en een lijst met alle kolommen. U kunt alleen kolommen opgeven die u daad werkelijk nodig hebt in uw query door gebruik te maken van een rang nummer voor elke gewenste kolom. U kunt ook kolommen zonder interesse weglaten.

Met de volgende query wordt het aantal afzonderlijke land/regio namen in een bestand geretourneerd, waarbij alleen de benodigde kolommen worden opgegeven:

> [!NOTE]
> Bekijk de WITH-component in de query hieronder en houd er rekening mee dat er ' 2 ' (zonder aanhalings tekens) aan het einde van de rij is waar u de kolom *[country_name]* definieert. Dit betekent dat de kolom *[country_name]* de tweede kolom in het bestand is. Met de query worden alle kolommen in het bestand genegeerd, met uitzonde ring van de tweede.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen ziet u hoe u:

- [Query's uitvoeren op Parquet-bestanden](query-parquet-files.md)
- [Query's uitvoeren op mappen en meerdere bestanden](query-folders-multiple-csv-files.md)
