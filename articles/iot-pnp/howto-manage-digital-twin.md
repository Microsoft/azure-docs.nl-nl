---
title: IoT-gegevens Plug en Play digitale tweelingen beheren
description: IoT-Plug en Play beheren met digital twin-API's
author: prashmo
ms.author: prashmo
ms.date: 12/17/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e68003878dc0e9275461100a59e0f45486c2978f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739868"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>IoT-Plug en Play digitale tweelingen beheren

IoT Plug en Play ondersteuning voor **Get digital twin** en Update digital **twin** operations to manage digital twins (Digitale tweelingen bijwerken om digitale tweelingen te beheren). U kunt de [REST API's of](/rest/api/iothub/service/digitaltwin) een van de [service-SDK's gebruiken.](libraries-sdks.md)

Op het moment van schrijven is de digital twin API-versie `2020-09-30` .

## <a name="update-a-digital-twin"></a>Een digital twin bijwerken

Een IoT Plug en Play implementeert een model dat wordt [beschreven door Digital Twins Definition Language v2 (DTDL).](https://github.com/Azure/opendigitaltwins-dtdl) Oplossingsontwikkelaars kunnen de **API Digital Twin bijwerken gebruiken om** de status van het onderdeel en de eigenschappen van de digitale tweeling bij te werken.

Het IoT Plug en Play apparaat dat als voorbeeld in dit artikel wordt gebruikt, implementeert het [temperatuurcontrollermodel](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) met [thermostaatonderdelen.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)

Het volgende codefragment toont het antwoord op een **Get digital twin-aanvraag** opgemaakt als een JSON-object. Zie Understand IoT Plug en Play [digital twins (IoT-gegevens](./concepts-digital-twin.md#digital-twin-example)en digitale tweelingen begrijpen) voor meer informatie over de digital twin-indeling:

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
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

Met digital twins kunt u een volledig onderdeel of eigenschap bijwerken met behulp van een [JSON-patch](http://jsonpatch.com/).

U kunt de eigenschap bijvoorbeeld `targetTemperature` als volgt bijwerken:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

Met de vorige update wordt de gewenste waarde van een eigenschap in het bijbehorende onderdeelniveau in het bijbehorende onderdeelniveau, `$metadata` zoals weergegeven in het volgende fragment. IoT Hub werkt de gewenste versie van de eigenschap bij:

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>Een onderdeel toevoegen, vervangen of verwijderen

Bewerkingen op onderdeelniveau vereisen een lege `$metadata` objectmarkering binnen de waarde.

Met een bewerking onderdeel toevoegen of vervangen worden de gewenste waarden van alle opgegeven eigenschappen. Ook worden de gewenste waarden geweken voor beschrijfbare eigenschappen die niet zijn opgegeven bij de update.

Als u een onderdeel verwijdert, worden de gewenste waarden van alle beschrijfbare eigenschappen geweken. Een apparaat synchroniseert deze verwijdering uiteindelijk en stopt met het rapporteren van de afzonderlijke eigenschappen. Het onderdeel wordt vervolgens verwijderd uit de digitale tweeling.

In het volgende JSON Patch-voorbeeld ziet u hoe u een onderdeel toevoegt, vervangt of verwijdert:

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>Een eigenschap toevoegen, vervangen of verwijderen

Met een bewerking voor toevoegen of vervangen stelt u de gewenste waarde van een eigenschap in. Het apparaat kan de status synchroniseren en een update van de waarde rapporteren, samen met `ack` een code, versie en beschrijving.

Als u een eigenschap verwijdert, wordt de gewenste waarde van de eigenschap geweken als deze is ingesteld. Het apparaat kan vervolgens stoppen met het rapporteren van deze eigenschap en wordt verwijderd uit het onderdeel. Als deze eigenschap de laatste eigenschap in het onderdeel is, wordt het onderdeel ook verwijderd.

In het volgende JSON Patch-voorbeeld ziet u hoe u een eigenschap in een onderdeel toevoegt, vervangt of verwijdert:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Regels voor het instellen van de gewenste waarde van een eigenschap van een digitale tweeling

**Naam**

De naam van een onderdeel of eigenschap moet een geldige DTDL v2-naam zijn.

Toegestane tekens zijn a-z, A-Z, 0-9 (niet als eerste teken) en onderstrepingsteken (niet als het eerste of laatste teken).

Een naam mag 1-64 tekens lang zijn.

**Eigenschapswaarde**

De waarde moet een geldige [DTDL v2-eigenschap zijn.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)

Alle primitieve typen worden ondersteund. Binnen complexe typen worden enums, kaarten en objecten ondersteund. Zie [DTDL v2 Schema's voor meer informatie.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas)

Eigenschappen ondersteunen geen matrix of een complex schema met een matrix.

Een maximale diepte van vijf niveaus wordt ondersteund voor een complex object.

Alle veldnamen binnen een complex object moeten geldige DTDL v2-namen zijn.

Alle kaartsleutels moeten geldige DTDL v2-namen zijn.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>Problemen met update van digital twin-API oplossen

De digital twin-API geeft het volgende algemene foutbericht weer:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Als deze fout wordt weergegeven, moet u ervoor zorgen dat de updatepatch de regels volgt voor het instellen van de [gewenste waarde van een eigenschap van een digitale tweeling](#rules-for-setting-the-desired-value-of-a-digital-twin-property)

Wanneer u een onderdeel bij werkt, moet u ervoor zorgen dat het [lege object $metadata markering](#add-replace-or-remove-a-component) is ingesteld.

Updates kunnen mislukken als de gerapporteerde waarden van een apparaat niet voldoen aan de [IoT Plug en Play-conventies.](./concepts-convention.md#writable-properties)

## <a name="next-steps"></a>Volgende stappen

Nu u meer hebt geleerd over digitale tweelingen, zijn hier enkele aanvullende resources:

- [Interactie met een apparaat vanuit uw oplossing](quickstart-service.md)
- [IoT Digital Twin-REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
