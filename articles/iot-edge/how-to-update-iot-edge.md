---
title: IoT Edge versie op apparaten bijwerken-Azure IoT Edge | Microsoft Docs
description: IoT Edge apparaten bijwerken om de nieuwste versies van de Security daemon en de IoT Edge-runtime uit te voeren
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/22/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3916855a62e506b12f72de713ccb56e89f846938
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171803"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>De IoT Edge-beveiligingsdaemon en -runtime bijwerken

Wanneer de IoT Edge-service nieuwe versies uitbrengt, moet u uw IoT Edge-apparaten bijwerken voor de nieuwste functies en beveiligings verbeteringen. Dit artikel bevat informatie over het bijwerken van uw IoT Edge-apparaten wanneer een nieuwe versie beschikbaar is.

Er moeten twee onderdelen van een IoT Edge apparaat worden bijgewerkt als u wilt overstappen op een nieuwere versie. De eerste is de beveiligings-daemon die op het apparaat wordt uitgevoerd en de runtime modules start wanneer het apparaat wordt gestart. Op dit moment kan de beveiligings-daemon alleen worden bijgewerkt vanaf het apparaat zelf. Het tweede onderdeel is de runtime, bestaande uit de modules IoT Edge hub en IoT Edge agent. Afhankelijk van hoe u uw implementatie structureert, kan de runtime worden bijgewerkt vanaf het apparaat of op afstand.

Zie [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases)om de nieuwste versie van Azure IOT Edge te vinden.

## <a name="update-the-security-daemon"></a>De beveiligings-daemon bijwerken

De IoT Edge Security daemon is een systeem eigen onderdeel dat moet worden bijgewerkt met behulp van Package Manager op het IoT Edge-apparaat.

Controleer de versie van de beveiligings-daemon die op het apparaat wordt uitgevoerd met behulp van de opdracht `iotedge version` .

### <a name="linux-devices"></a>Linux-apparaten

Op Linux x64-apparaten gebruikt u apt-get of uw geschikte pakket beheerder om de beveiligings-daemon bij te werken naar de meest recente versie.

De nieuwste opslagplaats configuratie ophalen van micro soft:

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

Kopieer de gegenereerde lijst.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installeer de open bare sleutel van micro soft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Apt bijwerken.

   ```bash
   sudo apt-get update
   ```

Controleer welke versies van IoT Edge beschikbaar zijn.

   ```bash
   apt list -a iotedge
   ```

Als u wilt bijwerken naar de meest recente versie van de beveiligings-daemon, gebruikt u de volgende opdracht waarmee u ook **libiothsm-STD** kunt bijwerken naar de nieuwste versie:

   ```bash
   sudo apt-get install iotedge
   ```

Als u wilt bijwerken naar een specifieke versie van de beveiligings-daemon, geeft u de versie op uit de apt-lijst uitvoer. Wanneer **iotedge** wordt bijgewerkt, wordt automatisch geprobeerd het **libiothsm-standaard** pakket bij te werken naar de meest recente versie, wat een conflict met de afhankelijkheid kan veroorzaken. Als u niet naar de meest recente versie gaat, moet u ervoor zorgen dat beide pakketten voor dezelfde versie worden doel. Met de volgende opdracht wordt bijvoorbeeld een specifieke versie van de 1.0.9-release geïnstalleerd:

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

