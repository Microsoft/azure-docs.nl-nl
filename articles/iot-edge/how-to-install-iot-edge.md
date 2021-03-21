---
title: Azure IoT Edge installeren | Microsoft Docs
description: Installatie-instructies Azure IoT Edge op Windows-of Linux-apparaten
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: kgremban
ms.openlocfilehash: 6a64bb2801830440dc49e72786c9c00a6e4796b3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201617"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Azure IoT Edge voor Linux installeren of verwijderen

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Met de Azure IoT Edge runtime wordt een apparaat omgezet in een IoT Edge apparaat. De runtime kan worden geïmplementeerd op apparaten als een Raspberry Pi of zo groot als een industriële server. Zodra een apparaat is geconfigureerd met de IoT Edge-runtime, kunt u beginnen met het implementeren van bedrijfslogica vanuit de cloud. Zie [inzicht krijgen in de Azure IOT Edge runtime en de bijbehorende architectuur](iot-edge-runtime.md)voor meer informatie.

Dit artikel bevat de stappen voor het installeren van de Azure IoT Edge runtime op Linux-apparaten.

## <a name="prerequisites"></a>Vereisten

* Een [geregistreerde apparaat-id](how-to-register-device.md)

  Als u uw apparaat bij symmetrische sleutel verificatie hebt geregistreerd, moet het apparaat connection string gereed zijn.

  Als u uw apparaat hebt geregistreerd met X. 509 zelfondertekende certificaat authenticatie, moet u ten minste één van de identiteits certificaten gebruiken die u hebt gebruikt voor het registreren van het apparaat en de bijbehorende persoonlijke sleutel die op uw apparaat beschikbaar is.

