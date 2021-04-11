---
title: Gebeurtenismeldingen
titleSuffix: Azure Digital Twins
description: Zie verschillende gebeurtenis typen en de verschillende meldings berichten interpreteren.
author: baanders
ms.author: baanders
ms.date: 4/8/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q4
ms.openlocfilehash: 42842b00120b7e918ca5b790cce92a74ab1b99d5
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259965"
---
# <a name="event-notifications"></a>Gebeurtenismeldingen

Verschillende gebeurtenissen in azure Digital Apparaatdubbels maken **meldingen**, waardoor de back-end van de oplossing op de hoogte kan worden wanneer er verschillende acties plaatsvinden. Deze worden vervolgens [doorgestuurd](concepts-route-events.md) naar verschillende locaties binnen en buiten Azure Digital apparaatdubbels die deze informatie kunnen gebruiken om actie te ondernemen.

Er zijn verschillende typen meldingen die kunnen worden gegenereerd en meldings berichten kunnen er anders uitzien, afhankelijk van het type gebeurtenis dat ze heeft gegenereerd. In dit artikel vindt u meer informatie over verschillende typen berichten en hoe deze eruit kunnen zien.

Dit diagram toont de verschillende meldings typen:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="notification-structure"></a>Meldings structuur

Over het algemeen bestaan er meldingen uit twee delen: de koptekst en de hoofd tekst. 

### <a name="event-notification-headers"></a>Bericht headers voor gebeurtenis

Kopteksten van meldings berichten worden weer gegeven met sleutel-waardeparen. Afhankelijk van het protocol dat wordt gebruikt (MQTT, AMQP, of HTTP), worden bericht headers op een andere manier geserialiseerd. In deze sectie worden algemene header-informatie voor meldings berichten besproken, ongeacht het gekozen protocol en de geselecteerde serialisatie.

