---
title: JSON-en AVRO parseren in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u complexe gegevens typen gebruikt zoals matrices, JSON-gegevens in CSV-indeling.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: conceptual
ms.date: 01/29/2020
ms.custom: devx-track-js
ms.openlocfilehash: f6cb131fb3ff3cab4122aac5e1c6960dee4f8421
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012237"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>JSON-en Avro-gegevens parseren in Azure Stream Analytics

Azure Stream Analytics ondersteuning voor het verwerken van gebeurtenissen in CSV-, JSON-en Avro-gegevens indelingen. JSON-en Avro-gegevens kunnen worden gestructureerd en bevatten complexe typen, zoals geneste objecten (records) en matrices. 

>[!NOTE]
>AVRO-bestanden die zijn gemaakt door Event hub Capture, gebruiken een specifieke indeling waarvoor u de functie voor *aangepaste deserialisatie* moet gebruiken. Zie [invoer lezen in elke indeling met behulp van aangepaste .net-deserialisatie](./custom-deserializer-examples.md)voor meer informatie.
>
>Stream Analytics AVRO-deserialisatie biedt geen ondersteuning voor het toewijzings type. Stream Analytics kan geen EventHub Capture-blobs lezen, omdat EventHub Capture gebruikmaakt van de toewijzing.


