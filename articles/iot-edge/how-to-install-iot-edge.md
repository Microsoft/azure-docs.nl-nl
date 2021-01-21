---
title: Azure IoT Edge installeren | Microsoft Docs
description: Installatie-instructies Azure IoT Edge op Windows-of Linux-apparaten
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
ms.openlocfilehash: ab783d6cb20f1c2fe31e8556dc57999df20d5637
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629807"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Azure IoT Edge voor Linux installeren of verwijderen

Met de Azure IoT Edge runtime wordt een apparaat omgezet in een IoT Edge apparaat. De runtime kan worden geïmplementeerd op apparaten als een Raspberry Pi of zo groot als een industriële server. Zodra een apparaat is geconfigureerd met de IoT Edge-runtime, kunt u beginnen met het implementeren van bedrijfslogica vanuit de cloud. Zie [inzicht krijgen in de Azure IOT Edge runtime en de bijbehorende architectuur](iot-edge-runtime.md)voor meer informatie.

Dit artikel bevat de stappen voor het installeren van de Azure IoT Edge runtime op Linux-apparaten.

## <a name="prerequisites"></a>Vereisten

* Een [geregistreerde apparaat-id](how-to-register-device.md)

  Als u uw apparaat bij symmetrische sleutel verificatie hebt geregistreerd, moet het apparaat connection string gereed zijn.

  Als u uw apparaat hebt geregistreerd met X. 509 zelfondertekende certificaat authenticatie, moet u ten minste één van de identiteits certificaten gebruiken die u hebt gebruikt voor het registreren van het apparaat en de bijbehorende persoonlijke sleutel die op uw apparaat beschikbaar is.

