---
title: Query's uitvoeren op mappen en meerdere bestanden met serverloze SQL-groep
description: Een serverloze SQL-groep ondersteunt het lezen van meerdere bestanden/mappen met behulp van joker tekens, die vergelijkbaar zijn met de joker tekens die in Windows-besturings systeem worden gebruikt.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 83c4d88e1a87f6b546e26dd55da338a36f16ebe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462618"
---
# <a name="query-folders-and-multiple-files"></a>Query uitvoeren op mappen en meerdere bestanden  

In dit artikel leert u hoe u een query schrijft met behulp van serverloze SQL-pool in azure Synapse Analytics.

Een serverloze SQL-groep ondersteunt het lezen van meerdere bestanden/mappen met behulp van joker tekens, die vergelijkbaar zijn met de joker tekens die in Windows-besturings systeem worden gebruikt. Er is echter meer flexibiliteit aanwezig omdat meerdere joker tekens zijn toegestaan.

## <a name="prerequisites"></a>Vereisten

De eerste stap bestaat uit het **maken van een database** waarin u de query's gaat uitvoeren. Initialiseer vervolgens de objecten door een [installatiescript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op die database. Met dit installatie script worden de gegevens bronnen, referenties voor het data base-bereik en externe bestands indelingen gemaakt die in deze voor beelden worden gebruikt.

U gebruikt de map *CSV/taxi* om de voorbeeld query's te volgen. Het bevat NYC-Gelee taxi-excursie records van 2016 juli tot juni 2018. Bestanden in *CSV/taxi* worden na jaar en maand benoemd met het volgende patroon: yellow_tripdata_ <year> - <month> . CSV

## <a name="read-all-files-in-folder"></a>Alle bestanden in de map lezen

In het onderstaande voor beeld worden alle NYC Yellow Taxi-gegevens bestanden uit de map *CSV/taxi* gelezen en wordt het totale aantal passagiers en onderdrukkingen per jaar geretourneerd. Ook wordt het gebruik van statistische functies weer gegeven.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle bestanden die worden geopend met één OPENROWSET, moeten dezelfde structuur hebben (bijvoorbeeld het aantal kolommen en de bijbehorende gegevens typen).

### <a name="read-subset-of-files-in-folder"></a>Subset van bestanden in de map lezen

In het onderstaande voor beeld worden de 2017 gele taxi-gegevens bestanden uit de map *CSV/taxi* gelezen met behulp van een Joker teken en wordt het totale ritbedrag bedrag per betalings type geretourneerd.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Alle bestanden die worden geopend met één OPENROWSET, moeten dezelfde structuur hebben (bijvoorbeeld het aantal kolommen en de bijbehorende gegevens typen).

## <a name="read-folders"></a>Mappen lezen

Het pad dat u opgeeft voor OPENROWSET, kan ook een pad naar een map zijn. De volgende secties bevatten deze query typen.

### <a name="read-all-files-from-specific-folder"></a>Alle bestanden van een specifieke map lezen

U kunt alle bestanden in een map lezen met behulp van het Joker teken op bestands niveau, zoals wordt weer gegeven in [alle bestanden in de map lezen](#read-all-files-in-folder). Maar er is een manier om een query uit te stellen op een map en alle bestanden in die map te gebruiken.

Als het pad in OpenRowset naar een map verwijst, worden alle bestanden in die map gebruikt als bron voor uw query. Met de volgende query worden alle bestanden in de map *CSV/taxi* gelezen.

> [!NOTE]
> Let op het bestaan van de/aan het einde van het pad in de onderstaande query. Hiermee wordt een map aangeduid. Als de/wordt wegge laten, wordt de query gericht op een bestand met de naam *taxi* .

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle bestanden die worden geopend met één OPENROWSET, moeten dezelfde structuur hebben (bijvoorbeeld het aantal kolommen en de bijbehorende gegevens typen).

### <a name="read-all-files-from-multiple-folders"></a>Alle bestanden uit meerdere mappen lezen

Het is mogelijk om bestanden uit meerdere mappen te lezen met behulp van een Joker teken. Met de volgende query worden alle bestanden gelezen van alle mappen in de *CSV* -map die namen hebben die beginnen met *t* en eindigen met *i*.

> [!NOTE]
> Let op het bestaan van de/aan het einde van het pad in de onderstaande query. Hiermee wordt een map aangeduid. Als de/wordt wegge laten, wordt in de query het doel bestand met de naam *&ast; i* weer te richten.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle bestanden die worden geopend met één OPENROWSET, moeten dezelfde structuur hebben (bijvoorbeeld het aantal kolommen en de bijbehorende gegevens typen).

Omdat u slechts één map hebt die voldoet aan de criteria, is het resultaat van de query hetzelfde als [alle bestanden in de map lezen](#read-all-files-in-folder).

## <a name="traverse-folders-recursively"></a>Door mappen recursief door bladeren

Een serverloze SQL-groep kan mappen recursief door bladeren als u/* * aan het einde van het pad opgeeft. Met de volgende query worden alle bestanden van alle mappen en submappen in de *CSV* -map gelezen.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/**', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle bestanden die worden geopend met één OPENROWSET, moeten dezelfde structuur hebben (bijvoorbeeld het aantal kolommen en de bijbehorende gegevens typen).

## <a name="multiple-wildcards"></a>Meerdere joker tekens

U kunt meerdere joker tekens gebruiken op verschillende padvariabelen. U kunt bijvoorbeeld een eerdere query verrijken om bestanden met alleen 2017 gegevens te lezen, van alle mappen met een naam die begint met *t* en eindigt met *i*.

> [!NOTE]
> Let op het bestaan van de/aan het einde van het pad in de onderstaande query. Hiermee wordt een map aangeduid. Als de/wordt wegge laten, wordt in de query het doel bestand met de naam *&ast; i* weer te richten.
> Er is een maximum limiet van 10 joker tekens per query.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle bestanden die worden geopend met één OPENROWSET, moeten dezelfde structuur hebben (bijvoorbeeld het aantal kolommen en de bijbehorende gegevens typen).

Omdat u slechts één map hebt die voldoet aan de criteria, is het resultaat van de query hetzelfde als de [subset van bestanden in de map lezen](#read-subset-of-files-in-folder) en [alle bestanden uit een specifieke map lezen](#read-all-files-from-specific-folder). Complexere gebruiks scenario's voor joker tekens worden behandeld in [Parquet-bestanden van query's](query-parquet-files.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie vindt u in het artikel [query-specifieke bestanden](query-specific-files.md) .
