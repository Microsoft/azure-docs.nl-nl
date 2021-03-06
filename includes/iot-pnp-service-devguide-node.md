---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: dfeeb451912dd32770a6ae92d73de83851d9d8f6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244667"
---
De volgende resources zijn ook beschikbaar:

- [ Documentatie overNode.js SDK](/javascript/api/azure-iothub)
- [Voor beelden van service-client](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [Digital Apparaatdubbels-voor beelden](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>Voor beelden van IoT Hub service-client

In deze sectie worden java script-voor beelden van de IoT Hub-serviceclient en het **REGI ster** -en **client** klassen weer gegeven. U gebruikt de **register** klasse om te communiceren met de status van het apparaat met behulp van Device apparaatdubbels. U kunt ook de **register** klasse gebruiken om apparaatregistratie in uw IOT hub op te [vragen](../articles/iot-hub/iot-hub-devguide-query-language.md) . U gebruikt de **client** klasse om opdrachten op het apparaat aan te roepen. Het [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -model voor het apparaat definieert de eigenschappen en opdrachten die het apparaat implementeert. In de code fragmenten `deviceId` bevat de variabele de apparaat-id van de IOT-Plug en Play apparaat dat is geregistreerd bij uw IOT-hub.

### <a name="get-the-device-twin-and-model-id"></a>De dubbele en model-ID van het apparaat ophalen

Voor het apparaat dubbele en model-ID van de IoT-Plug en Play apparaat dat is verbonden met uw IoT-hub:

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>Dubbele update van apparaat

Het volgende code fragment laat zien hoe de `targetTemperature` eigenschap op een apparaat kan worden bijgewerkt. In het voor beeld ziet u hoe u het dubbele moet verkrijgen voordat u het bijwerkt. De eigenschap is gedefinieerd in het standaard onderdeel van het apparaat:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

Het volgende code fragment laat zien hoe u de `targetTemperature` eigenschap van een onderdeel bijwerkt. In het voor beeld ziet u hoe u het dubbele moet verkrijgen voordat u het bijwerkt. De eigenschap wordt gedefinieerd in het onderdeel **thermostat1** :

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

Voor een eigenschap in een onderdeel ziet de patch voor de eigenschap er als volgt uit:

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Aanroep opdracht

Het volgende code fragment laat zien hoe u de opdracht aanroept die is `getMaxMinReport` gedefinieerd in een standaard onderdeel:

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

Het volgende code fragment laat zien hoe u de opdracht aanroept `getMaxMinReport` voor een onderdeel. De opdracht is gedefinieerd in het onderdeel **thermostat1** :

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub digitale dubbele voor beelden

U gebruikt de **DigitalTwinClient** -klasse om te communiceren met de status van het apparaat met behulp van digitale apparaatdubbels. Het [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -model voor het apparaat definieert de eigenschappen en opdrachten die het apparaat implementeert.

In deze sectie worden java script-voor beelden weer gegeven met de Digital Apparaatdubbels-API.

De `digitalTwinId` variabele bevat de apparaat-id van de IOT-Plug en Play apparaat dat is geregistreerd bij uw IOT-hub.

### <a name="get-the-digital-twin-and-model-id"></a>De digitale dubbele en model-ID ophalen

Om de digitale dubbele en model-ID op te halen van de IoT-Plug en Play apparaat dat is verbonden met uw IoT-hub:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>Digitale dubbele

Het volgende code fragment laat zien hoe de `targetTemperature` eigenschap op een apparaat kan worden bijgewerkt. De eigenschap is gedefinieerd in het standaard onderdeel van het apparaat:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

Het volgende code fragment laat zien hoe u de `targetTemperature` eigenschap van een onderdeel bijwerkt. De eigenschap wordt gedefinieerd in het onderdeel **thermostat1** :

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>Aanroep opdracht

Het volgende code fragment laat zien hoe u de opdracht aanroept die is `getMaxMinReport` gedefinieerd in een standaard onderdeel:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

Het volgende code fragment laat zien hoe u de opdracht aanroept `getMaxMinReport` voor een onderdeel. De opdracht is gedefinieerd in het onderdeel **thermostat1** :

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>Telemetrie van apparaat lezen

IoT Plug en Play-apparaten verzenden de telemetrie die in het DTDL-model is gedefinieerd, naar IoT Hub. IoT Hub stuurt de telemetrie standaard naar een Event Hubs-eind punt waar u het kunt gebruiken. Zie [IOT hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)voor meer informatie.

Het volgende code fragment laat zien hoe u de telemetrie kunt lezen vanuit het standaard Event Hubs-eind punt. De code in dit fragment wordt overgenomen van de IoT Hub Quick Start [telemetrie verzenden van een apparaat naar een IOT-hub en deze lezen met een back-endtoepassing](../articles/iot-hub/quickstart-send-telemetry-node.md):

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

In de volgende uitvoer van de vorige code wordt de telemetrie van de Tempe ratuur weer gegeven die wordt verzonden door het **TemperatureController** IOT Plug en Play-apparaat met meerdere onderdelen. De `dt-subject` eigenschap System toont de naam van het onderdeel dat de telemetrie heeft verzonden. In dit voor beeld zijn de twee onderdelen `thermostat1` en `thermostat2` zoals gedefinieerd in het DTDL-model. De `dt-dataschema` eigenschap System toont de model-id:

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>Dubbele wijzigings meldingen voor apparaat lezen

U kunt IoT Hub configureren voor het genereren van dubbele wijzigings meldingen van een apparaat om naar een ondersteund eind punt te sturen. Zie [IOT hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten > niet-telemetrie-gebeurtenissen](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)voor meer informatie.

De code die wordt weer gegeven in het vorige java script-code fragment genereert de volgende uitvoer wanneer IoT Hub Dubbelklik meldingen voor het apparaat genereert voor een thermo staat zonder onderdeel. De toepassings eigenschappen `iothub-message-schema` en `opType` geven informatie over het type wijzigings melding:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

De code die wordt weer gegeven in het vorige java script-code fragment genereert de volgende uitvoer wanneer IoT Hub apparaatstuurprogramma's met dubbele wijzigingen genereert voor een apparaat met onderdelen. In dit voor beeld wordt de uitvoer weer gegeven wanneer een temperatuur sensor-apparaat met een thermo staat-onderdeel meldingen genereert. De toepassings eigenschappen `iothub-message-schema` en `opType` geven informatie over het type wijzigings melding:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>Lees de informatie over digitale dubbele wijzigings meldingen

U kunt IoT Hub zo configureren dat er digitale dubbele wijzigings meldingen worden gegenereerd om naar een ondersteund eind punt te sturen. Zie [IOT hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten > niet-telemetrie-gebeurtenissen](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)voor meer informatie.

De code die wordt weer gegeven in het vorige java script-code fragment genereert de volgende uitvoer wanneer IoT Hub digitale dubbele wijzigings meldingen genereert voor een thermo staat zonder onderdeel. De toepassings eigenschappen `iothub-message-schema` en `opType` geven informatie over het type wijzigings melding:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

De code die wordt weer gegeven in het vorige java script-code fragment genereert de volgende uitvoer wanneer IoT Hub digitale dubbele wijzigings meldingen genereert voor een apparaat met onderdelen. In dit voor beeld wordt de uitvoer weer gegeven wanneer een temperatuur sensor-apparaat met een thermo staat-onderdeel meldingen genereert. De toepassings eigenschappen `iothub-message-schema` en `opType` geven informatie over het type wijzigings melding:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```
