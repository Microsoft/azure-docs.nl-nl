---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: b72bbe944a2709d639fdbc3ca467e6950a4c9fbf
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511456"
---
## <a name="model-id-announcement"></a>Aankondiging van model-ID

Als u de model-ID wilt aankondigen, moet het apparaat deze in de verbindings informatie bevatten:

```java
ClientOptions options = new ClientOptions();
options.setModelId(MODEL_ID);
deviceClient = new DeviceClient(deviceConnectionString, protocol, options);
```

De `ClientOptions` overbelasting is beschikbaar in alle `DeviceClient` methoden die worden gebruikt voor het initialiseren van een verbinding.

> [!TIP]
> Voor modules en IoT Edge, gebruikt u `ModuleClient` in plaats van `DeviceClient` .

## <a name="dps-payload"></a>DPS-nettolading

Apparaten met behulp van de [Device Provisioning Service (DPS)](../articles/iot-dps/about-iot-dps.md) kunnen de gebruiken die `modelId` tijdens het inrichtings proces moet worden gebruikt met behulp van de volgende JSON-nettolading.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Telemetrie, eigenschappen en opdrachten implementeren

Zoals beschreven in [inzicht in onderdelen in IoT Plug en Play-modellen](../articles/iot-pnp/concepts-components.md), moeten apparaten kunnen bepalen of ze onderdelen willen gebruiken om hun apparaten te beschrijven. Bij het gebruik van onderdelen moeten apparaten voldoen aan de regels die in deze sectie worden beschreven.

### <a name="telemetry"></a>Telemetrie

Voor een standaard onderdeel is geen speciale eigenschap vereist.

Bij het gebruik van geneste onderdelen moeten apparaten een bericht eigenschap met de naam van het onderdeel instellen:

```java
private static void sendTemperatureTelemetry(String componentName) {
  double currentTemperature = temperature.get(componentName);

  Map<String, Object> payload = singletonMap("temperature", currentTemperature);

  Message message = new Message(gson.toJson(payload));
  message.setContentEncoding("utf-8");
  message.setContentTypeFinal("application/json");

  if (componentName != null) {
      message.setProperty("$.sub", componentName);
  }
  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
}
```

### <a name="read-only-properties"></a>Alleen-lezen eigenschappen

Voor het melden van een eigenschap vanuit het standaard onderdeel is geen speciale construct vereist:

```java
Property reportedProperty = new Property("maxTempSinceLastReboot", 38.7);

deviceClient.sendReportedProperties(Collections.singleton(reportedProperty));
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

Bij het gebruik van geneste onderdelen moeten eigenschappen worden gemaakt binnen de naam van het onderdeel:

```java
Map<String, Object> componentProperty = new HashMap<String, Object>() {{
    put("__t", "c");
    put("maxTemperature", 38.7);
}};

Set<Property> reportedProperty = new Property("thermostat1", componentProperty)

deviceClient.sendReportedProperties(reportedProperty);
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     }
  }
}
```

### <a name="writable-properties"></a>Beschrijf bare eigenschappen

Deze eigenschappen kunnen worden ingesteld door het apparaat of worden bijgewerkt door de oplossing. Als met de oplossing een eigenschap wordt bijgewerkt, ontvangt de client een melding als een retour aanroep in de `DeviceClient` of `ModuleClient` . Als u de IoT Plug en Play-conventies wilt volgen, moet het apparaat de service op de hoogte stellen dat de eigenschap is ontvangen.

#### <a name="report-a-writable-property"></a>Een schrijf bare eigenschap rapporteren

Wanneer een apparaat een Beschrijf bare eigenschap rapporteert, moet dit de waarden bevatten die zijn `ack` gedefinieerd in de conventies.

Een Beschrijf bare eigenschap van het standaard onderdeel melden:

```java
@AllArgsConstructor
private static class EmbeddedPropertyUpdate {
  @NonNull
  @SerializedName("value")
  public Object value;
  @NonNull
  @SerializedName("ac")
  public Integer ackCode;
  @NonNull
  @SerializedName("av")
  public Integer ackVersion;
  @SerializedName("ad")
  public String ackDescription;
}

EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(23.2, 200, 3, "Successfully updated target temperature");
Property reportedPropertyCompleted = new Property("targetTemperature", completedUpdate);
deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

Als u een Beschrijf bare eigenschap van een genest onderdeel wilt melden, moet de dubbele markering het volgende bevatten:

```java
Map<String, Object> embeddedProperty = new HashMap<String, Object>() {{
    put("value", 23.2);
    put("ac", 200);
    put("av", 3);
    put("ad", "complete");
}};

Map<String, Object> componentProperty = new HashMap<String, Object>() {{
    put("__t", "c");
    put("targetTemperature", embeddedProperty);
}};

Set<Property> reportedProperty = new Property("thermostat1", componentProperty));

deviceClient.sendReportedProperties(reportedProperty);
```

Het dubbele apparaat wordt bijgewerkt met de volgende gerapporteerde eigenschap:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Abonneren op de gewenste eigenschaps updates

Services kunnen de gewenste eigenschappen bijwerken waarmee een melding op de verbonden apparaten wordt geactiveerd. Deze melding bevat de bijgewerkte gewenste eigenschappen, inclusief het versie nummer waarmee de update wordt geïdentificeerd. Apparaten moeten reageren met hetzelfde `ack` bericht als gerapporteerde eigenschappen.

