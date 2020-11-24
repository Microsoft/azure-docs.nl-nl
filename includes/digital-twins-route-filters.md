---
author: baanders
description: include-bestand voor filter opties voor Azure Digital Apparaatdubbels-route
ms.service: digital-twins
ms.topic: include
ms.date: 11/18/2020
ms.author: baanders
ms.openlocfilehash: 261c5fa47cddcc527e7c0a18fbd18aad9320ed4b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555691"
---
| Bestandsnaam | Beschrijving | Filter tekst schema | Ondersteunde waarden | 
| --- | --- | --- | --- |
| Waar/onwaar | Hiermee kunt u een route maken zonder te filteren, of een route uitschakelen zodat er geen gebeurtenissen worden verzonden | `<true/false>` | `true` = route is ingeschakeld zonder filtering <br> `false` = route is uitgeschakeld |
| Type | Het [type gebeurtenis dat](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) door uw digitale dubbele instantie wordt doorlopend | `type = '<eventType>'` | Dit zijn de mogelijke gebeurtenis type waarden: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Bron | Naam van het Azure Digital Apparaatdubbels-exemplaar | `source = '<hostname>'`| Dit zijn de mogelijke hostnamen: <br> **Voor meldingen**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net` <br> **Voor telemetrie**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>`|
| Onderwerp | Een beschrijving van de gebeurtenis in de context van de bovenstaande gebeurtenis bron | `subject = '<subject>'` | Dit zijn de mogelijke waarden voor het onderwerp: <br>**Voor meldingen**: het onderwerp is `<twinid>` <br> of een URI-indeling voor onderwerpen, die uniek worden geïdentificeerd door meerdere onderdelen of Id's:<br>`<twinid>/relationships/<relationshipid>`<br> **Voor telemetrie**: het onderwerp is het pad van het onderdeel (als de telemetrie wordt verzonden vanuit een twee ledig onderdeel), zoals `comp1.comp2` . Als de telemetrie niet vanuit een onderdeel wordt verzonden, is het veld onderwerp leeg. |
| Gegevens schema | DTDL-model-ID | `dataschema = '<model-dtmi-ID>'` | **Voor telemetrie**: het gegevens schema is de model-id van de dubbele of het onderdeel dat de telemetrie verzendt. Bijvoorbeeld: `dtmi:example:com:floor4;2` <br>**Voor meldingen**: gegevens schema kan worden geopend in de hoofd tekst van het bericht op `$body.$metadata.$model`|
| Inhoudstype | Inhouds type van gegevens waarde | `datacontenttype = '<contentType>'` | Het inhouds type is `application/json` |
| Specificatie versie | De versie van het gebeurtenis schema dat u gebruikt | `specversion = '<version>'` | De versie moet zijn `1.0` . Dit geeft de CloudEvents-schema versie 1,0 |
| Meldings tekst | Verwijzen naar een eigenschap in het `data` veld van een melding | `$body.<property>` | Zie [*How-to: informatie over gebeurtenis gegevens*](../articles/digital-twins/how-to-interpret-event-data.md) voor voor beelden van meldingen. Naar een eigenschap in het `data` veld kan worden verwezen met `$body`

Houd er rekening mee dat u meerdere filters kunt toevoegen aan een aanvraag als volgt: 

```json  
{
    "endpointName": "dt-endpoint", 
    "filter": "true", 
    "filter": "source = 'ADT-resource.api.wus2.digitaltwins.azure.net/myFloorID'", 
    "filter": "type = 'Microsoft.DigitalTwins.Twin.Delete'", 
    "filter": "specversion = '1.0'"
}
```

De volgende gegevens typen worden ondersteund als waarden die worden geretourneerd door verwijzingen naar de bovenstaande gegevens:

| Gegevenstype | Voorbeeld |
|-|-|-|
|**Tekenreeks**| `STARTS_WITH($body.$metadate.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**Geheel getal**|`$body.errorCode > 200`|
|**Dubbelklik**|`$body.temperature <= 5.5`|
|**BOOL**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

De volgende Opera tors worden ondersteund bij het definiëren van route filters:

|Familie|Operators|Voorbeeld|
|-|-|-|
|Logisch|EN, OF, ()|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|Vergelijking|<, <=, >, >=, =,! =|`$body.temperature <= 5.5`

De volgende functies worden ondersteund bij het definiëren van route filters:

|Functie|Beschrijving|Voorbeeld|
|--|--|--|
|STARTS_WITH (x, y)|Retourneert waar als de waarde `x` begint met de teken reeks `y` .|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH (x, y) | Retourneert waar als de waarde `x` eindigt met de teken reeks `y` .|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|BEVAT (x, y)| Retourneert waar als de waarde `x` de teken reeks bevat `y` .|`CONTAINS(subject, '<twinID>')`|

Wanneer u een filter implementeert of bijwerkt, kan het enkele minuten duren voordat de wijziging in de gegevens pijplijn wordt weer gegeven.