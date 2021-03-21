---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 3bf5ac4e01bca3bfc3cc8720a068bc53830b4747
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612636"
---
In deze zelfstudie ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing met meerdere onderdelen maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u de Azure CLI gebruikt om de telemetrie weer te geven die wordt verzonden. De voorbeeldtoepassing wordt geschreven in Java en is inbegrepen in de Azure IoT Device SDK voor Java. Een ontwikkelaar van oplossingen kan de Azure CLI gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

In deze zelfstudie ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing met onderdelen maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de gegevens weer te geven die naar de hub worden verzonden. De voorbeeldtoepassing wordt geschreven in Java en is inbegrepen in de Azure IoT Device SDK voor Java. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Download de voorbeeldcode.
> * De voorbeeld code maken.
> * Voer de toepassing voor voorbeeld apparaten uit en controleer of deze verbinding maakt met uw IoT-hub.
> * Controleer de bron code.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Om deze zelfstudie in Windows te voltooien, installeert u de volgende software in uw lokale Windows-omgeving:

* Java SE Development Kit 8. In [Java-langetermijnondersteuning voor Azure en Azure Stack](/java/azure/jdk/) onder **Langetermijnondersteuning** selecteert u **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>De code downloaden

Als u klaar bent met [Quickstart: Verbind een voorbeeld van een IoT Plug en Play-apparaat-app die in Windows wordt uitgevoerd met IoT Hub (Java)](../articles/iot-pnp/quickstart-connect-device.md). U hebt de opslagplaats al gekloond.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Azure IoT Java-SDK’s en -bibliotheken](https://github.com/Azure/azure-iot-sdk-java) te klonen op deze locatie:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="build-the-code"></a>De code bouwen

Ga in Windows naar de hoofdmap van de gekloonde Java-SDK-opslagplaats. Voer de volgende opdracht uit om de afhankelijkheden te maken:

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Het apparaatvoorbeeld uitvoeren

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Om de voorbeeldtoepassing uit te voeren, gaat u naar de map *\device\iot-device-samples\pnp-device-sample\temperature-controller-device-sample* en voert u de volgende opdracht uit:

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

Het apparaat is nu klaar om opdrachten en updates van eigenschappen te ontvangen, en is begonnen met het verzenden van telemetriegegevens naar de hub. Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>De code bekijken

Met dit voorbeeld wordt een IoT Plug and Play-temperatuurregelingsapparaat geïmplementeerd. Het model dat met dit voorbeeld wordt geïmplementeerd, maakt gebruik van [meerdere onderdelen](../articles/iot-pnp/concepts-modeling-guide.md). Het [Digital Twins Definition Language-modelbestand (DTDL) voor het thermostaatapparaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definieert de telemetrie, eigenschappen en opdrachten die het apparaat implementeert.

De apparaatcode maakt gebruik van de standaard `DeviceClient`-klasse om verbinding te maken met uw IoT-hub. Het apparaat verzendt de model-id van het DTDL-model dat het in de verbindingsaanvraag implementeert. Een apparaat dat een model-id verzendt, is een IoT Plug and Play-apparaat:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

De model-id wordt in de code opgeslagen, zoals weergegeven in het volgende fragment:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Nadat het apparaat verbinding heeft gemaakt met uw IoT-hub, registreert de code de opdrachthandlers.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

Er zijn afzonderlijke handlers voor de gewenste updates voor eigenschappen in de twee thermostaatonderdelen:

```java
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
```

De voorbeeldcode verzendt telemetrie vanuit elk thermostaatonderdeel:

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

De `sendTemperatureReading`-methode maakt gebruik van de `PnpHhelper`-klasse om berichten voor elk onderdeel te maken:

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

De `PnpHelper`-klasse bevat andere voorbeeldmethoden die u kunt gebruiken met een model met meerdere onderdelen.

Gebruik het hulpprogramma Azure IoT Explorer om de telemetrie en eigenschappen uit de twee thermostaatonderdelen weer te geven:

:::image type="content" source="media/iot-pnp-multiple-components-java/multiple-component.png" alt-text="Apparaat met meerdere onderdelen in Azure IoT Explorer":::

U kunt het hulpprogramma Azure IoT Explorer ook gebruiken om opdrachten aan te roepen in een van de twee thermostaatonderdelen of in het hoofdonderdeel.
