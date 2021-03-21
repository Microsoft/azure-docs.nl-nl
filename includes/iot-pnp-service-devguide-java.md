---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: 33752c1ebb83c5d63e8e1cb396c52f01f07046cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244503"
---
De volgende resources zijn ook beschikbaar:

- [Referentiedocumentatie voor Java SDK](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice)
- [Voor beelden van service-client](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)
- [Digital Apparaatdubbels-voor beelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples)

## <a name="iot-hub-service-client-examples"></a>Voor beelden van IoT Hub service-client

In deze sectie worden Java-voor beelden weer gegeven met behulp van de IoT Hub-serviceclient en de **DeviceTwin** -en **DeviceMethod** -klassen van de **com. Microsoft. Azure. SDK. IOT. service. DeviceTwin-** naam ruimte. U gebruikt de **DeviceTwin** -klasse om te communiceren met de status van het apparaat met behulp van apparaat apparaatdubbels. U kunt ook de **DeviceTwin** -klasse gebruiken om de [registratie van apparaten](../articles/iot-hub/iot-hub-devguide-query-language.md) in uw IOT hub op te vragen. U gebruikt de **DeviceMethod** -klasse om opdrachten op het apparaat aan te roepen. Het [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -model voor het apparaat definieert de eigenschappen en opdrachten die het apparaat implementeert. In de code fragmenten `deviceId` bevat de variabele de apparaat-id van de IOT-Plug en Play apparaat dat is geregistreerd bij uw IOT-hub.

### <a name="get-the-device-twin-and-model-id"></a>De dubbele en model-ID van het apparaat ophalen

Voor het apparaat dubbele en model-ID van de IoT-Plug en Play apparaat dat is verbonden met uw IoT-hub:

```java
DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);

// ...

DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-device-twin"></a>Dubbele update van apparaat

Het volgende code fragment laat zien hoe de `targetTemperature` eigenschap op een apparaat kan worden bijgewerkt. U moet de dubbele waarde ophalen voordat u deze bijwerkt. De eigenschap is gedefinieerd in het standaard onderdeel van het apparaat:

```java
DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);

double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair("targetTemperature", propertyValue)));
twinClient.updateTwin(twin);
```

Het volgende code fragment laat zien hoe u de `targetTemperature` eigenschap van een onderdeel bijwerkt. U moet de dubbele waarde ophalen voordat u deze bijwerkt. De eigenschap wordt gedefinieerd in het onderdeel **thermostat1** :

```java
public static Set<Pair> CreateComponentPropertyPatch(@NonNull String propertyName, @NonNull double propertyValue, @NonNull String componentName)
{
    JsonObject patchJson = new JsonObject();
    patchJson.addProperty("__t", "c");
    patchJson.addProperty(propertyName, propertyValue);
    return singleton(new Pair(componentName, patchJson));
}

// ...

DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);

double propertyValue = 60.2;
twin.setDesiredProperties(CreateComponentPropertyPatch("targetTemperature", propertyValue, "thermostat1"));
twinClient.updateTwin(twin);
```

Voor een eigenschap in een onderdeel ziet de patch voor de eigenschap er als volgt uit:

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 60.2
  }
}
```

### <a name="call-command"></a>Aanroep opdracht

Het volgende code fragment laat zien hoe u de opdracht aanroept die is `getMaxMinReport` gedefinieerd in een standaard onderdeel:

```java
DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, "getMaxMinReport", responseTimeout, connectTimeout, commandInput);

System.out.println("Method result status is: " + result.getStatus());
```

Het volgende code fragment laat zien hoe u de opdracht aanroept `getMaxMinReport` voor een onderdeel. De opdracht is gedefinieerd in het onderdeel **thermostat1** :

