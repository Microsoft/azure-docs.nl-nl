---
title: IoT Plug en Play Digital apparaatdubbels beheren
description: IoT Plug en Play-apparaat beheren met Digital-dubbele Api's
author: prashmo
ms.author: prashmo
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ddb8027c145f6a38bfcd953be66dae2943a20c3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654605"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>IoT Plug en Play Digital apparaatdubbels beheren

IoT Plug en Play biedt ondersteuning voor het verkrijgen van digitale **dubbele** en **bijgewerkte digitale dubbele** bewerkingen om digitale apparaatdubbels te beheren. U kunt de rest- [api's](/rest/api/iothub/service/digitaltwin) of een van de [service-sdk's](libraries-sdks.md)gebruiken.

Op het moment van schrijven is de Digital-dubbele API-versie `2020-09-30` .

## <a name="update-a-digital-twin"></a>Een digital twin bijwerken

Een IoT Plug en Play-apparaat implementeert een model dat wordt beschreven door [Digital Apparaatdubbels Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl). Ontwikkel aars van oplossingen kunnen de **update digitale dubbele API** gebruiken voor het bijwerken van de status van het onderdeel en de eigenschappen van de digitale twee.

Het IoT-Plug en Play apparaat dat als voor beeld wordt gebruikt in dit artikel, implementeert het [temperatuur controller model](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) met de [Thermo](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) staat-onderdelen.

Het volgende code fragment toont het antwoord op een **Get Digital-dubbele** aanvraag die is opgemaakt als een JSON-object. Zie voor meer informatie over de Digital-dubbele indeling [begrijpen IoT Plug en Play Digital apparaatdubbels](./concepts-digital-twin.md#digital-twin-example):

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

Met Digital apparaatdubbels kunt u een volledig onderdeel of eigenschap bijwerken met behulp van een [JSON-patch](http://jsonpatch.com/).

U kunt de `targetTemperature` eigenschap bijvoorbeeld als volgt bijwerken:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

Met de vorige update wordt de gewenste waarde van een eigenschap in het bijbehorende onderdeel niveau ingesteld `$metadata` , zoals wordt weer gegeven in het volgende code fragment. IoT Hub werkt de gewenste versie van de eigenschap bij:

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

Voor bewerkingen op onderdeel niveau is een lege object `$metadata` markering vereist in de waarde.

Met een bewerking toevoegen of vervangen onderdeel worden de gewenste waarden van alle gegeven eigenschappen ingesteld. Ook worden de gewenste waarden voor Beschrijf bare eigenschappen die niet bij de update zijn meegeleverd, verwijderd.

Als u een onderdeel verwijdert, worden de gewenste waarden van alle Beschrijf bare eigenschappen gewist. Een apparaat synchroniseert uiteindelijk deze verwijdering en stopt met het rapporteren van de afzonderlijke eigenschappen. Het onderdeel wordt vervolgens verwijderd uit de digitale twee.

In het volgende voor beeld van de JSON-patch ziet u hoe u een onderdeel kunt toevoegen, vervangen of verwijderen:

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

Met een toevoeg-of vervang bewerking stelt u de gewenste waarde van een eigenschap in. Het apparaat kan de status synchroniseren en een update van de waarde met een `ack` code, versie en beschrijving rapporteren.

Als u een eigenschap verwijdert, wordt de gewenste waarde van de eigenschap gewist als deze is ingesteld. Het apparaat kan vervolgens deze eigenschap niet meer melden en wordt verwijderd uit het onderdeel. Als deze eigenschap de laatste is in het onderdeel, wordt het onderdeel ook verwijderd.

In het volgende voor beeld van de JSON-patch ziet u hoe u een eigenschap binnen een onderdeel toevoegt, vervangt of verwijdert:

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

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Regels voor het instellen van de gewenste waarde van een digitale dubbele eigenschap

**Naam**

De naam van een onderdeel of eigenschap moet een geldige DTDL v2-naam hebben.

Toegestane tekens zijn a-z, A-Z, 0-9 (niet als het eerste teken) en het onderstrepings teken (niet als het eerste of laatste teken).

Een naam kan 1-64 tekens lang zijn.

**Eigenschaps waarde**

De waarde moet een geldige [DTDL v2-eigenschap](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)zijn.

Alle primitieve typen worden ondersteund. In complexe typen worden opsommingen, kaarten en objecten ondersteund. Zie [DTDL v2-schema's](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas)voor meer informatie.

Eigenschappen ondersteunen geen matrix of een complex schema met een matrix.

Er wordt een maximum diepte van vijf niveaus ondersteund voor een complex object.

Alle veld namen binnen complex object moeten geldige DTDL v2-namen hebben.

Alle kaart sleutels moeten geldige DTDL v2-namen zijn.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>Problemen met update digitale dubbele API-fouten oplossen

De digitale dubbele API genereert het volgende algemene fout bericht:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Als u deze fout ziet, zorg er dan voor dat de update patch de [regels voor het instellen van de gewenste waarde van een digitale dubbele eigenschap](#rules-for-setting-the-desired-value-of-a-digital-twin-property) volgt

Wanneer u een onderdeel bijwerkt, moet u ervoor zorgen dat de [lege object $metadata markering](#add-replace-or-remove-a-component) is ingesteld.

Updates kunnen mislukken als de gerapporteerde waarden van een apparaat niet voldoen aan de [IOT Plug en Play-conventies](./concepts-convention.md#writable-properties).

## <a name="next-steps"></a>Volgende stappen

Nu u over digitale apparaatdubbels hebt geleerd, zijn hier enkele aanvullende bronnen:

- [Interactie met een apparaat vanuit uw oplossing](quickstart-service.md)
- [IoT digitale dubbele REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
