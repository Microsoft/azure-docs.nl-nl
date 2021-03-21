---
title: Meer informatie over de Azure IoT Hub query-taal | Microsoft Docs
description: 'Hand leiding voor ontwikkel aars: beschrijving van de SQL-achtige IoT Hub query taal die wordt gebruikt om informatie op te halen over de apparaatdubbels en taken van een apparaat/module van uw IoT-hub.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: robinsh
ms.custom: devx-track-csharp
ms.openlocfilehash: cae2bcb1a3302814a426fa0cb2dfb36ba1b013fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218363"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>IoT Hub-querytaal voor apparaat- en moduledubbels, taken en-berichtroutering

IoT Hub biedt een krachtige SQL-achtige taal voor het ophalen van informatie over [apparaatdubbels](iot-hub-devguide-device-twins.md), [module apparaatdubbels](iot-hub-devguide-module-twins.md), [Jobs](iot-hub-devguide-jobs.md)en [bericht routering](iot-hub-devguide-messages-d2c.md). Dit artikel bevat het volgende:

* Een inleiding tot de belangrijkste functies van de IoT Hub query taal en
* De gedetailleerde beschrijving van de taal. Zie [query's in bericht routering](../iot-hub/iot-hub-devguide-routing-query-syntax.md)voor meer informatie over de query taal voor bericht routering.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Dubbele query's apparaat en module

[Apparaatdubbels](iot-hub-devguide-device-twins.md) en [module apparaatdubbels](iot-hub-devguide-module-twins.md) kunnen WILLe keurige JSON-objecten bevatten als beide tags en eigenschappen. Met IoT Hub kunt u apparaatdubbels en module apparaatdubbels als één JSON-document met alle dubbele informatie opvragen.

We gaan ervan uit dat uw IoT hub-apparaat apparaatdubbels de volgende structuur hebben (de module dubbele is vergelijkbaar met een extra moduleId):

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>Dubbele query's van het apparaat

IoT Hub stelt de apparaatdubbels van het apparaat in als een document verzameling met de naam **apparaten**. Met de volgende query wordt bijvoorbeeld de hele set apparaat apparaatdubbels opgehaald:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IOT sdk's](iot-hub-devguide-sdks.md) ondersteunen het pagineren van grote resultaten.

Met IoT Hub kunt u apparaatdubbels filteren met wille keurige voor waarden. Om bijvoorbeeld apparaat apparaatdubbels te ontvangen waarbij de tag **location. Region** is ingesteld op **ons** , gebruikt u de volgende query:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Booleaanse Opera tors en reken kundige vergelijkingen worden ook ondersteund. Gebruik bijvoorbeeld de volgende query om de apparaatdubbels in de VS op te halen en zo geconfigureerd dat telemetrie minder dan elke minuut wordt verzonden:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Als gebruiks gemak is het ook mogelijk om matrix constanten te gebruiken met de Opera tors **in** en **Nover enkele** (niet in). U kunt bijvoorbeeld de volgende query gebruiken om de apparaatdubbels van het apparaat op te halen dat WiFi of bekabelde connectiviteit rapporteert:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Het is vaak nodig om alle apparaatdubbels te identificeren die een specifieke eigenschap bevatten. IoT Hub ondersteunt de functie `is_defined()` voor dit doel. Gebruik bijvoorbeeld de volgende query om apparaatdubbels op te halen die de `connectivity` eigenschap definiëren:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Raadpleeg de sectie [WHERE-component](iot-hub-devguide-query-language.md#where-clause) voor de volledige verwijzing van de filter mogelijkheden.

Groepering en aggregaties worden ook ondersteund. Als u bijvoorbeeld wilt zoeken naar het aantal apparaten in elke configuratie status van de telemetrie, gebruikt u de volgende query:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

Deze groeperings query retourneert een resultaat zoals in het volgende voor beeld:

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

In dit voor beeld hebben drie apparaten een geslaagde configuratie gerapporteerd. er wordt nog steeds een configuratie toegepast en er is een fout gemeld.

Met projectie query's kunnen ontwikkel aars alleen de eigenschappen retour neren die ze van belang hebben. Als u bijvoorbeeld de tijd van de laatste activiteit wilt ophalen van alle apparaten zonder verbinding, gebruikt u de volgende query:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Dubbele query's in module

Het uitvoeren van query's op module apparaatdubbels is vergelijkbaar met het uitvoeren van query's op apparaatdubbels van apparaten, maar het gebruik van een andere verzameling/naam ruimte; in plaats van vanaf **apparaten** kunt u een query uitvoeren vanuit **apparaten. modules**:

```sql
SELECT * FROM devices.modules
```

Er is geen koppeling tussen apparaten en apparaten. modules-verzamelingen toegestaan. Als u een query wilt uitvoeren op module apparaatdubbels op verschillende apparaten, doet u dit op basis van tags. Met deze query worden alle module apparaatdubbels op alle apparaten met de scan status geretourneerd:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Met deze query wordt alle module apparaatdubbels geretourneerd met de scan status, maar alleen op de opgegeven subset van apparaten:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>C#-voor beeld

De functionaliteit van de query wordt weer gegeven door de [C# Service-SDK](iot-hub-devguide-sdks.md) in de **RegistryManager** -klasse.

Hier volgt een voor beeld van een eenvoudige query:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

Voor het **query** -object is een pagina grootte (maxi maal 100) geïnstantieerd. Vervolgens worden meerdere pagina's opgehaald door de **GetNextAsTwinAsync** -methoden meerdere keren aan te roepen.

In het object query worden meerdere **volgende** waarden weer gegeven, afhankelijk van de deserialisatie optie die de query vereist. Bijvoorbeeld dubbele en taak objecten, of gewone JSON bij het gebruik van projecties.

### <a name="nodejs-example"></a>Node.js-voor beeld

De functionaliteit van de query wordt weer gegeven door de [Azure IOT Service SDK voor Node.js](iot-hub-devguide-sdks.md) in het **register** object.

Hier volgt een voor beeld van een eenvoudige query:

```javascript
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

Voor het **query** -object is een pagina grootte (maxi maal 100) geïnstantieerd. Vervolgens worden meerdere pagina's opgehaald door meerdere keren de methode **nextAsTwin** aan te roepen.

In het object query worden meerdere **volgende** waarden weer gegeven, afhankelijk van de deserialisatie optie die de query vereist. Bijvoorbeeld dubbele en taak objecten, of gewone JSON bij het gebruik van projecties.

### <a name="limitations"></a>Beperkingen

> [!IMPORTANT]
> De query resultaten kunnen een paar minuten vertraging hebben ten opzichte van de laatste waarden in het apparaatdubbels van het apparaat. Als u een query wilt uitvoeren op een afzonderlijk apparaat apparaatdubbels op ID, gebruikt u de [Get dubbele rest API](/java/api/com.microsoft.azure.sdk.iot.device.devicetwin). Deze API retourneert altijd de nieuwste waarden en heeft een hogere beperkings limiet. U kunt de REST API rechtstreeks uitgeven of de equivalente functionaliteit gebruiken in een van de [Azure IOT hub service-sdk's](iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks).

Op dit moment worden vergelijkingen alleen ondersteund tussen primitieve typen (geen objecten), bijvoorbeeld `... WHERE properties.desired.config = properties.reported.config` wordt alleen ondersteund als deze eigenschappen primitieve waarden hebben.

## <a name="get-started-with-jobs-queries"></a>Aan de slag met taken query's

[Taken](iot-hub-devguide-jobs.md) bieden een manier om bewerkingen op sets apparaten uit te voeren. Elk apparaat bevat de gegevens van de taken waarvan het deel uitmaakt in een verzameling met de naam **taken**.

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Op dit moment kan deze verzameling worden opgevraagd als **devices.Jobs** in de query taal IOT hub.

> [!IMPORTANT]
> Op dit moment wordt de eigenschap Jobs nooit geretourneerd tijdens het opvragen van de apparaatdubbels van het apparaat. Dat wil zeggen query's die ' van apparaten ' bevatten. De eigenschap Jobs kan alleen rechtstreeks worden geopend met query's met behulp van `FROM devices.jobs` .
>
>

Als u bijvoorbeeld alle taken wilt ophalen (in het verleden en gepland) die van invloed zijn op één apparaat, kunt u de volgende query gebruiken:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Houd er rekening mee dat deze query de apparaatspecifieke status (en mogelijk het antwoord van de directe methode) van elke geretourneerde taak levert.

Het is ook mogelijk om te filteren met wille keurige Booleaanse voor waarden voor alle object eigenschappen in de **devices.Jobs** -verzameling.

Als u bijvoorbeeld alle voltooide dubbele update taken van het apparaat wilt ophalen die na september 2016 zijn gemaakt voor een specifiek apparaat, gebruikt u de volgende query:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

U kunt ook de uitkomsten per apparaat van één taak ophalen.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Beperkingen

Op dit moment bieden query's op **devices.Jobs** geen ondersteuning voor:

* Projecties zijn daarom alleen `SELECT *` mogelijk.
* Voor waarden die naast taak eigenschappen naar het apparaat verwijzen (Zie de voor gaande sectie).
* Aggregaties uitvoeren, zoals Count, Gem, groeperen op.

## <a name="basics-of-an-iot-hub-query"></a>Basis beginselen van een IoT Hub query

Elke IoT Hub query bestaat uit componenten SELECT en FROM, met optionele WHERE-en GROUP BY-componenten. Elke query wordt uitgevoerd op een verzameling JSON-documenten, bijvoorbeeld apparaatdubbels. De component FROM geeft de document verzameling aan die moet worden herhaald (**apparaten**, **apparaten. modules** of **devices.Jobs**). Vervolgens wordt het filter in de component WHERE toegepast. Met aggregaties worden de resultaten van deze stap gegroepeerd zoals opgegeven in de component GROUP BY. Voor elke groep wordt een rij gegenereerd zoals is opgegeven in de component SELECT.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM-component

In de component **from <from_specification>** kunnen slechts drie waarden worden uitgesteld: **van apparaten** om een query uit te zoeken naar apparaatdubbels, **van apparaten. modules** tot het opvragen van module apparaatdubbels of **van devices.Jobs** voor het uitvoeren van een query op gegevens per apparaat.

## <a name="where-clause"></a>WHERE-component

De component **WHERE <filter_condition>** is optioneel. Hiermee geeft u een of meer voor waarden op waaraan de JSON-documenten in de verzameling FROM moeten voldoen om te worden opgenomen als onderdeel van het resultaat. In elk JSON-document moet de opgegeven voor waarden worden geëvalueerd op ' True ' die in het resultaat moeten worden opgenomen.

De toegestane voor waarden worden beschreven in sectie- [expressies en voor waarden](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="select-clause"></a>SELECT-component

De **selectie <select_list>** is verplicht en geeft aan welke waarden worden opgehaald uit de query. Hiermee geeft u de JSON-waarden op die moeten worden gebruikt voor het genereren van nieuwe JSON-objecten.
Voor elk element van de gefilterde (en eventueel gegroepeerde) subset van de verzameling FROM, genereert de projectie fase een nieuw JSON-object. Dit object wordt samengesteld met de waarden die zijn opgegeven in de component SELECT.

Hieronder volgt de grammatica van de component SELECT:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** verwijst naar een wille keurige eigenschap van het JSON-document in de from-verzameling. Een aantal voor beelden van SELECT-componenten vindt u in de sectie aan de slag met Device-dubbele query's.

Momenteel zijn selectie componenten anders dan **Select*** worden alleen ondersteund in statistische query's op apparaatdubbels van het apparaat.

## <a name="group-by-clause"></a>Clausule GROUP BY

De component **GROUP BY <group_specification>** is een optionele stap die wordt uitgevoerd na het filter dat is opgegeven in de component WHERE en voordat de projectie is opgegeven in de SELECT. Er worden documenten gegroepeerd op basis van de waarde van een kenmerk. Deze groepen worden gebruikt voor het genereren van geaggregeerde waarden zoals opgegeven in de component SELECT.

Een voor beeld van een query met GROUP BY is:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

De formele syntaxis voor GROUP BY is:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** verwijst naar een wille keurige eigenschap van het JSON-document in de from-verzameling.

De component GROUP BY wordt momenteel alleen ondersteund bij het opvragen van de apparaatdubbels van het apparaat.

> [!IMPORTANT]
> De term `group` wordt momenteel behandeld als een speciaal tref woord in query's. `group`Als u de naam van uw eigenschap gebruikt, kunt u deze omsluiten met dubbele haken om fouten te voor komen, bijvoorbeeld `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'` .
>

## <a name="expressions-and-conditions"></a>Expressies en voor waarden

Een *expressie* op hoog niveau:

* Evalueert naar een exemplaar van een JSON-type (zoals Booleaans, getal, teken reeks, matrix of object).
* Wordt gedefinieerd door gegevens te bewerken die afkomstig zijn van het JSON-document van het apparaat en constanten die gebruikmaken van ingebouwde Opera tors en functies.

*Voor waarden* zijn expressies die resulteren in een Booleaanse waarde. Een constante die afwijkt van de Booleaanse **waarde True** , wordt als **Onwaar** beschouwd. Deze regel bevat **Null**, **ongedefinieerd**, een wille keurig object of matrix exemplaar, een wille keurige teken reeks en de Booleaanse waarde **False**.

De syntaxis voor expressies is:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Raadpleeg de volgende tabel om te begrijpen wat elk symbool in de syntaxis van expressies voor staat:

| Symbool | Definitie |
| --- | --- |
| attribute_name | Elke eigenschap van het JSON-document in de **from** -verzameling. |
| binary_operator | Een binaire operator die wordt weer gegeven in de sectie [Opera tors](#operators) . |
| function_name| Een functie die wordt weer gegeven in de sectie [functies](#functions) . |
| decimal_literal |Een float, uitgedrukt in een decimale notatie. |
| hexadecimal_literal |Een getal uitgedrukt in de teken reeks ' 0x ' gevolgd door een teken reeks van hexadecimale cijfers. |
| string_literal |Literals voor teken reeksen zijn Unicode-teken reeksen die worden vertegenwoordigd door een reeks van nul of meer Unicode-tekens of escape-reeksen. Letterlijke teken reeksen worden tussen enkele aanhalings tekens of dubbele aanhalings tekens geplaatst. Toegestane Escapes: `\'` , `\"` , `\\` , `\uXXXX` voor Unicode-tekens die worden gedefinieerd door 4 hexadecimale cijfers. |

### <a name="operators"></a>Operators

De volgende Opera tors worden ondersteund:

| Familie | Operators |
| --- | --- |
| Rekenkundig |+, -, *, /, % |
| Logisch |EN, OF, NIET |
| Vergelijking |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions

Bij het uitvoeren van een query op apparaatdubbels en Jobs is de enige ondersteunde functie:

| Functie | Beschrijving |
| -------- | ----------- |
| IS_DEFINED (eigenschap) | Retourneert een Booleaanse waarde die aangeeft of aan de eigenschap een waarden (inclusief `null` ) is toegewezen. |

In routes voor waarden worden de volgende wiskundige functies ondersteund:

| Functie | Beschrijving |
| -------- | ----------- |
| ABS (x) | Retourneert de absolute (positieve) waarde van de opgegeven numerieke expressie. |
| EXP (x) | Retourneert de exponentiële waarde van de opgegeven numerieke expressie (e ^ x). |
| VERMOGEN (x, y) | Retourneert de waarde van de opgegeven expressie naar de opgegeven macht (x ^ y).|
| VIER kant (x)    | Retourneert het kwadraat van de opgegeven numerieke waarde. |
| PLAFOND (x) | Retourneert het kleinste gehele getal dat groter is dan of gelijk is aan de opgegeven numerieke expressie. |
| FLOOR (x) | Retourneert het grootste gehele getal dat kleiner is dan of gelijk is aan de opgegeven numerieke expressie. |
| TEKEN (x) | Retourneert het positieve (+ 1), nul (0) of negatieve (-1) teken van de opgegeven numerieke expressie.|
| SQRT (x) | Retourneert de vierkantswortel van de opgegeven numerieke waarde. |

In routes-voor waarden worden de volgende typen controleren en casting ondersteund:

| Functie | Beschrijving |
| -------- | ----------- |
| AS_NUMBER | Converteert de invoer teken reeks naar een getal. `noop` Als invoer een getal is; `Undefined` als teken reeks geen getal voor stelt.|
| IS_ARRAY | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een matrix is. |
| IS_BOOL | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een Boolean is. |
| IS_DEFINED | Retourneert een Booleaanse waarde die aangeeft of aan de eigenschap een waarde is toegewezen. Dit wordt alleen ondersteund als de waarde een primitief type is. Primitieve typen bevatten een teken reeks, een Booleaanse waarde of een getal `null` . DateTime, object typen en matrices worden niet ondersteund. |
| IS_NULL | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie Null is. |
| IS_NUMBER | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een getal is. |
| IS_OBJECT | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een JSON-object is. |
| IS_PRIMITIVE | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een primitieve is (teken reeks, Booleaans, numeriek of `null` ). |
| IS_STRING | Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een teken reeks is. |

In routes-voor waarden worden de volgende teken reeks functies ondersteund:

| Functie | Beschrijving |
| -------- | ----------- |
| CONCAt (x, y,...) | Retourneert een tekenreeks die het resultaat is van het samenvoegen van twee of meer tekenreekswaarden. |
| LENGTE (x) | Retourneert het aantal tekens van de opgegeven teken reeks expressie.|
| LAGER (x) | Retourneert een tekenreeksexpressie na het converteren van tekens in hoofdletters naar kleine letters. |
| UPPER (x) | Retourneert een tekenreeksexpressie na het converteren van tekens in kleine letters naar hoofdletters. |
| Subtekenreeks (teken reeks, begin [, lengte]) | Retourneert een deel van een teken reeks expressie, beginnend bij het opgegeven teken op nul gebaseerde positie en gaat verder met de opgegeven lengte of naar het einde van de teken reeks. |
| INDEX_OF (teken reeks, fragment) | Retourneert de beginpositie van het eerste exemplaar van de tweede tekenreeksexpressie binnen de eerste opgegeven tekenreeksexpressie, of -1 als de tekenreeks niet is gevonden.|
| STARTS_WITH (x, y) | Retourneert een Booleaanse waarde die aangeeft of de eerste teken reeks expressie begint met de tweede. |
| ENDS_WITH (x, y) | Retourneert een Booleaanse waarde die aangeeft of de eerste teken reeks expressie eindigt op de tweede. |
| BEVAT (x, y) | Retourneert een Booleaanse waarde die aangeeft of de eerste tekenreeksexpressie de tweede bevat. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het uitvoeren van query's in uw apps met behulp van [Azure IOT sdk's](iot-hub-devguide-sdks.md).