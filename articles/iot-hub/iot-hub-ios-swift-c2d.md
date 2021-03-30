---
title: Cloud-naar-apparaat-berichten met Azure IoT Hub (iOS) | Microsoft Docs
description: Cloud-naar-apparaat-berichten verzenden naar een apparaat vanuit een Azure IoT-hub met behulp van de Azure IoT Sdk's voor iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.custom: mqtt
ms.openlocfilehash: 15c0df33b8f09ec71f2be913d72f0785dc766375
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93027530"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Cloud-naar-apparaat-berichten verzenden met IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub is een volledig beheerde service die zorgt voor betrouw bare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing. Het [verzenden van telemetrie van een apparaat naar een IOT hub](quickstart-send-telemetry-ios.md) Quick Start laat zien hoe u een IOT-hub kunt maken, een apparaat-id kunt inrichten en een gesimuleerde apparaat-app kunt coderen die apparaat-naar-Cloud-berichten verzendt.

In deze zelfstudie ontdekt u hoe u:

* Via de back-end van uw oplossing kunt u Cloud-naar-apparaat-berichten verzenden naar één apparaat via IoT Hub.

* Cloud-naar-apparaat-berichten op een apparaat ontvangen.

* Van de back-end van uw oplossing, aanvraag bezorgings bevestiging (*feedback*) aanvragen voor berichten die worden verzonden naar een apparaat vanuit IOT hub.

Meer informatie over Cloud-naar-apparaat-berichten vindt u in de [berichten sectie van de IOT hub-ontwikkelaars handleiding](iot-hub-devguide-messaging.md).

Aan het einde van dit artikel voert u twee snelle iOS-projecten uit:

* voor **beeld-apparaat**, dezelfde app die is gemaakt in [telemetrie verzenden van een apparaat naar een IOT-hub](quickstart-send-telemetry-ios.md), die verbinding maakt met uw IOT-hub en Cloud-naar-apparaat-berichten ontvangt.

* voor **beeld-service**: Hiermee wordt een Cloud-naar-apparaat-bericht naar de gesimuleerde apparaat-app verzonden via IOT hub, waarna de ontvangst bevestiging wordt ontvangen.

> [!NOTE]
> IoT Hub heeft SDK-ondersteuning voor veel platformen en talen (waaronder C, Java, python en Java script) via Azure IoT-apparaat-Sdk's. Zie het [Azure IOT-ontwikkelaars centrum](https://www.azure.com/develop/iot)voor stapsgewijze instructies voor het verbinden van uw apparaat met de code van deze zelf studie en over het algemeen tot Azure IOT hub.

## <a name="prerequisites"></a>Vereisten

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Een actieve IoT-hub in Azure.

* Het code voorbeeld van [Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)-voor beelden.