* Een Linux-apparaat

  Een x64-, ARM32-of ARM64 Linux-apparaat hebben. Micro soft biedt installatie pakketten voor Ubuntu Server 18,04 en Raspberry Pi OS stretch-besturings systemen.

  Zie [Azure IOT Edge ondersteunde systemen](support.md#operating-systems) voor de meest recente informatie over welke besturings systemen momenteel worden ondersteund voor productie scenario's.

  >[!NOTE]
  >Ondersteuning voor ARM64-apparaten is beschikbaar in de [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Bereid uw apparaat voor op toegang tot de micro soft-installatie pakketten.

  Installeer de configuratie van de opslagplaats die overeenkomt met het besturingssysteem van uw apparaat.

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

## <a name="install-iot-edge"></a>IoT Edge installeren

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

De IoT Edge Security daemon biedt en onderhoudt beveiligings standaarden op het IoT Edge apparaat. De daemon begint op elke keer dat de computer wordt opgestart en Boots trapt het apparaat door de rest van de IoT Edge runtime te starten.

De stappen in deze sectie vertegenwoordigen het gebruikelijke proces voor het installeren van de meest recente versie op een apparaat met Internet verbinding. Als u een specifieke versie moet installeren, zoals een voorlopige versie, of als u de installatie offline wilt installeren, volgt u de [installatie stappen voor offline of specifieke versie](#offline-or-specific-version-installation-optional) verderop in dit artikel.

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

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

De IoT Edge-service biedt en onderhoudt beveiligings standaarden op het IoT Edge apparaat. De service wordt gestart op elke keer dat de computer wordt opgestart en Boots trapt het apparaat door de rest van de IoT Edge runtime te starten.

De IoT Identity-service werd geïntroduceerd samen met versie 1,2 van IoT Edge. Met deze service wordt het inrichten en beheren van identiteiten voor IoT Edge en voor andere onderdelen van apparaten die met IoT Hub moeten communiceren, afgehandeld.

De stappen in deze sectie vertegenwoordigen het gebruikelijke proces voor het installeren van de meest recente versie op een apparaat met Internet verbinding. Als u een specifieke versie moet installeren, zoals een voorlopige versie, of als u de installatie offline wilt installeren, volgt u de [installatie stappen voor offline of specifieke versie](#offline-or-specific-version-installation-optional) verderop in dit artikel.

>[!NOTE]
>De stappen in deze sectie laten zien hoe u IoT Edge versie 1,2 installeert, die momenteel beschikbaar is als open bare preview. Als u op zoek bent naar de stappen voor het installeren van de meest recente, algemeen beschik bare versie van IoT Edge, raadpleegt u de [1,1 (LTS)](?view=iotedge-2018-06&preserve-view=true) -versie van dit artikel.
>
>Als u al een IoT Edge apparaat met een oudere versie hebt en u wilt upgraden naar 1,2, gebruikt u de stappen in [de IOT Edge Security daemon en runtime bijwerken](how-to-update-iot-edge.md). Versie 1,2 is voldoende verschillend van eerdere versies van IoT Edge die nodig zijn om een upgrade uit te voeren.

Pakketlijsten bijwerken op uw apparaat.

   ```bash
   sudo apt-get update
   ```

Controleer welke versies van IoT Edge beschikbaar zijn.

   ```bash
   apt list -a aziot-edge
   ```

Als u de meest recente versie van IoT Edge wilt installeren, gebruikt u de volgende opdracht waarmee ook de nieuwste versie van het identiteits service pakket wordt geïnstalleerd:

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- commenting out for public preview. reintroduce at GA

Or, if you want to install a specific version of IoT Edge and the identity service, specify the versions from the apt list output. Specify the same versions for both services.. For example, the following command installs the most recent version of the 1.2 release:

   ```bash
   sudo apt-get install aziot-edge=1.2* aziot-identity-service=1.2*
   ```

-->

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>Het apparaat inrichten met de Cloud identiteit

Nu de container-engine en de IoT Edge runtime op uw apparaat zijn geïnstalleerd, bent u klaar voor de volgende stap, waarmee u het apparaat kunt instellen met de Cloud identiteit en verificatie gegevens.

Kies het volgende gedeelte op basis van het verificatie type dat u wilt gebruiken:

* [Optie 1: verifiëren met symmetrische sleutels](#option-1-authenticate-with-symmetric-keys)
* [Optie 2: verifiëren met X. 509-certificaten](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Optie 1: verifiëren met symmetrische sleutels

Op dit punt wordt de IoT Edge runtime geïnstalleerd op uw Linux-apparaat en moet u het apparaat inrichten met de Cloud identiteit en verificatie gegevens.

In deze sectie worden de stappen beschreven voor het inrichten van een apparaat met symmetrische sleutel verificatie. U moet uw apparaat hebben geregistreerd bij IoT Hub en de connection string opgehaald van de apparaatgegevens. Als dat niet het geval is, volgt u de stappen in [een IOT edge apparaat registreren in IOT hub](how-to-register-device.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Open het configuratie bestand op het IoT Edge apparaat.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Zoek de inrichtings configuraties van het bestand en verwijder de opmerking bij de **hand matige inrichtings configuratie met behulp van een Connection String** sectie als dit nog niet is gebeurd.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Werk de waarde van **device_connection_string** bij met de verbindingsreeks van uw IoT Edge-apparaat. Zorg ervoor dat alle andere inrichtings secties van commentaar zijn. Zorg ervoor dat het **inrichten:** de regel heeft geen voorafgaande spatie en dat geneste items met twee spaties worden inge sprongen.

Als u de inhoud van het klembord in Nano wilt plakken gebruikt u `Shift+Right Click` of drukt u op `Shift+Insert`.

Sla het bestand op en sluit het.

   `CTRL + X`, `Y`, `Enter`

Nadat u de inrichtingsgegevens in het configuratiebestand hebt ingevoerd, start u de daemon opnieuw op:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Maak het configuratie bestand voor uw apparaat op basis van een sjabloon bestand dat wordt meegeleverd als onderdeel van de IoT Edge installatie.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

Open het configuratie bestand op het IoT Edge apparaat.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Ga naar de **inrichtings** sectie van het bestand en verwijder de hand matige inrichting van Connection String-regels.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string = "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Werk de waarde van **connection_string** met de Connection String van uw IOT edge-apparaat bij.

Als u de inhoud van het klembord in Nano wilt plakken gebruikt u `Shift+Right Click` of drukt u op `Shift+Insert`.

Sla het bestand op en sluit het.

   `CTRL + X`, `Y`, `Enter`

Nadat u de inrichtings gegevens hebt ingevoerd in het configuratie bestand, past u de wijzigingen toe:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>Optie 2: verifiëren met X. 509-certificaten

Op dit punt wordt de IoT Edge runtime geïnstalleerd op uw Linux-apparaat en moet u het apparaat inrichten met de Cloud identiteit en verificatie gegevens.

In deze sectie worden de stappen beschreven voor het inrichten van een apparaat met X. 509-certificaat verificatie. U moet uw apparaat hebben geregistreerd bij IoT Hub, zodat er vinger afdrukken worden gemaakt die overeenkomen met het certificaat en de persoonlijke sleutel op uw IoT Edge apparaat. Als dat niet het geval is, volgt u de stappen in [een IOT edge apparaat registreren in IOT hub](how-to-register-device.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Open het configuratie bestand op het IoT Edge apparaat.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Zoek de sectie inrichtings configuraties van het bestand en verwijder de **hand matige inrichtings configuratie met behulp van een X. 509-identiteits certificaat** sectie. Zorg ervoor dat alle andere inrichtings secties van commentaar zijn. Zorg ervoor dat het **inrichten:** de regel heeft geen voorafgaande spatie en dat geneste items met twee spaties worden inge sprongen.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
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

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Maak het configuratie bestand voor uw apparaat op basis van een sjabloon bestand dat wordt meegeleverd als onderdeel van de IoT Edge installatie.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

Open het configuratie bestand op het IoT Edge apparaat.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Zoek de sectie **inrichtings** van het bestand en verwijder de opmerkingen bij de regels voor hand matig inrichten met het 509-identiteits certificaat. Zorg ervoor dat alle andere inrichtings secties van commentaar zijn.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Werk de volgende velden bij:

* **iothub_hostname**: de hostnaam van de IOT-hub waarmee het apparaat verbinding maakt. Bijvoorbeeld `{IoT hub name}.azure-devices.net`.
* **device_id**: de id die u hebt ingevoerd tijdens het registreren van het apparaat.
* **identity_cert**: de URI naar een identiteits certificaat op het apparaat, bijvoorbeeld: `file:///path/identity_certificate.pem` . Het certificaat kan ook dynamisch worden uitgegeven via EST of een lokale certificerings instantie.
* **identity_pk**: de URI naar het persoonlijke sleutel bestand voor het gegeven identiteits certificaat, bijvoorbeeld: `file:///path/identity_key.pem` . U kunt ook een PKCS # 11-URI opgeven en vervolgens uw configuratie gegevens opgeven in de sectie **PKCS # 11** verderop in het configuratie bestand.

Sla het bestand op en sluit het.

   `CTRL + X`, `Y`, `Enter`

Nadat u de inrichtings gegevens hebt ingevoerd in het configuratie bestand, past u de wijzigingen toe:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>Geslaagde configuratie controleren

Controleer of de runtime goed is geïnstalleerd en geconfigureerd op uw IoT Edge-apparaat.

>[!TIP]
>U hebt verhoogde bevoegdheden nodig om `iotedge`-opdrachten uit te voeren. Nadat u zich de eerste keer na de installatie van de IoT Edge-runtime hebt afgemeld en opnieuw hebt aangemeld, worden uw machtigingen automatisch bijgewerkt. Gebruik tot die tijd `sudo` voorafgaand aan de opdrachten.

Controleer of de IoT Edge-systeem service wordt uitgevoerd.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

::: moniker-end

Als u problemen met de service moet oplossen, haalt u de servicelogboeken op.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

Gebruik het `check` hulp programma om de configuratie en verbindings status van het apparaat te controleren.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>Altijd gebruiken `sudo` om het controle programma uit te voeren, zelfs nadat uw machtigingen zijn bijgewerkt. Het hulp programma heeft verhoogde bevoegdheden nodig om toegang te krijgen tot het configuratie bestand om de configuratie status te controleren.

Bekijk alle modules die op uw IoT Edge-apparaat worden uitgevoerd. Wanneer de service voor de eerste keer wordt gestart, wordt alleen de **edgeAgent** -module weer geven. De edgeAgent-module wordt standaard uitgevoerd en helpt bij het installeren en starten van aanvullende modules die u op uw apparaat implementeert.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>Installatie van offline of specifieke versie (optioneel)

De stappen in deze sectie zijn bedoeld voor scenario's die niet worden gedekt door de standaard installatie stappen. Dit kan het volgende omvatten:

* IoT Edge offline installeren
* Een release Candi date-versie installeren

Volg de stappen in deze sectie als u een specifieke versie van de Azure IoT Edge runtime wilt installeren die niet beschikbaar is via `apt-get install` . De lijst met micro soft-pakketten bevat alleen een beperkt aantal recente versies en de bijbehorende subversies. deze stappen zijn dus voor iedereen die een oudere versie of versie van een release Candi date wil installeren.

Met behulp van krul opdrachten kunt u de onderdeel bestanden rechtstreeks vanuit de IoT Edge GitHub-opslag plaats richten.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. Ga naar de [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases)en zoek de release versie die u wilt richten.

2. Vouw de sectie **assets** uit voor die versie.

3. Elke release moet nieuwe bestanden hebben voor de IoT Edge Security daemon en de hsmlib. Als u IoT Edge wilt installeren op een offline apparaat, moet u deze bestanden vooraf downloaden. Gebruik anders de volgende opdrachten om deze onderdelen bij te werken.

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

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Als op uw apparaat momenteel IoT Edge versie 1,1 of ouder wordt uitgevoerd, verwijdert u de **iotedge** **-en libiothsm-standaard** pakketten voordat u de stappen in deze sectie volgt. Zie [Update van 1,0 of 1,1 naar 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)voor meer informatie.

1. Ga naar de [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases)en zoek de release versie die u wilt richten.

2. Vouw de sectie **assets** uit voor die versie.

3. Elke release moet nieuwe bestanden hebben voor IoT Edge en de identiteits service. Als u IoT Edge wilt installeren op een offline apparaat, moet u deze bestanden vooraf downloaden. Gebruik anders de volgende opdrachten om deze onderdelen bij te werken.

   1. Zoek het **aziot-identiteits-service-** bestand dat overeenkomt met de architectuur van uw IOT edge-apparaat. Klik met de rechter muisknop op de bestands koppeling en kopieer het koppelings adres.

   2. Gebruik de gekopieerde koppeling in de volgende opdracht om die versie van de identiteits service te installeren:

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo dpkg -i ./aziot-identity-service.deb
      ```

   3. Zoek het **aziot-Edge-** bestand dat overeenkomt met de architectuur van uw IOT edge-apparaat. Klik met de rechter muisknop op de bestands koppeling en kopieer het koppelings adres.

   4. Gebruik de gekopieerde koppeling in de volgende opdracht om die versie van IoT Edge te installeren.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo dpkg -i ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

Nu de container engine en de IoT Edge runtime op uw apparaat zijn geïnstalleerd, bent u klaar voor de volgende stap. Dit is om [het apparaat in te richten met de Cloud identiteit](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>IoT Edge verwijderen

Als u de IoT Edge-installatie wilt verwijderen van uw apparaat, gebruikt u de volgende opdrachten.

De IoT Edge-runtime verwijderen.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

Gebruik de `--purge` markering als u alle bestanden wilt verwijderen die zijn gekoppeld aan IOT Edge, inclusief uw configuratie bestanden. Als u deze optie uitschakelt, moet u IoT Edge opnieuw installeren en dezelfde configuratie-informatie in de toekomst gebruiken.

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