Een standaard onderdeel ziet de eigenschap single en maakt de gerapporteerde `ack` met de ontvangen versie:

```java
private static class TargetTemperatureUpdateCallback implements TwinPropertyCallBack {

    String propertyName = "targetTemperature";

    @Override
    public void TwinPropertyCallBack(Property property, Object context) {
        double targetTemperature = ((Number)property.getValue()).doubleValue();

        EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(temperature, 200, property.getVersion(), "Successfully updated target temperature");
        Property reportedPropertyCompleted = new Property(propertyName, completedUpdate);
        deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
    }
}

// ...

deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new TargetTemperatureUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback =
  Collections.singletonMap(
    new Property("targetTemperature", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), null));
deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

Het apparaat dubbele toont de eigenschap in de gewenste en gerapporteerde secties:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "Successfully updated target temperature"
      }
  }
}
```

Een geneste component ontvangt de gewenste eigenschappen met de naam van het onderdeel en moet de gerapporteerde eigenschap weer geven `ack` :

```java
private static final Map<String, Double> temperature = new HashMap<>();

private static class TargetTemperatureUpdateCallback implements TwinPropertyCallBack {

    String propertyName = "targetTemperature";

    @Override
    public void TwinPropertyCallBack(Property property, Object context) {
        String componentName = (String) context;

        if (property.getKey().equalsIgnoreCase(componentName)) {
            double targetTemperature = (double) ((TwinCollection) property.getValue()).get(propertyName);

            Map<String, Object> embeddedProperty = new HashMap<String, Object>() {{
                put("value", temperature.get(componentName));
                put("ac", 200);
                put("av", property.getVersion().longValue());
                put("ad", "Successfully updated target temperature.");
            }};

            Map<String, Object> componentProperty = new HashMap<String, Object>() {{
                put("__t", "c");
                put(propertyName, embeddedProperty);
            }};

            Set<Property> completedPropertyPatch = new Property(componentName, componentProperty));

            deviceClient.sendReportedProperties(completedPropertyPatch);
        } else {
            log.debug("Property: Received an unrecognized property update from service.");
        }
    }
}

// ...

deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
  new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
    new Property("thermostat1", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), "thermostat1")),
  new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
    new Property("thermostat2", null),
    new Pair<>(new TargetTemperatureUpdateCallback(), "thermostat2"))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

Het apparaat dubbele voor een genest onderdeel toont de gewenste en gerapporteerde secties als volgt:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Opdrachten

Een standaard onderdeel ontvangt de opdracht naam zoals deze is aangeroepen door de service.

Een genest onderdeel ontvangt de opdracht naam voorafgegaan door de naam van het onderdeel en het `*` scheidings teken.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);

// ...
private static final Map<String, Double> temperature = new HashMap<>();

private static class MethodCallback implements DeviceMethodCallback {
  final String reboot = "reboot";
  final String getMaxMinReport1 = "thermostat1*getMaxMinReport";
  final String getMaxMinReport2 = "thermostat2*getMaxMinReport";

  @Override
  public DeviceMethodData call(String methodName, Object methodData, Object context) {
    String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);

    switch (methodName) {
      case reboot:
        int delay = gson.fromJson(jsonRequest, Integer.class);

        Thread.sleep(delay * 1000);

        temperature.put("thermostat1", 0.0d);
        temperature.put("thermostat2", 0.0d);

        return new DeviceMethodData(200, null);

      // ...

      default:
        log.debug("Command: command=\"{}\" is not implemented, no action taken.", methodName);
          return new DeviceMethodData(404, null);
    }
  }
}
```

#### <a name="request-and-response-payloads"></a>Nettoladingen voor aanvragen en antwoorden

Opdrachten gebruiken typen om hun aanvraag-en reactie-nettoladingen te definiëren. Een apparaat moet de binnenkomende invoer parameter deserialiseren en het antwoord serialiseren.

In het volgende voor beeld ziet u hoe u een opdracht implementeert met complexe typen die zijn gedefinieerd in de payloads:

```json
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
    "name" : "tempReport",
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
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

De volgende code fragmenten laten zien hoe een apparaat deze opdracht definitie implementeert, met inbegrip van de typen die worden gebruikt voor het inschakelen van serialisatie en deserialisatie:

```java
deviceClient.subscribeToDeviceMethod(new GetMaxMinReportMethodCallback(), "getMaxMinReport", new MethodIotHubEventCallback(), "getMaxMinReport");

// ...

private static class GetMaxMinReportMethodCallback implements DeviceMethodCallback {
    String commandName = "getMaxMinReport";

    @Override
    public DeviceMethodData call(String methodName, Object methodData, Object context) {

        String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);
        Date since = gson.fromJson(jsonRequest, Date.class);

        String responsePayload = String.format(
                "{\"maxTemp\": %.1f, \"minTemp\": %.1f, \"avgTemp\": %.1f, \"startTime\": \"%s\", \"endTime\": \"%s\"}",
                maxTemp,
                minTemp,
                avgTemp,
                since,
                endTime);

        return new DeviceMethodData(StatusCode.COMPLETED.value, responsePayload);
    }
}
```

> [!Tip]
> De namen van de aanvraag en de reactie zijn niet aanwezig in de geserialiseerde nettoladingen die via de kabel worden verzonden.
