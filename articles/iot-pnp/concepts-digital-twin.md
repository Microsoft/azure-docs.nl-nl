---
title: Meer informatie over IoT Plug en Play digitale apparaatdubbels
description: Meer informatie over hoe IoT Plug en Play gebruikmaakt van digitale apparaatdubbels
author: prashmo
ms.author: prashmo
ms.date: 12/14/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 99c957e5bf6ffe69c94e109796590f5ab975c3cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656883"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>Meer informatie over IoT Plug en Play digitale apparaatdubbels

Een IoT Plug en Play-apparaat implementeert een model dat wordt beschreven door het [DTDL-schema (Digital Apparaatdubbels Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) . Een model beschrijft de set onderdelen, eigenschappen, opdrachten en telemetriegegevens die een bepaald apparaat kan hebben.

IoT Plug en Play maakt gebruik van DTDL versie 2. Zie [Digital Apparaatdubbels Definition Language (DTDL)-versie 2 Specification (Engelstalig)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) op github voor meer informatie over deze versie.

> [!NOTE]
> DTDL is niet exclusief voor IoT-Plug en Play. Andere IoT-Services, zoals [Azure Digital apparaatdubbels](../digital-twins/overview.md), gebruiken deze om hele omgevingen, zoals gebouwen en energie netwerken, weer te geven.

De Sdk's van de Azure IoT-service bevatten Api's waarmee een service de digitale twee apparaten van een apparaat kan communiceren. Een service kan bijvoorbeeld eigenschappen van een apparaat van de digitale twee gelezen of de digitale twee gebruiken om een opdracht op een apparaat aan te roepen. Zie [IOT hub digitale dubbele voor beelden](concepts-developer-guide-service.md#iot-hub-digital-twin-examples)voor meer informatie.

In het voor beeld IoT-Plug en Play apparaat in dit artikel wordt een [temperatuur controller model](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) geïmplementeerd met de onderdelen van de [Thermo](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) staat.

## <a name="device-twins-and-digital-twins"></a>Apparaat apparaatdubbels en Digital apparaatdubbels

Daarnaast onderhoudt Azure-IoT Hub ook een *apparaat* voor elk aangesloten apparaat. Een apparaat dat vergelijkbaar is met een digitale dubbele waarde, is dat het een weer gave van de eigenschappen van een apparaat is. De Sdk's van de Azure IoT-service bevatten Api's voor interactie met Device apparaatdubbels.

Een IoT-hub initialiseert een digitale dubbele en een apparaat, naar de eerste keer dat een IoT-Plug en Play apparaat wordt aangesloten.

Apparaatdubbels zijn JSON-documenten die status informatie van een apparaat opslaan, inclusief meta gegevens, configuraties en voor waarden. Zie [IOT hub service client-voor beelden](concepts-developer-guide-service.md#iot-hub-service-client-examples)voor meer informatie. Ontwikkelers van apparaten en oplossingen kunnen dezelfde set van Device-dubbele Api's en Sdk's blijven gebruiken om apparaten en oplossingen te implementeren met behulp van IoT Plug en Play-conventies.

De digitale dubbele Api's worden toegepast op DTDL-constructies op hoog niveau, zoals onderdelen, eigenschappen en opdrachten. De digitale twee Api's maken het gemakkelijker voor oplossingen bouwers om IoT-Plug en Play oplossingen te maken.

In een dubbele apparaat wordt de status van een Beschrijf bare eigenschap gesplitst in de secties *gewenste eigenschappen* en *gerapporteerde eigenschappen* . Alle alleen-lezen eigenschappen zijn beschikbaar in de sectie gerapporteerde eigenschappen.

In een digitale twee, een uniforme weer gave van de huidige en gewenste status van de eigenschap. De synchronisatie status voor een bepaalde eigenschap wordt opgeslagen in de sectie met de bijbehorende standaard onderdelen `$metadata` .

### <a name="device-twin-json-example"></a>Voor beeld van een dubbele JSON van een apparaat

Het volgende code fragment toont een IoT-Plug en Play als een JSON-object.

```json
{
  "deviceId": "sample-device",
  "modelId": "dtmi:com:example:TemperatureController;1",
  "version": 15,
  "properties": {
    "desired": {
      "thermostat1": {
        "__t": "c",
        "targetTemperature": 21.8
      },
      "$metadata": {...},
      "$version": 4
    },
    "reported": {
      "serialNumber": "alwinexlepaho8329",
      "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "__t": "c",
        "targetTemperature": {
          "value": 21.8,
          "ac": 200,
          "ad": "Successfully executed patch",
        }
      },
      "$metadata": {...},
      "$version": 11
    }
  }
}
```

### <a name="digital-twin-example"></a>Digitaal dubbel voor beeld

In het volgende code fragment ziet u de digitale dubbele indeling als een JSON-object:

```json
{
  "$dtId": "sample-device",
  "serialNumber": "alwinexlepaho8329",
  "thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
      "targetTemperature": {
        "desiredValue": 21.8,
        "desiredVersion": 4,
        "ackVersion": 4,
        "ackCode": 200,
        "ackDescription": "Successfully executed patch",
        "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
      },
      "maxTempSinceLastReboot": {
         "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:example:TemperatureController;1",
    "serialNumber": {
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

De volgende tabel beschrijft de velden in het digitale dubbele JSON-object:

| Veldnaam | Beschrijving |
| --- | --- |
| `$dtId` | Een door de gebruiker gegeven teken reeks die de ID van het digitale-dubbele apparaat vertegenwoordigt |
| `{propertyName}` | De waarde van een eigenschap in JSON |
| `$metadata.$model` | Beschrijving De ID van de model interface die dit digitale dubbele |
| `$metadata.{propertyName}.desiredValue` | [Alleen voor Beschrijf bare eigenschappen] De gewenste waarde van de opgegeven eigenschap |
| `$metadata.{propertyName}.desiredVersion` | [Alleen voor Beschrijf bare eigenschappen] De versie van de gewenste waarde die wordt onderhouden door IoT Hub|
| `$metadata.{propertyName}.ackVersion` | [Vereist, alleen voor Beschrijf bare eigenschappen] De versie die wordt bevestigd door het apparaat dat de digitale dubbele toepassing implementeert, moet groter zijn dan of gelijk zijn aan de gewenste versie |
| `$metadata.{propertyName}.ackCode` | [Vereist, alleen voor Beschrijf bare eigenschappen] De `ack` code die wordt geretourneerd door de apparaat-app die de digitale-twee implementeert |
| `$metadata.{propertyName}.ackDescription` | [Optioneel, alleen voor Beschrijf bare eigenschappen] De `ack` beschrijving die wordt geretourneerd door de apparaat-app die de digitale-twee implementeert |
| `$metadata.{propertyName}.lastUpdateTime` | IoT Hub behoudt de tijds tempel van de laatste update van de eigenschap door het apparaat. De tijds tempels zijn in UTC en worden gecodeerd in de ISO8601-indeling JJJJ-MM-DDTUU: MM: SS. mmmZ |
| `{componentName}` | Een JSON-object dat de eigenschaps waarden en meta gegevens van de component bevat. |
| `{componentName}.{propertyName}` | De waarde van de eigenschap van het onderdeel in JSON |
| `{componentName}.$metadata` | De meta gegevens voor het onderdeel. |

### <a name="properties"></a>Eigenschappen

Eigenschappen zijn gegevens velden die de status van een entiteit vertegenwoordigen (zoals de eigenschappen in veel object-georiënteerde programmeer talen).

#### <a name="read-only-property"></a>Alleen-lezen eigenschap

DTDL-schema:

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

In dit voor beeld `alwinexlepaho8329` is de huidige waarde van de `serialNumber` alleen-lezen eigenschap die is gerapporteerd door het apparaat.
De volgende fragmenten tonen de side-by-side JSON-weer gave van de `serialNumber` eigenschap:

:::row:::
   :::column span="":::
      **Dubbel apparaat**

```json
"properties": {
  "reported": {
    "serialNumber": "alwinexlepaho8329"
  }
}
```

   :::column-end:::
   :::column span="":::
      **Digitale dubbele**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>Beschrijf bare eigenschap

In de volgende voor beelden ziet u een Beschrijf bare eigenschap in het standaard onderdeel.

DTDL:

```json
{
  "@type": "Property",
  "name": "fanSpeed",
  "displayName": "Fan Speed",
  "writable": true,
  "schema": "double"
}
```

:::row:::
   :::column span="":::
      **Dubbel apparaat**

```json
{
  "properties": {
    "desired": {
      "fanSpeed": 2.0,
    },
    "reported": {
      "fanSpeed": {
        "value": 3.0,
        "ac": 200,
        "av": 1,
        "ad": "Successfully executed patch version 1"
      }
    }
  },
}
```

   :::column-end:::
   :::column span="":::
      **Digitale dubbele**

```json
{
  "fanSpeed": 3.0,
  "$metadata": {
    "fanSpeed": {
      "desiredValue": 2.0,
      "desiredVersion": 2,
      "ackVersion": 1,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch version 1",
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

In dit voor beeld `3.0` is de huidige waarde van de `fanSpeed` eigenschap die wordt gerapporteerd door het apparaat. `2.0` is de gewenste waarde die is ingesteld door de oplossing. De gewenste waarde en synchronisatie status van een eigenschap op hoofd niveau worden ingesteld in het hoofd niveau `$metadata` voor een digitale dubbele. Als het apparaat online is, kan het deze update Toep assen en de bijgewerkte waarde rapporteren.

### <a name="components"></a>Onderdelen

Onderdelen maken model interface samen als een assembly van andere interfaces.
De [Thermo](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) staat-interface kan bijvoorbeeld worden opgenomen als onderdelen `thermostat1` en  `thermostat2` in het model model van de [temperatuur regelaar](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) .

In een dubbele apparaat wordt een onderdeel aangeduid met de `{ "__t": "c"}` markering. In een digitale dubbele, de aanwezigheid van `$metadata` een onderdeel.

In dit voor beeld `thermostat1` is dit een onderdeel met twee eigenschappen:

- `maxTempSinceLastReboot` is een alleen-lezen eigenschap.
- `targetTemperature` is een schrijf bare eigenschap die door het apparaat is gesynchroniseerd. De gewenste waarde en synchronisatie status van deze eigenschappen bevinden zich in het onderdeel `$metadata` .

De volgende fragmenten tonen de side-by-side JSON-weer gave van het `thermostat1` onderdeel:

:::row:::
   :::column span="":::
      **Dubbel apparaat**

```json
"properties": {
  "desired": {
    "thermostat1": {
      "__t": "c",
      "targetTemperature": 21.8
    },
    "$metadata": {
    },
    "$version": 4
  },
  "reported": {
    "thermostat1": {
      "maxTempSinceLastReboot": 25.3,
      "__t": "c",
      "targetTemperature": {
        "value": 21.8,
        "ac": 200,
        "ad": "Successfully executed patch",
        "av": 4
      }
    },
    "$metadata": {
    },
    "$version": 11
  }
}
```

   :::column-end:::
   :::column span="":::
      **Digitale dubbele**

```json
"thermostat1": {
  "maxTempSinceLastReboot": 25.3,
  "targetTemperature": 21.8,
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 21.8,
      "desiredVersion": 4,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch",
      "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
    },
    "maxTempSinceLastReboot": {
       "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>Digitale dubbele Api's

De digitale twee-Api's zijn onder andere het **ophalen van digitale dubbele**, **bijwerkende digitale dubbele**, **onderdeel opdracht aanroepen** en het **aanroepen van opdracht** bewerkingen om een digitaal dubbel element te beheren. U kunt de rest- [api's](/rest/api/iothub/service/digitaltwin) rechtstreeks of via een [Service-SDK](../iot-pnp/libraries-sdks.md)gebruiken.

## <a name="digital-twin-change-events"></a>Gebeurtenissen met wijziging van dubbel

Wanneer digitale gebeurtenissen met wijziging in een dubbel zijn ingeschakeld, wordt een gebeurtenis geactiveerd wanneer de huidige of gewenste waarde van het onderdeel of de eigenschap wordt gewijzigd. Gebeurtenissen met een digitale dubbele wijziging worden gegenereerd in de indeling [JSON-patch](http://jsonpatch.com/) . De bijbehorende gebeurtenissen worden gegenereerd in de dubbele bestands indeling voor het apparaat als dubbele wijzigings gebeurtenissen zijn ingeschakeld.

Zie [IOT hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)voor meer informatie over het inschakelen van route ring voor apparaat-en digitale dubbele gebeurtenissen. Zie [IOT hub berichten maken en lezen voor meer](../iot-hub/iot-hub-devguide-messages-construct.md)informatie over de bericht indeling.

Zo wordt bijvoorbeeld de volgende gebeurtenis met een digitale dubbele wijziging geactiveerd wanneer `targetTemperature` deze is ingesteld door de oplossing:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

De volgende digitale gebeurtenis met dubbele wijziging wordt geactiveerd wanneer het apparaat rapporteert dat de hierboven gewenste wijziging is toegepast:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

> [!NOTE]
> Dubbele wijzigings meldings berichten worden verdubbeld wanneer deze zijn ingeschakeld in het apparaat en de digitale dubbele wijzigings melding.

## <a name="next-steps"></a>Volgende stappen

Nu u over digitale apparaatdubbels hebt geleerd, zijn hier enkele aanvullende bronnen:

- [IoT Plug en Play digitale dubbele Api's gebruiken](howto-manage-digital-twin.md)
- [Interactie met een apparaat vanuit uw oplossing](quickstart-service.md)
- [IoT digitale dubbele REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)