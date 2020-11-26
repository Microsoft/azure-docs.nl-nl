---
title: Azure IoT Edge installeren | Microsoft Docs
description: Installatie-instructies Azure IoT Edge op Windows-of Linux-apparaten
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kgremban
ms.openlocfilehash: a7794bcdfa4f82698fdc5875bc94dcf52b70166e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185093"
---
# <a name="install-or-uninstall-the-azure-iot-edge-runtime"></a>De runtime van Azure IoT Edge installeren of verwijderen

Met de Azure IoT Edge runtime wordt een apparaat omgezet in een IoT Edge apparaat. De runtime kan worden geïmplementeerd op apparaten als een Raspberry Pi of zo groot als een industriële server. Zodra een apparaat is geconfigureerd met de IoT Edge-runtime, kunt u beginnen met het implementeren van bedrijfslogica vanuit de cloud. Zie [inzicht krijgen in de Azure IOT Edge runtime en de bijbehorende architectuur](iot-edge-runtime.md)voor meer informatie.

Er zijn twee stappen om een IoT Edge apparaat in te stellen. De eerste stap is het installeren van de runtime en de bijbehorende afhankelijkheden, die in dit artikel worden besproken. De tweede stap is om het apparaat te verbinden met de identiteit in de Cloud en verificatie met IoT Hub in te stellen. Deze stappen vindt u in de volgende artikelen.

Dit artikel bevat de stappen voor het installeren van de Azure IoT Edge runtime op Linux-of Windows-apparaten. Voor Windows-apparaten hebt u een extra keuze voor het gebruik van Linux-containers of Windows-containers. Op dit moment worden Windows-containers in Windows aanbevolen voor productie scenario's. Linux-containers in Windows zijn handig voor het ontwikkelen en testen van scenario's, met name als u op een Windows-PC ontwikkelt om te implementeren op Linux-apparaten.

## <a name="prerequisites"></a>Vereisten

