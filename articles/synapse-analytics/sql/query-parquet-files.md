---
title: Parquet-bestanden doorzoeken met serverloze SQL-groep
description: In dit artikel leert u hoe u een query kunt uitvoeren op Parquet-bestanden met serverloze SQL-pool.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: cce4c6aff986c2e8c3d879d962714e13f6b2e7ae
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694676"
---
# <a name="query-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Parquet-bestanden doorzoeken met serverloze SQL-pool in azure Synapse Analytics

In dit artikel leert u hoe u een query schrijft met behulp van een serverloze SQL-groep die Parquet-bestanden gaat lezen.

## <a name="quickstart-example"></a>Quick start-voor beeld

`OPENROWSET` met de functie kunt u de inhoud van het Parquet-bestand lezen door de URL naar uw bestand op te geven.

### <a name="read-parquet-file"></a>Parquet-bestand lezen

De eenvoudigste manier om de inhoud van uw bestand te bekijken is door de `PARQUET` bestands-URL te leveren `OPENROWSET` en Parquet op te geven `FORMAT` . Als het bestand openbaar beschikbaar is of als uw Azure AD-identiteit toegang heeft tot dit bestand, moet u de inhoud van het bestand kunnen zien met behulp van de query zoals die in het volgende voor beeld wordt weer gegeven:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

Zorg ervoor dat u toegang tot dit bestand hebt. Als uw bestand is beveiligd met een SAS-sleutel of een aangepaste Azure-identiteit, moet u de [referentie voor het server niveau voor SQL-aanmelding](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)instellen.

> [!IMPORTANT]
> Zorg ervoor dat u een UTF-8-database sortering gebruikt, `Latin1_General_100_BIN2_UTF8` omdat de teken reeks waarden in PARQUET-bestanden worden gecodeerd met UTF-8-code ring.
> Het verschil tussen de tekst codering in het PARQUET-bestand en de sortering kan onverwachte conversie fouten tot gevolg hebben.
> U kunt eenvoudig de standaard sortering van de huidige data base wijzigen met behulp van de volgende T-SQL-instructie: `alter database current collate Latin1_General_100_BIN2_UTF8`

### <a name="data-source-usage"></a>Gebruik van gegevens bronnen

In het vorige voor beeld wordt het volledige pad naar het bestand gebruikt. Als alternatief kunt u een externe gegevens bron maken met de locatie die naar de hoofdmap van de opslag verwijst en die gegevens bron en het relatieve pad naar het bestand in `OPENROWSET` functie gebruiken:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

Als een gegevens bron wordt beveiligd met een SAS-sleutel of aangepaste identiteit, kunt u de [gegevens bron configureren met de referentie data base-Scope](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Expliciet schema opgeven

`OPENROWSET` Hiermee kunt u expliciet opgeven welke kolommen u wilt lezen uit het bestand met behulp van de `WITH` component:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

> [!IMPORTANT]
> Zorg ervoor dat u explicilty een UTF-8-sortering opgeeft (bijvoorbeeld `Latin1_General_100_BIN2_UTF8` ) voor alle teken reeks kolommen in- `WITH` component of een bepaalde UTF-8-sortering op database niveau instellen.
> De tekst codering in het bestand en de sortering van de teken reeks kolom kan onverwachte conversie fouten tot gevolg hebben.
> U kunt eenvoudig de standaard sortering van de huidige data base wijzigen met behulp van de volgende T-SQL-instructie: `alter database current collate Latin1_General_100_BIN2_UTF8`
> U kunt de sortering eenvoudig instellen voor de kolom typen met behulp van de volgende definitie: `geo_id varchar(6) collate Latin1_General_100_BIN2_UTF8`

In de volgende secties ziet u hoe u verschillende typen PARQUET-bestanden kunt opvragen.

## <a name="prerequisites"></a>Vereisten

De eerste stap bestaat uit het **maken van een Data Base** met een gegevens bron die verwijst naar een [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) -opslag account. Initialiseer vervolgens de objecten door een [installatiescript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op die database. Met dit installatie script worden de gegevens bronnen, referenties voor het data base-bereik en externe bestands indelingen gemaakt die in deze voor beelden worden gebruikt.

## <a name="dataset"></a>Gegevensset

In dit voor beeld wordt een [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) -gegevensset gebruikt. U kunt een query uitvoeren op Parquet-bestanden op dezelfde manier als bij het [lezen van CSV-bestanden](query-parquet-files.md). Het enige verschil is dat de `FILEFORMAT` para meter moet worden ingesteld op `PARQUET` . In de voor beelden in dit artikel ziet u de specifieke informatie over het lezen van Parquet-bestanden.

## <a name="query-set-of-parquet-files"></a>Query set van Parquet-bestanden

U kunt alleen de interessante kolommen opgeven wanneer u Parquet-bestanden opvraagt.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Automatische schemadeductie

U hoeft de component OPENROWSET WITH niet te gebruiken bij het lezen van Parquet-bestanden. Kolom namen en gegevens typen worden automatisch uit Parquet-bestanden gelezen.

In het onderstaande voor beeld ziet u de mogelijkheden voor het automatisch afnemen van schema's voor Parquet-bestanden. Het retourneert het aantal rijen in september 2017 zonder een schema op te geven.

> [!NOTE]
> U hoeft geen kolommen op te geven in de component OPENROWSET WITH bij het lezen van Parquet-bestanden. In dat geval gebruiken de query service voor serverloze SQL-Pools meta gegevens in het Parquet-bestand en bindt kolommen op naam.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Gepartitioneerde gegevens opvragen

De gegevensset die in dit voor beeld wordt gegeven, wordt in afzonderlijke submappen verdeeld (gepartitioneerd). U kunt met behulp van de functie filepath specifieke partities richten. In dit voor beeld worden de ritbedrag bedragen per jaar, maand en payment_type voor de eerste drie maanden van 2017 weer gegeven.

> [!NOTE]
> De query van de serverloze SQL-groep is compatibel met het Hive/Hadoop-partitie schema.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>Type toewijzing

Voor het Parquet-type toewijzing aan het type toewijzing van SQL Native type is [toegewezen voor Parquet](develop-openrowset.md#type-mapping-for-parquet).

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het [opvragen van Parquet-geneste typen](query-parquet-nested-types.md).
