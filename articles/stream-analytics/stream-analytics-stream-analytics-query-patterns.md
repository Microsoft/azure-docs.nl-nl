---
title: Algemene query patronen in Azure Stream Analytics
description: In dit artikel worden verschillende veelgebruikte query patronen en ontwerpen beschreven die nuttig zijn voor Azure Stream Analytics taken.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/18/2019
ms.custom: devx-track-js
ms.openlocfilehash: 8fcda77858b3feb78a04971a7ad1f20c0ea51fa1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019989"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Algemene query patronen in Azure Stream Analytics

Query's in Azure Stream Analytics worden uitgedrukt in een SQL-achtige query taal. De taal constructies worden beschreven in de naslag gids voor de [Stream Analytics-query taal](/stream-analytics-query/stream-analytics-query-language-reference) . 

In het ontwerp van de query kunt u eenvoudige Pass-Through logica gebruiken om gebeurtenis gegevens van de ene invoer stroom te verplaatsen naar een uitvoer gegevens opslag, of een uitgebreide patroon vergelijking en tijdelijke analyse voor het berekenen van aggregaties over diverse tijd Vensters als in het [bouwen van een IOT-oplossing met behulp van stream Analytics](stream-analytics-build-an-iot-solution-using-stream-analytics.md) Guide. U kunt gegevens uit meerdere invoer koppelen om streaming-gebeurtenissen te combi neren en u kunt opzoeken op basis van statische referentie gegevens om de gebeurtenis waarden te verrijken. U kunt ook gegevens schrijven naar meerdere uitvoer.

In dit artikel vindt u een overzicht van oplossingen voor verschillende veelgebruikte query patronen op basis van Real-World-scenario's.

## <a name="supported-data-formats"></a>Ondersteunde gegevens indelingen

Azure Stream Analytics ondersteunt het verwerken van gebeurtenissen in CSV-, JSON-en Avro-gegevens indelingen.

JSON en Avro kunnen complexe typen bevatten, zoals geneste objecten (records) of matrices. Raadpleeg het artikel [JSON-en Avro-gegevens parseren](stream-analytics-parsing-json.md) voor meer informatie over het werken met deze complexe gegevens typen.

## <a name="send-data-to-multiple-outputs"></a>Gegevens verzenden naar meerdere uitvoer

Meerdere **Select** -instructies kunnen worden gebruikt voor het uitvoeren van gegevens naar verschillende uitvoer-Sinks. Eén **Select** kan bijvoorbeeld een waarschuwing op basis van een drempel waarde uitvoeren terwijl een andere een gebeurtenis kan uitvoeren naar Blob Storage.

**Invoer**:

| Merk | Tijd |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Uitvoer ArchiveOutput**:

| Merk | Tijd |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Uitvoer AlertOutput**:

| Merk | Tijd | Count |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000 Z |3 |

**Query**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

De component **into** vertelt stream Analytics van welke uitvoer de gegevens moeten worden geschreven. De eerste **selectie** definieert een Pass Through-query waarmee gegevens worden ontvangen van de invoer en verzonden naar de uitvoer met de naam **ArchiveOutput**. Met de tweede query worden enkele eenvoudige aggregatie en filters uitgevoerd voordat de resultaten worden verzonden naar een downstream-waarschuwings systeem uitvoer met de naam **AlertOutput**.

Houd er rekening mee dat de **with** -component kan worden gebruikt om meerdere subquery blokken te definiëren. Deze optie biedt het voor deel van het openen van minder lezers aan de invoer bron.

**Query**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

Raadpleeg de [ **with** -component](/stream-analytics-query/with-azure-stream-analytics)voor meer informatie.

## <a name="simple-pass-through-query"></a>Eenvoudige Pass Through-query

Een eenvoudige Pass Through-query kan worden gebruikt om de gegevens van de invoer stroom te kopiëren naar de uitvoer. Als er bijvoorbeeld een gegevens stroom met informatie over het realtime-Voer tuig moet worden opgeslagen in een SQL database voor brief analyse, wordt de taak door een eenvoudige Pass Through-query uitgevoerd.