Zie [Azure IOT Edge ondersteunde systemen](support.md#operating-systems) voor de meest recente informatie over welke besturings systemen momenteel worden ondersteund voor productie scenario's.

# <a name="linux"></a>[Linux](#tab/linux)

Een x64-, ARM32-of ARM64 Linux-apparaat hebben. Micro soft biedt installatie pakketten voor Ubuntu Server 16,04, Ubuntu Server 18,04 en Raspbian stretch-besturings systemen.

>[!NOTE]
>Ondersteuning voor ARM64-apparaten is beschikbaar in de [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bereid uw apparaat voor op toegang tot de micro soft-installatie pakketten.

1. Installeer de configuratie van de opslag plaats die overeenkomt met het besturings systeem van uw apparaat.

   * **Ubuntu-Server 16,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Ubuntu-Server 18,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Kopieer de gegenereerde lijst naar de map sources. list. d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Installeer de open bare sleutel van micro soft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

# <a name="windows"></a>[Windows](#tab/windows)

### <a name="windows-version"></a>Windows-versie

Voor IoT Edge met Windows-containers is Windows versie 1809/build 17762 vereist. Dit is de meest recente [build van Windows lange termijn ondersteuning](/windows/release-information/). Voor ontwikkel-en test scenario's geldt dat elke SKU (Pro, Enter prise, Server, enzovoort) die ondersteuning biedt voor de functie containers werkt. Controleer echter de lijst met ondersteunde systemen voordat u naar de productie gaat.

IoT Edge met Linux-containers kan worden uitgevoerd op elke versie van Windows die voldoet aan de [vereisten voor docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

### <a name="container-engine-requirements"></a>Vereisten voor container-engine

Azure IoT Edge is afhankelijk van een met [OCI compatibele](https://www.opencontainers.org/) container engine. Zorg ervoor dat uw apparaat ondersteuning biedt voor containers.

Als u IoT Edge installeert op een virtuele machine, schakelt u geneste virtualisatie in en wijst u ten minste 2 GB geheugen toe. Voor virtuele machines van de 2e generatie is geneste virtualisatie standaard ingeschakeld. Voor VMware is er een wissel knop om de functie op uw virtuele machine in te scha kelen.

Als u IoT Edge installeert op een IoT core-apparaat, gebruikt u de volgende opdracht in een [externe Power shell-sessie](/windows/iot-core/connect-your-device/powershell) om te controleren of Windows-containers op uw apparaat worden ondersteund:

```powershell
Get-Service vmcompute
```

---

Azure IoT Edge software pakketten zijn onderhevig aan de licentie voorwaarden in elk pakket ( `usr/share/doc/{package-name}` of de `LICENSE` map). Lees de licentie voorwaarden voordat u een pakket gebruikt. Uw installatie en het gebruik van een pakket zijn uw acceptatie van deze voor waarden. Als u niet akkoord gaat met de licentie voorwaarden, mag u dat pakket niet gebruiken.

## <a name="install-a-container-engine"></a>Een container Engine installeren

Azure IoT Edge is afhankelijk van een met OCI compatibele container runtime. Voor productie scenario's wordt u aangeraden de Moby-engine te gebruiken. De Moby-engine is de enige container engine die officieel wordt ondersteund met Azure IoT Edge. Docker CE/EE container installatie kopieën zijn compatibel met de Moby-runtime.

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="windows"></a>[Windows](#tab/windows)

Gebruik voor productie scenario's de op Moby gebaseerde engine die is opgenomen in het installatie script. Er zijn geen extra stappen voor het installeren van de engine.

Voor IoT Edge met Linux-containers moet u uw eigen container runtime opgeven. Installeer [docker Desktop](https://docs.docker.com/docker-for-windows/install/) op uw apparaat en configureer dit voor het [gebruik van Linux-containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) voordat u doorgaat.

---

## <a name="install-the-iot-edge-security-daemon"></a>Installeer de IoT Edge Security daemon

De IoT Edge Security daemon biedt en onderhoudt beveiligings standaarden op het IoT Edge apparaat. De daemon begint op elke keer dat de computer wordt opgestart en Boots trapt het apparaat door de rest van de IoT Edge runtime te starten.

De stappen in deze sectie vertegenwoordigen het gebruikelijke proces voor het installeren van de meest recente versie op een apparaat met Internet verbinding. Als u een specifieke versie moet installeren, zoals een voorlopige versie, of als u offline moet installeren, volgt u de installatie stappen voor [offline of specifieke versie](#offline-or-specific-version-installation) in de volgende sectie.

# <a name="linux"></a>[Linux](#tab/linux)

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

Als u een specifieke versie van de beveiligings-daemon wilt installeren, geeft u de versie op uit de uitvoer van de apt-lijst. U kunt ook dezelfde versie opgeven voor het **libiothsm-STD-** pakket, waarbij anders de meest recente versie wordt geïnstalleerd. Met de volgende opdracht wordt bijvoorbeeld de meest recente versie van de 1.0.8-release geïnstalleerd:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Als de versie die u wilt installeren niet wordt weer gegeven, volgt u de installatie stappen voor [offline of specifieke versie](#offline-or-specific-version-installation) in de volgende sectie. In deze sectie wordt beschreven hoe u een eerdere versie van de IoT Edge Security daemon of versie van de Candi date kunt richten.

# <a name="windows"></a>[Windows](#tab/windows)

>[!TIP]
>Voor IoT core-apparaten raden we u aan om de installatie opdrachten uit te voeren met een externe Power shell-sessie. Zie [using Power shell for Windows IOT](/windows/iot-core/connect-your-device/powershell)(Engelstalig) voor meer informatie.

1. Voer PowerShell uit als beheerder.

   Gebruik een AMD64-sessie van Power shell, niet Power shell (x86). Als u niet zeker weet welk sessie type u gebruikt, voert u de volgende opdracht uit:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Voer de opdracht [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) uit, waarmee de volgende taken worden uitgevoerd:

   * Hiermee wordt gecontroleerd of de Windows-computer een ondersteunde versie heeft.
   * Hiermee schakelt u de containers-functie in.
   * Hiermee downloadt u de Moby-engine en de IoT Edge-runtime.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   De `Deploy-IoTEdge` opdracht wordt standaard ingesteld op het gebruik van Windows-containers. Als u Linux-containers wilt gebruiken, voegt u de `ContainerOs` para meter toe:

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. Op dit moment kunnen IoT-kern apparaten automatisch opnieuw worden opgestart. Windows 10-of Windows Server-apparaten vragen u mogelijk om opnieuw op te starten. Als dit het geval is, start u het apparaat nu opnieuw op.

Wanneer u IoT Edge op een apparaat installeert, kunt u extra para meters gebruiken om het proces te wijzigen, met inbegrip van:

* Direct verkeer om een proxy server te passeren
* Wijs het installatie programma naar een lokale map voor offline-installatie.

Zie [Power shell-scripts voor IOT Edge in Windows](reference-windows-scripts.md)voor meer informatie over deze aanvullende para meters.

---

Nu de container-engine en de IoT Edge runtime op uw apparaat zijn geïnstalleerd, bent u klaar voor de volgende stap, waarmee u uw apparaat kunt registreren bij IoT Hub en het apparaat kunt instellen met de Cloud identiteit en verificatie gegevens.

Kies het volgende artikel op basis van het verificatie type dat u wilt gebruiken:

* De verificatie van de [symmetrische sleutel](how-to-manual-provision-symmetric-key.md) is sneller om aan de slag te gaan.
* [X. 509-certificaat verificatie](how-to-manual-provision-x509.md) is veiliger voor productie scenario's.

## <a name="offline-or-specific-version-installation"></a>Installatie van offline of specifieke versie

De stappen in deze sectie zijn bedoeld voor scenario's die niet worden gedekt door de standaard installatie stappen. Dit kan het volgende omvatten:

* IoT Edge offline installeren
* Een release Candi date-versie installeren
* Installeer op Windows een andere versie dan de nieuwste

# <a name="linux"></a>[Linux](#tab/linux)

Volg de stappen in deze sectie als u een specifieke versie van de Azure IoT Edge runtime wilt installeren die niet beschikbaar is via `apt-get install` . De lijst met micro soft-pakketten bevat alleen een beperkt aantal recente versies en de bijbehorende subversies. deze stappen zijn dus voor iedereen die een oudere versie of versie van een release Candi date wil installeren.

Met behulp van krul opdrachten kunt u de onderdeel bestanden rechtstreeks vanuit de IoT Edge GitHub-opslag plaats richten.

<!-- TODO: Offline installation? -->

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

# <a name="windows"></a>[Windows](#tab/windows)

Tijdens de installatie worden drie bestanden gedownload:

* Een Power shell-script, dat de installatie-instructies bevat
* Microsoft Azure IoT Edge cab, dat de IoT Edge Security daemon (iotedged), Moby container engine en Moby CLI bevat
* Installatie programma voor het Visual C++ Redistributable-pakket (VC runtime)

Als uw apparaat offline is tijdens de installatie, of als u een specifieke versie van IoT Edge wilt installeren, kunt u deze bestanden van tevoren downloaden naar het apparaat. Wanneer het tijd is om te installeren, plaatst u het installatie script in de map met de gedownloade bestanden. Het installatie programma controleert eerst de Directory en downloadt alleen de onderdelen die niet worden gevonden. Als alle bestanden offline beschikbaar zijn, kunt u installeren zonder Internet verbinding.

1. Zie [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases)voor de nieuwste IOT Edge installatie bestanden samen met vorige versies.

2. Zoek de versie die u wilt installeren en down load de volgende bestanden van het gedeelte **assets** van de release opmerkingen op uw IOT-apparaat:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab van Releases 1.0.9 of nieuwer, of Microsoft-Azure-IoTEdge.cab van Releases 1.0.8 en ouder.

   Microsoft-Azure-IotEdge-arm32.cab is ook alleen beschikbaar vanaf 1.0.9 voor test doeleinden. IoT Edge wordt momenteel niet ondersteund op Windows ARM32-apparaten.

   Het is belang rijk dat u het Power shell-script uit dezelfde versie gebruikt als het CAB-bestand dat u gebruikt, omdat de functionaliteit wordt gewijzigd zodat de functies in elke release worden ondersteund.

3. Als het CAB-bestand dat u hebt gedownload een architectuur achtervoegsel heeft, wijzigt u de naam van het bestand in alleen **Microsoft-Azure-IoTEdge.cab**.

4. U kunt eventueel ook een installatie programma downloaden voor het herdistribueerbare pakket van Visual C++. Het Power shell-script maakt bijvoorbeeld gebruik van deze versie: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Sla het installatie programma in dezelfde map op uw IoT-apparaat op als de IoT Edge bestanden.

5. Als u wilt installeren met offline onderdelen, [punt bron](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) de lokale kopie van het Power shell-script. 

6. Voer de opdracht [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) uit met de `-OfflineInstallationPath` para meter. Geef het absolute pad naar de bestands directory op. Bijvoorbeeld:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   De implementatie opdracht gebruikt alle onderdelen die zijn gevonden in de lokale bestands directory. Als het CAB-bestand of het Visual C++-installatie programma ontbreekt, wordt geprobeerd deze te downloaden.

---

Nu de container-engine en de IoT Edge runtime op uw apparaat zijn geïnstalleerd, bent u klaar voor de volgende stap, waarmee u [een IOT edge apparaat in IOT hub kunt verifiëren](how-to-manual-provision-symmetric-key.md).

## <a name="uninstall-iot-edge"></a>IoT Edge verwijderen

Als u de IoT Edge-installatie wilt verwijderen van uw apparaat, gebruikt u de volgende opdrachten.

# <a name="linux"></a>[Linux](#tab/linux)

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

# <a name="windows"></a>[Windows](#tab/windows)

Als u de IoT Edge-installatie wilt verwijderen van uw Windows-apparaat, gebruikt u de opdracht [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) van een beheer-Power shell-venster. Met deze opdracht wordt de IoT Edge runtime verwijderd, samen met uw bestaande configuratie en de Moby-Engine gegevens.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

De `Uninstall-IoTEdge` opdracht werkt niet in Windows IOT core. Als u IoT Edge wilt verwijderen, moet u uw Windows IoT Core-installatie kopie opnieuw implementeren.

Voor meer informatie over verwijderings opties gebruikt u de opdracht `Get-Help Uninstall-IoTEdge -full` .

---

## <a name="next-steps"></a>Volgende stappen

Nadat u de IoT Edge runtime hebt geïnstalleerd, moet u uw apparaat configureren om verbinding te maken met IoT Hub. In de volgende artikelen wordt beschreven hoe u een nieuw apparaat in de Cloud registreert en hoe u het apparaat met de identiteits-en verificatie gegevens levert.

Kies het volgende artikel op basis van het type verificatie dat u wilt gebruiken:

* **Symmetrische sleutel**: zowel IOT hub als het IOT edge-apparaat hebben een kopie van een beveiligde sleutel. Wanneer het apparaat verbinding maakt met IoT Hub, controleren ze of de sleutels overeenkomen. Deze verificatie methode is sneller om aan de slag te gaan, maar niet zo veilig.

  [Een Azure IoT Edge apparaat instellen met behulp van symmetrische sleutel verificatie](how-to-manual-provision-symmetric-key.md)

* **X. 509 zelf-ondertekend**: het IOT edge apparaat heeft x. 509-identiteits certificaten en IOT hub krijgt de vinger afdruk van de certificaten. Wanneer het apparaat verbinding maakt met IoT Hub, vergelijken ze het certificaat met de vinger afdruk. Deze verificatie methode is veiliger en aanbevolen voor productie scenario's.

  [Een Azure IoT Edge apparaat instellen met X. 509-certificaat verificatie](how-to-manual-provision-x509.md)