* Een Linux-apparaat

  Een x64-, ARM32-of ARM64 Linux-apparaat hebben. Micro soft biedt installatie pakketten voor Ubuntu Server 16,04, Ubuntu Server 18,04 en Raspberry Pi OS stretch-besturings systemen.

  Zie [Azure IOT Edge ondersteunde systemen](support.md#operating-systems) voor de meest recente informatie over welke besturings systemen momenteel worden ondersteund voor productie scenario's.

  >[!NOTE]
  >Ondersteuning voor ARM64-apparaten is beschikbaar in de [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Bereid uw apparaat voor op toegang tot de micro soft-installatie pakketten.

  Installeer de configuratie van de opslagplaats die overeenkomt met het besturingssysteem van uw apparaat.

  * **Ubuntu Server 16.04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Ubuntu Server 18.04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Raspberry Pi OS Stretch**:

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  Kopieer de gegenereerde lijst naar de map sources.list.d.

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Installeer de openbare Microsoft GPG-sleutel.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Azure IoT Edge software pakketten zijn onderhevig aan de licentie voorwaarden in elk pakket ( `usr/share/doc/{package-name}` of de `LICENSE` map). Lees de licentie voorwaarden voordat u een pakket gebruikt. Uw installatie en het gebruik van een pakket zijn uw acceptatie van deze voor waarden. Als u niet akkoord gaat met de licentie voorwaarden, mag u dat pakket niet gebruiken.

## <a name="install-a-container-engine"></a>Een container Engine installeren

Azure IoT Edge is afhankelijk van een met OCI compatibele container runtime. Voor productie scenario's wordt u aangeraden de Moby-engine te gebruiken. De Moby-engine is de enige container engine die officieel wordt ondersteund met Azure IoT Edge. Docker CE/EE container installatie kopieën zijn compatibel met de Moby-runtime.

Pakketlijsten bijwerken op uw apparaat.

   ```bash
   sudo apt-get update
   ```

Installeer de Moby-engine.

   ```bash
   sudo apt-get install moby-engine
   ```

Als er fouten optreden bij het installeren van de Moby-container engine, controleert u uw Linux-kernel op Moby-compatibiliteit. Sommige fabrikanten van Inge sloten apparaten verzenden apparaat-installatie kopieën die aangepaste Linux-kernels bevatten zonder de functies die vereist zijn voor de compatibiliteit van de container-engine. Voer de volgende opdracht uit, waarbij gebruik wordt gemaakt van het [controle-configuratie script](https://github.com/moby/moby/blob/master/contrib/check-config.sh) dat wordt meegeleverd door Moby, om de configuratie van de kernel te controleren:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Controleer in de uitvoer van het script of alle items onder `Generally Necessary` en `Network Drivers` zijn ingeschakeld. Als u onderdelen mist, schakelt u deze in door de kernel opnieuw te bouwen op basis van de bron en de bijbehorende modules te selecteren voor opname in de juiste kernel. config. En als u een configuratie Generator van een kernel gebruikt `defconfig` , zoals of, kunt u `menuconfig` de betreffende functies zoeken en inschakelen en de kernel opnieuw samen stellen. Nadat u de zojuist gewijzigde kernel hebt geïmplementeerd, voert u het script check-config opnieuw uit om te controleren of alle vereiste onderdelen zijn ingeschakeld.

## <a name="install-the-iot-edge-security-daemon"></a>Installeer de IoT Edge Security daemon

De IoT Edge Security daemon biedt en onderhoudt beveiligings standaarden op het IoT Edge apparaat. De daemon begint op elke keer dat de computer wordt opgestart en Boots trapt het apparaat door de rest van de IoT Edge runtime te starten.

De stappen in deze sectie vertegenwoordigen het gebruikelijke proces voor het installeren van de meest recente versie op een apparaat met Internet verbinding. Als u een specifieke versie moet installeren, zoals een voorlopige versie, of als u offline moet installeren, volgt u de installatie stappen voor [offline of specifieke versie](#offline-or-specific-version-installation-optional) in de volgende sectie.

Pakketlijsten bijwerken op uw apparaat.

   ```bash
   sudo apt-get update
   ```

Controleer welke versies van IoT Edge beschikbaar zijn.

   ```bash
   apt list -a iotedge
   ```

Als u de meest recente versie van de beveiligings-daemon wilt installeren, gebruikt u de volgende opdracht waarmee ook de meest recente versie van het **libiothsm-STD-** pakket wordt geïnstalleerd:

   ```bash
   sudo apt-get install iotedge
   ```

Als u een specifieke versie van de beveiligings-daemon wilt installeren, geeft u de versie op uit de apt-lijst uitvoer. U kunt ook dezelfde versie opgeven voor het **libiothsm-STD-** pakket, waarbij anders de meest recente versie wordt geïnstalleerd. Met de volgende opdracht wordt bijvoorbeeld de meest recente versie van de 1.0.10-release geïnstalleerd:

   ```bash
   sudo apt-get install iotedge=1.0.10* libiothsm-std=1.0.10*
   ```

Als de versie die u wilt installeren niet wordt weer gegeven, volgt u de installatie stappen voor [offline of specifieke versie](#offline-or-specific-version-installation-optional) verderop in dit artikel. In deze sectie wordt beschreven hoe u een eerdere versie van de IoT Edge Security daemon of versie van de Candi date kunt richten.

## <a name="provision-the-device-with-its-cloud-identity"></a>Het apparaat inrichten met de Cloud identiteit

Nu de container-engine en de IoT Edge runtime op uw apparaat zijn geïnstalleerd, bent u klaar voor de volgende stap, waarmee u het apparaat kunt instellen met de Cloud identiteit en verificatie gegevens.

Kies het volgende gedeelte op basis van het verificatie type dat u wilt gebruiken:

* [Optie 1: verifiëren met symmetrische sleutels](#option-1-authenticate-with-symmetric-keys)
* [Optie 2: verifiëren met X. 509-certificaten](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Optie 1: verifiëren met symmetrische sleutels

Op dit punt wordt de IoT Edge runtime geïnstalleerd op uw Linux-apparaat en moet u het apparaat inrichten met de Cloud identiteit en verificatie gegevens.

In deze sectie worden de stappen beschreven voor het inrichten van een apparaat met symmetrische sleutel verificatie. U moet uw apparaat hebben geregistreerd bij IoT Hub en de connection string opgehaald van de apparaatgegevens. Als dat niet het geval is, volgt u de stappen in [een IOT edge apparaat registreren in IOT hub](how-to-register-device.md).

Open het configuratie bestand op het IoT Edge apparaat.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Zoek de inrichtingsconfiguraties van het bestand en verwijder het commentaar in de sectie **Handmatige configuratie met behulp van een verbindingsreeks**.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

Werk de waarde van **device_connection_string** bij met de verbindingsreeks van uw IoT Edge-apparaat. Zorg ervoor dat alle andere inrichtings secties van commentaar zijn. Zorg ervoor dat het **inrichten:** de regel heeft geen voorafgaande spatie en dat geneste items met twee spaties worden inge sprongen.

Als u de inhoud van het klembord in Nano wilt plakken gebruikt u `Shift+Right Click` of drukt u op `Shift+Insert`.

Sla het bestand op en sluit het.

   `CTRL + X`, `Y`, `Enter`

Nadat u de inrichtingsgegevens in het configuratiebestand hebt ingevoerd, start u de daemon opnieuw op:

   ```bash
   sudo systemctl restart iotedge
   ```

### <a name="option-2-authenticate-with-x509-certificates"></a>Optie 2: verifiëren met X. 509-certificaten

Op dit punt wordt de IoT Edge runtime geïnstalleerd op uw Linux-apparaat en moet u het apparaat inrichten met de Cloud identiteit en verificatie gegevens.

In deze sectie worden de stappen beschreven voor het inrichten van een apparaat met X. 509-certificaat verificatie. U moet uw apparaat hebben geregistreerd bij IoT Hub, zodat er vinger afdrukken worden gemaakt die overeenkomen met het certificaat en de persoonlijke sleutel op uw IoT Edge apparaat. Als dat niet het geval is, volgt u de stappen in [een IOT edge apparaat registreren in IOT hub](how-to-register-device.md).

Open het configuratie bestand op het IoT Edge apparaat.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Zoek de sectie inrichtings configuraties van het bestand en verwijder de **hand matige inrichtings configuratie met behulp van een X. 509-identiteits certificaat** sectie. Zorg ervoor dat alle andere inrichtings secties van commentaar zijn. Zorg ervoor dat het **inrichten:** de regel heeft geen voorafgaande spatie en dat geneste items met twee spaties worden inge sprongen.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

Werk de volgende velden bij:

* **iothub_hostname**: de hostnaam van de IOT-hub waarmee het apparaat verbinding maakt. Bijvoorbeeld `{IoT hub name}.azure-devices.net`.
* **device_id**: de id die u hebt ingevoerd tijdens het registreren van het apparaat.
* **identity_cert**: de URI naar een identiteits certificaat op het apparaat. Bijvoorbeeld `file:///path/identity_certificate.pem`.
* **identity_pk**: de URI naar het persoonlijke sleutel bestand voor het gegeven identiteits certificaat. Bijvoorbeeld `file:///path/identity_key.pem`.

Sla het bestand op en sluit het.

   `CTRL + X`, `Y`, `Enter`

Nadat u de inrichtingsgegevens in het configuratiebestand hebt ingevoerd, start u de daemon opnieuw op:

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="verify-successful-configuration"></a>Geslaagde configuratie controleren

Controleer of de runtime goed is geïnstalleerd en geconfigureerd op uw IoT Edge-apparaat.

1. Controleer of de IoT Edge-beveiligingsdaemon wordt uitgevoerd als een systeemservice.

   ```bash
   sudo systemctl status iotedge
   ```

   >[!TIP]
   >U hebt verhoogde bevoegdheden nodig om `iotedge`-opdrachten uit te voeren. Nadat u zich de eerste keer na de installatie van de IoT Edge-runtime hebt afgemeld en opnieuw hebt aangemeld, worden uw machtigingen automatisch bijgewerkt. Gebruik tot die tijd `sudo` voorafgaand aan de opdrachten.

2. Als u problemen met de service moet oplossen, haalt u de servicelogboeken op.

   ```bash
   journalctl -u iotedge
   ```

3. Gebruik het `check` hulp programma om de configuratie en verbindings status van het apparaat te controleren.

   ```bash
   sudo iotedge check
   ```

   >[!TIP]
   >Altijd gebruiken `sudo` om het controle programma uit te voeren, zelfs nadat uw machtigingen zijn bijgewerkt. Het hulp programma heeft verhoogde bevoegdheden nodig om toegang te krijgen tot het bestand **config. yaml** om de configuratie status te controleren.

4. Bekijk alle modules die op uw IoT Edge-apparaat worden uitgevoerd. Wanneer de service voor de eerste keer wordt gestart, wordt alleen de **edgeAgent** -module weer geven. De edgeAgent-module wordt standaard uitgevoerd en helpt bij het installeren en starten van aanvullende modules die u op uw apparaat implementeert.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>Installatie van offline of specifieke versie (optioneel)

De stappen in deze sectie zijn bedoeld voor scenario's die niet worden gedekt door de standaard installatie stappen. Dit kan het volgende omvatten:

* IoT Edge offline installeren
* Een release Candi date-versie installeren

Volg de stappen in deze sectie als u een specifieke versie van de Azure IoT Edge runtime wilt installeren die niet beschikbaar is via `apt-get install` . De lijst met micro soft-pakketten bevat alleen een beperkt aantal recente versies en de bijbehorende subversies. deze stappen zijn dus voor iedereen die een oudere versie of versie van een release Candi date wil installeren.

Met behulp van krul opdrachten kunt u de onderdeel bestanden rechtstreeks vanuit de IoT Edge GitHub-opslag plaats richten.

1. Ga naar de [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases)en zoek de release versie die u wilt richten.

2. Vouw de sectie **assets** uit voor die versie.

3. Elke release moet nieuwe bestanden hebben voor de IoT Edge Security daemon en de hsmlib. Gebruik de volgende opdrachten om deze onderdelen bij te werken.

   1. Zoek het bestand **libiothsm-STD** dat overeenkomt met de architectuur van uw IOT edge-apparaat. Klik met de rechter muisknop op de bestands koppeling en kopieer het koppelings adres.

   2. Gebruik de gekopieerde koppeling in de volgende opdracht om die versie van de hsmlib te installeren:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Zoek het **iotedge** -bestand dat overeenkomt met de architectuur van uw IOT edge-apparaat. Klik met de rechter muisknop op de bestands koppeling en kopieer het koppelings adres.

   4. Gebruik de gekopieerde koppeling in de volgende opdracht om die versie van de IoT Edge Security daemon te installeren.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Nu de container engine en de IoT Edge runtime op uw apparaat zijn geïnstalleerd, bent u klaar voor de volgende stap. Dit is om [het apparaat in te richten met de Cloud identiteit](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>IoT Edge verwijderen

Als u de IoT Edge-installatie wilt verwijderen van uw apparaat, gebruikt u de volgende opdrachten.

De IoT Edge-runtime verwijderen.

```bash
sudo apt-get remove --purge iotedge
```

Wanneer de IoT Edge runtime wordt verwijderd, worden alle containers die zijn gemaakt, gestopt, maar nog steeds aanwezig op het apparaat. Bekijk alle containers om te zien welke er aanwezig zijn.

```bash
sudo docker ps -a
```

Verwijder de containers van het apparaat, met inbegrip van de twee runtime-containers.

```bash
sudo docker rm -f <container name>
```

Ten slotte verwijdert u de container runtime van het apparaat.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Volgende stappen

Ga door met het [implementeren van IOT Edge-modules](how-to-deploy-modules-portal.md) voor meer informatie over het implementeren van modules op uw apparaat.
