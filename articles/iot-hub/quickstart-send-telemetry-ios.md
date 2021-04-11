---
title: Quick Start-telemetrie verzenden naar Azure IoT Hub Snelstartgids (iOS)
description: In deze snelstart voert u een iOS-voorbeeldtoepassing uit om gesimuleerde telemetrie te verzenden naar een IoT-hub en telemetrie van de IoT-hub te lezen voor verwerking in de cloud.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
ms.date: 04/03/2019
ms.openlocfilehash: 21d25bd461a1a7e0cd2718cbe0ea32be06cf9055
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066990"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Quickstart: Telemetrie verzenden van een apparaat naar een IoT-hub (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub is een Azure-service waarmee grote hoeveelheden telemetrie van uw IoT-apparaten naar de cloud kunt opnemen voor opslag of verwerking. In dit artikel verzendt u telemetrie van een toepassing met een gesimuleerd apparaat naar IoT Hub. Vervolgens kunt u de gegevens bekijken vanuit een back-endtoepassing.

In dit artikel wordt gebruikgemaakt van een vooraf geschreven Swift toepassing om de telemetrie te verzenden, en van een CLI-hulpprogramma om de telemetrie uit IoT Hub te lezen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- De voorbeeldcode downloaden uit [Azure-voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)

- De nieuwste versie van [XCode](https://developer.apple.com/xcode/), met de nieuwste versie van de iOS-SDK. Deze quickstart is getest met XCode 10.2 en iOS 12.2.

- De nieuwste versie van [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

- Zorg ervoor dat de poort 8883 is geopend in de firewall. In het apparaatvoorbeeld in deze quickstart wordt het MQTT-protocol gebruikt, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdrachten uit in Azure Cloud Shell om de apparaat-id te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **myiOSdevice**: dit is de naam van het apparaat dat u gaat registreren. Het is raadzaam om **myiOSdevice** te gebruiken zoals wordt weergegeven. Als u een andere naam voor het apparaat kiest, moet u deze naam ook in de rest van dit artikel gebruiken, en moet u de apparaatnaam bijwerken in de voorbeeldtoepassingen voordat u deze uitvoert.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myiOSdevice
   ```

1. Voer de volgende opdracht uit in Azure Cloud Shell om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id myiOSdevice --output table
   ```

   Noteer de apparaatverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myiOSdevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde verderop in de snelstart.

## <a name="send-simulated-telemetry"></a>Gesimuleerde telemetrie verzenden

De voorbeeldtoepassing wordt uitgevoerd op een iOS-apparaat dat verbinding maakt met een apparaatspecifiek eindpunt op uw IoT-hub en gesimuleerde temperatuur- en vochtigheidstelemetrie verzendt. 

### <a name="install-cocoapods"></a>CocoaPods installeren

CocoaPods beheren afhankelijkheden voor iOS-projecten die gebruikmaken van bibliotheken van derden.

Navigeer in een lokaal terminalvenster naar de Azure-IoT-Samples-iOS-map die u als vereiste hebt gedownload. Navigeer vervolgens naar het voorbeeldproject:

```sh
cd quickstart/sample-device
```

Zorg ervoor dat XCode is gesloten en voer vervolgens de volgende opdracht uit om de CocoaPods te installeren die worden gedeclareerd in het **podfile**-bestand:

```sh
pod install
```

Naast het installeren van de pods die nodig zijn voor uw project, heeft de installatieopdracht ook een XCode-werkruimtebestand gemaakt dat al geconfigureerd is om de pods te gebruiken voor afhankelijkheden. 

### <a name="run-the-sample-application"></a>De voorbeeldtoepassing uitvoeren 

1. Open de voorbeeldwerkruimte in XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Vouw het project **MQTT Client Sample** uit en vouw vervolgens de map met dezelfde naam uit.  
3. Open **ViewController.swift** om het te bewerken in XCode. 
4. Zoek de variabele **connectionString** op en werk de waarde bij met de apparaatverbindingsreeks die u eerder hebt genoteerd.
5. Sla uw wijzigingen op. 
6. Voer het project in de apparaatemulator uit met de knop **Build and run** of de toetscombinatie **command+r**. 

   ![Het project uitvoeren](media/quickstart-send-telemetry-ios/run-sample.png)

7. Wanneer de emulator wordt geopend, selecteert u **Start** in de voorbeeld-app.

In de volgende schermafbeelding ziet u voorbeelduitvoer van de gesimuleerde telemetrie die de toepassing naar uw IoT-hub verzendt:

   ![Het gesimuleerde apparaat uitvoeren](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>De telemetrie van uw hub lezen

De voorbeeld-app die u hebt uitgevoerd op de XCode-emulator toont gegevens over van het apparaat verzonden berichten. U kunt de gegevens ook via uw IoT-hub bekijken terwijl ze worden ontvangen. De IoT Hub CLI-extensie kan verbinding maken met het **Gebeurtenissen**-eindpunt aan de servicezijde van uw IoT Hub. De extensie ontvangt de berichten die van het gesimuleerde apparaat naar de cloud worden verzonden. Een back-endtoepassing van IoT Hub wordt meestal uitgevoerd in de cloud om berichten van apparaat naar cloud te ontvangen en verwerken.

Voer de volgende opdrachten uit in Azure Cloud Shell. Vervang daarbij `YourIoTHubName` door de naam van uw IoT-hub:

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name {YourIoTHubName}
```

In de volgende schermafbeelding ziet u de uitvoer op het moment dat de extensie telemetriegegevens ontvangt die door het gesimuleerde apparaat naar de hub zijn verzonden:

De volgende schermafbeelding geeft het soort telemetrie weer dat u in uw lokale terminalvenster ziet:

![Telemetrie bekijken](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart stelt u een IoT-hub in, registreert u een apparaat, verzendt u gesimuleerde telemetrie van een iOS-apparaat naar de hub en leest u de telemetrie vanaf de hub. 

Ga verder met de volgende snelstartgids als u wilt weten hoe u een gesimuleerd apparaat beheert vanuit een back-endtoepassing.

> [!div class="nextstepaction"]
> [Snelstart: Een apparaat beheren dat is verbonden met een IoT-hub](quickstart-control-device-node.md)