Sommige meldingen voldoen aan de [CloudEvents](https://cloudevents.io/) -standaard. CloudEvents-conformiteit is als volgt.
* Meldingen die worden verzonden vanaf apparaten blijven de bestaande specificaties voor meldingen volgen
* De meldingen die worden verwerkt en verzonden door IoT Hub blijven de bestaande specificaties voor de melding volgen, behalve wanneer IoT Hub kiest voor de ondersteuning van CloudEvents, zoals via Event Grid
* Meldingen die worden verzonden vanuit [digitale apparaatdubbels](concepts-twins-graph.md) met een [model](concepts-models.md) dat overeenkomt met CloudEvents
* Meldingen die worden verwerkt en verzonden door Azure Digital Apparaatdubbels voldoen aan CloudEvents

Services moeten een Volg nummer toevoegen aan alle meldingen om de volg orde aan te geven of hun eigen ordening op een andere manier te onderhouden. 

Meldingen die worden verzonden door Azure Digital Apparaatdubbels naar Event Grid worden automatisch ingedeeld in het CloudEvents-schema of het EventGridEvent-schema, afhankelijk van het schema type dat in het event grid-onderwerp is gedefinieerd. 

Extensie kenmerken voor headers worden toegevoegd als eigenschappen van het Event Grid schema in de payload. 

### <a name="event-notification-bodies"></a>Gebeurtenis meldings teksten

De hoofd tekst van meldings berichten wordt hier in JSON beschreven. Afhankelijk van de gewenste serialisatie voor de hoofd tekst van het bericht (bijvoorbeeld met JSON, CBOR, protobuf enzovoort), kan de bericht tekst op een andere manier worden geserialiseerd.

De set velden die de hoofd tekst bevat, verschilt per meldings type.

In de volgende secties vindt u meer informatie over de verschillende typen meldingen die worden verzonden door IoT Hub en Azure Digital Apparaatdubbels (of andere Azure IoT-Services). U leest informatie over de dingen die elk meldings type activeren en de set velden die zijn opgenomen in elk type meldings hoofdtekst.

## <a name="digital-twin-change-notifications"></a>Digitale dubbele wijzigings meldingen

Er worden **digitale dubbele wijzigings meldingen** geactiveerd wanneer er een digitale twee worden bijgewerkt, zoals:
* Wanneer eigenschaps waarden of meta gegevens worden gewijzigd.
* Wanneer meta gegevens van een digitaal element of onderdeel worden gewijzigd. Een voor beeld van dit scenario is het wijzigen van het model van een digitale dubbele.

### <a name="properties"></a>Eigenschappen

Dit zijn de velden in de hoofd tekst van een digitale, dubbele wijzigings melding.

| Name    | Waarde |
| --- | --- |
| `id` | Id van de melding, zoals een UUID of een item dat door de service wordt onderhouden. `source` + `id` is uniek voor elke afzonderlijke gebeurtenis |
| `source` | Naam van het IoT hub-of Azure Digital Apparaatdubbels-exemplaar, zoals *myhub.Azure-devices.net* of *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `data` | Een JSON-patch document met een beschrijving van de update van de dubbele. Zie voor meer informatie [hoofd tekst](#body-details) van de beschrijving. |
| `specversion` | *1,0*<br>Het bericht voldoet aan deze versie van de [CloudEvents-specificatie](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | ID van de digitale dubbele |
| `time` | Tijds tempel voor wanneer de bewerking is opgetreden op de digitale twee |
| `traceparent` | Een W3C-tracerings context voor de gebeurtenis |

### <a name="body-details"></a>Details van hoofd tekst

In het bericht bevat het `data` veld een JSON-patch document met de update voor de digitale twee.

Stel bijvoorbeeld dat een digitaal is bijgewerkt met behulp van de volgende patch.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

De gegevens in de bijbehorende melding (als synchroon door de service wordt uitgevoerd, zoals Azure Digital Apparaatdubbels het bijwerken van een digitale dubbele) zou een hoofd tekst hebben als:

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

Dit is de informatie die wordt weer gegeven in het `data` veld van het levens meldings bericht.

## <a name="digital-twin-lifecycle-notifications"></a>Digitale dubbele levenscyclus meldingen

Alle [digitale apparaatdubbels](concepts-twins-graph.md) verzenden meldingen, ongeacht of ze [IOT Hub apparaten vertegenwoordigen in azure Digital apparaatdubbels](how-to-ingest-iot-hub-data.md) of niet. Dit wordt veroorzaakt door **levenscyclus meldingen**, die betrekking hebben op het digitale dubbele zelf.

Levenscyclus meldingen worden geactiveerd wanneer:
* Er is een digitaal, twee, gemaakt
* Er is een digitale-dubbele verdubbeling verwijderd

### <a name="properties"></a>Eigenschappen

Dit zijn de velden in de hoofd tekst van een levenscyclus melding.

| Name | Waarde |
| --- | --- |
| `id` | Id van de melding, zoals een UUID of een item dat door de service wordt onderhouden. `source` + `id` is uniek voor elke afzonderlijke gebeurtenis. |
| `source` | Naam van het IoT hub-of Azure Digital Apparaatdubbels-exemplaar, zoals *myhub.Azure-devices.net* of *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `data` | De gegevens van de dubbele levens cyclus gebeurtenis. Zie voor meer informatie [hoofd tekst](#body-details-1) van de beschrijving. |
| `specversion` | *1,0*<br>Het bericht voldoet aan deze versie van de [CloudEvents-specificatie](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | ID van de digitale dubbele |
| `time` | Tijds tempel voor wanneer de bewerking is opgetreden op de dubbele |
| `traceparent` | Een W3C-tracerings context voor de gebeurtenis |

### <a name="body-details"></a>Details van hoofd tekst

Hier volgt een voor beeld van een bericht over levens cyclus meldingen: 

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

In het bericht bevat het `data` veld de gegevens van de betrokken digitale twee, weer gegeven in JSON-indeling. Het schema hiervoor is *Digital Apparaatdubbels Resource 7,1*.

Bij het maken van gebeurtenissen `data` weerspiegelt de payload de status van de dubbele nadat de resource is gemaakt, zodat deze alle door het systeem gegenereerde elementen zou moeten bevatten, net als bij een `GET` aanroep.

Hier volgt een voor beeld van een gegevens voor een [IOT-Plug en Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) -apparaat, met onderdelen en geen eigenschappen op het hoogste niveau. Eigenschappen die niet zinvol zijn voor apparaten (zoals gerapporteerde eigenschappen), moeten worden wegge laten. Dit is de informatie die wordt weer gegeven in het `data` veld van het levens meldings bericht.

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Hier volgt nog een voor beeld van digitale dubbele gegevens. Deze is gebaseerd op een [model](concepts-models.md)en ondersteunt geen onderdelen:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

## <a name="digital-twin-relationship-change-notifications"></a>Meldingen over wijziging van digitale dubbele relatie

**Relatie wijzigings meldingen** worden geactiveerd wanneer een relatie van een digitale-dubbele verbinding wordt gemaakt, bijgewerkt of verwijderd. 

### <a name="properties"></a>Eigenschappen

Dit zijn de velden in de hoofd tekst van een wijzigings melding voor een relatie.

| Name    | Waarde |
| --- | --- |
| `id` | Id van de melding, zoals een UUID of een item dat door de service wordt onderhouden. `source` + `id` is uniek voor elke afzonderlijke gebeurtenis |
| `source` | De naam van het Azure Digital Apparaatdubbels-exemplaar, zoals *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `data` | De nettolading van de relatie die is gewijzigd. Zie voor meer informatie [hoofd tekst](#body-details-2) van de beschrijving. |
| `specversion` | *1,0*<br>Het bericht voldoet aan deze versie van de [CloudEvents-specificatie](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | ID van de relatie, zoals `<twinID>/relationships/<relationshipID>` |
| `time` | Tijds tempel voor wanneer de bewerking is opgetreden op de relatie |
| `traceparent` | Een W3C-tracerings context voor de gebeurtenis |

### <a name="body-details"></a>Details van hoofd tekst

In het bericht bevat het `data` veld de payload van een relatie in JSON-indeling. Er wordt gebruikgemaakt van dezelfde indeling als een `GET` aanvraag voor een relatie via de [DIGITALTWINS-API](/rest/api/digital-twins/dataplane/twins). 

Hier volgt een voor beeld van de gegevens voor een update-relatie melding. ' Een relatie bijwerken ' betekent dat de eigenschappen van de relatie zijn gewijzigd, zodat de gegevens de bijgewerkte eigenschap en de nieuwe waarde tonen. Dit is de informatie die wordt weer gegeven in het `data` veld van het meldings bericht van de digitale dubbele relatie.

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

Hier volgt een voor beeld van de gegevens voor een melding voor maken of verwijderen van een relatie. Voor `Relationship.Delete` is de hoofd tekst hetzelfde als de `GET` aanvraag en krijgt deze de meest recente status voordat deze wordt verwijderd.

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

## <a name="digital-twin-telemetry-messages"></a>Digitale dubbele telemetriegegevens van berichten

**Telemetrie-berichten** worden ontvangen in azure Digital apparaatdubbels van verbonden apparaten die metingen verzamelen en verzenden.

### <a name="properties"></a>Eigenschappen

Dit zijn de velden in de hoofd tekst van een telemetrie-bericht.

| Name    | Waarde |
| --- | --- |
| `id` | De id van de melding die door de klant wordt verschaft bij het aanroepen van de telemetrie-API. |
| `source` | Volledig gekwalificeerde naam van de dubbele activiteit waarnaar de telemetrie-gebeurtenis is verzonden. Maakt gebruik van de volgende indeling: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>` . |
| `specversion` | *1,0*<br>Het bericht voldoet aan deze versie van de [CloudEvents-specificatie](https://github.com/cloudevents/spec). |
| `type` | `microsoft.iot.telemetry` |
| `data` | Het telemetrie-bericht dat naar apparaatdubbels is verzonden. De nettolading wordt niet gewijzigd en kan niet worden uitgelijnd met het schema van de dubbele verzen ding die de telemetrie heeft verzonden. |
| `dataschema` | Het gegevens schema is de model-ID van de dubbele of het onderdeel dat de telemetrie verzendt. Bijvoorbeeld `dtmi:example:com:floor4;2`. |
| `datacontenttype` | `application/json` |
| `traceparent` | Een W3C-tracerings context voor de gebeurtenis. |

### <a name="body-details"></a>Details van hoofd tekst

De hoofd tekst bevat de telemetrie-meting en enige contextuele informatie over het apparaat.

Hier volgt een voor beeld van een telemetrie-bericht tekst: 

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het leveren van gebeurtenissen op verschillende locaties, met behulp van eind punten en routes:
* [*Concepten: gebeurtenis routes*](concepts-route-events.md)
