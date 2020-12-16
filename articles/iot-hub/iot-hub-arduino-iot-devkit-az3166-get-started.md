---
title: IoT DevKit AZ3166 verbinden met een Azure-IoT Hub
description: In deze zelf studie leert u hoe u IoT DevKit AZ3166 kunt instellen en verbinden met Azure IoT Hub zodat het gegevens kan verzenden naar het Azure-Cloud platform.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.custom:
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.openlocfilehash: 3e5ab1667ee0cda459785efa624bd7f4fc6818b8
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562953"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit AZ3166 verbinden met Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

U kunt de [MXChip IOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) gebruiken om IOT-oplossingen (prototype Internet of Things) te ontwikkelen die profiteren van Microsoft Azure Services. Het bevat een Arduino-compatibel bord met uitgebreide rand apparatuur en Sens oren, een open-source kaart pakket en een rijke [voorbeeld galerie](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Wat u leert

* Hoe u een IoT hub maakt en een apparaat registreert voor de MXChip IoT DevKit.
* De IoT-DevKit verbinden met Wi-Fi en de IoT Hub connection string configureren.
* Hoe u de telemetrie-gegevens van de DevKit-sensor verzendt naar uw IoT-hub.
* De ontwikkel omgeving voorbereiden en de toepassing ontwikkelen voor de IoT-DevKit.

Hebt u nog geen DevKit? Probeer de [DevKit Simulator](https://azure-samples.github.io/iot-devkit-web-simulator/) of [Koop een DevKit](https://aka.ms/iot-devkit-purchase).

U vindt de bron code voor alle DevKit-zelf studies uit de [Galerie met code voorbeelden](/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Wat u nodig hebt

- Een MXChip IoT DevKit-bord met een micro USB-kabel. [Nu downloaden](https://aka.ms/iot-devkit-purchase).
- Een computer met Windows 10, macOS 10.10 + of Ubuntu 18.04 +.
- Een actief Azure-abonnement. [Activeer een gratis Microsoft Azure-abonnement van 30 dagen](https://azureinfo.microsoft.com/us-freetrial.html).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
  
## <a name="prepare-your-hardware"></a>Uw hardware voorbereiden

De volgende hardware aansluiten op uw computer:

* DevKit Board
* Micro USB-kabel

![Vereiste hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Voer de volgende stappen uit om de DevKit te verbinden met uw computer:

1. Sluit de USB-kabel aan op uw computer.

2. Sluit de Micro-USB aan op de DevKit.

3. Met de groene LED voor Power wordt de verbinding bevestigd.

   ![Hardware-verbindingen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Quick Start: verzend telemetrie van DevKit naar een IoT Hub

De Snelstartgids maakt gebruik van vooraf gecompileerde DevKit-firmware om de telemetrie naar de IoT Hub te verzenden. Voordat u het uitvoert, maakt u een IoT-hub en registreert u een apparaat bij de hub.

### <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdrachten uit in Azure Cloud Shell om de apparaat-id te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **MyNodeDevice**: de naam van het apparaat dat u wilt registreren. Gebruik **MyNodeDevice**, zoals weergegeven. Als u een andere naam voor het apparaat kiest, moet u deze naam in de rest van dit artikel gebruiken, en moet u de apparaatnaam bijwerken in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Als er een fout optreedt `device-identity` , installeert u de [Azure IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI aan uw CLI Shell-instantie toe te voegen. De IoT-extensie voegt opdrachten toe die specifiek zijn voor IoT Hub, IoT Edge en IoT Device Provisioning Service (DPS) aan Azure CLI.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Voer de volgende opdrachten uit in Azure Cloud Shell om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Noteer de apparaatverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde verderop in de snelstartgids.

### <a name="send-devkit-telemetry"></a>DevKit-telemetrie verzenden

De DevKit maakt verbinding met een apparaat-specifiek eind punt op uw IoT-hub en verzendt een telemetrie van de Tempe ratuur en vochtigheid.

1. Down load de nieuwste versie van de [GetStarted-firmware](https://aka.ms/devkit/prod/getstarted/latest) voor IOT DevKit.

1. Zorg ervoor dat IoT DevKit verbinding maakt met uw computer via USB. Verkenner openen er is een USB-apparaat voor massa opslag met de naam **AZ3166**.

    ![Windows Verkenner openen](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Slepen en neerzetten van de firmware die zojuist is gedownload naar het apparaat voor massa opslag. deze wordt automatisch Flash.

    ![Firmware kopiëren](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. Houd op de DevKit knop **b**, druk op de knop **Reset** en laat deze los en laat vervolgens de knop **b** los. Uw DevKit voert de AP-modus in. Ter bevestiging: in het scherm wordt de SSID (Service Set Identifier) van de DevKit en het IP-adres van de configuratie portal weer gegeven.

    ![Knop opnieuw instellen, knop B en SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![De modus AP instellen](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Gebruik een webbrowser op een ander wifi-apparaat (computer of mobiele telefoon) om verbinding te maken met de IoT DevKit-SSID van de vorige stap. Als u wordt gevraagd om een wacht woord, laat dit leeg.

    ![SSID verbinden](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Open **192.168.0.1** in de browser. Selecteer de Wi-Fi waarmee u de IoT DevKit wilt verbinden, typ het Wi-Fi wacht woord en plak het apparaat connection string u eerder een notitie hebt gemaakt. Klik vervolgens op Save.

    ![Configuratie-UI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit biedt alleen ondersteuning voor een 2,4 GHz-netwerk. Raadpleeg de [Veelgestelde vragen](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) voor meer informatie.

1. De WiFi-informatie en de connection string van het apparaat worden opgeslagen in de IoT-DevKit wanneer u de pagina met resultaten ziet.

    ![Configuratie resultaat](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Nadat Wi-Fi is geconfigureerd, blijven uw referenties voor die verbinding op het apparaat behouden, zelfs als het apparaat is ontkoppeld.

1. De IoT DevKit wordt binnen enkele seconden opnieuw opgestart. Op het scherm DevKit ziet u het IP-adres voor de DevKit dat wordt gevolgd door de telemetriegegevens, inclusief de waarde voor Tempe ratuur en vochtigheid met aantal berichten verzenden naar Azure IoT Hub.

    ![Wi-Fi-IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Gegevens verzenden](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Als u de telemetriegegevens wilt verifiëren die naar Azure worden verzonden, voert u de volgende opdracht uit in Azure Cloud Shell:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

Volg deze stappen om de ontwikkelings omgeving voor te bereiden voor de DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Visual Studio code installeren met Azure IoT tools extension-pakket

1. Installeer [ARDUINO IDE](https://www.arduino.cc/en/Main/Software). Het biedt de benodigde hulpprogramma keten voor het compileren en uploaden van Arduino-code.
    * **Windows**: Windows Installer versie gebruiken. Installeer niet vanuit de App Store.
    * **macOS**: Sleep de geëxtraheerde **Arduino. app** naar map en zet deze neer. `/Applications`
    * **Ubuntu**: pak het bestand uit in een map, zoals `$HOME/Downloads/arduino-1.8.8`

2. Installeer [Visual Studio code](https://code.visualstudio.com/), een bron code-editor op meerdere platformen met krachtige IntelliSense, de ondersteuning voor het volt ooien van code en het opsporen van fouten, evenals uitgebreide uitbrei dingen die kunnen worden geïnstalleerd vanuit Marketplace.

3. Start VS code, zoek naar **Arduino** in de uitbreidings Marketplace en installeer deze. Deze uitbrei ding biedt verbeterde ervaring voor het ontwikkelen op het Arduino-platform.

    ![Arduino installeren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Zoek naar [Azure IOT-Hulpprogram ma's](https://aka.ms/azure-iot-tools) op de uitbreidings Marketplace en installeer deze.

    ![Scherm opname van Azure IoT-Hulpprogram Ma's in de uitbreidings Marketplace.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Of kopieer en plak deze URL in een browser venster: `vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > Het extensie pakket voor Azure IoT-Hulpprogram Ma's bevat de [Azure IOT-Device Workbench](https://aka.ms/iot-workbench) die wordt gebruikt voor het ontwikkelen en opsporen van fouten op verschillende IOT Devkit-apparaten. De [Azure IoT Hub-extensie](https://aka.ms/iot-toolkit), die zich ook in het Azure IoT Tools-extensiepakket bevindt, wordt gebruikt voor het beheren van en communiceren met Azure IoT Hubs.

5. VS-code configureren met Arduino-instellingen.

    Klik in Visual Studio code op **File > preferences > Settings** (op MacOS, **code > Preferences > Settings**). Klik vervolgens op het pictogram **open instellingen (JSON)** in de rechter bovenhoek van de pagina *instellingen* .

    ![Azure IoT Tools installeren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Voeg de volgende regels toe om Arduino te configureren, afhankelijk van uw platform: 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        Vervang de tijdelijke aanduiding **{username}** hieronder met uw gebruikersnaam.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Klik `F1` om het opdracht palet te openen, typ en selecteer **Arduino: Board Manager**. Zoek naar **AZ3166** en installeer de meest recente versie.

    ![DevKit SDK installeren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>ST-Link Stuur Programma's installeren

[St-link/v2](https://www.st.com/en/development-tools/st-link-v2.html) is de USB-interface die door IOT DevKit wordt gebruikt om te communiceren met uw ontwikkel machine. U moet deze in Windows installeren om de gecompileerde apparaatcode weer te geven op de DevKit. Volg de specifieke stappen voor uw besturingssysteem om de machine toegang te geven tot uw apparaat.

* **Windows**: down load en installeer USB-stuur programma vanaf de [STMicroelectronics-website](https://www.st.com/en/development-tools/stsw-link009.html).
* **MacOS**: er is geen stuur programma vereist voor macOS.
* **Ubuntu**: Voer de opdrachten uit op de terminal en meld u af en weer aan om de groepswijziging door te voeren:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Nu bent u allemaal ingesteld voor het voorbereiden en configureren van uw ontwikkel omgeving. Laat ons het GetStarted-voor beeld dat u zojuist hebt uitgevoerd, bouwen.

## <a name="build-your-first-project"></a>Uw eerste project bouwen

### <a name="open-sample-code-from-sample-gallery"></a>Voorbeeld code uit voorbeeld galerie openen

De IoT-DevKit bevat een uitgebreide galerie met voor beelden die u kunt gebruiken om verbinding te maken tussen de DevKit en verschillende Azure-Services.

1. Zorg ervoor dat uw IoT-DevKit **niet is verbonden** met uw computer. Start VS code eerst en sluit de DevKit aan op uw computer.

1. Klik `F1` om het opdracht palet te openen, typ en selecteer **Azure IOT Device Workbench: voor beelden openen...**. Selecteer vervolgens **IOT DevKit** as Board.

1. Ga op de pagina met IoT Workbench-voor beelden naar **aan de slag** en klik op voor **Beeld openen**. Selecteert vervolgens het standaardpad voor het downloaden van de voorbeeld code.

    ![Voor beeld openen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>IoT Hub en apparaat van Azure inrichten

In plaats van Azure IoT Hub en het apparaat vanuit de Azure Portal in te richten, kunt u dit doen in de VS code zonder de ontwikkel omgeving te verlaten.

1. Klik in het venster Nieuw geopend project `F1` om het opdracht palet te openen, typ en selecteer **Azure IOT Device Workbench: Azure-Services inrichten...**. Volg de stapsgewijze hand leiding om het inrichten van uw Azure IoT Hub te volt ooien en het IoT Hub apparaat te maken.

    ![Opdracht inrichten](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Als u zich niet hebt aangemeld bij Azure. Volg de pop-upmelding voor het aanmelden.

1. Selecteer het abonnement dat u wilt gebruiken.

    ![Sub selecteren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Selecteer of maak vervolgens een nieuwe [resource groep](../azure-resource-manager/management/overview.md#terminology).

    ![Resourcegroep selecteren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. In de resource groep die u hebt opgegeven, volgt u de hand leiding voor het selecteren of maken van een nieuwe Azure-IoT Hub.

    ![IoT Hub stappen selecteren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![IoT Hub selecteren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Geselecteerde IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. In het venster uitvoer ziet u dat Azure IoT Hub ingericht.

    ![IoT Hub ingericht](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Selecteer of maak een nieuw apparaat in azure IoT Hub u hebt ingericht.

    ![IoT-apparaat tappen selecteren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selecteer IoT-apparaat ingericht](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Nu hebt u de Azure IoT Hub ingericht en het apparaat erin gemaakt. De connection string van het apparaat wordt ook opgeslagen in VS code voor het configureren van de IoT DevKit later.

    ![Gereed voor inrichting](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Apparaatcode configureren en compileren

1. Controleer in de status balk rechtsonder de **MXCHIP AZ3166** wordt weer gegeven als geselecteerd bord en seriële poort met **STMicroelectronics** wordt gebruikt.

    ![Bord en COM selecteren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Klik `F1` om het opdracht palet te openen, typ en selecteer **Azure IOT Device Workbench: Apparaatinstellingen configureren...** en selecteer vervolgens **configuratie apparaat verbindings reeks > Selecteer IOT hub apparaat verbindings reeks**.

1. Houd op DevKit de **knop** **opnieuw instellen** in de vervolg keuzelijst en laat deze knop los en laat vervolgens **knop a** los. Uw DevKit voert de configuratie modus in en slaat de connection string op.

    ![Verbindingsreeks](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Klik `F1` opnieuw, typ en selecteer **Azure IOT Device Workbench: code van het apparaat uploaden**. De code wordt nu gecompileerd en geüpload naar DevKit.

    ![Arduino uploaden](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

De DevKit wordt opnieuw opgestart en het uitvoeren van de code wordt gestart.

> [!NOTE]
> Als er fouten of onderbrekingen zijn, kunt u deze altijd herstellen door de opdracht opnieuw uit te voeren.

## <a name="test-the-project"></a>Het project testen

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>De telemetrie weer geven die is verzonden naar Azure IoT Hub

Klik op het pictogram netstroom op de status balk om de seriële monitor te openen:

![Seriële monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

De voorbeeld toepassing wordt uitgevoerd wanneer u de volgende resultaten ziet:

* Op de seriële monitor wordt het bericht weer gegeven dat naar de IoT Hub is verzonden.
* De LED van de MXChip IoT DevKit knippert.

![Uitvoer van seriële monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> Er kan een fout optreden tijdens het testen waarbij de LED niet knippert, het Azure Portal geen inkomende gegevens van het apparaat weergeeft, maar het OLED scherm van het apparaat wordt weer gegeven als **actief...**. Om het probleem op te lossen, gaat u in de Azure Portal naar het apparaat in de IoT-hub en verzendt u een bericht naar het apparaat. Als u het volgende antwoord ziet in de seriële monitor in VS code, is het mogelijk dat directe communicatie vanaf het apparaat wordt geblokkeerd op het niveau van de router. Controleer de firewall-en router regels die zijn geconfigureerd voor de apparaten die verbinding maken. Zorg er ook voor dat de uitgaande poort 1833 is geopend.
> 
> FOUT: mqtt_client. c (ln 454): fout: fout bij het openen van de verbinding met het eind punt  
> INFO: verbindings status  >>>: verbinding verbroken  
> FOUT: tlsio_mbedtls. c (ln 604): de onderliggende IO kan niet worden geopend  
> FOUT: mqtt_client. c (ln 1042): fout: io_open mislukt  
> FOUT: iothubtransport_mqtt_common. c (ln 2283): kan geen verbinding maken met het adres atcsliothub.azure-devices.net.  
> INFO:  >>>opnieuw verbinding maken.  
> INFO: IoThub-versie: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>De telemetrie weer geven die is ontvangen door Azure IoT Hub

U kunt [Azure IOT-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) gebruiken voor het bewaken van D2C-berichten (Device-to-Cloud) in IOT hub.

1. Meld u aan [Azure Portal](https://portal.azure.com/), zoek de IOT hub die u hebt gemaakt.

    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. Klik in het deel venster **beleid voor gedeelde toegang** op het **iothubowner-beleid** en noteer de verbindings reeks van uw IOT-hub.

    ![Azure IoT Hub connection string](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. In VS code, klikt `F1` u op, typt en selecteert u **Azure IOT hub: Stel IOT hub verbindings reeks** in. Kopieer de connection string ernaar.

    ![Azure IoT Hub connection string instellen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Vouw het deel venster **apparaten van Azure IOT hub** aan de linkerkant uit, klik met de rechter muisknop op de apparaatnaam die u hebt gemaakt en selecteer controle van het **ingebouwde gebeurtenis starten**.

    ![D2C-bericht bewaken](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. In het deel venster **uitvoer** ziet u de binnenkomende D2C-berichten voor de IOT hub.

    ![Scherm opname van de binnenkomende D2C-berichten naar de IoT Hub.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>De code bekijken

Het `GetStarted.ino` is het belangrijkste Arduino-schets bestand.

![D2C-bericht](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Als u wilt zien hoe telemetrie van het apparaat wordt verzonden naar de Azure IoT Hub, opent u het `utility.cpp` bestand in dezelfde map. Bekijk [API-verwijzing](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) voor meer informatie over het gebruik van Sens oren en rand apparatuur op IOT DevKit.

De `DevKitMQTTClient` gebruikte is een wrapper van de **iothub_client** van de [Microsoft Azure IOT-sdk's en-bibliotheken voor C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) om met Azure IOT hub te communiceren.

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, kunt u controleren op een oplossing in de [Veelgestelde vragen over IOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) of contact opnemen met ons van [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). U kunt ook feedback geven door een opmerking op deze pagina te laten staan.

## <a name="next-steps"></a>Volgende stappen

U hebt een MXChip IoT-DevKit verbonden met uw IoT-hub en u hebt de vastgelegde sensor gegevens verzonden naar uw IoT-hub.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