## <a name="record-data-types"></a>Record gegevens typen
Record gegevens typen worden gebruikt voor het weer geven van JSON-en Avro-matrices wanneer overeenkomende indelingen worden gebruikt in de gegevens stromen voor invoer. In deze voor beelden ziet u een voor beeld van een sensor, waarmee invoer gebeurtenissen in JSON-indeling worden gelezen. Hier volgt een voor beeld van één gebeurtenis:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```

### <a name="access-nested-fields-in-known-schema"></a>Toegang krijgen tot geneste velden in een bekend schema
Gebruik punt notatie (.) om eenvoudig toegang te krijgen tot geneste velden rechtstreeks vanuit uw query. Met deze query worden bijvoorbeeld de breedte-en lengte coördinaten geselecteerd onder de locatie-eigenschap in de voor gaande JSON-gegevens. De punt notatie kan worden gebruikt om te navigeren op meerdere niveaus, zoals hieronder wordt weer gegeven.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

Het resultaat is:

|DeviceID|Lat|Omvang|Temperatuur|Versie|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Alle eigenschappen selecteren
U kunt alle eigenschappen van een geneste record selecteren met behulp van het Joker teken ' * '. Kijk eens naar het volgende voorbeeld:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Het resultaat is:

|DeviceID|Lat|Omvang|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Toegang krijgen tot geneste velden als eigenschaps naam een variabele is

Gebruik de functie [GetRecordPropertyValue](/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) als de naam van de eigenschap een variabele is. Zo kunt u dynamische query's maken zonder hardcoding-eigenschaps namen.

Stel bijvoorbeeld dat de gegevens stroom van de steek proef moet **worden gekoppeld met referentie gegevens** die drempels voor elke sensor van het apparaat bevatten. Hieronder ziet u een fragment van dergelijke referentie gegevens.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

Het doel is hier om de gegevensset van de voor beelden boven aan het artikel te koppelen aan die referentie gegevens en om één gebeurtenis uit te voeren voor elke sensor meting boven de drempel waarde. Dit betekent dat met onze bovenstaande gebeurtenis meer uitvoer gebeurtenissen kunnen worden gegenereerd als meerdere Sens oren boven hun respectieve drempel waarden vallen, dankzij de samen voeging. Zie de sectie hieronder om Vergelijk bare resultaten te krijgen zonder een koppeling te maken.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue** selecteert de eigenschap in *SensorReadings*, die overeenkomt met de naam van de eigenschap die afkomstig is van de referentie gegevens. Vervolgens wordt de gekoppelde waarde uit *SensorReadings* geëxtraheerd.

Het resultaat is:

|DeviceID|Sensornaam|AlertMessage|
|-|-|-|
|12345|Vochtigheid|Waarschuwing: sensor boven drempel waarde|

### <a name="convert-record-fields-into-separate-events"></a>Record velden omzetten in afzonderlijke gebeurtenissen

Als u record velden wilt omzetten in afzonderlijke gebeurtenissen, gebruikt u de operator [Apply](/stream-analytics-query/apply-azure-stream-analytics) samen met de functie [GetRecordProperties](/stream-analytics-query/getrecordproperties-azure-stream-analytics) .

Met de oorspronkelijke voorbeeld gegevens kan de volgende query worden gebruikt voor het uitpakken van eigenschappen in verschillende gebeurtenissen.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Het resultaat is:

|DeviceID|Sensornaam|AlertMessage|
|-|-|-|
|12345|Temperatuur|80|
|12345|Vochtigheid|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[object object]|

Met [with](/stream-analytics-query/with-azure-stream-analytics)kunt u deze gebeurtenissen vervolgens naar verschillende bestemmingen routeren:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>JSON-record parseren in SQL-referentie gegevens
Wanneer u Azure SQL Database als referentie gegevens in uw taak gebruikt, is het mogelijk om een kolom met gegevens in JSON-indeling te hebben. Hieronder kunt u een voorbeeld bekijken.

|DeviceID|Gegevens|
|-|-|
|12345|{"sleutel": "waarde1"}|
|54321|{"sleutel": "Value2"}|

U kunt de JSON-record in de *gegevens* kolom parseren door een door de gebruiker gedefinieerde Java script-functie te schrijven.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

U kunt vervolgens een stap in uw Stream Analytics query maken, zoals hieronder wordt weer gegeven om toegang te krijgen tot de velden van uw JSON-records.

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>Matrix gegevens typen

Matrix gegevens typen zijn een geordende verzameling waarden. Enkele typische bewerkingen op matrix waarden worden hieronder beschreven. In deze voor beelden worden de functies [GetArrayElement](/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](/stream-analytics-query/getarraylength-azure-stream-analytics)en de operator [Apply](/stream-analytics-query/apply-azure-stream-analytics) gebruikt.

Hier volgt een voor beeld van één gebeurtenis. Beide `CustomSensor03` en `SensorMetadata` zijn van het type **matrix**:

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Werken met een specifiek matrix element

Selecteer een matrix element bij een opgegeven index (selecteren van het eerste matrix element):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Het resultaat is:

|firstElement|
|-|
|12|

### <a name="select-array-length"></a>Matrix lengte selecteren

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Het resultaat is:

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Matrix elementen omzetten in afzonderlijke gebeurtenissen

Selecteer alle matrix elementen als afzonderlijke gebeurtenissen. De operator apply in combi natie met de ingebouwde functie [GetArrayElements](/stream-analytics-query/getarrayelements-azure-stream-analytics) haalt alle matrix elementen [op](/stream-analytics-query/apply-azure-stream-analytics) als afzonderlijke gebeurtenissen:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Het resultaat is:

|DeviceId|Array index|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
Het resultaat is:

|DeviceId|smKey|smValue|
|-|-|-|
|12345|Fabrikant|ABC|
|12345|Versie|1.2.45|

Als de geëxtraheerde velden in kolommen moeten worden weer gegeven, is het mogelijk om de gegevensset te draaien met de syntaxis [with](/stream-analytics-query/with-azure-stream-analytics) naast de bewerking [samen voegen](/stream-analytics-query/join-azure-stream-analytics) . Voor deze samen voeging is een [tijds grens](/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) vereist die duplicatie voor komt:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

Het resultaat is:

|DeviceId|Lat|Omvang|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Zie ook
[Gegevens typen in Azure Stream Analytics](/stream-analytics-query/data-types-azure-stream-analytics)