---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: dfe7edaf1bac054c2e7f379617aa6276b9b3127e
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491076"
---
## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing gemaakt met behulp van de sjabloon **Aangepaste toepassing**. Zie voor meer informatie de [snelstart over het maken van een toepassing](../articles/iot-central/core/quick-deploy-iot-central.md). De toepassing moet op of na 14 juli 2020 zijn gemaakt.
* Een ontwikkel machine met Java SE Development Kit 8 of hoger. U kunt de Java 8 (LTS) JDK voor meerdere platforms downloaden om [Zulu-builds van openjdk te downloaden](https://www.azul.com/downloads/zulu-community/).
* [Apache Maven 3](https://maven.apache.org/download.cgi).
* Een lokale kopie van de [Microsoft Azure IoT-SDK voor Java](https://github.com/Azure/azure-iot-sdk-java) GitHub-opslagplaats die de voorbeeldcode bevat. Gebruik deze koppeling om een kopie van de opslagplaats te downloaden: [ZIP-bestand downloaden](https://github.com/Azure/azure-iot-sdk-java/archive/master.zip). Pak het bestand vervolgens uit op een geschikte locatie op uw lokale computer.

## <a name="review-the-code"></a>De code bekijken

Open in de kopie van de Microsoft Azure IoT SDK voor Java die u eerder hebt gedownload het bestand *Azure-IOT-SDK-Java/apparaat/IOT-device-samples/PNP-device-sample/temperature-controller-Device-sample/src/main/Java/samples/com/micro soft/Azure/SDK/IOT/Device/TemperatureController. java* in een tekst editor.

Wanneer u het voorbeeld uitvoert om verbinding te maken met IoT Central, wordt daarvoor gebruikgemaakt van de Device Provisioning Service (DPS) om het apparaat te registreren en een verbindingsreeks te genereren. Het voorbeeld haalt de DPS-verbindingsgegevens die nodig zijn op uit de opdrachtregelomgeving.

De methode `main`:

* Roept `initializeAndProvisionDevice` aan om de model-id `dtmi:com:example:TemperatureController;2` in te stellen, gebruikt DPS om het apparaat in te richten en registreren, maakt een **DeviceClient**-instantie en maakt verbinding met uw IoT Central-toepassing. IoT Central gebruikt de model-id om de apparaatsjabloon voor dit apparaat te identificeren of te genereren. Zie [Een apparaat koppelen met een apparaatsjabloon](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template) voor meer informatie.
* Maakt opdracht-handlers voor `getMaxMinReport` de `reboot` opdrachten en.
* Hiermee maakt u update-handlers voor eigenschappen voor de Beschrijf bare `targetTemperature` Eigenschappen.
* Hiermee worden de initiële waarden voor de eigenschappen in de **apparaatgegevens** interface en de eigenschappen van het geheugen en het **serie nummer** van het **apparaat** verzonden.
* Start een thread voor het verzenden van de temperatuur telemetrie van de twee Thermo Staten en werk de `maxTempSinceLastReboot` eigenschap elke vijf seconden bij.

```java
public static void main(String[] args) throws IOException, URISyntaxException, ProvisioningDeviceClientException, InterruptedException {

  // ...
  
  switch (deviceSecurityType.toLowerCase())
  {
    case "dps":
    {
      if (validateArgsForDpsFlow())
      {
        initializeAndProvisionDevice();
        break;
      }
      throw new IllegalArgumentException("Required environment variables are not set for DPS flow, please recheck your environment.");
    }
    case "connectionstring":
    {
      // ...
    }
    default:
    {
      // ...
    }
  }
  
  deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
  
  deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
  Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
      new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
          new Property(THERMOSTAT_1, null),
          new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
      new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
          new Property(THERMOSTAT_2, null),
          new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
  ).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));
  
  deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
  
  updateDeviceInformation();
  sendDeviceMemory();
  sendDeviceSerialNumber();
  
  final AtomicBoolean temperatureReset = new AtomicBoolean(true);
  maxTemperature.put(THERMOSTAT_1, 0.0d);
  maxTemperature.put(THERMOSTAT_2, 0.0d);
  
  new Thread(new Runnable() {
    @SneakyThrows({InterruptedException.class, IOException.class})
    @Override
    public void run() {
      while (true) {
        if (temperatureReset.get()) {
          // Generate a random value between 5.0°C and 45.0°C for the current temperature reading for each "Thermostat" component.
          temperature.put(THERMOSTAT_1, BigDecimal.valueOf(random.nextDouble() * 40 + 5).setScale(1, RoundingMode.HALF_UP).doubleValue());
          temperature.put(THERMOSTAT_2, BigDecimal.valueOf(random.nextDouble() * 40 + 5).setScale(1, RoundingMode.HALF_UP).doubleValue());
        }

        sendTemperatureReading(THERMOSTAT_1);
        sendTemperatureReading(THERMOSTAT_2);

        temperatureReset.set(temperature.get(THERMOSTAT_1) == 0 && temperature.get(THERMOSTAT_2) == 0);
        Thread.sleep(5 * 1000);
      }
    }
  }).start();
}
```

De methode `initializeAndProvisionDevice` laat zien hoe het apparaat DPS gebruikt om te registreren en verbinding te maken met IoT Central. De payload omvat de model-id die in IoT Central wordt gebruikt om [het apparaat te koppelen aan een apparaatsjabloon](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template):

```java
private static void initializeAndProvisionDevice() throws ProvisioningDeviceClientException, IOException, URISyntaxException, InterruptedException {
  SecurityProviderSymmetricKey securityClientSymmetricKey = new SecurityProviderSymmetricKey(deviceSymmetricKey.getBytes(), registrationId);
  ProvisioningDeviceClient provisioningDeviceClient;
  ProvisioningStatus provisioningStatus = new ProvisioningStatus();

  provisioningDeviceClient = ProvisioningDeviceClient.create(globalEndpoint, scopeId, provisioningProtocol, securityClientSymmetricKey);

  AdditionalData additionalData = new AdditionalData();
  additionalData.setProvisioningPayload(com.microsoft.azure.sdk.iot.provisioning.device.plugandplay.PnpHelper.createDpsPayload(MODEL_ID));

  provisioningDeviceClient.registerDevice(new ProvisioningDeviceClientRegistrationCallbackImpl(), provisioningStatus, additionalData);

  while (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() != ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED)
  {
    if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ERROR ||
        provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_DISABLED ||
        provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_FAILED)
    {
      provisioningStatus.exception.printStackTrace();
      System.out.println("Registration error, bailing out");
      break;
    }
    System.out.println("Waiting for Provisioning Service to register");
    Thread.sleep(MAX_TIME_TO_WAIT_FOR_REGISTRATION);
  }

  ClientOptions options = new ClientOptions();
  options.setModelId(MODEL_ID);

  if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED) {
    System.out.println("IotHUb Uri : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri());
    System.out.println("Device ID : " + provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId());

    String iotHubUri = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri();
    String deviceId = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId();

    deviceClient = DeviceClient.createFromSecurityProvider(iotHubUri, deviceId, securityClientSymmetricKey, IotHubClientProtocol.MQTT, options);
    deviceClient.open();
  }
}
```

De- `sendTemperatureTelemetry` methode laat zien hoe het apparaat de telemetrie van de Tempe ratuur verzendt van een onderdeel naar IOT Central. Deze methode maakt gebruik van de- `PnpConvention` klasse voor het maken van het bericht:

```java
  private static void sendTemperatureTelemetry(String componentName) {
    String telemetryName = "temperature";
    double currentTemperature = temperature.get(componentName);

    Message message = PnpConvention.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
    deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);

    // Add the current temperature entry to the list of temperature readings.
    Map<Date, Double> currentReadings;
    if (temperatureReadings.containsKey(componentName)) {
      currentReadings = temperatureReadings.get(componentName);
    } else {
      currentReadings = new HashMap<>();
    }
    currentReadings.put(new Date(), currentTemperature);
    temperatureReadings.put(componentName, currentReadings);
  }
```

De `updateMaxTemperatureSinceLastReboot` methode verzendt een `maxTempSinceLastReboot` bijgewerkte eigenschap van een component naar IOT Central. Deze methode maakt gebruik van de- `PnpConvention` klasse om de patch te maken:

```java
private static void updateMaxTemperatureSinceLastReboot(String componentName) throws IOException {
  String propertyName = "maxTempSinceLastReboot";
  double maxTemp = maxTemperature.get(componentName);

  Set<Property> reportedProperty = PnpConvention.createComponentPropertyPatch(propertyName, maxTemp, componentName);
  deviceClient.sendReportedProperties(reportedProperty);
}
```

De `TargetTemperatureUpdateCallback` klasse bevat de `TwinPropertyCallBack` methode voor het afhandelen van Beschrijf bare eigenschaps updates voor een onderdeel van IOT Central. Deze methode maakt gebruik van de- `PnpConvention` klasse om het antwoord te maken:

```java
private static class TargetTemperatureUpdateCallback implements TwinPropertyCallBack {

  final String propertyName = "targetTemperature";

  @SneakyThrows({IOException.class, InterruptedException.class})
  @Override
  public void TwinPropertyCallBack(Property property, Object context) {
    String componentName = (String) context;

    if (property.getKey().equalsIgnoreCase(componentName)) {
      double targetTemperature = (double) ((TwinCollection) property.getValue()).get(propertyName);

      Set<Property> pendingPropertyPatch = PnpConvention.createComponentWritablePropertyResponse(
          propertyName,
          targetTemperature,
          componentName,
          StatusCode.IN_PROGRESS.value,
          property.getVersion().longValue(),
          null);
      deviceClient.sendReportedProperties(pendingPropertyPatch);

      // Update temperature in 2 steps
      double step = (targetTemperature - temperature.get(componentName)) / 2;
      for (int i = 1; i <=2; i++) {
        temperature.put(componentName, BigDecimal.valueOf(temperature.get(componentName) + step).setScale(1, RoundingMode.HALF_UP).doubleValue());
        Thread.sleep(5 * 1000);
      }

      Set<Property> completedPropertyPatch = PnpConvention.createComponentWritablePropertyResponse(
          propertyName,
          temperature.get(componentName),
          componentName,
          StatusCode.COMPLETED.value,
          property.getVersion().longValue(),
          "Successfully updated target temperature.");
      deviceClient.sendReportedProperties(completedPropertyPatch);
    } else {
        // ...
    }
  }
}
```

De `MethodCallback` klasse bevat de `call` methode voor het afhandelen van onderdeel opdrachten die worden aangeroepen vanuit IOT Central:

```java
private static class MethodCallback implements DeviceMethodCallback {
  final String reboot = "reboot";
  final String getMaxMinReport1 = "thermostat1*getMaxMinReport";
  final String getMaxMinReport2 = "thermostat2*getMaxMinReport";

  @SneakyThrows(InterruptedException.class)
  @Override
  public DeviceMethodData call(String methodName, Object methodData, Object context) {
    String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);

    switch (methodName) {
      case reboot:
        int delay = getCommandRequestValue(jsonRequest, Integer.class);
        Thread.sleep(delay * 1000L);

        temperature.put(THERMOSTAT_1, 0.0d);
        temperature.put(THERMOSTAT_2, 0.0d);

        maxTemperature.put(THERMOSTAT_1, 0.0d);
        maxTemperature.put(THERMOSTAT_2, 0.0d);

        temperatureReadings.clear();
        return new DeviceMethodData(StatusCode.COMPLETED.value, null);

        case getMaxMinReport1:
        case getMaxMinReport2:
          String[] words = methodName.split("\\*");
          String componentName = words[0];

          if (temperatureReadings.containsKey(componentName)) {
            Date since = getCommandRequestValue(jsonRequest, Date.class);

            Map<Date, Double> allReadings = temperatureReadings.get(componentName);
            Map<Date, Double> filteredReadings = allReadings.entrySet().stream()
                .filter(map -> map.getKey().after(since))
                .collect(Collectors.toMap(Entry::getKey, Entry::getValue));

            if (!filteredReadings.isEmpty()) {
              SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss'Z'");
              double maxTemp = Collections.max(filteredReadings.values());
              double minTemp = Collections.min(filteredReadings.values());
              double avgTemp = filteredReadings.values().stream().mapToDouble(Double::doubleValue).average().orElse(Double.NaN);
              String startTime =  sdf.format(Collections.min(filteredReadings.keySet()));
              String endTime =  sdf.format(Collections.max(filteredReadings.keySet()));

              String responsePayload = String.format(
                  "{\"maxTemp\": %.1f, \"minTemp\": %.1f, \"avgTemp\": %.1f, \"startTime\": \"%s\", \"endTime\": \"%s\"}",
                  maxTemp,
                  minTemp,
                  avgTemp,
                  startTime,
                  endTime);

              return new DeviceMethodData(StatusCode.COMPLETED.value, responsePayload);
            }

            return new DeviceMethodData(StatusCode.NOT_FOUND.value, null);
          }

          return new DeviceMethodData(StatusCode.NOT_FOUND.value, null);

        default:
            return new DeviceMethodData(StatusCode.NOT_FOUND.value, null);
    }
  }
}
```

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

Ga in Windows naar de hoofdmap van de Azure IoT SDK voor de Java-opslag plaats die u hebt gedownload.

Voer de volgende opdracht uit om de voorbeeldtoepassing te maken:

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-code"></a>De code uitvoeren

Als u de voorbeeld toepassing wilt uitvoeren, opent u een opdracht regel omgeving en gaat u naar de map *Azure-IOT-SDK-Java/Device/IOT-apparaat-samples/PnP-apparaat-sample/Tempe ratuur-controller-Device-voor beeld* van de map *src* met het voorbeeld bestand *TemperatureController. java* .

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Voer het voorbeeld uit:

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

In de volgende uitvoer ziet u hoe het apparaat zich registreert bij IoT Central en er verbinding mee maakt. Het voorbeeld begint met het verzenden van telemetriegegevens:

```output
2021-03-30 15:33:25.138 DEBUG TemperatureController:123 - Initialize the device client.
Waiting for Provisioning Service to register
Waiting for Provisioning Service to register
IotHUb Uri : iotc-60a.....azure-devices.net
Device ID : sample-device-01
2021-03-30 15:33:38.294 DEBUG TemperatureController:247 - Opening the device client.
2021-03-30 15:33:38.307 INFO  ExponentialBackoffWithJitter:98 - NOTE: A new instance of ExponentialBackoffWithJitter has been created with the following properties. Retry Count: 2147483647, Min Backoff Interval: 100, Max Backoff Interval: 10000, Max Time Between Retries: 100, Fast Retry Enabled: true
2021-03-30 15:33:38.321 INFO  ExponentialBackoffWithJitter:98 - NOTE: A new instance of ExponentialBackoffWithJitter has been created with the following properties. Retry Count: 2147483647, Min Backoff Interval: 100, Max Backoff Interval: 10000, Max Time Between Retries: 100, Fast Retry Enabled: true
2021-03-30 15:33:38.427 DEBUG MqttIotHubConnection:274 - Opening MQTT connection...
2021-03-30 15:33:38.427 DEBUG Mqtt:123 - Sending MQTT CONNECT packet...
2021-03-30 15:33:44.628 DEBUG Mqtt:126 - Sent MQTT CONNECT packet was acknowledged
2021-03-30 15:33:44.630 DEBUG Mqtt:256 - Sending MQTT SUBSCRIBE packet for topic devices/sample-device-01/messages/devicebound/#
2021-03-30 15:33:44.731 DEBUG Mqtt:261 - Sent MQTT SUBSCRIBE packet for topic devices/sample-device-01/messages/devicebound/# was acknowledged
2021-03-30 15:33:44.733 DEBUG MqttIotHubConnection:279 - MQTT connection opened successfully
2021-03-30 15:33:44.733 DEBUG IotHubTransport:302 - The connection to the IoT Hub has been established
2021-03-30 15:33:44.734 INFO  IotHubTransport:1429 - Updating transport status to new status CONNECTED with reason CONNECTION_OK
2021-03-30 15:33:44.735 DEBUG IotHubTransport:1439 - Invoking connection status callbacks with new status details
2021-03-30 15:33:44.739 DEBUG IotHubTransport:394 - Client connection opened successfully
2021-03-30 15:33:44.740 INFO  DeviceClient:438 - Device client opened successfully
2021-03-30 15:33:44.740 DEBUG TemperatureController:152 - Set handler for "reboot" command.
2021-03-30 15:33:44.742 DEBUG TemperatureController:153 - Set handler for "getMaxMinReport" command.
2021-03-30 15:33:44.774 INFO  IotHubTransport:489 - Message was queued to be sent later ( Message details: Correlation Id [029d30d4-acbd-462d-b155-82d53ce7786c] Message Id [1b2adf93-ba81-41e4-b8c7-7c90c8b0d6a1] Device Operation Type [DEVICE_OPERATION_METHOD_SUBSCRIBE_REQUEST] )
2021-03-30 15:33:44.774 DEBUG TemperatureController:156 - Set handler to receive "targetTemperature" updates.
2021-03-30 15:33:44.775 INFO  IotHubTransport:1344 - Sending message ( Message details: Correlation Id [029d30d4-acbd-462d-b155-82d53ce7786c] Message Id [1b2adf93-ba81-41e4-b8c7-7c90c8b0d6a1] Device Operation Type [DEVICE_OPERATION_METHOD_SUBSCRIBE_REQUEST] )
2021-03-30 15:33:44.779 DEBUG Mqtt:256 - Sending MQTT SUBSCRIBE packet for topic $iothub/methods/POST/#
2021-03-30 15:33:44.793 INFO  IotHubTransport:489 - Message was queued to be sent later ( Message details: Correlation Id [f2f9ed95-9778-44f2-b9ec-f60c84061251] Message Id [0d5abdb2-6460-414c-a10e-786ee24cacff] Device Operation Type [DEVICE_OPERATION_TWIN_SUBSCRIBE_DESIRED_PROPERTIES_REQUEST] )
2021-03-30 15:33:44.794 INFO  IotHubTransport:489 - Message was queued to be sent later ( Message details: Correlation Id [417d659a-7324-43fa-84eb-8a3f3d07963c] Message Id [55532cad-8a5a-489f-9aa8-8f0e5bc21541] Request Id [0] Device Operation Type [DEVICE_OPERATION_TWIN_GET_REQUEST] )
2021-03-30 15:33:44.819 INFO  IotHubTransport:489 - Message was queued to be sent later ( Message details: Correlation Id [d46a0d8a-8a18-4014-abeb-768bd9b17ad2] Message Id [780abc81-ce42-4e5f-aa80-e4785883604e] Device Operation Type [DEVICE_OPERATION_TWIN_SUBSCRIBE_DESIRED_PROPERTIES_REQUEST] )
2021-03-30 15:33:44.881 DEBUG Mqtt:261 - Sent MQTT SUBSCRIBE packet for topic $iothub/methods/POST/# was acknowledged
2021-03-30 15:33:44.882 INFO  IotHubTransport:1344 - Sending message ( Message details: Correlation Id [f2f9ed95-9778-44f2-b9ec-f60c84061251] Message Id [0d5abdb2-6460-414c-a10e-786ee24cacff] Device Operation Type [DEVICE_OPERATION_TWIN_SUBSCRIBE_DESIRED_PROPERTIES_REQUEST] )
2021-03-30 15:33:44.882 DEBUG Mqtt:256 - Sending MQTT SUBSCRIBE packet for topic $iothub/twin/res/#
2021-03-30 15:33:44.893 INFO  IotHubTransport:489 - Message was queued to be sent later ( Message details: Correlation Id [a77b1c02-f043-4477-b610-e31a774772c0] Message Id [2e2f6bee-c480-42cf-ac31-194118930846] Request Id [1] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
2021-03-30 15:33:44.904 DEBUG TemperatureController:423 - Property: Update - component = "deviceInformation" is COMPLETED.
2021-03-30 15:33:44.915 INFO  IotHubTransport:489 - Message was queued to be sent later ( Message details: Correlation Id [bbb7e3cf-3550-4fdf-90f9-0787740f028a] Message Id [e06ac385-ae0d-46dd-857a-d9725707527a] )
2021-03-30 15:33:44.915 DEBUG TemperatureController:434 - Telemetry: Sent - {"workingSet": 1024.0KiB }
2021-03-30 15:33:44.915 INFO  IotHubTransport:489 - Message was queued to be sent later ( Message details: Correlation Id [6dbef765-cc9a-4e72-980a-2fe5b0cd77e1] Message Id [49bbad33-09bf-417a-9d6e-299ba7b7c562] Request Id [2] Device Operation Type [DEVICE_OPERATION_TWIN_UPDATE_REPORTED_PROPERTIES_REQUEST] )
2021-03-30 15:33:44.916 DEBUG TemperatureController:442 - Property: Update - {"serialNumber": SR-123456} is COMPLETED
2021-03-30 15:33:44.927 INFO  IotHubTransport:489 - Message was queued to be sent later ( Message details: Correlation Id [86787c32-87a5-4c49-9083-c7f2b17446a7] Message Id [0a45fa0c-a467-499d-b214-9bb5995772ba] )
2021-03-30 15:33:44.927 DEBUG TemperatureController:461 - Telemetry: Sent - {"temperature": 5.8°C} with message Id 0a45fa0c-a467-499d-b214-9bb5995772ba.
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

U kunt zien hoe het apparaat reageert op opdrachten en updates van eigenschappen:

```output
2021-03-30 15:43:57.133 DEBUG TemperatureController:309 - Command: Received - component="thermostat1", generating min, max, avg temperature report since Tue Mar 30 06:00:00 BST 2021
2021-03-30 15:43:57.153 DEBUG TemperatureController:332 - Command: MaxMinReport since Tue Mar 30 06:00:00 BST 2021: "maxTemp": 35.6°C, "minTemp": 35.6°C, "avgTemp": 35.6°C, "startTime": 2021-03-30T15:43:41Z, "endTime": 2021-03-30T15:43:56Z
2021-03-30 15:43:57.394 DEBUG TemperatureController:502 - Command - Response from IoT Hub: command name=null, status=OK_EMPTY


...

2021-03-30 15:48:47.808 DEBUG TemperatureController:372 - Property: Received - component="thermostat2", {"targetTemperature": 67.0°C}.
2021-03-30 15:48:47.837 DEBUG TemperatureController:382 - Property: Update - component="thermostat2", {"targetTemperature": 67.0°C} is IN_PROGRESS

```