* De nieuwste versie van [XCode](https://developer.apple.com/xcode/), met de nieuwste versie van de iOS-SDK. Deze snelstart is getest met XCode 9.3 en iOS 11.3.

* De nieuwste versie van [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

* Zorg ervoor dat de poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="simulate-an-iot-device"></a>Een IoT-apparaat simuleren

In deze sectie simuleert u een iOS-apparaat waarop een Swift-toepassing wordt uitgevoerd om Cloud-naar-apparaat-berichten van de IoT-hub te ontvangen. 

Dit is het voorbeeld apparaat dat u in het artikel maakt, [verzendt telemetrie van een apparaat naar een IOT-hub](quickstart-send-telemetry-ios.md). Als u deze al hebt uitgevoerd, kunt u deze sectie overs Laan.

### <a name="install-cocoapods"></a>CocoaPods installeren

CocoaPods beheren afhankelijkheden voor iOS-projecten die gebruikmaken van bibliotheken van derden.

Navigeer in een terminalvenster naar de Azure-IoT-Samples-iOS-map die u hebt gedownload bij de vereisten. Navigeer vervolgens naar het voorbeeldproject:

```sh
cd quickstart/sample-device
```

Zorg ervoor dat XCode is gesloten en voer vervolgens de volgende opdracht uit om de CocoaPods te installeren die worden gedeclareerd in het **podfile**-bestand:

```sh
pod install
```

Naast het installeren van de pods die nodig zijn voor uw project, heeft de installatieopdracht ook een XCode-werkruimtebestand gemaakt dat al geconfigureerd is om de pods te gebruiken voor afhankelijkheden.

### <a name="run-the-sample-device-application"></a>De voorbeeld toepassing voor apparaten uitvoeren

1. Haal de connection string op voor uw apparaat. U kunt deze teken reeks kopiëren uit de [Azure Portal](https://portal.azure.com) op de Blade met de apparaatgegevens of deze ophalen met de volgende CLI-opdracht:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Open de voorbeeldwerkruimte in XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Vouw het **voorbeeld** project van de MQTT-client uit en vervolgens de map met dezelfde naam.  

3. Open **ViewController.swift** om het te bewerken in XCode. 

4. Zoek de **Connections Tring** -variabele en werk de waarde bij met het apparaat Connection String dat u in de eerste stap hebt gekopieerd.

5. Sla uw wijzigingen op. 

6. Voer het project in de apparaatemulator uit met de knop **Build and run** of de toetscombinatie **command+r**.

   ![Scherm afbeelding toont de knop opbouwen en uitvoeren in de emulator van het apparaat.](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

In dit artikel maakt u een back-end-service om Cloud-naar-apparaat-berichten te verzenden via de IoT-hub die u hebt gemaakt in [telemetrie van een apparaat naar een IOT-hub verzenden](quickstart-send-telemetry-ios.md). Als u Cloud-naar-apparaat-berichten wilt verzenden, moet u de service **Connect** -machtiging hebben. Standaard wordt elke IoT Hub gemaakt met een gedeeld toegangs beleid met de naam **service** dat deze machtiging verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Een service apparaat simuleren

In deze sectie simuleert u een tweede iOS-apparaat met een snelle app die Cloud-naar-apparaat-berichten verzendt via de IoT-hub. Deze configuratie is handig voor IoT-scenario's waarbij een iPhone of iPad werkt als een controller voor andere iOS-apparaten die zijn verbonden met een IoT-hub.

### <a name="install-cocoapods"></a>CocoaPods installeren

CocoaPods beheren afhankelijkheden voor iOS-projecten die gebruikmaken van bibliotheken van derden.

Navigeer naar de map Azure IoT iOS-voor beelden die u hebt gedownload in de vereisten. Ga vervolgens naar het voorbeeld service project:

```sh
cd quickstart/sample-service
```

Zorg ervoor dat XCode is gesloten en voer vervolgens de volgende opdracht uit om de CocoaPods te installeren die worden gedeclareerd in het **podfile**-bestand:

```sh
pod install
```

Naast het installeren van de pods die nodig zijn voor uw project, heeft de installatieopdracht ook een XCode-werkruimtebestand gemaakt dat al geconfigureerd is om de pods te gebruiken voor afhankelijkheden.

### <a name="run-the-sample-service-application"></a>De voorbeeld service toepassing uitvoeren

1. Open de voorbeeldwerkruimte in XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Vouw het project **AzureIoTServiceSample** uit en vouw vervolgens de map met dezelfde naam uit.  

3. Open **ViewController.swift** om het te bewerken in XCode. 

4. Zoek naar de **Connections Tring** -variabele en werk de waarde bij met de Service Connection String die u eerder hebt gekopieerd in [de IoT hub-Connection String ophalen](#get-the-iot-hub-connection-string).

5. Sla uw wijzigingen op.

6. In Xcode wijzigt u de emulator-instellingen in een ander iOS-apparaat dan u hebt gebruikt om het IoT-apparaat uit te voeren. XCode kan niet meerdere emulators van hetzelfde type uitvoeren.

   ![Het Emulator-apparaat wijzigen](media/iot-hub-ios-swift-c2d/change-device.png)

7. Voer het project uit in de emulator van het apparaat met de knop **opbouwen en uitvoeren** of de toetsen combinatie **opdracht + r**.

   ![Scherm afbeelding toont de knop bouwen en uitvoeren.](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Een Cloud-naar-apparaat-bericht verzenden

U bent nu klaar om de twee toepassingen te gebruiken om Cloud-naar-apparaat-berichten te verzenden en te ontvangen.

1. Klik in de app voor **beeld van Ios-app** die wordt uitgevoerd op het gesimuleerde IOT-apparaat op **Start**. De toepassing begint met het verzenden van apparaat-naar-Cloud-berichten, maar begint ook met Luis teren naar Cloud-naar-apparaat-berichten.

   ![Voor beeld van IoT-apparaat-app weer geven](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. Voer in de **IoTHub service-client voor beeld** -app die wordt uitgevoerd op het gesimuleerde service-apparaat de id in voor het IOT-apparaat waarnaar u een bericht wilt verzenden. 

3. Schrijf een bericht met een lees bare tekst en klik vervolgens op **verzenden**.

    Zodra u op verzenden klikt, worden er verschillende acties uitgevoerd. Het service voorbeeld verzendt het bericht naar uw IoT-hub, waartoe de app toegang heeft vanwege de service connection string die u hebt ingevoerd. Uw IoT-hub controleert de apparaat-ID, verzendt het bericht naar het doel apparaat en stuurt een bevestigings bevestiging naar het bron apparaat. De app die op uw gesimuleerde IoT-apparaat wordt uitgevoerd, controleert op berichten van IoT Hub en drukt de tekst af van de meest recente op het scherm.

    De uitvoer moet er ongeveer uitzien als in het volgende voor beeld:

   ![Cloud-naar-apparaat-berichten weer geven](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Cloud-naar-apparaat-berichten kunt verzenden en ontvangen.

Zie de documentatie van [Azure IOT Solution Accelerators](https://azure.microsoft.com/documentation/suites/iot-suite/) voor een overzicht van de volledige end-to-end-oplossingen die gebruikmaken van IOT hub.

Raadpleeg de [IOT hub ontwikkelaars handleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IOT hub.