```java
DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, "thermostat1*getMaxMinReport", responseTimeout, connectTimeout, commandInput);

System.out.println("Method result status is: " + result.getStatus());
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub digitale dubbele voor beelden

U gebruikt de **DigitalTwinAsyncClient** -klasse in de naam ruimte **com. Microsoft. Azure. SDK. IOT. service. digitaltwin** om te communiceren met de status van het apparaat met behulp van digitale apparaatdubbels. In de volgende voor beelden worden ook de klassen **UpdateOperationUtility** en **BasicDigitalTwin** van dezelfde naam ruimte gebruikt. Het [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -model voor het apparaat definieert de eigenschappen en opdrachten die het apparaat implementeert.

De `digitalTwinid` variabele bevat de apparaat-id van de IOT-Plug en Play apparaat dat is geregistreerd bij uw IOT-hub.

### <a name="get-the-digital-twin-and-model-id"></a>De digitale dubbele en model-ID ophalen

Om de digitale dubbele en model-ID op te halen van de IoT-Plug en Play apparaat dat is verbonden met uw IoT-hub:

```java
DigitalTwinAsyncClient asyncClient = DigitalTwinAsyncClient.createFromConnectionString(iotHubConnectionString);

CountDownLatch latch = new CountDownLatch(1);
asyncClient.getDigitalTwin(digitalTwinid, BasicDigitalTwin.class)
    .subscribe(
        getResponse ->
        {
            System.out.println("Digital Twin Model Id: " + getResponse.getMetadata().getModelId());
            System.out.println("Digital Twin: " + prettyBasicDigitalTwin(getResponse));
            latch.countDown();
        },
        error ->
        {
            System.out.println("Get Digital Twin failed: " + error);
            latch.countDown();
        });

latch.await(10, TimeUnit.SECONDS);

// ...

private static String prettyBasicDigitalTwin(BasicDigitalTwin basicDigitalTwin)
{
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(basicDigitalTwin);
}
```

### <a name="update-digital-twin"></a>Digitale dubbele

Het volgende code fragment laat zien hoe de `targetTemperature` eigenschap op een apparaat kan worden bijgewerkt. De eigenschap is gedefinieerd in het standaard onderdeel van het apparaat:

```java
DigitalTwinAsyncClient asyncClient = DigitalTwinAsyncClient.createFromConnectionString(iotHubConnectionString);

CountDownLatch latch1 = new CountDownLatch(1);

UpdateOperationUtility updateOperationUtility = new UpdateOperationUtility();

// Add a new property.
updateOperationUtility.appendAddPropertyOperation("/" + "targetTemperature", 35);
asyncClient.updateDigitalTwin(digitalTwinid, updateOperationUtility.getUpdateOperations())
    .subscribe(
        getResponse ->
        {
            System.out.println("Updated Digital Twin");
            latch1.countDown();
        },
        error ->
        {
            System.out.println("Update Digital Twin failed: " + error);
            latch1.countDown();
        });
latch1.await(10, TimeUnit.SECONDS);
GetDigitalTwin();

// Replace an existing property.
CountDownLatch latch2 = new CountDownLatch(1);
updateOperationUtility.appendReplacePropertyOperation("/targetTemperature", 50);
asyncClient.updateDigitalTwin(digitalTwinid, updateOperationUtility.getUpdateOperations())
    .subscribe(
        getResponse ->
        {
            System.out.println("Updated Digital Twin");
            latch2.countDown();
        },
        error ->
        {
            System.out.println("Update Digital Twin failed: " + error);
            latch2.countDown();
        });

latch2.await(10, TimeUnit.SECONDS);
GetDigitalTwin();
```

Het volgende code fragment laat zien hoe u de `targetTemperature` eigenschap van een onderdeel bijwerkt. De eigenschap wordt gedefinieerd in het onderdeel **thermostat1** :

```java
DigitalTwinClient client = DigitalTwinClient.createFromConnectionString(iotHubConnectionString);

// Get digital twin.
ServiceResponseWithHeaders<String, DigitalTwinGetHeaders> getResponse = client.getDigitalTwinWithResponse(digitalTwinid, String.class);

