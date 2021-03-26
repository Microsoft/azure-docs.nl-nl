---
title: Een MXCHIP-AZ3166 verbinden met Azure IoT Central Snelstartgids
description: Gebruik Azure RTO'S embedded-software om een MXCHIP AZ3166-apparaat te verbinden met Azure IoT en telemetrie te verzenden.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 03/17/2021
ms.openlocfilehash: 367f527180a310f2cbc74b1ccdc1102e1e53d1cf
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605978"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-central"></a>Quick Start: een MXCHIP AZ3166 Devkit verbinden met IoT Central

**Van toepassing op**: [ontwikkelen van Inge sloten apparaten](about-iot-develop.md#embedded-device-development)<br>
**Totale voltooiings tijd**: 30 minuten

[![Code zoeken](media/common/browse-github-code.png)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

In deze zelf studie gebruikt u Azure RTO'S om een MXCHIP AZ3166 IoT DevKit (hierna MXCHIP DevKit) te verbinden met Azure IoT. Het artikel maakt deel uit van de reeks aan de [slag met het ontwikkelen van Azure IOT embedded-apparaten](quickstart-device-development.md). De serie introduceert ontwikkel aars van apparaten met Azure RTO'S en laat zien hoe u verschillende evaluatie pakketten voor apparaten verbindt met Azure IoT.

U gaat de volgende taken uitvoeren:

* Een set Inge sloten ontwikkel hulpprogramma's installeren voor het Program meren van een MXCHIP DevKit in C
* Een installatie kopie bouwen en flashen op de MXCHIP-DevKit
* Azure IoT Central gebruiken om Cloud onderdelen te maken, eigenschappen weer te geven, telemetrie van apparaat weer te geven en direct opdrachten aan te roepen

## <a name="prerequisites"></a>Vereisten

* Een PC met micro soft Windows 10
* [Git](https://git-scm.com/downloads) voor het klonen van de opslag plaats
* Hardware

    > * De [MXCHIP AZ3166 IOT DevKit](https://aka.ms/iot-devkit) (MXCHIP DevKit)
    > * Wi-Fi 2,4 GHz
    > * USB 2,0 een mannelijk-naar-micro-USB-kabel

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

Als u uw ontwikkel omgeving wilt instellen, moet u eerst een GitHub-opslag plaats klonen dat alle assets bevat die u nodig hebt voor de zelf studie. Vervolgens installeert u een set hulpprogram ma's voor Program meren.

### <a name="clone-the-repo-for-the-tutorial"></a>De opslag plaats voor de zelf studie klonen

Kloon de volgende opslag plaats voor het downloaden van alle voorbeeld code van het apparaat, installatie scripts en offline versies van de documentatie. Als u deze opslag plaats eerder in een andere zelf studie hebt gekloond, hoeft u dit niet opnieuw te doen.

Als u de opslag plaats wilt klonen, voert u de volgende opdracht uit:

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>De hulpprogram ma's installeren

De gekloonde opslag plaats bevat een installatie script waarmee de vereiste hulpprogram ma's worden geïnstalleerd en geconfigureerd. Als u deze hulpprogram ma's in de aan de slag-hand leiding hebt geïnstalleerd in een andere zelf studie, hoeft u dit niet opnieuw te doen.

> [!NOTE]
> Met het installatie script worden de volgende hulpprogram ma's geïnstalleerd:
> * [Cmake](https://cmake.org): Build
> * [Arm gcc](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): compileren
> * [Termite](https://www.compuphase.com/software_termite.htm): seriële poort uitvoer voor verbonden apparaten bewaken

De hulpprogram ma's installeren:

1. Ga in Verkenner naar het volgende pad in de opslag plaats en voer het installatie script uit met de naam *get-toolchain.bat*:

    > *getting-started\tools\get-toolchain.bat*

1. Nadat de installatie is uitgevoerd, opent u een nieuw console venster om de configuratie wijzigingen van het installatie script te herkennen. Gebruik deze console om de resterende programmeer taken in de zelf studie te volt ooien. U kunt Windows CMD, Power shell of git bash voor Windows gebruiken.
1. Voer de volgende code uit om te controleren of CMake-versie 3,14 of hoger is geïnstalleerd.

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>De Cloud onderdelen maken

### <a name="create-the-iot-central-application"></a>De IoT Central-toepassing maken

Er zijn verschillende manieren om apparaten te verbinden met Azure IoT. In deze sectie leert u hoe u een apparaat verbindt met behulp van Azure IoT Central. IoT Central is een IoT-toepassings platform dat de kosten en complexiteit van het maken en beheren van IoT-oplossingen vermindert.

Een nieuwe toepassing maken:
1. Selecteer in [Azure IOT Central Portal](https://apps.azureiotcentral.com/) **mijn apps** in het navigatie menu aan de zijkant.
1. Selecteer **+ nieuwe toepassing**.
1. Selecteer **Aangepaste apps**.
1. Voeg een toepassings naam en een URL toe.
1. Kies het **gratis** prijs plan om een proef versie van 7 dagen te activeren.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-custom.png" alt-text="Een aangepaste app maken in azure IoT Central":::

1. Selecteer **Maken**.

    Nadat IoT Central de toepassing hebt ingericht, wordt u automatisch omgeleid naar het nieuwe toepassings dashboard.

    > [!NOTE]
    > Als u een bestaande IoT Central toepassing hebt, kunt u deze gebruiken om de stappen in dit artikel uit te voeren in plaats van een nieuwe toepassing te maken.

### <a name="create-a-new-device"></a>Een nieuw apparaat maken

In deze sectie gebruikt u het toepassings Dashboard van IoT Central om een nieuw apparaat te maken. U gebruikt de verbindings gegevens voor het zojuist gemaakte apparaat om veilig verbinding te maken met uw fysieke apparaat in een later gedeelte.

Een apparaat maken:
1. Selecteer in het toepassings dashboard **apparaten** in het navigatie menu aan de zijkant.
1. Selecteer **+ Nieuw** om het venster **een nieuw apparaat maken** te openen.
1. De Device-sjabloon behouden als niet- **toegewezen**.
1. Vul de gewenste apparaatnaam en apparaat-ID in.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-device.png" alt-text="Een apparaat maken in azure IoT Central":::

1. Selecteer de knop **Create** (Maken).
1. Het apparaat dat u zojuist hebt gemaakt, wordt weer gegeven in de lijst **alle apparaten** .  Selecteer de naam van het apparaat om de details weer te geven.
1. Selecteer **verbinding maken** in de bovenste menu balk om de verbindings gegevens weer te geven die worden gebruikt voor het configureren van het apparaat in de volgende sectie.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-connection-info.png" alt-text="Verbindings Details van apparaat weer geven":::

1. Noteer de verbindings waarden voor de volgende connection string-para meters die worden weer gegeven in het dialoog venster **verbinden** . U voegt deze waarden in de volgende stap toe aan een configuratie bestand:

    > * `ID scope`
    > * `Device ID`
    > * `Primary key`

## <a name="prepare-the-device"></a>Het apparaat voorbereiden

Als u de MXCHIP-DevKit wilt verbinden met Azure, wijzigt u een configuratie bestand voor Wi-Fi-en Azure IoT-instellingen, bouwt u de installatie kopie opnieuw op en flasht u de installatie kopie naar het apparaat.

### <a name="add-configuration"></a>Configuratie toevoegen

1. Open het volgende bestand in een tekst editor:

    > *getting-started\MXChip\AZ3166\app\ azure_config. h*

1. Stel de Wi-Fi constanten in op de volgende waarden in uw lokale omgeving.

    |Constante naam|Waarde|
    |-------------|-----|
    |`WIFI_SSID` |{*Uw Wi-Fi SSID*}|
    |`WIFI_PASSWORD` |{*Uw Wi-Fi wacht woord*}|
    |`WIFI_MODE` |{*Een van de genummerde Wi-Fi modus waarden in het bestand*}|

1. Stel de constanten van het Azure IoT-apparaat in op de waarden die u hebt opgeslagen nadat u Azure-resources hebt gemaakt.

    |Constante naam|Waarde|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*Uw ID-bereik waarde*}|
    |`IOT_DPS_REGISTRATION_ID` |{*Uw waarde voor apparaat-id*}|
    |`IOT_DEVICE_SAS_KEY` |{*Uw primaire sleutel waarde*}|

1. Sla het bestand op en sluit het.

### <a name="build-the-image"></a>De installatiekopie bouwen

Voer in uw-console of in Verkenner het script *rebuild.bat* op het volgende pad uit om de installatie kopie te bouwen:

> *getting-started\MXChip\AZ3166\tools\rebuild.bat*

Nadat de build is voltooid, controleert u of het binaire bestand is gemaakt in het volgende pad:

> *getting-started\MXChip\AZ3166\build\app\ mxchip_azure_iot. bin*

### <a name="flash-the-image"></a>De afbeelding flashen

1. Zoek op de MXCHIP-DevKit de **Reset** -knop en de micro USB-poort. U gebruikt deze onderdelen in de volgende stappen. Beide zijn gemarkeerd in de volgende afbeelding:

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/mxchip-iot-devkit.png" alt-text="Belang rijke onderdelen op het MXChip Devkit-bord zoeken":::

1. Verbind de micro USB-kabel met de micro USB-poort op de MXCHIP DevKit en verbind deze met de computer.
1. Ga in Verkenner naar het binaire bestand dat u in de vorige sectie hebt gemaakt.
1. Kopieer het binaire bestand *mxchip_azure_iot. bin*.
1. Ga in Verkenner naar het MXCHIP DevKit-apparaat dat op uw computer is aangesloten. Het apparaat wordt weer gegeven als een station op uw systeem met het station label **AZ3166**.
1. Plak het binaire bestand in de hoofdmap van de MXCHIP Devkit. Flashen wordt automatisch gestart en binnen een paar seconden voltooid.

    > [!NOTE]
    > Tijdens het Knip proces wisselt een groene LED over op MXCHIP DevKit.

### <a name="confirm-device-connection-details"></a>Details van verbinding met apparaat bevestigen

U kunt de **Termite** -app gebruiken om de communicatie te controleren en te controleren of het apparaat juist is ingesteld.

1. **Termite** starten.
    > [!TIP]
    > Als u geen verbinding kunt maken met Termite met uw Devkit, installeert u het [stuur programma St-link](https://my.st.com/content/ccc/resource/technical/software/driver/files/stsw-link009.zip) en probeert u het opnieuw. Zie  [probleem oplossing](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md) voor extra stappen.
1. Selecteer **Instellingen**.
1. Controleer in het dialoog venster **instellingen voor seriële poort** de volgende instellingen en update indien nodig:
    * **Baud-rate**: 115.200
    * **Poort**: de poort waarop uw MXCHIP-DevKit is aangesloten. Als er meerdere poort opties in de vervolg keuzelijst staan, kunt u de juiste poort vinden om te gebruiken. Open Windows **Apparaatbeheer** en Bekijk **poorten** om te bepalen welke poort moet worden gebruikt.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/termite-settings.png" alt-text="Instellingen in de Termite-app bevestigen":::

1. Selecteer OK.
1. Druk op de knop **Reset** op het apparaat. De knop is gelabeld op het apparaat en bevindt zich in de buurt van de micro USB-connector.
1. Controleer in de **Termite** -app de volgende controlepunt waarden om te bevestigen dat het apparaat is geïnitialiseerd en is verbonden met Azure IOT.

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 10.0.0.123
        Mask: 255.255.255.0
        Gateway: 10.0.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 10.0.0.1
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 185.242.56.3
        SNTP time update: Nov 16, 2020 23:47:35.385 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: ***
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***
        Device id: ***
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoTHub
    SUCCESS: Azure IoT Hub client initialized

    Starting Main loop
    ```

Houd Termite geopend om de uitvoer van het apparaat in de volgende stappen te controleren.

## <a name="verify-the-device-status"></a>De apparaatstatus verifiëren

De apparaatstatus weer geven in IoT Central portal:
1. Selecteer in het toepassings dashboard **apparaten** in het navigatie menu aan de zijkant.
1. Controleer of de **Apparaatstatus** is bijgewerkt naar **ingericht**.
1. Controleer of de **sjabloon** voor het apparaat is bijgewerkt om aan de slag te gaan met de **hand leiding**.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-view-status.png" alt-text="Apparaatstatus weer geven in IoT Central":::

## <a name="view-telemetry"></a>Telemetrie bekijken

Met IoT Central kunt u de stroom van telemetrie van uw apparaat weer geven naar de Cloud.

Telemetrie weer geven in IoT Central portal:

1. Selecteer in het toepassings dashboard **apparaten** in het navigatie menu aan de zijkant.
1. Selecteer het apparaat in de lijst met apparaten.
1. Bekijk de telemetrie wanneer het apparaat berichten verzendt naar de Cloud op het tabblad **overzicht** .

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-telemetry.png" alt-text="Telemetrie van apparaten weer geven in IoT Central":::

    > [!NOTE]
    > U kunt ook telemetrie van het apparaat bewaken met behulp van de Termite-app.

## <a name="call-a-direct-method-on-the-device"></a>Een rechtstreekse methode aanroepen op het apparaat

U kunt IoT Central ook gebruiken om een directe methode aan te roepen die u op uw apparaat hebt geïmplementeerd. Directe methoden hebben een naam en kunnen eventueel een JSON-Payload, een Configureer bare verbinding en een time-out voor de methode hebben. In deze sectie roept u een methode aan waarmee u een LED kunt inschakelen of uitschakelen.

Een methode aanroepen in IoT Central portal:

1. Selecteer het tabblad **opdrachten** op de pagina apparaat.
1. Selecteer **status** en selecteer **uitvoeren**.  Het LED-lampje moet worden ingeschakeld.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-invoke-method.png" alt-text="Een rechtstreekse methode aanroepen op een apparaat":::

1. Schakel de selectie **status** uit en selecteer **uitvoeren**. Het LED-lampje moet worden uitgeschakeld.

## <a name="view-device-information"></a>Apparaatgegevens weer geven

U kunt de apparaatgegevens van IoT Central weer geven.

Selecteer tabblad **info** op de pagina apparaat.

:::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-about.png" alt-text="Informatie over het apparaat weer geven in IoT Central":::

## <a name="debugging"></a>Foutopsporing

Zie [fouten opsporen met Visual Studio code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md)voor meer informatie over het opsporen van fouten in de toepassing.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de Azure-resources die u in deze zelf studie hebt gemaakt, niet meer nodig hebt, kunt u deze uit de IoT Central Portal verwijderen. Als u echter een andere zelf studie in deze aan de slag-hand leiding doorloopt, kunt u de resources die u al hebt gemaakt, behouden en opnieuw gebruiken.

De volledige Azure IoT Central-voorbeeld toepassing en alle bijbehorende apparaten en resources verwijderen:
1. Selecteer **beheer**  >  **uw toepassing**.
1. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een aangepaste installatie kopie gemaakt die de voorbeeld code van Azure RTO'S bevat, waarna de installatie kopie wordt geknipperd naar het MXCHIP DevKit-apparaat. U hebt ook de IoT Central Portal gebruikt om Azure-resources te maken, de MXCHIP DevKit veilig te verbinden met Azure, telemetrie weer te geven en berichten te verzenden.

* Voor ontwikkel aars van apparaten is de voorgestelde volgende stap het weer geven van de andere zelf studies in de reeks aan de slag [met het ontwikkelen van Azure IOT embedded-apparaten](quickstart-device-development.md).
* Zie [probleem oplossing](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md)als u problemen ondervindt bij het initialiseren of verbinding maken met uw apparaat nadat u de stappen in deze hand leiding hebt volgen.
* Zie [Azure Rto's gebruiken in de hand leiding aan de](https://github.com/azure-rtos/getting-started/blob/master/docs/using-azure-rtos.md)slag voor meer informatie over hoe Azure rto's-onderdelen worden gebruikt in de voorbeeld code voor deze zelf studie.

    > [!IMPORTANT]
    > Azure RTO'S voorziet Oem's van onderdelen om communicatie te beveiligen en om code-en gegevens isolatie te maken met behulp van onderliggende MCU/MPU-beveiligings mechanismen voor hardware. Elke OEM is echter uiteindelijk verantwoordelijk om ervoor te zorgen dat het apparaat voldoet aan de veranderende beveiligings vereisten.

