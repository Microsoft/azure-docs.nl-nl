---
title: Meta gegevens van bestanden in query's gebruiken
description: De functie OPENROWSET bevat bestands-en padgegevens over elk bestand dat in de query wordt gebruikt om gegevens te filteren of te analyseren op basis van de bestands naam en/of het pad van de map.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d24ae1f42c685589309506b2d5e0eab157b2bc42
ms.sourcegitcommit: 5e2f5efba1957ba40bd951c3dcad42f4a00734ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2020
ms.locfileid: "96299612"
---
# <a name="use-file-metadata-in-serverless-sql-pool-queries"></a>Meta gegevens van een bestand in serverloze SQL-pool query's gebruiken

Een serverloze SQL-pool kan meerdere bestanden en mappen adresseren zoals beschreven in het artikel [query mappen en meerdere bestanden](query-folders-multiple-csv-files.md) . In dit artikel vindt u informatie over het gebruik van meta gegevens over bestands-en mapnamen in de query's.

Het kan voor komen dat u moet weten welk bestands-of bronmap-bron overeenkomt met een specifieke rij in de resultatenset.

U kunt de functie gebruiken `filepath` en `filename` bestands namen en/of het pad in de resultatenset retour neren. U kunt ze ook gebruiken om gegevens te filteren op basis van de bestands naam en/of het pad van de map. Deze functies worden beschreven in de syntaxis sectie [Bestands naam functie](query-data-storage.md#filename-function) en [filepath-functie](query-data-storage.md#filepath-function). In de volgende secties vindt u korte beschrijvingen en voor beelden.

## <a name="prerequisites"></a>Vereisten

De eerste stap bestaat uit het **maken van een Data Base** met een gegevens bron die verwijst naar het opslag account. Initialiseer vervolgens de objecten door een [installatiescript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) uit te voeren op die database. Met dit installatie script worden de gegevens bronnen, referenties voor het data base-bereik en externe bestands indelingen gemaakt die in deze voor beelden worden gebruikt.

## <a name="functions"></a>Functies

### <a name="filename"></a>Bestands

Deze functie retourneert de naam van het bestand waaruit de rij afkomstig is.

In het volgende voor beeld worden de NYC Yellow Taxi-gegevens bestanden voor de laatste drie maanden van 2017 gelezen en wordt het aantal onderdrukkingen per bestand geretourneerd. Het gedeelte OPENROWSET van de query geeft aan welke bestanden worden gelezen.

```sql
SELECT
    nyc.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) nyc
GROUP BY nyc.filename();
```

In het volgende voor beeld ziet u hoe *filename ()* kan worden gebruikt in de component WHERE om de bestanden te filteren die moeten worden gelezen. Het opent de volledige map in het onderdeel OPENROWSET van de query en filtert bestanden in de component WHERE.

De resultaten zijn hetzelfde als in het vorige voor beeld.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2) 
        WITH (C1 varchar(200) ) AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.csv', 'yellow_tripdata_2017-11.csv', 'yellow_tripdata_2017-12.csv')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Bestandspad

De functie filepath retourneert een volledig of gedeeltelijk pad:

- Als u zonder para meter aangeroepen, wordt het volledige bestandspad geretourneerd waaruit de rij afkomstig is. Als DATA_SOURCE wordt gebruikt in OpenRowset, wordt pad geretourneerd ten opzichte van DATA_SOURCE. 
- Als deze wordt aangeroepen met een para meter, wordt een deel van het pad geretourneerd dat overeenkomt met het Joker teken op de positie die is opgegeven in de para meter. Zo retourneert parameter waarde 1 een deel van het pad dat overeenkomt met het eerste Joker teken.

In het volgende voor beeld worden NYC Yellow Taxi-gegevens bestanden voor de laatste drie maanden van 2017 gelezen. Hiermee wordt het aantal onderdrukkingen per bestandspad geretourneerd. Het gedeelte OPENROWSET van de query geeft aan welke bestanden worden gelezen.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-1*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

In het volgende voor beeld ziet u hoe *filepath ()* kan worden gebruikt in de component WHERE om de bestanden te filteren die moeten worden gelezen.

U kunt de joker tekens gebruiken in het deel van de query OPENROWSET en de bestanden in de component WHERE filteren. De resultaten zijn hetzelfde als in het vorige voor beeld.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_*-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',        
        FIRSTROW = 2
    )
WITH (
    vendor_id INT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Volgende stappen

In het volgende artikel vindt u informatie over het uitvoeren van een [query op Parquet-bestanden](query-parquet-files.md).