// Construct the options for conditional update.
DigitalTwinUpdateRequestOptions options = new DigitalTwinUpdateRequestOptions();
options.setIfMatch(getResponse.headers().eTag());

UpdateOperationUtility updateOperationUtility = new UpdateOperationUtility();

Map<String, Object> t1properties = new HashMap<>();
t1properties.put("targetTemperature", 50);
updateOperationUtility.appendReplaceComponentOperation("/thermostat1", t1properties);

digitalTwinUpdateOperations = updateOperationUtility.getUpdateOperations();
updateResponse = client.updateDigitalTwinWithResponse(digitalTwinid, digitalTwinUpdateOperations, options);
System.out.println("Update Digital Twin response status: " + updateResponse.response().message());

getResponse = client.getDigitalTwinWithResponse(digitalTwinid, String.class);
```

### <a name="call-command"></a>Aanroep opdracht

Het volgende code fragment laat zien hoe u de opdracht aanroept die is `getMaxMinReport` gedefinieerd in een standaard onderdeel:

```java
CountDownLatch latch = new CountDownLatch(1);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);

// Invoke a method on root level.
asyncClient.invokeCommand(digitalTwinid, "getMaxMinReport", commandInput)
    .subscribe(
        response ->
        {
            System.out.println("Invoked Command getMaxMinReport response: " + prettyString(response.getPayload()));
            latch.countDown();
        },
        error ->
        {
            RestException ex = (RestException)error;
            if(ex.response().code() == 404) {
                System.out.println("Invoked Command getMaxMinReport failed: " + error);
            }
            else {
                System.out.println("Ensure the device sample is running for this sample to succeed");
            }
            latch.countDown();
        });

latch.await(10, TimeUnit.SECONDS);

// ...

private static String prettyString(String str)
{
    Gson gson = new Gson();
    Gson gsonBuilder = new GsonBuilder().setPrettyPrinting().create();
    return gsonBuilder.toJson(gson.fromJson(str, Object.class));
}
```

Het volgende code fragment laat zien hoe u de opdracht aanroept `getMaxMinReport` voor een onderdeel. De opdracht is gedefinieerd in het onderdeel **thermostat1** :

```java
DigitalTwinClient client = DigitalTwinClient.createFromConnectionString(iotHubConnectionString);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);

DigitalTwinInvokeCommandRequestOptions options = new DigitalTwinInvokeCommandRequestOptions();
try {
    ServiceResponseWithHeaders<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> commandResponse = client.invokeComponentCommandWithResponse(digitalTwinid, "thermostat1", "getMaxMinReport", commandInput, options);
    System.out.println("Command getMaxMinReport, payload: " + prettyString(commandResponse.body().getPayload()));
    System.out.println("Command getMaxMinReport, status: " + commandResponse.body().getStatus());
} catch (RestException ex)
{
    if(ex.response().code() == 404)
    {
        System.out.println("Ensure the device sample is running for this sample to succeed.");
    }
    else
    {
        throw ex;
    }
}

// ...

private static String prettyString(String str)
{
    Gson gson = new Gson();
    Gson gsonBuilder = new GsonBuilder().setPrettyPrinting().create();
    return gsonBuilder.toJson(gson.fromJson(str, Object.class));
}
```

## <a name="read-device-telemetry"></a>Telemetrie van apparaat lezen

IoT Plug en Play-apparaten verzenden de telemetrie die in het DTDL-model is gedefinieerd, naar IoT Hub. IoT Hub stuurt de telemetrie standaard naar een Event Hubs-eind punt waar u het kunt gebruiken. Zie [IOT hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)voor meer informatie.

Het volgende code fragment laat zien hoe u de telemetrie kunt lezen vanuit het standaard Event Hubs-eind punt. De code in dit fragment wordt overgenomen van de IoT Hub Quick Start [telemetrie verzenden van een apparaat naar een IOT-hub en deze lezen met een back-endtoepassing](../articles/iot-hub/quickstart-send-telemetry-java.md):

```java
import com.azure.messaging.eventhubs.EventHubClientBuilder;
import com.azure.messaging.eventhubs.EventHubConsumerAsyncClient;

