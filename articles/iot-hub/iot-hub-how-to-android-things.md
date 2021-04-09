---
title: Ontwikkelen voor Android-platformen met behulp van Azure IoT Sdk's | Microsoft Docs
description: "Ontwikkelaars handleiding: meer informatie over het ontwikkelen van Android-dingen met behulp van Azure IoT Hub Sdk's."
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: 6008ca1549629bcbb582f38de2ab1b453cd73a99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94843754"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Ontwikkelen voor Android-platformen met behulp van Azure IoT Sdk's

[Azure IOT hub sdk's](./iot-hub-devguide-sdks.md) bieden ondersteuning voor de eerste laag voor populaire platforms, zoals Windows, Linux, OSX, MBED en mobiele platformen zoals Android en IOS.  Als onderdeel van onze toezeg ging om meer keuze en flexibiliteit in IoT-implementaties mogelijk te maken, ondersteunt de Java SDK ook [Android](https://developer.android.com/things/) -platformen.  Ontwikkel aars kunnen gebruikmaken van de voor delen van Android-besturings systeem aan de kant van het apparaat, terwijl [Azure IOT hub](about-iot-hub.md) als de Central Message hub wordt geschaald naar miljoenen apparaten die met elkaar verbonden zijn.

Deze zelf studie bevat een overzicht van de stappen voor het bouwen van een toepassing op Android-zaken met behulp van de Azure IoT Java SDK.

## <a name="prerequisites"></a>Vereisten

* Een Android-apparaat dat hardware ondersteunt waarvoor Android-besturings systemen worden uitgevoerd.  U kunt de [documentatie voor Android-dingen](https://developer.android.com/things/get-started/kits#flash-at) volgen voor informatie over het besturings systeem Flash Android.  Zorg ervoor dat uw Android-apparaat is verbonden met internet met essentiële rand apparatuur, zoals het toetsen bord, de weer gave en de muis.  In deze zelf studie wordt Raspberry Pi 3 gebruikt.

* Nieuwste versie van [Android Studio](https://developer.android.com/studio/)

* Nieuwste versie van [Git](https://git-scm.com/)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdrachten uit in Azure Cloud Shell om de IoT Hub CLI-extensie toe te voegen en de apparaat-id te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **MyAndroidThingsDevice** : dit is de naam die is opgegeven voor het geregistreerde apparaat. Gebruik MyAndroidThingsDevice zoals weer gegeven. Als u een andere naam voor het apparaat kiest, moet u deze naam ook in de rest van dit artikel gebruiken. Bovendien moet u de apparaatnaam bijwerken in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Voer de volgende opdrachten uit in Azure Cloud Shell om de *apparaat Connection String* voor het apparaat dat u zojuist hebt geregistreerd, op te halen. Vervang `YourIoTHubName` hieronder door de naam die u voor uw IOT-hub hebt gekozen.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Noteer de apparaatverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde verderop in de snelstartgids.

## <a name="building-an-android-things-application"></a>Een Android-toepassing bouwen

1. De eerste stap bij het bouwen van een Android-toepassing is het maken van verbinding met uw Android-apparaten. Verbind uw Android-apparaat met een weer gave en sluit het aan op internet. Android-dingen bieden [documentatie](https://developer.android.com/things/get-started/kits) over het maken van verbinding met WiFi. Nadat u verbinding hebt gemaakt met internet, moet u rekening houden met het IP-adres dat wordt vermeld onder netwerken.

2. Gebruik het hulp programma [ADB](https://developer.android.com/studio/command-line/adb) om verbinding te maken met uw Android-apparaat met het IP-adres dat hierboven wordt vermeld. Controleer de verbinding met behulp van deze opdracht vanaf uw Terminal. U ziet dat uw apparaten worden weer gegeven als ' verbonden '.

   ```
   adb devices
   ```

3. Down load het voor beeld voor Android/Android-dingen uit deze [opslag plaats](https://github.com/Azure-Samples/azure-iot-samples-java) of gebruik git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. Open in Android Studio het Android-project in ' \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample '.

5. Open het bestand gradle. Properties en vervang ' Device_connection_string ' door uw apparaat connection string eerder hebt opgemerkt.
 
6. Klik op uitvoeren-fout opsporing en selecteer uw apparaat om deze code te implementeren op uw Android-apparaten.

7. Wanneer de toepassing is gestart, ziet u een toepassing die wordt uitgevoerd op uw Android-apparaat. Deze voorbeeld toepassing verzendt wille keurig gegenereerde temperatuur metingen.

## <a name="read-the-telemetry-from-your-hub"></a>De telemetrie van uw hub lezen

U kunt de gegevens weer geven via uw IoT-hub zodra deze wordt ontvangen. De IoT Hub CLI-extensie kan verbinding maken met het **Gebeurtenissen**-eindpunt aan de servicezijde van uw IoT Hub. De extensie ontvangt de berichten die van het gesimuleerde apparaat naar de cloud worden verzonden. Een back-endtoepassing van IoT Hub wordt meestal uitgevoerd in de cloud om berichten van apparaat naar cloud te ontvangen en verwerken.

Voer de volgende opdrachten uit in Azure Cloud Shell. Vervang daarbij `YourIoTHubName` door de naam van uw IoT-hub:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [beheren van connectiviteit en betrouw bare berichten](iot-hub-reliability-features-in-sdks.md) met behulp van de IOT hub sdk's.
* Meer informatie over het [ontwikkelen van mobiele platforms](iot-hub-how-to-develop-for-mobile-devices.md) , zoals IOS en Android.
* [Azure IoT SDK-platform ondersteuning](iot-hub-device-sdk-platform-support.md)