Als de versie die u wilt installeren niet beschikbaar is via apt-get, kunt u krul gebruiken om een wille keurige versie te richten op basis van de opslag plaats [IOT Edge releases](https://github.com/Azure/azure-iotedge/releases) . Voor de versie die u wilt installeren, zoekt u de juiste **libiothsm-STD-** en **iotedge** -bestanden voor uw apparaat. Voor elk bestand klikt u met de rechter muisknop op de bestands koppeling en kopieert u het koppelings adres. Gebruik het koppelings adres om de specifieke versies van die onderdelen te installeren:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Windows-apparaten

Gebruik het Power shell-script voor het bijwerken van de beveiligings-daemon op Windows-apparaten. Het script haalt automatisch de nieuwste versie van de beveiligings-daemon op.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Als u de Update-IoTEdge opdracht uitvoert, wordt de beveiligings-daemon van uw apparaat verwijderd en bijgewerkt, samen met de twee runtime container installatie kopieën. Het bestand config. yaml wordt op het apparaat bewaard, evenals gegevens van de Moby-container Engine (als u Windows-containers gebruikt). Het houden van de configuratie gegevens betekent dat u de gegevens van de connection string of de Device Provisioning Service voor uw apparaat niet opnieuw hoeft op te geven tijdens het update proces.

Als u wilt bijwerken naar een specifieke versie van de beveiligings-daemon, zoekt u de versie die u wilt richten op basis van [IOT Edge releases](https://github.com/Azure/azure-iotedge/releases). Down load het **Microsoft-Azure-IoTEdge.cab** bestand in die versie. Vervolgens gebruikt u de `-OfflineInstallationPath` para meter om naar de locatie van het lokale bestand te verwijzen. Bijvoorbeeld:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>De `-OfflineInstallationPath` para meter zoekt naar een bestand met de naam **Microsoft-Azure-IoTEdge.cab** in de opgegeven map. Vanaf IoT Edge versie 1.0.9-RC4 zijn er twee cab-bestanden beschikbaar voor gebruik, één voor AMD64-apparaten en één voor ARM32. Down load het juiste bestand voor uw apparaat en wijzig de naam van het bestand om het structuur achtervoegsel te verwijderen.

Voor meer informatie over Update opties gebruikt u de opdracht `Get-Help Update-IoTEdge -full` of raadpleegt u het [Power shell-script voor IOT Edge in Windows](reference-windows-scripts.md).

## <a name="update-the-runtime-containers"></a>De runtime-containers bijwerken

De manier waarop u de IoT Edge agent-en IoT Edge hub-containers bijwerkt, is afhankelijk van of u rollende Tags (zoals 1,0) of specifieke tags (zoals 1.0.7) gebruikt in uw implementatie.

Controleer de versie van de IoT Edge agent en IoT Edge hub-modules op uw apparaat met behulp van de opdrachten `iotedge logs edgeAgent` of `iotedge logs edgeHub` .

  ![Container versie zoeken in Logboeken](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge Tags begrijpen

De installatie kopieën van IoT Edge agent en IoT Edge hub worden gelabeld met de IoT Edge-versie waaraan ze zijn gekoppeld. Er zijn twee verschillende manieren om labels te gebruiken met de runtime-installatie kopieën:

* **Roulerende Tags** : gebruik alleen de eerste twee waarden van het versie nummer om de meest recente afbeelding op te halen die overeenkomt met die cijfers. Bijvoorbeeld: 1,0 wordt bijgewerkt wanneer er een nieuwe versie is die naar de nieuwste versie van 1.0. x wijst. Als de container-runtime op uw IoT Edge-apparaat de installatie kopie opnieuw ophaalt, worden de runtime modules bijgewerkt naar de meest recente versie. Deze aanpak wordt aanbevolen voor ontwikkelings doeleinden. Implementaties van de Azure Portal standaard naar roulerende Tags.

* **Specifieke labels** : gebruik alle drie de waarden van het versie nummer om de installatie kopie versie expliciet in te stellen. 1.0.7 wordt bijvoorbeeld niet gewijzigd na de eerste release. Wanneer u klaar bent om bij te werken, kunt u een nieuw versie nummer declareren in het implementatie manifest. Deze aanpak wordt voorgesteld voor productie doeleinden.

### <a name="update-a-rolling-tag-image"></a>Een afbeelding van een roulerende tag bijwerken

Als u roulerende Tags in uw implementatie gebruikt (bijvoorbeeld mcr.microsoft.com/azureiotedge-hub:**1,0**), moet u de container runtime op het apparaat afdwingen om de nieuwste versie van de installatie kopie te halen.

De lokale versie van de installatie kopie verwijderen van uw IoT Edge-apparaat. Op Windows-computers verwijdert het verwijderen van de Security daemon ook de runtime-installatie kopieën. u hoeft deze stap dus niet opnieuw uit te voeren.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Mogelijk moet u de vlag Force gebruiken `-f` om de installatie kopieën te verwijderen.

De IoT Edge-service haalt de nieuwste versies van de runtime-installatie kopieën op en start deze automatisch opnieuw op het apparaat.

### <a name="update-a-specific-tag-image"></a>Een specifieke tag-afbeelding bijwerken

Als u specifieke tags in uw implementatie gebruikt (bijvoorbeeld mcr.microsoft.com/azureiotedge-hub:**1.0.8**), hoeft u alleen de tag in het implementatie manifest bij te werken en de wijzigingen toe te passen op uw apparaat.

1. Selecteer uw IoT Edge apparaat in de IoT Hub in het Azure Portal en selecteer **modules instellen**.

1. Selecteer in de sectie **IOT Edge modules** **runtime-instellingen**.

   ![Runtime-instellingen configureren](./media/how-to-update-iot-edge/configure-runtime.png)

1. Werk in **runtime-instellingen** de **afbeeldings** waarde voor **Edge hub** bij met de gewenste versie. Selecteer nog niet **Opslaan** .

   ![Versie van Edge hub-installatie kopie bijwerken](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Vouw de **hub** -instellingen van de Edge samen of schuif omlaag en werk de **afbeeldings** waarde voor de **rand agent** bij met dezelfde versie.

   ![Versie van de Edge hub-agent bijwerken](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Selecteer **Opslaan**.

1. Selecteer **controleren + maken**, Controleer de implementatie en selecteer **maken**.

## <a name="update-offline-or-to-a-specific-version"></a>Offline of naar een specifieke versie bijwerken

Als u een apparaat offline wilt bijwerken of een update wilt uitvoeren naar een specifieke versie van IoT Edge in plaats van de meest recente versie, kunt u dit doen met de `-OfflineInstallationPath` para meter.

Er worden twee onderdelen gebruikt om een IoT Edge apparaat bij te werken:

* Een Power shell-script, dat de installatie-instructies bevat
* Microsoft Azure IoT Edge cab, dat de IoT Edge Security daemon (iotedged), Moby container engine en Moby CLI bevat

1. Zie [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases)voor de nieuwste IOT Edge installatie bestanden samen met vorige versies.

2. Zoek de versie die u wilt installeren en down load de volgende bestanden van het gedeelte **assets** van de release opmerkingen op uw IOT-apparaat:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab van Releases 1.0.9 of nieuwer, of Microsoft-Azure-IoTEdge.cab van Releases 1.0.8 en ouder.

   Microsoft-Azure-IotEdge-arm32.cab is ook alleen beschikbaar vanaf 1.0.9 voor test doeleinden. IoT Edge wordt momenteel niet ondersteund op Windows ARM32-apparaten.

   Het is belang rijk dat u het Power shell-script uit dezelfde versie gebruikt als het CAB-bestand dat u gebruikt, omdat de functionaliteit wordt gewijzigd zodat de functies in elke release worden ondersteund.

3. Als het CAB-bestand dat u hebt gedownload een architectuur achtervoegsel heeft, wijzigt u de naam van het bestand in alleen **Microsoft-Azure-IoTEdge.cab**.

4. Als u wilt bijwerken met offline onderdelen, [punt bron](/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) het lokale exemplaar van het Power shell-script. Vervolgens gebruikt u de `-OfflineInstallationPath` para meter als onderdeel van de `Update-IoTEdge` opdracht en geeft u het absolute pad naar de bestands directory op. Bijvoorbeeld:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Update-IoTEdge -OfflineInstallationPath <path>
   ```

## <a name="update-to-a-release-candidate-version"></a>Bijwerken naar een release Candi date-versie

Azure IoT Edge brengt regel matig nieuwe versies van de IoT Edge service uit. Voor elke stabiele versie zijn er een of meer release Candi date (RC)-versies. RC-versies bevatten alle geplande functies voor de release, maar zijn nog steeds bezig met testen en valideren. Als u een nieuwe functie vroegtijdig wilt testen, kunt u een RC-versie installeren en feedback geven via GitHub.

Release Candi date-versies volgen dezelfde Nummerings Conventie voor releases, maar hebben wel **-RC** plus een incrementeel nummer toegevoegd aan het einde. U kunt de release kandidaten in dezelfde lijst met [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases) zien als de stabiele versies. Zoek bijvoorbeeld naar **1.0.9-RC5** en **1.0.9-RC6**, twee van de release kandidaten die werden ontvangen vóór **1.0.9**. U kunt ook zien dat RC-versies zijn gemarkeerd met **voorlopige** labels.

De IoT Edge-agent-en-hub-modules hebben RC-versies die zijn gelabeld met dezelfde Conventie. Bijvoorbeeld **MCR.Microsoft.com/azureiotedge-hub:1.0.9-RC6**.

Als previews worden versies van release Candi date niet opgenomen als de meest recente versie die de reguliere installatie Programma's doel. In plaats daarvan moet u hand matig de assets richten voor de RC-versie die u wilt testen. Het installeren of bijwerken van een RC-versie is in het algemeen hetzelfde als het richten op een andere specifieke versie van IoT Edge.

Gebruik de secties in dit artikel voor meer informatie over het bijwerken van een IoT Edge apparaat naar een specifieke versie van de Security daemon of runtime-modules.

Als u IoT Edge installeert in plaats van een bestaande installatie te upgraden, gebruikt u de stappen in de [installatie van offline of specifieke versie](how-to-install-iot-edge.md#offline-or-specific-version-installation).

## <a name="next-steps"></a>Volgende stappen

Bekijk de nieuwste [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases).

Blijf up-to-date met recente updates en aankondigingen in het [Internet of Things blog](https://azure.microsoft.com/blog/topics/internet-of-things/)