// ...

EventHubClientBuilder eventHubClientBuilder = new EventHubClientBuilder()
    .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
    .connectionString(eventHubCompatibleConnectionString);

try (EventHubConsumerAsyncClient eventHubConsumerAsyncClient = eventHubClientBuilder.buildAsyncConsumerClient()) {

    receiveFromAllPartitions(eventHubConsumerAsyncClient);

}

// ...

private static void receiveFromAllPartitions(EventHubConsumerAsyncClient eventHubConsumerAsyncClient) {

eventHubConsumerAsyncClient
    .receive(false) // set this to false to read only the newly available events
    .subscribe(partitionEvent -> {
        System.out.println();
        System.out.printf("%nTelemetry received from partition %s:%n%s",
            partitionEvent.getPartitionContext().getPartitionId(), partitionEvent.getData().getBodyAsString());
        System.out.printf("%nApplication properties (set by device):%n%s", partitionEvent.getData().getProperties());
        System.out.printf("%nSystem properties (set by IoT Hub):%n%s",
            partitionEvent.getData().getSystemProperties());
    }, ex -> {
        System.out.println("Error receiving events " + ex);
    }, () -> {
        System.out.println("Completed receiving events");
    });
}
```

In de volgende uitvoer van de vorige code wordt de telemetrie van de Tempe ratuur weer gegeven die wordt verzonden door de niet-component **Thermo** staat IOT Plug en Play apparaat die alleen het standaard onderdeel heeft. De `dt-dataschema` eigenschap System toont de model-id:

```cmd/sh
Telemetry received from partition 1:
{"temperature": 10.700000}
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=dd960185-6ddb-4b5f-89bb-e26b0b3c201e, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:28:10 BST 2020, dt-dataschema=dtmi:com:example:Thermostat;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637375776990653481, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=1c05cece-070b-4e2e-b2e8-e263858594a3, content-type=application/json}

Telemetry received from partition 1:
{"temperature": 10.700000}
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=d10a7350-43ef-4cf6-9db5-a4b08684cd9d, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:28:15 BST 2020, dt-dataschema=dtmi:com:example:Thermostat;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637375776990653481, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=d3a80af4-1246-41a0-a09a-582a12c17a00, content-type=application/json}
```

In de volgende uitvoer van de vorige code wordt de telemetrie van de Tempe ratuur weer gegeven die wordt verzonden door het **TemperatureController** IOT Plug en Play-apparaat met meerdere onderdelen. De `dt-subject` eigenschap System toont de naam van het onderdeel dat de telemetrie heeft verzonden. In dit voor beeld zijn de twee onderdelen `thermostat1` en `thermostat2` zoals gedefinieerd in het DTDL-model. De `dt-dataschema` eigenschap System toont de model-id:

```cmd/sh
Telemetry received from partition 1:
null
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=413002d0-2107-4c08-8f4a-995ae1f4047b, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:31:14 BST 2020, dt-dataschema=dtmi:com:example:TemperatureController;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637387902591517456, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=da8bd068-850e-43fb-862f-66080d5969e4, content-type=application/json, dt-subject=thermostat1}

