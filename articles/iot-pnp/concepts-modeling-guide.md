---
title: Wat is IoT Plug en Play-apparaatprofielen? Microsoft Docs
description: Meer informatie over de DTDL-model taal (Digital Apparaatdubbels Definition Language) voor IoT Plug en Play-apparaten. Het artikel bevat informatie over primitieve en complexe gegevens typen, hergebruik patronen die gebruikmaken van onderdelen en overname en semantische typen. Het artikel bevat richt lijnen over de keuze van de dubbele model-id van het apparaat en de hulp middelen voor het ontwerpen van modellen.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a3389408b0942875aa7d760f1c514b995e381f9c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609199"
---
# <a name="iot-plug-and-play-modeling-guide"></a>Model gids voor IoT Plug en Play

De kern van IoT Plug en Play is een _model_ apparaat waarmee de mogelijkheden van een apparaat worden beschreven in een IoT-Plug en Play toepassing. Dit model is gestructureerd als een reeks interfaces die het volgende definiëren:

- _Eigenschappen_ die de alleen-lezen- of schrijfbare status van apparaat of andere entiteit vertegenwoordigen. Een serienummer van een apparaat kan bijvoorbeeld een alleen-lezeneigenschap zijn en een doeltemperatuur op een thermostaat kan een schrijfbare eigenschap zijn.
- _Telemetrie_ -velden waarmee de gegevens worden gedefinieerd die worden verzonden door een apparaat, ongeacht of de gegevens een gewone stroom van sensor leesingen, een incidentele fout of een informatie bericht zijn.
- _Opdrachten_ waarmee functies of bewerkingen die op het apparaat kunnen worden uitgevoerd, worden beschreven. Met een opdracht kan bijvoorbeeld een gateway opnieuw worden opgestart of een foto worden gemaakt met een externe camera.

Zie voor meer informatie over de manier waarop IoT-Plug en Play apparaten modellen gebruikt, [iot Plug en Play Device Developer Guide](concepts-developer-guide-device.md) en [IOT Plug en Play service Developer Guide](concepts-developer-guide-service.md).

