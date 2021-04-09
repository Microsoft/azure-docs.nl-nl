---
title: IoT Plug en Play-conventies | Microsoft Docs
description: Beschrijving van de conventies IoT Plug en Play verwacht dat apparaten worden gebruikt wanneer ze telemetrie en eigenschappen verzenden en opdrachten en eigenschaps updates verwerken.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a8dfb3cacf798559273361ec75cab4570a8a5228
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582677"
---
# <a name="iot-plug-and-play-conventions"></a>Conventies voor IoT Plug en Play

IoT Plug en Play-apparaten moeten een aantal conventies volgen wanneer ze berichten uitwisselen met een IoT-hub. IoT Plug en Play-apparaten gebruiken het MQTT-protocol om te communiceren met IoT Hub.

Apparaten kunnen [modules](../iot-hub/iot-hub-devguide-module-twins.md)bevatten of worden geïmplementeerd in een [IOT Edge module](../iot-edge/about-iot-edge.md) die wordt gehost door de IOT Edge-runtime.

U beschrijft de telemetrie, eigenschappen en opdrachten die een IoT Plug en Play-apparaat implementeert met een [DTDL-model (Digital Apparaatdubbels Definition Language v2)](https://github.com/Azure/opendigitaltwins-dtdl) . Er zijn twee typen model waarnaar wordt verwezen in dit artikel:

- **Geen onderdeel** : een model zonder onderdelen. Het model declareert telemetrie, eigenschappen en opdrachten als eigenschappen op het hoogste niveau in de sectie inhoud van de hoofd interface. In het hulp programma Azure IoT Explorer wordt dit model weer gegeven als één _standaard onderdeel_.
- **Meerdere onderdelen** : een model dat uit twee of meer interfaces bestaat. Een hoofd interface, die wordt weer gegeven als het _standaard onderdeel_, met telemetrie, eigenschappen en opdrachten. Een of meer interfaces die zijn gedeclareerd als onderdelen met aanvullende telemetrie, eigenschappen en opdrachten.

Zie voor meer informatie [IoT Plug en Play Modeling Guide (Engelstalig](concepts-modeling-guide.md)).

## <a name="identify-the-model"></a>Het model identificeren

Voor het aankondigen van het model dat wordt geïmplementeerd, bevat een IoT-Plug en Play apparaat of-module de model-ID in het MQTT-verbindings pakket door toe te voegen `model-id` aan het `USERNAME` veld.

Voor het identificeren van het model dat een apparaat of module implementeert, kan een service de model-ID ophalen uit:

- Het dubbele veld van het apparaat `modelId` .
- Het digitale dubbele `$metadata.$model` veld.
- Een digitale dubbele wijzigings melding.

## <a name="telemetry"></a>Telemetrie

Voor telemetrie die van een apparaat zonder onderdeel wordt verzonden, zijn geen extra meta gegevens vereist. Het systeem voegt de `dt-dataschema` eigenschap toe.

De telemetrie die vanaf een apparaat met meerdere onderdelen wordt verzonden, moet worden toegevoegd `$.sub` als een bericht eigenschap. Het systeem voegt de `dt-subject` Eigenschappen en toe `dt-dataschema` .

## <a name="read-only-properties"></a>Alleen-lezen eigenschappen

### <a name="sample-no-component-read-only-property"></a>Voor beeld geen eigenschap met het kenmerk alleen-lezen

Een apparaat of module kan een wille keurige geldige JSON verzenden die volgt op de DTDL v2-regels.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Voor beeld van gerapporteerde payload van eigenschap:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>Voor beeld van meerdere eigenschappen met het kenmerk alleen-lezen

Het apparaat of de module moet de `{"__t": "c"}` markering toevoegen om aan te geven dat het element naar een onderdeel verwijst.

DTDL die verwijst naar een onderdeel:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL die het onderdeel definieert:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Voor beeld van gerapporteerde payload van eigenschap:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>Beschrijf bare eigenschappen

Het apparaat of de module moet bevestigen dat deze de eigenschap heeft ontvangen door een gerapporteerde eigenschap te verzenden. De gerapporteerde eigenschap moet het volgende bevatten:

- `value` -de werkelijke waarde van de eigenschap (doorgaans de ontvangen waarde, maar het apparaat kan besluiten een andere waarde te rapporteren).
- `ac` -een bevestigings code die gebruikmaakt van een HTTP-status code.
- `av` -een bevestigings versie die naar de `$version` gewenste eigenschap verwijst. U kunt deze waarde vinden in de gewenste JSON-payload van de eigenschap.
- `ad` -een optionele beschrijving van de bevestiging.

Wanneer een apparaat wordt gestart, moet dit het apparaat dubbele aanvragen en controleren of er Beschrijf bare eigenschaps updates zijn. Als de versie van een Beschrijf bare eigenschap is verhoogd terwijl het apparaat offline was, moet het apparaat een antwoord op een eigenschap hebben verzonden om te bevestigen dat het een update heeft ontvangen.

Wanneer een apparaat voor de eerste keer wordt gestart, kan het een initiële waarde voor een gerapporteerde eigenschap verzenden als er geen oorspronkelijke gewenste eigenschap van de hub wordt ontvangen. In dit geval moet het apparaat worden ingesteld `av` op `1` . Bijvoorbeeld:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

Een apparaat kan de gerapporteerde eigenschap gebruiken om andere informatie aan de hub door te geven. Het apparaat kan bijvoorbeeld reageren met een reeks lopende berichten, zoals:

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

Wanneer het apparaat de doel temperatuur bereikt, verzendt het het volgende bericht:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

Een apparaat kan een fout melden, zoals:

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>Voor beeld van geen onderdeel schrijf bare eigenschap

Wanneer een apparaat meerdere gerapporteerde eigenschappen in één nettolading ontvangt, kunnen de gerapporteerde eigenschaps reacties over meerdere nettoladingen worden verzonden.

Een apparaat of module kan een wille keurige geldige JSON verzenden die volgt op de DTDL v2-regels:

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Voor beeld van gewenste payload van eigenschap:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

Voor beeld van de gerapporteerde eigenschap First Payload:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

Voor beeld van een gerapporteerde eigenschap Second Payload:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>Voor beeld van de eigenschap schrijf bare meerdere componenten

Het apparaat of de module moet de `{"__t": "c"}` markering toevoegen om aan te geven dat het element naar een onderdeel verwijst.

De markering wordt alleen verzonden voor updates van eigenschappen die in een onderdeel zijn gedefinieerd. Updates van eigenschappen die in het standaard onderdeel zijn gedefinieerd, bevatten niet de markering. Zie voor [beeld geen onderdeel schrijf bare eigenschap](#sample-no-component-writable-property)

Wanneer een apparaat meerdere gerapporteerde eigenschappen in één nettolading ontvangt, kunnen de gerapporteerde eigenschaps reacties over meerdere nettoladingen worden verzonden.

Het apparaat of de module moet bevestigen dat de eigenschappen zijn ontvangen door de gerapporteerde eigenschappen te verzenden:

DTDL die verwijst naar een onderdeel:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL die het onderdeel definieert:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Voor beeld van gewenste payload van eigenschap:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

Voor beeld van de gerapporteerde eigenschap First Payload:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

Voor beeld van een gerapporteerde eigenschap Second Payload:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>Opdracht

Geen onderdeel interfaces gebruiken de opdracht naam zonder voor voegsel.

Op een apparaat of module gebruiken meerdere onderdeel interfaces opdracht namen met de volgende indeling: `componentName*commandName` .

## <a name="next-steps"></a>Volgende stappen

Nu u over Plug en Play conventies van IoT hebt geleerd, zijn hier enkele aanvullende bronnen:

- [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Apparaat-SDK voor C](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](/rest/api/iothub/device)
- [Model gids voor IoT Plug en Play](concepts-modeling-guide.md)