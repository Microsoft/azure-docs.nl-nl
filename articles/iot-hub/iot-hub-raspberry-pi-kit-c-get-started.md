---
title: Raspberry Pi verbinden met Azure IoT Hub met behulp van C | Microsoft Docs
description: Meer informatie over het instellen en verbinden van Raspberry Pi met Azure IoT Hub voor Raspberry PI om gegevens te verzenden naar het Azure-Cloud platform
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 52da3c28faa375f8b308e3fe78329fec4f996af9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97804051"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Raspberry Pi verbinden met Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

In deze zelf studie begint u met het leren van de basis principes van het werken met Raspberry Pi met Raspbian. Vervolgens leert u hoe u uw apparaten probleemloos verbindt met de Cloud met behulp van [Azure IOT hub](about-iot-hub.md). Voor Windows 10 IoT core-voor beelden gaat u naar het [Windows-ontwikkelaars centrum](https://www.windowsondevices.com/).

Hebt u nog geen Kit? Probeer [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md). Of koop [hier](https://azure.microsoft.com/develop/iot/starter-kits)een nieuwe kit.

## <a name="what-you-do"></a>Wat u doet

* Maak een IoT-hub.

* Registreer een apparaat voor pi in uw IoT-hub.

* Setup Raspberry pi.

* Voer een voorbeeld toepassing op PI uit om sensor gegevens naar uw IoT-hub te verzenden.

Verbind Raspberry Pi met een IoT-hub die u maakt. Vervolgens voert u een voorbeeld toepassing uit op pi voor het verzamelen van de Tempe ratuur-en vochtigheids gegevens van een BME280-sensor. Ten slotte verzendt u de sensor gegevens naar uw IoT-hub.

## <a name="what-you-learn"></a>Wat u leert

* Een Azure IoT hub maken en uw nieuwe apparaat connection string ophalen.

* Hoe kan ik Pi verbinden met een BME280-sensor.

* Sensor gegevens verzamelen door een voorbeeld toepassing op PI uit te voeren.

* Sensor gegevens verzenden naar uw IoT-hub.

## <a name="what-you-need"></a>Wat u nodig hebt

![Wat u nodig hebt](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* De Raspberry pi 2 of Raspberry Pi 3 Board.

* Een actief Azure-abonnement. Als u geen Azure-account hebt, kunt u binnen een paar minuten [een gratis Azure-proef account maken](https://azure.microsoft.com/free/) .

* Een monitor, een USB-toetsen bord en een muis die verbinding maken met pi.

* Een Mac of een PC waarop Windows of Linux wordt uitgevoerd.

* Een Internet verbinding.

* Een microSD-kaart van 16 GB of hoger.

* Een USB-SD-adapter of microSD-kaart voor het branden van de installatie kopie van het besturings systeem op de microSD-kaart.

* Een 5-volt 2-burst-voeding met de 6 meter micro USB-kabel.

De volgende items zijn optioneel:

* Een geassembleerd Adafruit BME280-Tempe ratuur, druk en vochtigheids sensor.

* Een breadboard.

* 6 F/M jumper draden.

* Een diffuse 10-mm LED.

> [!NOTE]
> Deze items zijn optioneel omdat het code voorbeeld gesimuleerde sensor gegevens ondersteunt.
>

## <a name="create-an-iot-hub"></a>Een IoT-hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry Pi instellen

Stel nu de Raspberry pi in.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Het Raspbian-besturings systeem installeren voor pi

Bereid de microSD-kaart voor op de installatie van de Raspbian-installatie kopie.

1. Down load Raspbian.

   1. [Down load Raspbian stretch met Desktop](https://www.raspberrypi.org/software/) (het zip-bestand).

   2. Pak de Raspbian-installatie kopie uit naar een map op uw computer.

2. Installeer Raspbian op de microSD-kaart.

   1. [Down load en installeer het hulp programma voor de security descriptor SD-smartcard brander](https://etcher.io/).

   2. Voer de etser uit en selecteer de Raspbian-installatie kopie die u in stap 1 hebt geëxtraheerd.

   3. Selecteer het microSD-kaart station. Houd er rekening mee dat de omkaderer mogelijk al het juiste station heeft geselecteerd.

   4. Klik op Flash om Raspbian te installeren op de microSD-kaart.

   5. Verwijder de microSD-kaart van uw computer wanneer de installatie is voltooid. Het is veilig om de microSD-kaart direct te verwijderen, omdat de microSD-kaart door etser automatisch wordt uitworpen of ontkoppeld na voltooiing.

   6. Plaats de microSD-kaart in pi.

### <a name="enable-ssh-and-spi"></a>SSH en SPI inschakelen

1. Verbind Pi met de monitor, het toetsen bord en de muis, start Pi en meld u vervolgens aan bij Raspbian door `pi` de gebruikers naam en `raspberry` het wacht woord te gebruiken.
 
2. Klik op het pictogram Raspberry > **voor keuren**  >  **Raspberry Pi-configuratie**.

   ![Het menu Raspbian-voor keuren](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. Stel op het tabblad **interfaces** **SPI** en **SSH** in op **inschakelen** en klik vervolgens op **OK**. Als u geen fysieke Sens oren hebt en gesimuleerde sensor gegevens wilt gebruiken, is deze stap optioneel.

   ![Schakel SPI en SSH in op Raspberry pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Als u SSH en SPI wilt inschakelen, kunt u meer naslag documenten vinden op [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) en [RASPI-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>De sensor aansluiten op pi

Gebruik de breadboard-en jumper draden om een LED en een BME280 te verbinden met PI als volgt. Als u de sensor niet hebt, kunt u [deze sectie overs Laan](#connect-pi-to-the-network).

![De Raspberry Pi-en sensor verbinding](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

De BME280-sensor kan gegevens over Tempe ratuur en vochtigheid verzamelen. En de LED knippert als er een communicatie tussen het apparaat en de Cloud is.

Voor sensor pincodes gebruikt u de volgende bedrading:

| Begin (sensor & LED)     | Einde (Board)            | Kabel kleur   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (PIN 5G)         | GPIO 4 (pincode 7)         | Witte kabel   |
| LED GND (PIN 6G)         | GND (pincode 6)            | Zwarte kabel   |
| VDD (PIN 18F)            | 3,3 v PWR (PIN 17)      | Witte kabel   |
| GND (PIN 20F)            | GND (pincode 20)           | Zwarte kabel   |
| SCK (PIN 21F)            | SPI0 SCLK (pincode 23)     | Oranje kabel  |
| SDO (PIN 22F)            | SPI0 MISO (PIN 21)     | Gele kabel  |
| SDI (PIN 23F)            | SPI0 MOSI (pincode 19)     | Groene kabel   |
| CS (PIN 24F)             | SPI0-CS (pincode 24)       | Blauwe kabel    |

Klik hier om [Raspberry pi 2 weer te geven &-toewijzing van 3 pincodes](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) voor uw referentie.

Nadat u BME280 hebt verbonden met uw Raspberry Pi, zou deze moeten lijken op de onderstaande afbeelding.

![Verbonden Pi en BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Pi verbinden met het netwerk

Schakel pi in met behulp van de micro USB-kabel en de voeding. Gebruik de Ethernet-kabel om pi verbinding te maken met uw bekabelde netwerk of volg de [instructies van de Raspberry Pi Foundation](https://www.raspberrypi.org/documentation/configuration/wireless/) om pi verbinding te laten maken met uw draadloze netwerk. Nadat uw Pi is verbonden met het netwerk, moet u rekening houden met het [IP-adres van uw Pi](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Verbonden met een bekabeld netwerk](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Een voorbeeld toepassing uitvoeren op pi

### <a name="sign-into-your-raspberry-pi"></a>Meld u aan bij uw Raspberry pi

1. Gebruik een van de volgende SSH-clients van de hostcomputer om verbinding te maken met uw Raspberry pi.
   
   **Windows-gebruikers**
   1. Down load en Installeer [putty](https://www.putty.org/) voor Windows. 
   1. Kopieer het IP-adres van uw Pi naar de sectie hostnaam (of IP-adres) en selecteer SSH als het verbindings type.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac-en Ubuntu-gebruikers**

   Gebruik de ingebouwde SSH-client op Ubuntu of macOS. Mogelijk moet u uitvoeren `ssh pi@<ip address of pi>` om pi via SSH te verbinden.
   > [!NOTE]
   > De standaard gebruikersnaam is `pi` en het wacht woord `raspberry` .


### <a name="configure-the-sample-application"></a>De voorbeeldtoepassing configureren

1. Kloon de voorbeeld toepassing door de volgende opdracht uit te voeren:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Installatie script uitvoeren:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Als u **geen fysieke BME280 hebt**, kunt u '--gesimuleerde gegevens ' gebruiken als opdracht regel parameter voor het simuleren van de tempe ratuur&vochtigheids gegevens. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>De voorbeeld toepassing bouwen en uitvoeren

1. Bouw de voorbeeld toepassing door de volgende opdracht uit te voeren:

   ```bash
   cmake . && make
   ```
   
   ![Uitvoer maken](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Voer de voorbeeld toepassing uit door de volgende opdracht uit te voeren:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Zorg ervoor dat u het apparaat connection string kopieert naar de enkele aanhalings tekens.
   >

Als het goed is, ziet u de volgende uitvoer met de sensorgegevens en de berichten die naar uw IoT-hub worden verzonden.

![Uitvoer: sensorgegevens die van Raspberry Pi naar uw IoT Hub worden verzonden](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Lees de berichten die door uw hub zijn ontvangen

Een manier om berichten te bewaken die door uw IoT-hub van uw apparaat worden ontvangen, is door de Azure IoT-Hulpprogram Ma's voor Visual Studio code te gebruiken. Zie [Azure IOT-Hulpprogram ma's voor Visual Studio code gebruiken voor het verzenden en ontvangen van berichten tussen uw apparaat en IOT hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)voor meer informatie.

Ga verder met de volgende sectie voor meer manieren om gegevens te verwerken die door uw apparaat worden verzonden.

## <a name="next-steps"></a>Volgende stappen

U hebt een voorbeeld toepassing uitgevoerd voor het verzamelen van sensor gegevens en deze naar uw IoT-hub te verzenden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]