Voor het definiëren van een model gebruikt u de Digital Apparaatdubbels Definition Language (DTDL). DTDL maakt gebruik van een JSON-variant met de naam [JSON-LD](https://json-ld.org/). Het volgende code fragment toont het model van een thermo staat-apparaat:

- Heeft een unieke model-ID: `dtmi:com:example:Thermostat;1` .
- Verzendt een telemetrie van de Tempe ratuur.
- Heeft een schrijf bare eigenschap om de doel temperatuur in te stellen.
- Heeft een alleen-lezen eigenschap voor het rapporteren van de maximale Tempe ratuur sinds de laatste keer opnieuw opstarten.
- Reageert op een opdracht die de maximale, minimale en gemiddelde Tempe ratuur gedurende een bepaalde periode aanvraagt.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

Het model van de Thermo staat heeft één interface. In latere voor beelden in dit artikel worden complexere modellen weer gegeven die gebruikmaken van onderdelen en overname.

In dit artikel wordt beschreven hoe u uw eigen modellen ontwerpt en ontwerpt en onderwerpen, zoals gegevens typen, model structuur en hulpprogram ma's.

Zie [Digital Apparaatdubbels Definition Language v2](https://github.com/Azure/opendigitaltwins-dtdl) Specification (Engelstalig) voor meer informatie.

## <a name="model-structure"></a>Modelstructuur

Eigenschappen, telemetrie en opdrachten worden gegroepeerd in interfaces. In deze sectie wordt beschreven hoe u interfaces kunt gebruiken om eenvoudige en complexe modellen te beschrijven met behulp van onderdelen en overname.

### <a name="model-ids"></a>Model-Id's

Elke interface heeft een unieke digitale dubbele model-id (DTMI). Complexe modellen gebruiken DTMIs om onderdelen te identificeren. Toepassingen kunnen gebruikmaken van de DTMIs die apparaten verzenden om model definities te vinden in een opslag plaats.

DTMIs moet voldoen aan de naamgevings conventies die zijn vereist voor de [IoT Plug en Play-model opslagplaats](https://github.com/Azure/iot-plugandplay-models):

- Het voor voegsel DTMI is `dtmi:` .
- Het DTMI-achtervoegsel is het versie nummer van het model, zoals `;2` .
- De hoofd tekst van de DTMI wordt toegewezen aan de map en het bestand in de model opslagplaats waar het model is opgeslagen. Het versie nummer maakt deel uit van de bestands naam.

Het model dat wordt geïdentificeerd door de DTMI `dtmi:com:Example:Thermostat;2` wordt bijvoorbeeld opgeslagen in de *DTMI/com/example/thermostat-2.jsin* het bestand.

Het volgende code fragment toont het overzicht van een interface definitie met de unieke DTMI:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>Geen onderdelen

Een eenvoudig model, zoals de hierboven weer gegeven Thermo staat, gebruikt geen Inge sloten of trapsgewijze componenten. Telemetrie, eigenschappen en opdrachten worden gedefinieerd in het `contents` knoop punt van de interface.

In het volgende voor beeld ziet u een deel van een eenvoudig model dat geen gebruik maakt van onderdelen:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Hulpprogram ma's zoals Azure IoT Explorer en de ontwerper van de IoT Central-Apparaatbeheer labelen een zelfstandige interface zoals de Thermo staat als _standaard onderdeel_.

De volgende scherm afbeelding laat zien hoe het model wordt weer gegeven in het hulp programma Azure IoT Explorer:

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Standaard onderdeel in azure IoT Explorer":::

De volgende scherm afbeelding laat zien hoe het model wordt weer gegeven als het standaard onderdeel in IoT Central de ontwerp functie voor Apparaatbeheer. Selecteer **identiteit weer geven** om de DTMI van het model te bekijken:

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="Scherm opname van het model van de Thermo staat in IoT Central Designer":::

De model-ID wordt opgeslagen in een eigenschap van een apparaatonafhankelijk-apparaat, zoals in de volgende scherm afbeelding wordt weer gegeven:

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="Model-ID in digitale dubbele eigenschap":::

Een DTDL-model zonder onderdelen is een nuttige vereenvoudiging voor een apparaat of een IoT Edge module met één set telemetrie, eigenschappen en opdrachten. Een model dat geen gebruikmaakt van-onderdelen, maakt het eenvoudig om een bestaand apparaat of module te migreren naar een IoT Plug en Play apparaat of module: u maakt een DTDL-model waarin het apparaat of de module wordt beschreven zonder dat hiervoor onderdelen moeten worden gedefinieerd.

> [!TIP]
> Een module kan een Device- [module](../iot-hub/iot-hub-devguide-module-twins.md) of een [IOT Edge module](../iot-edge/about-iot-edge.md)zijn.

### <a name="reuse"></a>Opnieuw gebruiken

Er zijn twee manieren om Interface definities opnieuw te gebruiken. Gebruik meerdere onderdelen in een model om te verwijzen naar andere interface definities. Gebruik overname om bestaande interface definities uit te breiden.

### <a name="multiple-components"></a>Meerdere onderdelen

Met onderdelen kunt u een model interface bouwen als een assembly van andere interfaces.

De [Thermo](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) staat-interface wordt bijvoorbeeld gedefinieerd als een model. U kunt deze interface als een of meer onderdelen opnemen wanneer u het [temperatuur controller model](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)definieert. In het volgende voor beeld worden deze onderdelen `thermostat1` en genoemd `thermostat2` .

Er zijn twee of meer onderdeel secties voor een DTDL-model met meerdere onderdelen. Elke sectie heeft `@type` ingesteld op `Component` en verwijst expliciet naar een schema zoals wordt weer gegeven in het volgende code fragment:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Dit model bevat drie onderdelen die zijn gedefinieerd in de sectie inhoud-twee `Thermostat` onderdelen en een `DeviceInformation` onderdeel. De sectie inhoud bevat ook eigenschappen, telemetrie en opdracht definities.

De volgende scherm afbeeldingen laten zien hoe dit model wordt weer gegeven in IoT Central. De eigenschappen, telemetrie en de opdracht definities in de temperatuur controller worden weer gegeven in het **standaard onderdeel** van het hoogste niveau. De eigenschappen, telemetrie en de opdracht definities voor elke Thermo staat worden weer gegeven in de onderdeel definities:

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="Scherm opname van de sjabloon voor temperatuur controller-apparaten in IoT Central.":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="Scherm opname van de weer gave van de Thermo staat in de sjabloon voor het temperatuur regelaar-apparaat in IoT Central.":::

Zie voor meer informatie over het schrijven van apparaatcode die samenwerkt met-onderdelen, de [ontwikkelaars handleiding voor IoT Plug en Play-apparaten](concepts-developer-guide-device.md).

Zie voor meer informatie over het schrijven van service code die interkatten met onderdelen op een apparaat is, de [hand leiding voor IoT Plug en Play service-ontwikkel aars](concepts-developer-guide-service.md).

### <a name="inheritance"></a>Overname

Met overname kunt u de mogelijkheden van een interface opnieuw gebruiken in een basis interface om de mogelijkheden te verlengen. Verschillende modellen kunnen bijvoorbeeld algemene mogelijkheden delen, zoals een serie nummer:

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="Voor beeld van overname in een model apparaat met een thermo staat en een stroom controller die de mogelijkheden van een basis interface delen." border="false":::

Het volgende code fragment toont een DTML-model dat het `extends` sleutel woord gebruikt voor het definiëren van de overname relatie die in het vorige diagram wordt weer gegeven:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

In de volgende scherm afbeelding ziet u dit model in de omgeving van de IoT Central Device-sjabloon:

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="Scherm opname van interface overname in IoT Central":::

Wanneer u code voor apparaten of services schrijft, hoeft uw code geen speciale actie te ondernemen om overgenomen interfaces af te handelen. In het voor beeld in deze sectie rapporteert de apparaatcode het serie nummer alsof het deel uitmaakt van de Thermo staat-interface.

### <a name="tips"></a>Tips

U kunt onderdelen en overname combi neren wanneer u een model maakt. Het volgende diagram toont een `thermostat` model dat wordt overgenomen van een `baseDevice` interface. De `baseDevice` interface bevat een onderdeel dat zelf van een andere interface neemt:

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="Diagram van een model dat gebruikmaakt van zowel onderdelen als overname." border="false":::

In het volgende code fragment ziet u een DTML-model dat de `extends` `component` sleutel woorden en gebruikt om de overname relatie en het onderdeel gebruik te definiëren dat in het vorige diagram wordt weer gegeven:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>Gegevenstypen

Gegevens typen gebruiken om telemetrie, eigenschappen en opdracht parameters te definiëren. Gegevens typen kunnen primitief of complex zijn. Complexe gegevens typen gebruiken primitieven of andere complexe typen. De maximale diepte voor complexe typen is vijf niveaus.

### <a name="primitive-types"></a>Primitieve typen

De volgende tabel toont de set primitieve typen die u kunt gebruiken:

| Primitief type | Description |
| --- | --- |
| `boolean` | Een booleaanse waarde |
| `date` | Een volledige datum zoals gedefinieerd in [sectie 5,6 van RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |
| `dateTime` | Een datum/tijd zoals gedefinieerd in [RFC 3339](https://tools.ietf.org/html/rfc3339) |
| `double` | Een IEEE 8-byte zwevende punt |
| `duration` | Een duur in de ISO 8601-indeling |
| `float` | Een IEEE 4-byte drijvende komma |
| `integer` | Een ondertekend geheel getal van 4 bytes |
| `long` | Een geheel getal met 8 bytes dat is ondertekend |
| `string` | Een UTF8-teken reeks |
| `time` | Een volledige tijd zoals gedefinieerd in [sectie 5,6 van RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |

Het volgende code fragment toont een voor beeld van een telemetrie-definitie die gebruikmaakt `double` van het type in het `schema` veld:

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>Complexe gegevens typen

Complexe gegevens typen zijn een *matrix*, *opsomming*, *kaart*, *object* of een van de georuimtelijke typen.

#### <a name="arrays"></a>Matrices

Een matrix is een gegevens type dat kan worden geïndexeerd, waarbij alle elementen van hetzelfde type zijn. Het element type kan een primitief of complex type zijn.

Het volgende code fragment toont een voor beeld van een telemetrie-definitie die gebruikmaakt `Array` van het type in het `schema` veld. De elementen van de matrix zijn Booleaanse waarden:

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>Opsommingen

Een opsomming beschrijft een type met een set benoemde labels die worden toegewezen aan waarden. De waarden kunnen gehele getallen of teken reeksen zijn, maar de labels zijn altijd teken reeksen.

Het volgende code fragment toont een voor beeld van een telemetrie-definitie die gebruikmaakt `Enum` van het type in het `schema` veld. De waarden in de opsomming zijn gehele getallen:

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Maps

Een kaart is een type met sleutel-waardeparen waarbij de waarden allemaal hetzelfde type hebben. De sleutel in een kaart moet een teken reeks zijn. De waarden in een kaart kunnen elk type zijn, inclusief een ander complex type.

Het volgende code fragment toont een voor beeld van een eigenschaps definitie die het `Map` type in het `schema` veld gebruikt. De waarden in de kaart zijn teken reeksen:

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>Objecten

Een object type bestaat uit benoemde velden. De typen van de velden in een object toewijzing kunnen primitieve of complexe typen zijn.

Het volgende code fragment toont een voor beeld van een telemetrie-definitie die gebruikmaakt `Object` van het type in het `schema` veld. De velden in het object zijn `dateTime` , `duration` en `string` typen:

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>Georuimtelijke typen

DTDL biedt een reeks georuimtelijke typen, gebaseerd op [geojson](https://geojson.org/), voor het model leren van geografische gegevens structuren:,,,, `point` `multiPoint` `lineString` `multiLineString` `polygon` en `multiPolygon` . Deze typen zijn vooraf gedefinieerde geneste structuren van matrices, objecten en opsommingen.

Het volgende code fragment toont een voor beeld van een telemetrie-definitie die gebruikmaakt `point` van het type in het `schema` veld:

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

Omdat de georuimtelijke typen op een matrix zijn gebaseerd, kunnen ze momenteel niet worden gebruikt in eigenschaps definities.

## <a name="semantic-types"></a>Semantische typen

Het gegevens type van een eigenschap of telemetrie-definitie specificeert de indeling van de gegevens die door een apparaat worden uitgewisseld met een service. Het semantische type bevat informatie over telemetrie en eigenschappen die een toepassing kan gebruiken om te bepalen hoe een waarde moet worden verwerkt of weer gegeven. Elk semantisch type heeft een of meer gekoppelde eenheden. Celsius en Fahrenheit zijn bijvoorbeeld eenheden voor het semantische temperatuur type. IoT Central Dash boards en analyses kunnen gebruikmaken van de informatie over semantisch type om te bepalen hoe telemetrie of eigenschaps waarden en weer gave-eenheden worden getekend. Zie informatie over [de Digital apparaatdubbels model-parser](concepts-model-parser.md)voor meer informatie over hoe u de model-parser kunt gebruiken om de semantische typen te lezen.

Het volgende code fragment toont een voor beeld van een telemetrie-definitie die informatie over semantisch type bevat. Het semantische type `Temperature` wordt toegevoegd aan de `@type` matrix en de `unit` waarde `degreeCelsius` is een van de geldige eenheden voor het semantische type:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>Lokalisatie

Toepassingen, zoals IoT Central, gebruiken informatie in het model om een gebruikers interface dynamisch te bouwen rond de gegevens die worden uitgewisseld met een IoT Plug en Play-apparaat. Tegels op een dash board kunnen bijvoorbeeld namen en beschrijvingen weer geven voor telemetrie, eigenschappen en opdrachten.

De optionele `description` en `displayName` velden in het model bevatten teken reeksen die zijn bedoeld voor gebruik in een gebruikers interface. Deze velden kunnen gelokaliseerde teken reeksen bevatten die een toepassing kan gebruiken om een gelokaliseerde gebruikers interface weer te geven.

Het volgende code fragment toont een voor beeld van een telemetrie-temperatuur definitie die gelokaliseerde teken reeksen bevat:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

Het toevoegen van gelokaliseerde teken reeksen is optioneel. Het volgende voor beeld heeft slechts één standaard taal:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>Levens cyclus en hulpprogram ma's

De vier levenscyclus fasen voor een apparaatprofiel zijn ontwerpen, publiceren, gebruiken en versie beheer:

### <a name="author"></a>Auteur

DTML zijn JSON-documenten die u kunt maken in een tekst editor. In IoT Central kunt u echter de GUI-omgeving van de sjabloon gebruiken om een DTML-model te maken. In IoT Central kunt u het volgende doen:

- Interfaces maken waarmee eigenschappen, telemetrie en opdrachten worden gedefinieerd.
- Gebruik onderdelen om meerdere interfaces samen te verzamelen.
- Overname relaties tussen interfaces definiëren.
- Importeer en exporteer DTML-model bestanden.

Zie [een nieuw IOT-apparaattype definiëren in uw Azure IOT Central-toepassing](../iot-central/core/howto-set-up-template.md)voor meer informatie.

Met de [DTDL editor voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) krijgt u een op tekst gebaseerde bewerkings omgeving met syntaxis validatie en automatisch aanvullen voor een betere controle over de ontwerp ervaring van het model.

### <a name="publish"></a>Publiceren

Als u uw DTML-modellen deelbaar en detecteerbaar wilt maken, publiceert u deze in een opslag plaats voor apparaat modellen.

Voordat u een model in de open bare opslag plaats publiceert, kunt u de `dmr-client` hulpprogram ma's gebruiken om uw model te valideren.

Zie voor meer informatie de [opslag plaats voor apparaat modellen](concepts-model-repository.md).

### <a name="use"></a>Gebruik

Toepassingen, zoals IoT Central, gebruiken model modellen. In IoT Central maakt een model deel uit van de sjabloon van het apparaat dat de mogelijkheden van het apparaat beschrijft. IoT Central maakt gebruik van de Device-sjabloon om een gebruikers interface dynamisch te bouwen voor het apparaat, met inbegrip van Dash boards en analyses.

Een aangepaste oplossing kan de [Digital apparaatdubbels model-parser](concepts-model-parser.md) gebruiken om inzicht te krijgen in de mogelijkheden van een apparaat dat het model implementeert. Zie [iot Plug en Play-modellen gebruiken in een IOT-oplossing](concepts-model-discovery.md)voor meer informatie.

### <a name="version"></a>Versie

Om ervoor te zorgen dat apparaten en oplossingen aan de server zijde die gebruikmaken van modellen blijven werken, zijn gepubliceerde modellen onveranderbaar.

De DTMI bevat een versie nummer dat u kunt gebruiken om meerdere versies van een model te maken. Apparaten en oplossingen aan de server zijde kunnen gebruikmaken van de specifieke versie die ze hebben ontworpen voor gebruik.

IoT Central implementeert meer versies van regels voor de modellen van apparaten. Als u een sjabloon voor een apparaat en het bijbehorende model in IoT Central, kunt u apparaten van eerdere versies naar latere versies migreren. Gemigreerde apparaten kunnen echter geen nieuwe mogelijkheden gebruiken zonder firmware-upgrade. Zie [een nieuwe versie van een sjabloon voor een apparaat maken](../iot-central/core/howto-version-device-template.md)voor meer informatie.

## <a name="limits-and-constraints"></a>Limieten en beperkingen

De volgende lijst bevat een overzicht van een aantal belang rijke beperkingen en limieten voor modellen:

- Op dit moment is de maximale diepte voor matrices, kaarten en objecten vijf niveaus.
- U kunt geen matrices gebruiken in eigenschaps definities.
- U kunt interfaces uitbreiden naar een diepte van 10 niveaus.
- Een interface kan uit Maxi maal twee andere interfaces worden uitgebreid.
- Een onderdeel kan geen ander onderdeel bevatten.

## <a name="next-steps"></a>Volgende stappen

Nu u over Apparaatbeheer hebt geleerd, zijn hier enkele aanvullende bronnen:

- [De DTDL-hulpprogram ma's voor ontwerpen installeren en gebruiken](howto-use-dtdl-authoring-tools.md)
- [Digital Apparaatdubbels Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Model opslagplaatsen](./concepts-model-repository.md)
