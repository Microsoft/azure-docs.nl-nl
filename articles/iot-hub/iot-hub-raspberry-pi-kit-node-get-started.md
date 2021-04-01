---
title: Raspberry Pi verbinden met Azure IoT Hub in de Cloud (Node.js)
description: Meer informatie over het instellen en verbinden van Raspberry Pi met Azure IoT Hub voor Raspberry pi voor het verzenden van gegevens naar het Azure-Cloud platform in deze zelf studie.
author: wesmc7777
manager: eliotgra
keywords: Azure IOT Raspberry Pi, Raspberry Pi IOT hub, Raspberry Pi gegevens verzenden naar de Cloud, Raspberry Pi naar de Cloud
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
- devx-track-js
ms.openlocfilehash: c96f674b64401250d45542d0f59f13654cf37caa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97802521"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Verbinding maken tussen Raspberry Pi en Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

In deze zelf studie begint u met het leren van de basis principes van het werken met Raspberry Pi met Raspbian. Vervolgens leert u hoe u uw apparaten probleemloos verbindt met de Cloud met behulp van [Azure IOT hub](about-iot-hub.md). Voor Windows 10 IoT core-voor beelden gaat u naar het [Windows-ontwikkelaars centrum](https://www.windowsondevices.com/).

Hebt u nog geen Kit? Probeer [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md). Of koop [hier](https://azure.microsoft.com/develop/iot/starter-kits)een nieuwe kit.

## <a name="what-you-do"></a>Wat u doet

* Maak een IoT-hub.

* Registreer een apparaat voor pi in uw IoT-hub.

* Stel Raspberry pi in.

* Voer een voorbeeld toepassing op PI uit om sensor gegevens naar uw IoT-hub te verzenden.

## <a name="what-you-learn"></a>Wat u leert

* Een Azure IoT hub maken en uw nieuwe apparaat connection string ophalen.

* Hoe kan ik Pi verbinden met een BME280-sensor.

* Sensor gegevens verzamelen door een voorbeeld toepassing op PI uit te voeren.

* Sensor gegevens verzenden naar uw IoT-hub.

## <a name="what-you-need"></a>Wat u nodig hebt

![Wat u nodig hebt](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Een Raspberry pi 2 of Raspberry Pi 3-bord.

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

* Een monitor, een USB-toetsen bord en een muis die verbinding maken met pi.

* Een Mac of PC waarop Windows of Linux wordt uitgevoerd.

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
> Als u de optionele items niet hebt, kunt u gesimuleerde sensor gegevens gebruiken.

## <a name="create-an-iot-hub"></a>Een IoT-hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry Pi instellen

### <a name="install-the-raspbian-operating-system-for-pi"></a>Het Raspbian-besturings systeem installeren voor pi

Bereid de microSD-kaart voor op de installatie van de Raspbian-installatie kopie.

1. Down load Raspbian.

   a. [Raspbian Buster met Desktop](https://www.raspberrypi.org/software/) (het zip-bestand).

   b. Pak de Raspbian-installatie kopie uit naar een map op uw computer.

2. Installeer Raspbian op de microSD-kaart.

   a. [Down load en installeer het hulp programma voor de security descriptor SD-smartcard brander](https://etcher.io/).

   b. Voer de etser uit en selecteer de Raspbian-installatie kopie die u in stap 1 hebt geëxtraheerd.

   c. Selecteer het microSD-kaart station. Etser heeft mogelijk al het juiste station geselecteerd.

   d. Klik op Flash om Raspbian te installeren op de microSD-kaart.

   e. Verwijder de microSD-kaart van uw computer wanneer de installatie is voltooid. Het is veilig om de microSD-kaart direct te verwijderen, omdat de microSD-kaart door etser automatisch wordt uitworpen of ontkoppeld na voltooiing.

   f. Plaats de microSD-kaart in pi.

### <a name="enable-ssh-and-i2c"></a>SSH en I2C inschakelen

1. Sluit pi aan op de monitor, het toetsen bord en de muis.

2. Start Pi en meld u vervolgens aan bij Raspbian door gebruik te maken van `pi` de gebruikers naam en `raspberry` het wacht woord.

3. Klik op het pictogram Raspberry > **voor keuren**  >  **Raspberry Pi-configuratie**.

   ![Het menu Raspbian-voor keuren](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. Stel op het tabblad **interfaces** de optie **I2C** en **SSH** in op **inschakelen** en klik vervolgens op **OK**. Als u geen fysieke Sens oren hebt en gesimuleerde sensor gegevens wilt gebruiken, is deze stap optioneel.

   ![I2C en SSH inschakelen op Raspberry pi](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Als u SSH en I2C wilt inschakelen, kunt u meer naslag documenten vinden op [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) en [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>De sensor aansluiten op pi

Gebruik de breadboard-en jumper draden om een LED en een BME280 te verbinden met PI als volgt. Als u de sensor niet hebt, kunt u [deze sectie overs Laan](#connect-pi-to-the-network).

![De Raspberry Pi-en sensor verbinding](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

De BME280-sensor kan gegevens over Tempe ratuur en vochtigheid verzamelen. De LED knippert wanneer het apparaat een bericht naar de Cloud verzendt.

Voor sensor pincodes gebruikt u de volgende bedrading:

| Begin (sensor & LED)     | Einde (Board)            | Kabel kleur   |
| -----------------------  | ---------------------- | ------------: |
| VDD (PIN 5G)             | 3,3 v-PWR (pin 1)       | Witte kabel   |
| GND (PIN 7G)             | GND (pincode 6)            | Bruine kabel   |
| SDI (pincode 10G)            | I2C1 SDA (pincode 3)       | Rode kabel     |
| SCK (PIN 8G)             | I2C1 SQL (pincode 5)       | Oranje kabel  |
| LED VDD (PIN 18F)        | GPIO 24 (pincode 18)       | Witte kabel   |
| LED GND (PIN 17F)        | GND (pincode 20)           | Zwarte kabel   |

Klik hier om [Raspberry pi 2 weer te geven &-toewijzing van 3 pincodes](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) voor uw referentie.

Nadat u BME280 hebt verbonden met uw Raspberry Pi, zou deze moeten lijken op de onderstaande afbeelding.

![Verbonden Pi en BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Pi verbinden met het netwerk

Schakel pi in met behulp van de micro USB-kabel en de voeding. Gebruik de Ethernet-kabel om pi verbinding te maken met uw bekabelde netwerk of volg de [instructies van de Raspberry Pi Foundation](https://www.raspberrypi.org/documentation/configuration/wireless/) om pi verbinding te laten maken met uw draadloze netwerk. Nadat uw Pi is verbonden met het netwerk, moet u rekening houden met het [IP-adres van uw Pi](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Verbonden met een bekabeld netwerk](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Zorg ervoor dat pi is verbonden met hetzelfde netwerk als uw computer. Als uw computer bijvoorbeeld is verbonden met een draadloos netwerk terwijl Pi is verbonden met een bekabeld netwerk, ziet u mogelijk het IP-adres niet in de devdisco-uitvoer.

## <a name="run-a-sample-application-on-pi"></a>Een voorbeeld toepassing uitvoeren op pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>De voorbeeld toepassing klonen en de vereiste pakketten installeren

1. Maak verbinding met uw Raspberry Pi met een van de volgende SSH-clients van de hostcomputer:

   **Windows-gebruikers**

   a. Down load en Installeer [putty](https://www.putty.org/) voor Windows.

   b. Kopieer het IP-adres van uw Pi naar de sectie hostnaam (of IP-adres) en selecteer SSH als het verbindings type.

   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac-en Ubuntu-gebruikers**

   Gebruik de ingebouwde SSH-client op Ubuntu of macOS. Mogelijk moet u uitvoeren `ssh pi@<ip address of pi>` om pi via SSH te verbinden.

   > [!NOTE]
   > De standaard gebruikersnaam is `pi` en het wacht woord `raspberry` .

2. Installeer Node.js en NPM naar uw pi.

   Controleer eerst uw Node.js versie.

   ```bash
   node -v
   ```

   Als de versie lager is dan 10. x, of als er geen Node.js is op uw Pi, installeert u de nieuwste versie.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. De voorbeeld toepassing klonen.

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. Installeer alle pakketten voor het voor beeld. De installatie omvat Azure IoT Device SDK, de BME280-sensor bibliotheek en de bedradings-Pi bibliotheek.

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >Het kan enkele minuten duren voordat dit installatie proces is voltooid, afhankelijk van de netwerk verbinding.

### <a name="configure-the-sample-application"></a>De voorbeeldtoepassing configureren

1. Open het configuratie bestand door de volgende opdrachten uit te voeren:

   ```bash
   nano config.json
   ```

   ![Configuratie bestand](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   Dit bestand bevat twee items die u kunt configureren. De eerste is `interval` , waarmee het tijds interval (in milliseconden) wordt gedefinieerd tussen berichten die naar de cloud worden verzonden. De tweede is `simulatedData` , een Booleaanse waarde die aangeeft of gesimuleerde sensor gegevens moeten worden gebruikt.

   Als u **de sensor niet hebt**, stelt u de `simulatedData` waarde in op `true` om te zorgen dat de voorbeeld toepassing gesimuleerde sensor gegevens maakt en gebruikt.

   *Opmerking: het I2C-adres dat in deze zelf studie wordt gebruikt, is standaard 0x77. Afhankelijk van uw configuratie kan het ook 0x76 zijn: als er een I2C-fout optreedt, probeert u de waarde te wijzigen in 118 en gaat u na of dit beter werkt. Als u wilt zien welk adres wordt gebruikt door uw sensor, voert u `sudo i2cdetect -y 1` uit in een shell op de Raspberry pi*

2. Sla het bestand op en sluit het af door Control-O te typen > > Control-X in te voeren.

### <a name="run-the-sample-application"></a>De voorbeeldtoepassing uitvoeren

Voer de voorbeeld toepassing uit door de volgende opdracht uit te voeren:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Zorg ervoor dat u het apparaat connection string kopieert naar de enkele aanhalings tekens.

Als het goed is, ziet u de volgende uitvoer met de sensorgegevens en de berichten die naar uw IoT-hub worden verzonden.

![Uitvoer: sensorgegevens die van Raspberry Pi naar uw IoT Hub worden verzonden](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Lees de berichten die door uw hub zijn ontvangen

Een manier om berichten te bewaken die door uw IoT-hub van uw apparaat worden ontvangen, is door de Azure IoT-Hulpprogram Ma's voor Visual Studio code te gebruiken. Zie [Azure IOT-Hulpprogram ma's voor Visual Studio code gebruiken voor het verzenden en ontvangen van berichten tussen uw apparaat en IOT hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)voor meer informatie.

Ga verder met de volgende sectie voor meer manieren om gegevens te verwerken die door uw apparaat worden verzonden.

## <a name="next-steps"></a>Volgende stappen

U hebt een voorbeeld toepassing uitgevoerd voor het verzamelen van sensor gegevens en deze naar uw IoT-hub te verzenden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
