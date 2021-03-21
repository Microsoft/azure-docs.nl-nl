---
title: Test certificaten maken-Azure IoT Edge | Microsoft Docs
description: Maak test certificaten en leer hoe u deze kunt installeren op een Azure IoT Edge apparaat om de productie-implementatie voor te bereiden.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d8cf3dbe9d1dc2ad329a0b5ab8fa9554c85ae55c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199076"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Democertificaten maken om IoT Edge-apparaatfuncties te testen

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Voor IoT Edge-apparaten zijn certificaten vereist voor beveiligde communicatie tussen de runtime, de modules en downstream-apparaten.
Als u geen certificerings instantie hebt voor het maken van de vereiste certificaten, kunt u demonstratie certificaten gebruiken om IoT Edge functies in uw test omgeving uit te proberen.
In dit artikel wordt de functionaliteit van de scripts voor het genereren van certificaten beschreven die IoT Edge biedt voor het testen.

Deze certificaten verlopen binnen 30 dagen en mogen niet worden gebruikt in een productie scenario.

U kunt certificaten op elke machine maken en deze vervolgens naar uw IoT Edge apparaat kopiëren.
Het is eenvoudiger om uw primaire machine te gebruiken om de certificaten te maken in plaats van deze op uw IoT Edge apparaat zelf te genereren.
Door uw primaire machine te gebruiken, kunt u de scripts eenmaal instellen en ze vervolgens gebruiken om certificaten voor meerdere apparaten te maken.

Volg deze stappen om demo certificaten te maken voor het testen van uw IoT Edge scenario:

1. [Stel scripts](#set-up-scripts) in voor het genereren van certificaten op het apparaat.
2. [Maak het basis-CA-certificaat](#create-root-ca-certificate) dat u gebruikt om alle andere certificaten voor uw scenario te ondertekenen.
3. Genereer de certificaten die u nodig hebt voor het scenario dat u wilt testen:
   * [IOT Edge certificaten voor apparaat-Id's maken](#create-iot-edge-device-identity-certificates) voor het inrichten van apparaten met X. 509-certificaat verificatie, hetzij hand matig of met de IOT hub Device Provisioning Service.
   * [Maak IOT Edge CA-certificaten](#create-iot-edge-device-ca-certificates) voor IOT edge apparaten in Gateway scenario's.
   * [Stroomafwaartse certificaten maken](#create-downstream-device-certificates) voor het verifiëren van downstream-apparaten in een gateway scenario.

## <a name="prerequisites"></a>Vereisten

Een ontwikkelings machine waarop Git is geïnstalleerd.

## <a name="set-up-scripts"></a>Scripts instellen

De IoT Edge opslagplaats op GitHub bevat scripts voor het genereren van certificaten die u kunt gebruiken om demo certificaten te maken.
In deze sectie vindt u instructies voor het voorbereiden van het uitvoeren van scripts voor uitvoering op uw computer, hetzij op Windows of Linux.
Als u zich op een Linux-computer bevindt, gaat u verder met het [instellen van Linux](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Ingesteld op Windows

Als u demo certificaten wilt maken op een Windows-apparaat, moet u OpenSSL installeren en vervolgens de generatie scripts klonen en zo instellen dat deze lokaal worden uitgevoerd in Power shell.

#### <a name="install-openssl"></a>OpenSSL installeren

Installeer OpenSSL voor Windows op de computer die u gebruikt om de certificaten te genereren.
Als OpenSSL al op uw Windows-apparaat is geïnstalleerd, zorg er dan voor dat openssl.exe beschikbaar is in uw omgevings variabele PATH.

Er zijn verschillende manieren om OpenSSL te installeren, met inbegrip van de volgende opties:

* **Eenvoudiger:** Down load en installeer eventuele [binaire bestanden van openssl](https://wiki.openssl.org/index.php/Binaries)van derden, bijvoorbeeld van [openssl op sourceforge](https://sourceforge.net/projects/openssl/). Voeg het volledige pad naar openssl.exe toe aan de omgevings variabele PATH.

* **Aanbevolen:** Down load de OpenSSL-bron code en bouw de binaire bestanden op uw machine door uzelf of via [vcpkg](https://github.com/Microsoft/vcpkg). De onderstaande instructies gebruiken vcpkg voor het downloaden van de bron code, het compileren en installeren van OpenSSL op uw Windows-computer, met eenvoudige stappen.

   1. Ga naar een map waarin u vcpkg wilt installeren. Volg de instructies om [vcpkg](https://github.com/Microsoft/vcpkg)te downloaden en te installeren.

   2. Zodra vcpkg is geïnstalleerd, voert u de volgende opdracht uit vanuit een Power shell-prompt om het OpenSSL-pakket voor Windows x64 te installeren. De installatie duurt doorgaans ongeveer 5 minuten.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Voeg toe `<vcpkg path>\installed\x64-windows\tools\openssl` aan de omgevings variabele PATH zodat het openssl.exe-bestand beschikbaar is voor aanroep.

#### <a name="prepare-scripts-in-powershell"></a>Scripts voorbereiden in Power shell

De Azure IoT Edge Git-opslag plaats bevat scripts die u kunt gebruiken voor het genereren van test certificaten.
In deze sectie kloont u de IoT Edge opslag plaats en voert u de scripts uit.

1. Open een Power shell-venster in de beheerders modus.

2. Kloon de IoT Edge git-opslagplaats, die scripts bevat om democertificaten te genereren. Gebruik de `git clone` opdracht of [down load de zip](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navigeer naar de map waarin u wilt werken. In dit artikel noemen we deze directory *\<WRKDIR>* . Alle certificaten en sleutels worden gemaakt in deze werkmap.

4. Kopieer de configuratie en script bestanden van de gekloonde opslag plaats naar uw werkmap.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Als u de opslag plaats hebt gedownload als een ZIP-bestand, is de naam van de map `iotedge-master` en is de rest van het pad hetzelfde.

5. Schakel Power shell in om de scripts uit te voeren.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Breng de functies die worden gebruikt door de scripts in de globale naam ruimte van Power shell.

   ```powershell
   . .\ca-certs.ps1
   ```

   In het Power shell-venster wordt een waarschuwing weer gegeven dat de certificaten die zijn gegenereerd door dit script alleen voor test doeleinden zijn en niet mogen worden gebruikt in productie scenario's.

7. Controleer of OpenSSL correct is geïnstalleerd en controleer of er geen naam conflicten met bestaande certificaten zijn. Als er problemen zijn, moet u in de script uitvoer beschrijven hoe u deze op uw systeem kunt oplossen.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Instellen op Linux

Als u demo certificaten wilt maken op een Windows-apparaat, moet u de generatie scripts klonen en instellen om lokaal uit te voeren in bash.

1. Kloon de IoT Edge git-opslagplaats, die scripts bevat om democertificaten te genereren.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navigeer naar de map waarin u wilt werken. In het hele artikel wordt naar deze map verwezen *\<WRKDIR>* . Alle certificaat- en sleutelbestanden worden in deze map gemaakt.
  
3. Kopieer de config- en scriptbestanden van de gekloonde IoT Edge-opslagplaats in uw werkmap.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>Basis-CA-certificaat maken

Het basis-CA-certificaat wordt gebruikt om alle andere demo certificaten te maken voor het testen van een IoT Edge scenario.
U kunt hetzelfde basis-CA-certificaat blijven gebruiken om demo certificaten te maken voor meerdere IoT Edge of downstream-apparaten.

Als u al een basis-CA-certificaat in de werkmap hebt, maakt u er geen nieuwe.
Het nieuwe basis-CA-certificaat overschrijft het oude en de downstream-certificaten die van de oude worden uitgevoerd, werken niet meer.
Als u meerdere basis-CA-certificaten wilt, dient u deze te beheren in afzonderlijke mappen.

Voordat u verdergaat met de stappen in deze sectie, volgt u de stappen in de sectie [scripts instellen](#set-up-scripts) om een werkmap voor te bereiden met de scripts voor het genereren van het certificaat van de demo.

### <a name="windows"></a>Windows

1. Ga naar de werkmap waar u de scripts voor het genereren van certificaten hebt geplaatst.

1. Maak het basis-CA-certificaat en laat het één tussenliggend certificaat ondertekenen. De certificaten worden allemaal in uw werkmap geplaatst.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Met deze script opdracht worden verschillende certificaat-en sleutel bestanden gemaakt, maar wanneer artikelen worden gevraagd voor het **basis-CA-certificaat**, gebruikt u het volgende bestand:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Ga naar de werkmap waar u de scripts voor het genereren van certificaten hebt geplaatst.

1. Maak het basis-CA-certificaat en één tussenliggend certificaat.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Met deze script opdracht worden verschillende certificaat-en sleutel bestanden gemaakt, maar wanneer artikelen worden gevraagd voor het **basis-CA-certificaat**, gebruikt u het volgende bestand:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-identity-certificates"></a>Certificaten voor de identiteit van IoT Edge-apparaten maken

Certificaten voor apparaat-id's worden gebruikt om IoT Edge-apparaten in te richten als u ervoor kiest om X. 509-certificaat verificatie te gebruiken. Deze certificaten werken of u hand matige inrichting of automatische inrichting via Azure IoT Hub Device Provisioning Service (DPS) gebruikt.

Certificaten voor identiteiten van apparaten gaan in de **inrichtings** sectie van het configuratie bestand op het IOT edge-apparaat.

Volg de stappen in de secties [scripts instellen](#set-up-scripts) en [basis-CA-certificaat maken](#create-root-ca-certificate) voordat u doorgaat met de stappen in deze sectie.

### <a name="windows"></a>Windows

Maak het certificaat voor de IoT Edge apparaat-id en de persoonlijke sleutel met de volgende opdracht:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

De naam die u aan deze opdracht geeft, is de apparaat-ID voor het IoT Edge apparaat in IoT Hub.

De nieuwe apparaat-id-opdracht maakt verschillende certificaat-en sleutel bestanden, waaronder drie die u gebruikt bij het maken van een afzonderlijke inschrijving in DPS en het installeren van de IoT Edge runtime:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Maak het certificaat voor de IoT Edge apparaat-id en de persoonlijke sleutel met de volgende opdracht:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

De naam die u aan deze opdracht geeft, is de apparaat-ID voor het IoT Edge apparaat in IoT Hub.

Het script maakt verschillende certificaat-en sleutel bestanden, waaronder drie die u gebruikt bij het maken van een afzonderlijke inschrijving in DPS en het installeren van de IoT Edge runtime:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-iot-edge-device-ca-certificates"></a>CA-certificaten voor IoT Edge-apparaten maken

Elk IoT Edge apparaat dat naar productie gaat, moet een CA-certificaat van het apparaat bevatten waarnaar wordt verwezen vanuit het configuratie bestand.
Het CA-certificaat van het apparaat is verantwoordelijk voor het maken van certificaten voor modules die op het apparaat worden uitgevoerd.
Het is ook nodig voor gateway-scenario's, omdat het CA-certificaat van het apparaat de identiteit van de IoT Edge apparaat controleert op downstream-apparaten.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
CA-certificaten van apparaten gaan in het gedeelte **certificaat** van het bestand config. yaml op het IOT edge-apparaat.
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
CA-certificaten voor apparaten worden in de sectie met de **rand-ca** van het bestand config. toml op het apparaat van IOT Edge.
:::moniker-end

Volg de stappen in de secties [scripts instellen](#set-up-scripts) en [basis-CA-certificaat maken](#create-root-ca-certificate) voordat u doorgaat met de stappen in deze sectie.

### <a name="windows"></a>Windows

1. Navigeer naar de werkmap met de scripts voor het genereren van certificaten en het basis-CA-certificaat.

2. Maak het CA-certificaat en de persoonlijke sleutel van het IoT Edge met de volgende opdracht. Geef een naam op voor het CA-certificaat.

   ```powershell
   New-CACertsEdgeDevice "<CA cert name>"
   ```

   Met deze opdracht worden verschillende certificaat-en sleutel bestanden gemaakt. Het volgende certificaat en sleutel paar moeten naar een IoT Edge apparaat worden gekopieerd en in het configuratie bestand worden verwezen:

   * `<WRKDIR>\certs\iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<CA cert name>.key.pem`

De naam die is door gegeven aan de opdracht **New-CACertsEdgeDevice** , mag niet hetzelfde zijn als de para meter hostname in het configuratie bestand of de id van het apparaat in IOT hub.

### <a name="linux"></a>Linux

1. Navigeer naar de werkmap met de scripts voor het genereren van certificaten en het basis-CA-certificaat.

2. Maak het CA-certificaat en de persoonlijke sleutel van het IoT Edge met de volgende opdracht. Geef een naam op voor het CA-certificaat.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "<CA cert name>"
   ```

   Met deze script opdracht maakt u een aantal certificaat-en sleutel bestanden. Het volgende certificaat en sleutel paar moeten naar een IoT Edge apparaat worden gekopieerd en in het configuratie bestand worden verwezen:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

De naam die is door gegeven aan de **create_edge_device_ca_certificate** opdracht mag niet hetzelfde zijn als de para meter hostname in het configuratie bestand of de id van het apparaat in IOT hub.

## <a name="create-downstream-device-certificates"></a>Stroomafwaartse certificaten van het apparaat maken

Als u een downstream IoT-apparaat instelt voor een gateway scenario en u X. 509-verificatie wilt gebruiken, kunt u demo certificaten voor het downstream-apparaat genereren.
Als u symmetrische sleutel verificatie wilt gebruiken, hoeft u geen extra certificaten voor het downstream-apparaat te maken.
Er zijn twee manieren om een IoT-apparaat te verifiëren met behulp van X. 509-certificaten: met een zelfondertekend certificaat of door CERTIFICERINGs instanties ondertekende certificaten te gebruiken.
Voor X. 509 zelfondertekende verificatie, ook wel vingerafdruk verificatie genoemd, moet u nieuwe certificaten maken om op uw IoT-apparaat te plaatsen.
Deze certificaten bevatten een vinger afdruk die u met IoT Hub voor verificatie kunt delen.
Voor X. 509 certificerings instantie (CA) ondertekende verificatie hebt u een basis-CA-certificaat geregistreerd in IoT Hub dat u gebruikt om certificaten voor uw IoT-apparaat te ondertekenen.
Elk apparaat dat gebruikmaakt van een certificaat dat is uitgegeven door het basis-CA-certificaat of een van de tussenliggende certificaten, mag worden geverifieerd.

De scripts voor het genereren van certificaten kunnen u helpen bij het maken van demo certificaten om een van deze verificatie scenario's te testen.

Volg de stappen in de secties [scripts instellen](#set-up-scripts) en [basis-CA-certificaat maken](#create-root-ca-certificate) voordat u doorgaat met de stappen in deze sectie.

### <a name="self-signed-certificates"></a>Zelfondertekende certificaten

Wanneer u een IoT-apparaat verifieert met zelfondertekende certificaten, moet u apparaat certificaten maken op basis van het basis-CA-certificaat voor uw oplossing.
Vervolgens haalt u een ' vinger afdruk ' van de certificaten op om IoT Hub aan te bieden.
Uw IoT-apparaat moet ook een kopie van de apparaat certificaten hebben, zodat het kan worden geverifieerd met IoT Hub.

#### <a name="windows"></a>Windows

1. Navigeer naar de werkmap met de scripts voor het genereren van certificaten en het basis-CA-certificaat.

2. Maak twee certificaten (primair en secundair) voor het downstream-apparaat. Een eenvoudige naam Conventie voor het gebruik van is het maken van de certificaten met de naam van het IoT-apparaat en vervolgens het primaire of secundaire label. Bijvoorbeeld:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Met deze script opdracht maakt u een aantal certificaat-en sleutel bestanden. De volgende certificaat-en sleutel paren moeten worden gekopieerd naar het downstream IoT-apparaat en worden verwezen in de toepassingen die verbinding maken met IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. De SHA1-vinger afdruk (een vinger afdruk in IoT Hub contexten) ophalen van elk certificaat. De vinger afdruk is een teken reeks van 40 hexadecimale tekens. Gebruik de volgende openssl-opdracht om het certificaat weer te geven en de vinger afdruk te zoeken:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint
   ```

   Voer deze opdracht twee keer uit en eenmaal voor het primaire certificaat en eenmaal voor het secundaire certificaat. U geeft de vinger afdrukken voor beide certificaten op wanneer u een nieuw IoT-apparaat registreert met zelfondertekende X. 509-certificaten.

#### <a name="linux"></a>Linux

1. Navigeer naar de werkmap met de scripts voor het genereren van certificaten en het basis-CA-certificaat.

2. Maak twee certificaten (primair en secundair) voor het downstream-apparaat. Een eenvoudige naam Conventie voor het gebruik van is het maken van de certificaten met de naam van het IoT-apparaat en vervolgens het primaire of secundaire label. Bijvoorbeeld:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Met deze script opdracht maakt u een aantal certificaat-en sleutel bestanden. De volgende certificaat-en sleutel paren moeten worden gekopieerd naar het downstream IoT-apparaat en worden verwezen in de toepassingen die verbinding maken met IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. De SHA1-vinger afdruk (een vinger afdruk in IoT Hub contexten) ophalen van elk certificaat. De vinger afdruk is een teken reeks van 40 hexadecimale tekens. Gebruik de volgende openssl-opdracht om het certificaat weer te geven en de vinger afdruk te zoeken:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   U geeft de primaire en secundaire vinger afdruk op wanneer u een nieuw IoT-apparaat registreert met zelfondertekende X. 509-certificaten.

### <a name="ca-signed-certificates"></a>CA-ondertekende certificaten

Wanneer u een IoT-apparaat verifieert met zelfondertekende certificaten, moet u het basis-CA-certificaat voor uw oplossing uploaden naar IoT Hub.
Vervolgens voert u een verificatie uit om te bewijzen IoT Hub dat u eigenaar bent van het basis certificaat van de certificerings instantie.
Ten slotte gebruikt u hetzelfde basis-CA-certificaat om apparaat certificaten te maken die op uw IoT-apparaat moeten worden geplaatst, zodat het kan worden geverifieerd met IoT Hub.

De certificaten in deze sectie zijn voor de stappen in het [instellen van X. 509-beveiliging in uw Azure IOT hub](../iot-hub/iot-hub-security-x509-get-started.md).

#### <a name="windows"></a>Windows

1. Upload het basis-CA-certificaat bestand vanuit uw werkmap `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` naar uw IOT-hub.

2. Gebruik de code in de Azure Portal om te controleren of u eigenaar bent van het basis-CA-certificaat.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Maak een certificaat keten voor het downstream-apparaat. Gebruik dezelfde apparaat-ID als het apparaat is geregistreerd bij in IoT Hub.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Met deze script opdracht maakt u een aantal certificaat-en sleutel bestanden. De volgende certificaat-en sleutel paren moeten worden gekopieerd naar het downstream IoT-apparaat en worden verwezen in de toepassingen die verbinding maken met IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Upload het basis-CA-certificaat bestand vanuit uw werkmap `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` naar uw IOT-hub.

2. Gebruik de code in de Azure Portal om te controleren of u eigenaar bent van het basis-CA-certificaat.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Maak een certificaat keten voor het downstream-apparaat. Gebruik dezelfde apparaat-ID als het apparaat is geregistreerd bij in IoT Hub.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Met deze script opdracht maakt u een aantal certificaat-en sleutel bestanden. De volgende certificaat-en sleutel paren moeten worden gekopieerd naar het downstream IoT-apparaat en worden verwezen in de toepassingen die verbinding maken met IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