**Invoer**:

| Merk | Tijd | Gewicht |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Uitvoer**:

| Merk | Tijd | Gewicht |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Query**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

Een **Select** *-query projecten alle velden van een binnenkomende gebeurtenis en verzendt deze naar de uitvoer. **Op dezelfde manier kunt u** ook gebruiken om alleen de verplichte velden van de invoer te projecteren. Als in dit voor beeld het *merk* en de *tijd* van het Voer tuig de enige vereiste velden zijn die moeten worden opgeslagen, kunnen deze velden worden opgegeven in de **Select** -instructie.

**Invoer**:

| Merk | Tijd | Gewicht |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**Uitvoer**:

| Merk | Tijd |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:04.0000000 Z |

**Query**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```

## <a name="string-matching-with-like-and-not-like"></a>Teken reeks vergelijking met LIKE en niet als

**Net** als bij en **niet zoals** kan worden gebruikt om te controleren of een veld overeenkomt met een bepaald patroon. U kunt bijvoorbeeld een filter maken om alleen de licentie platen te retour neren die beginnen met de letter ' A ' en eindigen met het cijfer 9.

**Invoer**:

| Merk | License_plate | Tijd |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Uitvoer**:

| Merk | License_plate | Tijd |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Query**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Gebruik de instructie **like** om de waarde van het **License_plate** veld te controleren. Deze moet beginnen met de letter ' A ' en vervolgens een wille keurige teken reeks van nul of meer tekens hebben, eindigend op het getal 9.

## <a name="calculation-over-past-events"></a>Berekening over eerdere gebeurtenissen

De functie **lag** kan worden gebruikt om eerdere gebeurtenissen binnen een tijd venster te bekijken en te vergelijken met de huidige gebeurtenis. De huidige Car-versie kan bijvoorbeeld worden gegenereerd als deze afwijkt van de laatste auto die door de gratis is gemaakt.

**Invoer**:

| Merk | Tijd |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Uitvoer**:

| Merk | Tijd |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Query**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Gebruik **lag** om één gebeurtenis terug in de invoer stroom te bekijken, de waarde voor het *maken* op te halen en te vergelijken met de waarde van de huidige gebeurtenis en de gebeurtenis uit *te voeren.*

Raadpleeg voor meer informatie de [**vertraging**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>De laatste gebeurtenis in een venster retour neren

Wanneer gebeurtenissen in realtime door het systeem worden verbruikt, is er geen functie waarmee kan worden bepaald of een gebeurtenis de laatste is voor het betreffende tijd venster. Hiervoor moet de invoer stroom worden gekoppeld met een andere, waarbij de tijd van een gebeurtenis de maximale tijd voor alle gebeurtenissen in dat venster is.

**Invoer**:

| License_plate | Merk | Tijd |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Uitvoer**:

| License_plate | Merk | Tijd |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Query**:

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

In de eerste stap van de query wordt de maximale tijds tempel in Windows van 10 minuten gevonden. Dit is het tijds tempel van de laatste gebeurtenis voor dat venster. De tweede stap neemt de resultaten van de eerste query samen met de oorspronkelijke stroom om de gebeurtenis te vinden die overeenkomt met de laatste tijds tempels in elk venster. 

**DATEDIFF** is een datum-specifieke functie waarmee het tijds verschil tussen twee datum/tijd-velden wordt vergeleken en geretourneerd. Raadpleeg date- [functies](/stream-analytics-query/date-and-time-functions-azure-stream-analytics)voor meer informatie.

Raadpleeg [**join**](/stream-analytics-query/join-azure-stream-analytics)voor meer informatie over het toevoegen van streams.

## <a name="data-aggregation-over-time"></a>Gegevens aggregatie in de loop van de tijd

Als u informatie wilt berekenen over een tijd venster, kunnen gegevens samen worden geaggregeerd. In dit voor beeld wordt een telling berekend over de laatste tien seconden van de tijd voor elke specifieke auto.

**Invoer**:

| Merk | Tijd | Gewicht |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**Uitvoer**:

| Merk | Count |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Query**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Deze aggregatie groepeert de auto's door deze elke 10 seconden te *maken* en te tellen. De uitvoer heeft het *merk* en *aantal* auto's dat heeft geleid door de gratis.

TumblingWindow is een functie die wordt gebruikt om gebeurtenissen samen te groeperen. Een aggregatie kan worden toegepast op alle gegroepeerde gebeurtenissen. Zie [windowing functions](stream-analytics-window-functions.md)(Engelstalig) voor meer informatie.

Raadpleeg [statistische functies](/stream-analytics-query/aggregate-functions-azure-stream-analytics)voor meer informatie over aggregatie.

## <a name="periodically-output-values"></a>Periodieke uitvoer waarden

Bij onregelmatig of ontbrekende gebeurtenissen kan een regel matige interval uitvoer worden gegenereerd op basis van een meer sparere gegevens invoer. Genereer bijvoorbeeld elke 5 seconden een gebeurtenis die het meest recent weer gegeven gegevens punt rapporteert.

**Invoer**:

| Tijd | Waarde |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Uitvoer (eerste 10 rijen)**:

| Window_end | Last_event. Tegelijk | Last_event. Value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000 Z |2014-01-01T14:01:00.000 Z |1 |
| 2014-01-01T14:01:05.000 Z |2014-01-01T14:01:05.000 Z |2 |
| 2014-01-01T14:01:10.000 Z |2014-01-01T14:01:10.000 Z |3 |
| 2014-01-01T14:01:15.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:20.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:25.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:30.000 Z |2014-01-01T14:01:30.000 Z |5 |
| 2014-01-01T14:01:35.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:40.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:45.000 Z |2014-01-01T14:01:35.000 Z |6 |

**Query**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

Met deze query worden elke vijf seconden gebeurtenissen gegenereerd en wordt de laatste gebeurtenis uitgevoerd die eerder is ontvangen. De duur van de **HOPPINGWINDOW** bepaalt hoe ver terug de query zoekt naar de meest recente gebeurtenis.

Raadpleeg het [venster verspringen](/stream-analytics-query/hopping-window-azure-stream-analytics)voor meer informatie.

## <a name="correlate-events-in-a-stream"></a>Gebeurtenissen in een stroom correleren

Het correleren van gebeurtenissen in dezelfde stroom kan worden uitgevoerd door eerdere gebeurtenissen te bekijken met behulp van de functie **lag** . Een uitvoer kan bijvoorbeeld elke keer worden gegenereerd wanneer twee opeenvolgende auto's van hetzelfde *merk* de afgelopen 90 seconden de gratis door lopen.

**Invoer**:

| Merk | License_plate | Tijd |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000 Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000 Z |

**Uitvoer**:

| Merk | Tijd | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000 Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000 Z |

**Query**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

De functie **lag** kan één gebeurtenis terugvinden in de invoer stroom en de waarde voor het *maken* ophalen, vergeleken met de waarde voor het *maken* van de huidige gebeurtenis.  Zodra aan de voor waarde is voldaan, kunnen de gegevens van de vorige gebeurtenis worden geprojecteerd met behulp van een **vertraging** in de **Select** -instructie.

Raadpleeg voor meer informatie de [vertraging](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>De duur tussen gebeurtenissen detecteren

De duur van een gebeurtenis kan worden berekend door te kijken naar de laatste start gebeurtenis zodra een eind gebeurtenis is ontvangen. Deze query kan handig zijn om te bepalen hoe lang een gebruiker op een pagina of een functie doorbrengt.

**Invoer**:  

| Gebruiker | Functie | Gebeurtenis | Tijd |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Starten |2015-01-01T00:00:01.0000000 Z |
| user@location.com |RightMenu |Beëindigen |2015-01-01T00:00:08.0000000 Z |

**Uitvoer**:  

| Gebruiker | Functie | Duur |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Query**:

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

De functie **last** kan worden gebruikt om de laatste gebeurtenis binnen een bepaalde voor waarde op te halen. In dit voor beeld is de voor waarde een gebeurtenis van het type start, waarbij de zoek opdracht wordt gepartitioneerd **door de gebruiker en** het onderdeel. Op deze manier worden elke gebruiker en elk onderdeel onafhankelijk behandeld bij het zoeken naar de start gebeurtenis. De **limiet duur** beperkt de zoek opdracht terug in de tijd naar 1 uur tussen de eind-en begin gebeurtenissen.

## <a name="count-unique-values"></a>Unieke waarden tellen

**Count** en **DISTINCT** kunnen worden gebruikt voor het tellen van het aantal unieke veld waarden dat binnen een tijd venster in de stroom wordt weer gegeven. U kunt een query maken om te berekenen *hoeveel unieke auto's* er worden door gegeven via de telefoon stand in een periode van twee seconden.

**Invoer**:

| Merk | Tijd |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Uitvoer**

| Count_make | Tijd |
| --- | --- |
| 2 |2015-01-01T00:00:02.000 Z |
| 1 |2015-01-01T00:00:04.000 Z |

**Query:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**Aantal (DISTINCT maken)** retourneert het aantal afzonderlijke waarden in de kolom **maken** binnen een tijd venster.
Raadpleeg voor meer informatie de functie [ **aantal** statistische functies](/stream-analytics-query/count-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>De eerste gebeurtenis in een venster ophalen

**IsFirst** kan worden gebruikt om de eerste gebeurtenis in een tijd venster op te halen. Bijvoorbeeld: het uitvoeren van de eerste informatie over de auto op elke periode van tien minuten.

**Invoer**:

| License_plate | Merk | Tijd |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Uitvoer**:

| License_plate | Merk | Tijd |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |

**Query**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**IsFirst** kan ook de gegevens partitioneren en de eerste gebeurtenis berekenen op elke specifieke *auto die* elke periode van tien minuten wordt gevonden.

**Uitvoer**:

| License_plate | Merk | Tijd |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Query**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

Zie [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics)voor meer informatie.

## <a name="remove-duplicate-events-in-a-window"></a>Dubbele gebeurtenissen in een venster verwijderen

Wanneer er een bewerking wordt uitgevoerd, zoals het berekenen van de gemiddelden van gebeurtenissen in een bepaald tijd venster, moeten dubbele gebeurtenissen worden gefilterd. In het volgende voor beeld is de tweede gebeurtenis een duplicaat van de eerste.

**Invoer**:  

| DeviceId | Tijd | Kenmerk | Waarde |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000 Z |Temperatuur |50 |
| 1 |2018-07-27T00:00:01.0000000 Z |Temperatuur |50 |
| 2 |2018-07-27T00:00:01.0000000 Z |Temperatuur |40 |
| 1 |2018-07-27T00:00:05.0000000 Z |Temperatuur |60 |
| 2 |2018-07-27T00:00:05.0000000 Z |Temperatuur |50 |
| 1 |2018-07-27T00:00:10.0000000 Z |Temperatuur |100 |

**Uitvoer**:  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Query**:

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**Aantal (DISTINCT time)** retourneert het aantal DISTINCT-waarden in de tijd kolom binnen een tijd venster. De uitvoer van de eerste stap kan vervolgens worden gebruikt om het gemiddelde per apparaat te berekenen door dubbele waarden te verwijderen.

Zie [aantal (DISTINCT time)](/stream-analytics-query/count-azure-stream-analytics)voor meer informatie.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Geef logica op voor verschillende cases/waarden (CASE-instructies)

**Case** -instructies kunnen verschillende berekeningen bieden voor verschillende velden, op basis van een bepaald criterium. Wijs bijvoorbeeld Lane ' A ' toe aan auto's van *Make1* en Lane ' B ' naar een ander merk.

**Invoer**:

| Merk | Tijd |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Uitvoer**:

| Merk |Dispatch_to_lane | Tijd |
| --- | --- | --- |
| Make1 |Één |2015-01-01T00:00:01.0000000 Z |
| Make2 |B |2015-01-01T00:00:02.0000000 Z |

**Oplossing**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

De **Case** -expressie vergelijkt een expressie met een reeks eenvoudige expressies om het resultaat te bepalen. In dit voor beeld worden de Voer tuigen van *Make1* naar Lane ' A ' verzonden terwijl de Voer tuigen van een ander merk Lane ' B ' krijgen.

Raadpleeg voor meer informatie Case- [expressie](/stream-analytics-query/case-azure-stream-analytics).

## <a name="data-conversion"></a>Gegevens conversie

Gegevens kunnen in realtime worden omgezet met behulp van de **cast** -methode. Het auto gewicht kan bijvoorbeeld worden geconverteerd van het type **nvarchar (max)** naar het type **bigint** en worden gebruikt voor een numerieke berekening.

**Invoer**:

| Merk | Tijd | Gewicht |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Uitvoer**:

| Merk | Gewicht |
| --- | --- |
| Make1 |3000 |

**Query**:

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Gebruik een **cast** -instructie om het bijbehorende gegevens type op te geven. Zie de lijst met ondersteunde gegevens typen in [gegevens typen (Azure stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

Voor meer informatie over [functies voor gegevens conversie](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="detect-the-duration-of-a-condition"></a>De duur van een voor waarde detecteren

De functie **lag** kan worden gebruikt om de duur van de voor waarde vast te stellen voor voor waarden die meerdere gebeurtenissen omvatten. Stel bijvoorbeeld dat een bug heeft geresulteerd in alle auto's met een onjuist gewicht (meer dan 20.000 ponden) en de duur van die fout moet worden berekend.

**Invoer**:

| Merk | Tijd | Gewicht |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:02.0000000 Z |25.000 |
| Make1 |2015-01-01T00:00:03.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |25.000 |
| Make1 |2015-01-01T00:00:05.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:06.0000000 Z |25.000 |
| Make1 |2015-01-01T00:00:07.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:08.0000000 Z |2000 |

**Uitvoer**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000 Z |2015-01-01T00:00:07.000 Z |

**Query**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
De eerste **Select** -instructie correleert de huidige gewichts meting met de vorige meting, waarbij deze samen met de huidige meting wordt geprojecteerd. De tweede **selecteren** zoekt terug naar de laatste gebeurtenis waarbij de *previous_weight* kleiner is dan 20000, waarbij het huidige gewicht kleiner is dan 20000 en de *previous_weight* van de huidige gebeurtenis groter is dan 20000.

De End_fault is de huidige niet-fout gebeurtenis waarbij de vorige gebeurtenis is mislukt en de Start_fault de laatste niet-fout gebeurtenis vóór dat.

## <a name="process-events-with-independent-time-substreams"></a>Gebeurtenissen verwerken met onafhankelijke tijd (substreamen)

Gebeurtenissen kunnen te laat of in onjuiste volg orde arriveren als gevolg van de klok tussen de gebeurtenissen van de producent, de klok tussen partities of netwerk latentie.
Zo is de klok voor *TollID* 2 vijf seconden achter *TollID* 1, en de klok van het apparaat voor *TollID* 3 is tien seconden achter *TollID* 1. Een berekening kan onafhankelijk voor elke gratis worden uitgevoerd, waarbij alleen de eigen klok gegevens worden beschouwd als een tijds tempel.

**Invoer**:

| LicensePlate | Merk | Tijd | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000 Z | 1 |
| YHN 6970 |Make2 |2015-07-27T00:00:05.0000000 Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00:00:01.0000000 Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000 Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000 Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000 Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00:00:07.0000000 Z | 3 |

**Uitvoer**:

| TollID | Count |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Query**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

De component **Time Stamp over by** kijkt op elke tijd lijn van het apparaat onafhankelijk van substreamen. De uitvoer gebeurtenis voor elke *TollID* wordt gegenereerd wanneer ze worden berekend, wat inhoudt dat de gebeurtenissen zich bevinden ten opzichte van elke *TollID* in plaats van dat ze opnieuw moeten worden gerangschikt alsof alle apparaten dezelfde klok hebben.

Raadpleeg [Time Stamp](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering)voor meer informatie.

## <a name="session-windows"></a>Sessie Vensters

Een sessie venster is een venster dat steeds groter wordt wanneer gebeurtenissen zich voordoen en gesloten worden voor berekeningen als er na een bepaalde tijd geen gebeurtenis wordt ontvangen of als het venster de maximale duur bereikt.
Dit venster is vooral handig bij het berekenen van gebruikers interactie gegevens. Een venster wordt gestart wanneer een gebruiker met het systeem begint en sluit wanneer er geen gebeurtenissen worden waargenomen, wat betekent dat de gebruiker is gestopt.
Een gebruiker heeft bijvoorbeeld interactie met een webpagina waar het aantal klikken wordt geregistreerd, een sessie venster kan worden gebruikt om te bepalen hoe lang de gebruiker met de site communiceert.

**Invoer**:

| User_id | Tijd | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000 Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000 Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000 Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000 Z | "www.example.com/e.html" |

**Uitvoer**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | 2017-01-26T00:01:10.0000000 Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000 Z | 2017-01-26T00:01:15.0000000 Z | 20 |

**Query**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

De **Select** projects de gegevens die relevant zijn voor de gebruikers interactie, samen met de duur van de interactie. Het groeperen van de gegevens op gebruiker en een **SessionWindow** die wordt gesloten als er binnen 1 minuut geen interactie plaatsvindt, met een maximale grootte van 60 minuten.

Raadpleeg [Session-venster](/stream-analytics-query/session-window-azure-stream-analytics) voor meer informatie over **SessionWindow**.

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Taal uitbreid baarheid met door de gebruiker gedefinieerde functie in Java script en C #

Azure Stream Analytics query taal kan worden uitgebreid met aangepaste functies die zijn geschreven in Java script of C#. Door de gebruiker gedefinieerde functies (UDF) zijn aangepaste/complexe berekeningen die niet eenvoudig kunnen worden weer gegeven met de **SQL** -taal. Deze Udf's kunnen eenmaal worden gedefinieerd en meerdere keren worden gebruikt in een query. Een UDF kan bijvoorbeeld worden gebruikt om een hexadecimale waarde *nvarchar (max)* te converteren naar een *bigint* -waarde.

**Invoer**:

| Device_id | HexValue |
| --- | --- |
| 1 | B4 |
| 2 | "11B" |
| 3 | "121" |

**Uitvoer**:

| Device_id | Decimaal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

De door de gebruiker gedefinieerde functie berekent de *bigint* -waarde van de HexValue bij elke gebeurtenis die wordt verbruikt.

Raadpleeg [Java script](./stream-analytics-javascript-user-defined-functions.md) en [C#](./stream-analytics-edge-csharp-udf.md)voor meer informatie.

## <a name="advanced-pattern-matching-with-match_recognize"></a>Geavanceerd patroon dat overeenkomt met MATCH_RECOGNIZE

**MATCH_RECOGNIZE** is een geavanceerde patroon overeenkomst dat kan worden gebruikt voor het afstemmen van een reeks gebeurtenissen op een goed gedefinieerde reguliere-expressie patroon.
Zo wordt een ATM tijdens real-time gecontroleerd op fouten tijdens de werking van de ATM als er twee opeenvolgende waarschuwings berichten zijn die de beheerder moet worden gewaarschuwd.

**Invoer**:

| ATM_id | Operation_id | Return_Code | Tijd |
| --- | --- | --- | --- |
| 1 | "Pincode invoeren" | Geleverd | 2017-01-26T00:10:00.0000000 Z |
| 2 | "Sleuf voor het openen van geld" | Geleverd | 2017-01-26T00:10:07.0000000 Z |
| 2 | "De Money-sleuf sluiten" | Geleverd | 2017-01-26T00:10:11.0000000 Z |
| 1 | "Aantal intrekken opgeven" | Geleverd | 2017-01-26T00:10:08.0000000 Z |
| 1 | "Sleuf voor het openen van geld" | Waarschuwing | 2017-01-26T00:10:14.0000000 Z |
| 1 | "Bank saldo afdrukken" | Waarschuwing | 2017-01-26T00:10:19.0000000 Z |

**Uitvoer**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Sleuf voor het openen van geld" | 2017-01-26T00:10:14.0000000 Z |

```SQL
SELECT *
FROM input TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    LIMIT DURATION(minute, 1)
    PARTITON BY ATM_id
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Deze query komt overeen met ten minste twee opeenvolgende fout gebeurtenissen en genereert een waarschuwing wanneer aan de voor waarden wordt voldaan.
Met het **patroon** wordt de reguliere expressie gedefinieerd die moet worden gebruikt voor het vergelijken, in dit geval een wille keurig aantal geslaagde bewerkingen, gevolgd door ten minste twee opeenvolgende fouten.
Geslaagd en mislukt worden gedefinieerd met behulp van Return_Code waarde en wanneer aan de voor waarde wordt voldaan, worden de **metingen** geprojecteerd met *ATM_id*, de eerste waarschuwings bewerking en de eerste waarschuwings tijd.

