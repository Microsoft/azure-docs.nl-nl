---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: a925c3a17988ef6f4b95a1e3cf4dd5fb8baa4829
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102511126"
---
De volgende resources zijn ook beschikbaar:

- [Referentiedocumentatie voor Python SDK](/python/api/azure-iot-hub/azure.iot.hub)
- [Voor beelden van service-client](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [Digital Apparaatdubbels-voor beelden](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>Voor beelden van IoT Hub service-client

In deze sectie worden python-voor beelden weer gegeven met behulp van de IoT Hub-serviceclient en de klassen **IoTHubRegistryManager** en **CloudToDeviceMethod** . U gebruikt de **IoTHubRegistryManager** -klasse om te communiceren met de status van het apparaat met behulp van apparaat apparaatdubbels. U kunt ook de **IoTHubRegistryManager** -klasse gebruiken om de [registratie van apparaten](../articles/iot-hub/iot-hub-devguide-query-language.md) in uw IOT hub op te vragen. U gebruikt de **CloudToDeviceMethod** -klasse om opdrachten op het apparaat aan te roepen. Het [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -model voor het apparaat definieert de eigenschappen en opdrachten die het apparaat implementeert. In de code fragmenten `device_id` bevat de variabele de apparaat-id van de IOT-Plug en Play apparaat dat is geregistreerd bij uw IOT-hub.

### <a name="get-the-device-twin-and-model-id"></a>De dubbele en model-ID van het apparaat ophalen

Voor het apparaat dubbele en model-ID van de IoT-Plug en Play apparaat dat is verbonden met uw IoT-hub:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>Dubbele update van apparaat

Het volgende code fragment laat zien hoe de `targetTemperature` eigenschap op een apparaat kan worden bijgewerkt. In het voor beeld ziet u hoe u de dubbele informatie moet ophalen `etag` voordat u deze bijwerkt. De eigenschap is gedefinieerd in het standaard onderdeel van het apparaat:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

Het volgende code fragment laat zien hoe u de `targetTemperature` eigenschap van een onderdeel bijwerkt. In het voor beeld ziet u hoe u de dubbele informatie moet ophalen `ETag` voordat u deze bijwerkt. De eigenschap wordt gedefinieerd in het onderdeel **thermostat1** :

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
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

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

Het volgende code fragment laat zien hoe u de opdracht aanroept `getMaxMinReport` voor een onderdeel. De opdracht is gedefinieerd in het onderdeel **thermostat1** :

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub digitale dubbele voor beelden

U gebruikt de **DigitalTwinClient** -klasse om te communiceren met de status van het apparaat met behulp van digitale apparaatdubbels. Het [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -model voor het apparaat definieert de eigenschappen en opdrachten die het apparaat implementeert.

De `device_id` variabele bevat de apparaat-id van de IOT-Plug en Play apparaat dat is geregistreerd bij uw IOT-hub.

### <a name="get-the-digital-twin-and-model-id"></a>De digitale dubbele en model-ID ophalen

Om de digitale dubbele en model-ID op te halen van de IoT-Plug en Play apparaat dat is verbonden met uw IoT-hub:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>Digitale dubbele

Het volgende code fragment laat zien hoe de `targetTemperature` eigenschap op een apparaat kan worden bijgewerkt. De eigenschap is gedefinieerd in het standaard onderdeel van het apparaat:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

Het volgende code fragment laat zien hoe u de `targetTemperature` eigenschap van een onderdeel bijwerkt. De eigenschap wordt gedefinieerd in het onderdeel **thermostat1** :

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>Aanroep opdracht

Het volgende code fragment laat zien hoe u de opdracht aanroept die is `getMaxMinReport` gedefinieerd in een standaard onderdeel:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

Het volgende code fragment laat zien hoe u de opdracht aanroept `getMaxMinReport` voor een onderdeel. De opdracht is gedefinieerd in het onderdeel **thermostat1** :

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>Telemetrie van apparaat lezen

IoT Plug en Play-apparaten verzenden de telemetrie die in het DTDL-model is gedefinieerd, naar IoT Hub. IoT Hub stuurt de telemetrie standaard naar een Event Hubs-eind punt waar u het kunt gebruiken. Zie [IOT hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)voor meer informatie.

Het volgende code fragment laat zien hoe u de telemetrie kunt lezen vanuit het standaard Event Hubs-eind punt. De code in dit fragment wordt overgenomen van de IoT Hub Quick Start [telemetrie verzenden van een apparaat naar een IOT-hub en deze lezen met een back-endtoepassing](../articles/iot-hub/quickstart-send-telemetry-python.md):

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

In de volgende uitvoer van de vorige code wordt de telemetrie van de Tempe ratuur weer gegeven die wordt verzonden door de niet-component **Thermo** staat IOT Plug en Play apparaat die alleen het standaard onderdeel heeft. De `dt-dataschema` eigenschap System toont de model-id:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

In de volgende uitvoer van de vorige code wordt de telemetrie van de Tempe ratuur weer gegeven die wordt verzonden door het **TemperatureController** IOT Plug en Play-apparaat met meerdere onderdelen. De `dt-subject` eigenschap System toont de naam van het onderdeel dat de telemetrie heeft verzonden. In dit voor beeld zijn de twee onderdelen `thermostat1` en `thermostat2` zoals gedefinieerd in het DTDL-model. De `dt-dataschema` eigenschap System toont de model-id:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>Dubbele wijzigings meldingen voor apparaat lezen

U kunt IoT Hub configureren voor het genereren van dubbele wijzigings meldingen van een apparaat om naar een ondersteund eind punt te sturen. Zie [IOT hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten > niet-telemetrie-gebeurtenissen](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)voor meer informatie.

De code die wordt weer gegeven in het vorige python-code fragment genereert de volgende uitvoer wanneer IoT Hub apparaatstuurprogramma's met dubbele wijzigings meldingen genereert voor een thermo staat zonder onderdeel. De toepassings eigenschappen `iothub-message-schema` en `opType` geven informatie over het type wijzigings melding:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

De code die wordt weer gegeven in het vorige python-code fragment genereert de volgende uitvoer wanneer IoT Hub apparaatstuurprogramma's met dubbele wijzigings meldingen voor een apparaat met onderdelen genereert. In dit voor beeld wordt de uitvoer weer gegeven wanneer een temperatuur sensor-apparaat met een thermo staat-onderdeel meldingen genereert. De toepassings eigenschappen `iothub-message-schema` en `opType` geven informatie over het type wijzigings melding:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>Lees de informatie over digitale dubbele wijzigings meldingen

U kunt IoT Hub zo configureren dat er digitale dubbele wijzigings meldingen worden gegenereerd om naar een ondersteund eind punt te sturen. Zie [IOT hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten > niet-telemetrie-gebeurtenissen](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)voor meer informatie.

De code die wordt weer gegeven in het vorige python-code fragment genereert de volgende uitvoer wanneer IoT Hub digitale dubbele wijzigings meldingen genereert voor een thermo staat zonder onderdeel. De toepassings eigenschappen `iothub-message-schema` en `opType` geven informatie over het type wijzigings melding:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

De code die wordt weer gegeven in het vorige python-code fragment genereert de volgende uitvoer wanneer IoT Hub digitale dubbele wijzigings meldingen genereert voor een apparaat met onderdelen. In dit voor beeld wordt de uitvoer weer gegeven wanneer een temperatuur sensor-apparaat met een thermo staat-onderdeel meldingen genereert. De toepassings eigenschappen `iothub-message-schema` en `opType` geven informatie over het type wijzigings melding:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```