Telemetry received from partition 1:
null
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=2d9407e5-413f-4f8d-bd30-cd153e03c72f, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:31:14 BST 2020, dt-dataschema=dtmi:com:example:TemperatureController;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637387902591517456, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=ed419c4e-ef2c-4acf-8991-6245059c5fdc, content-type=application/json, dt-subject=thermostat2}
```

## <a name="read-device-twin-change-notifications"></a>Dubbele wijzigings meldingen voor apparaat lezen

U kunt IoT Hub configureren voor het genereren van dubbele wijzigings meldingen van een apparaat om naar een ondersteund eind punt te sturen. Zie [IOT hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten > niet-telemetrie-gebeurtenissen](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)voor meer informatie.

De code die in het vorige code fragment van Java wordt weer gegeven, genereert de volgende uitvoer wanneer IoT Hub Dubbelklik meldingen voor het apparaat genereert voor een thermo staat zonder onderdeel. De toepassings eigenschappen `iothub-message-schema` en `opType` geven informatie over het type wijzigings melding:

```cmd/sh
Telemetry received from partition 1:
{"version":11,"properties":{"reported":{"maxTempSinceLastReboot":43.4,"$metadata":{"$lastUpdated":"2020-10-20T11:50:41.123127Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-20T11:50:41.123127Z"}},"$version":10}}}
Application properties (set by device):
{operationTimestamp=2020-10-20T11:50:41.1231270+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=twinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@12fd5bb4, correlation-id=11339418426a, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:50:41 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=twinChangeEvents, creation-time=0, content-type=application/json}
```

De code die wordt weer gegeven in het vorige code fragment van Java genereert de volgende uitvoer wanneer IoT Hub apparaatstuurprogramma's met dubbele wijzigings meldingen voor een apparaat met onderdelen genereert. In dit voor beeld wordt de uitvoer weer gegeven wanneer een temperatuur sensor-apparaat met een thermo staat-onderdeel meldingen genereert. De toepassings eigenschappen `iothub-message-schema` en `opType` geven informatie over het type wijzigings melding:

```cmd/sh
Telemetry received from partition 1:
{"version":9,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":32.5},"$metadata":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z","thermostat1":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z","__t":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z"}}},"$version":8}}}
Application properties (set by device):
{operationTimestamp=2020-10-20T11:48:01.2960851+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=twinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@23949bae, correlation-id=113334d542e1, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:48:01 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=twinChangeEvents, creation-time=0, content-type=application/json}
```

## <a name="read-digital-twin-change-notifications"></a>Lees de informatie over digitale dubbele wijzigings meldingen

U kunt IoT Hub zo configureren dat er digitale dubbele wijzigings meldingen worden gegenereerd om naar een ondersteund eind punt te sturen. Zie [IOT hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten > niet-telemetrie-gebeurtenissen](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)voor meer informatie.

De code die wordt weer gegeven in het vorige code fragment van Java genereert de volgende uitvoer wanneer IoT Hub digitale dubbele wijzigings meldingen genereert voor een thermo staat zonder onderdeel. De toepassings eigenschappen `iothub-message-schema` en `opType` geven informatie over het type wijzigings melding:

```cmd/sh
Telemetry received from partition 1:
[{"op":"replace","path":"/$metadata/maxTempSinceLastReboot/lastUpdateTime","value":"2020-10-20T11:52:40.627628Z"},{"op":"replace","path":"/maxTempSinceLastReboot","value":16.9}]
Application properties (set by device):
{operationTimestamp=2020-10-20T11:52:40.6276280+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=digitalTwinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@4475ce2a, correlation-id=1133db52c0e0, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:52:40 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=digitalTwinChangeEvents, creation-time=0, content-type=application/json-patch+json}
```

De code die wordt weer gegeven in het vorige code fragment van Java genereert de volgende uitvoer wanneer IoT Hub digitale dubbele wijzigings meldingen genereert voor een apparaat met onderdelen. In dit voor beeld wordt de uitvoer weer gegeven wanneer een temperatuur sensor-apparaat met een thermo staat-onderdeel meldingen genereert. De toepassings eigenschappen `iothub-message-schema` en `opType` geven informatie over het type wijzigings melding:

```cmd/sh
Telemetry received from partition 1:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-20T11:31:04.7811405Z"}},"maxTempSinceLastReboot":27.2}}]
Application properties (set by device):
{operationTimestamp=2020-10-20T11:31:04.7811405+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=digitalTwinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@75981aa, correlation-id=1130d6f4d212, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:31:04 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=digitalTwinChangeEvents, creation-time=0, content-type=application/json-patch+json}
```