Zie [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)voor meer informatie.

## <a name="geofencing-and-geospatial-queries"></a>Geoomheiningen en georuimtelijke query's

Azure Stream Analytics biedt ingebouwde georuimtelijke functies die kunnen worden gebruikt voor het implementeren van scenario's zoals vloot beheer, het delen van wijzigingen, verbonden auto's en het bijhouden van activa.
Georuimtelijke gegevens kunnen in geojson-of WKT-indelingen worden opgenomen als onderdeel van de gebeurtenis stroom of referentie gegevens.
Bijvoorbeeld een bedrijf dat is gespecialiseerd in productie machines voor het afdrukken van Pass ports, het leasen van hun machines aan overheden en consulates. De locatie van deze machines wordt sterk beheerd, om te voor komen dat het niet kan worden gebruikt voor het vervalsen van paspoorten. Elke machine is voorzien van een GPS-tracker en deze gegevens worden door gegeven aan een Azure Stream Analytics taak.
De productie zou de locatie van deze machines moeten bijhouden en moeten worden gewaarschuwd als een van hen een bevoegd gebied verlaat, op deze manier kunnen ze Extern uitschakelen, waarschuwings instanties en de apparatuur ophalen.

**Invoer**:

| Equipment_id | Equipment_current_location | Tijd |
| --- | --- | --- |
| 1 | "PUNT (-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000 Z |
| 1 | "PUNT (-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000 Z |
| 1 | "PUNT (-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000 Z |
| 1 | "PUNT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

**Invoer van referentie gegevens**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "VEELHOEK ((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**Uitvoer**:

| Equipment_id | Equipment_alert_location | Tijd |
| --- | --- | --- |
| 1 | "PUNT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

Met de query kan de fabrikant de locatie van de machines automatisch bewaken en waarschuwingen ontvangen wanneer een computer de toegestane geofence verlaat. Met de ingebouwde georuimtelijke functie kunnen gebruikers GPS-gegevens in de query gebruiken zonder bibliotheken van derden.

Raadpleeg voor meer informatie de scenario's voor [geoomheining en georuimtelijke aggregatie met Azure stream Analytics](geospatial-scenarios.md) artikel.

## <a name="get-help"></a>Hulp vragen

Probeer voor meer hulp onze [micro soft Q&een vraag pagina voor Azure stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)