---
title: Azure IoT Edge installeren voor Windows | Microsoft Docs
description: Azure IoT Edge voor Windows-containers installeren op Windows-apparaten
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: bb87d09b67658f9a3d7c68f635bfcd9a29de675c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201637"
---
# <a name="install-and-manage-azure-iot-edge-with-windows-containers"></a>Azure IoT Edge installeren en beheren met Windows-containers

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Met de Azure IoT Edge runtime wordt een apparaat omgezet in een IoT Edge apparaat. Zodra een apparaat is geconfigureerd met de IoT Edge-runtime, kunt u beginnen met het implementeren van bedrijfslogica vanuit de cloud. Zie [inzicht krijgen in de Azure IOT Edge runtime en de bijbehorende architectuur](iot-edge-runtime.md)voor meer informatie.

Er zijn twee stappen om een IoT Edge apparaat in te stellen. De eerste stap is het installeren van de runtime en de bijbehorende afhankelijkheden. De tweede stap is om het apparaat te verbinden met de identiteit in de Cloud en verificatie met IoT Hub in te stellen.

Dit artikel bevat de stappen voor het installeren van de Azure IoT Edge runtime met Windows-containers. Als u Linux-containers wilt gebruiken op een Windows-apparaat, raadpleegt u het artikel [Azure IOT Edge voor Linux op Windows](how-to-install-iot-edge-on-windows.md) .

>[!NOTE]
>Azure IoT Edge met Windows-containers worden niet ondersteund vanaf versie 1,2 van Azure IoT Edge.
>
>Overweeg het gebruik van de nieuwe methode voor het uitvoeren van IoT Edge op Windows-apparaten [Azure IOT Edge voor Linux in Windows](iot-edge-for-linux-on-windows.md).

## <a name="prerequisites"></a>Vereisten

* Een Windows-apparaat

  Voor IoT Edge met Windows-containers is Windows versie 1809/build 17763 vereist. Dit is de meest recente [build van Windows lange termijn ondersteuning](/windows/release-information/). Controleer de [lijst met ondersteunde systemen](support.md#operating-systems) voor een lijst met ondersteunde sku's.

* Een [geregistreerde apparaat-id](how-to-register-device.md)

  Als u uw apparaat bij symmetrische sleutel verificatie hebt geregistreerd, moet het apparaat connection string gereed zijn.

  Als u uw apparaat hebt geregistreerd met X. 509 zelfondertekende certificaat authenticatie, moet u ten minste één van de identiteits certificaten gebruiken die u hebt gebruikt voor het registreren van het apparaat en de bijbehorende persoonlijke persoonlijke sleutel die op uw apparaat beschikbaar is.

## <a name="install-a-container-engine"></a>Een container Engine installeren

Azure IoT Edge is gebaseerd op een OCI-compatibele container-runtime, zoals [Moby](https://github.com/moby/moby). Een op Moby gebaseerde engine die is opgenomen in het installatie script. Er zijn geen extra stappen voor het installeren van de engine.

## <a name="install-the-iot-edge-security-daemon"></a>Installeer de IoT Edge Security daemon

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

3. Start het apparaat opnieuw op wanneer dit wordt gevraagd.

Wanneer u IoT Edge op een apparaat installeert, kunt u extra para meters gebruiken om het proces te wijzigen, met inbegrip van:

* Direct verkeer om een proxy server te passeren
* Wijs het installatie programma naar een lokale map voor offline-installatie.

Zie [Power shell-scripts voor IOT Edge met Windows-containers](reference-windows-scripts.md)voor meer informatie over deze aanvullende para meters.

## <a name="provision-the-device-with-its-cloud-identity"></a>Het apparaat inrichten met de Cloud identiteit

Nu de container-engine en de IoT Edge runtime op uw apparaat zijn geïnstalleerd, bent u klaar voor de volgende stap, waarmee u het apparaat kunt instellen met de Cloud identiteit en verificatie gegevens.

Kies het volgende gedeelte op basis van het verificatie type dat u wilt gebruiken:

* [Optie 1: verifiëren met symmetrische sleutels](#option-1-authenticate-with-symmetric-keys)
* [Optie 2: verifiëren met X. 509-certificaten](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Optie 1: verifiëren met symmetrische sleutels

Op dit punt wordt de IoT Edge runtime geïnstalleerd op uw Windows-apparaat en moet u het apparaat inrichten met de Cloud identiteit en verificatie gegevens.

In deze sectie worden de stappen beschreven voor het inrichten van een apparaat met symmetrische sleutel verificatie. U moet uw apparaat hebben geregistreerd bij IoT Hub en de connection string opgehaald van de apparaatgegevens. Als dat niet het geval is, volgt u de stappen in [een IOT edge apparaat registreren in IOT hub](how-to-register-device.md).

1. Voer Power shell uit als beheerder op het IoT Edge-apparaat.

2. Gebruik de opdracht [initialiseren-IoTEdge](reference-windows-scripts.md#initialize-iotedge) om de IOT Edge runtime op uw computer te configureren. De opdracht wordt standaard ingesteld op handmatig inrichten met Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Als u het IoTEdgeSecurityDaemon.ps1 script op uw apparaat hebt gedownload voor offline of installatie van een specifieke versie, moet u ervoor zorgen dat u naar de lokale kopie van het script verwijst.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Wanneer u hierom wordt gevraagd, geeft u de connection string op van het apparaat dat u in de vorige sectie hebt opgehaald. Het apparaat connection string het fysieke apparaat koppelt aan een apparaat-ID in IoT Hub en biedt verificatie-informatie.

   De connection string van het apparaat heeft de volgende indeling en mag geen aanhalings tekens bevatten: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

Wanneer u een apparaat hand matig inricht, kunt u extra para meters gebruiken om het proces te wijzigen, met inbegrip van:

* Direct verkeer om een proxy server te passeren
* Een specifieke edgeAgent-container installatie kopie declareren en referenties opgeven als deze zich in een persoonlijk REGI ster bevinden

Zie [Power shell-scripts voor IOT Edge met Windows-containers](reference-windows-scripts.md)voor meer informatie over deze aanvullende para meters.

### <a name="option-2-authenticate-with-x509-certificates"></a>Optie 2: verifiëren met X. 509-certificaten

Op dit punt wordt de IoT Edge runtime geïnstalleerd op uw Windows-apparaat en moet u het apparaat inrichten met de Cloud identiteit en verificatie gegevens.

In deze sectie worden de stappen beschreven voor het inrichten van een apparaat met X. 509-certificaat verificatie. U moet uw apparaat hebben geregistreerd bij IoT Hub, zodat er vinger afdrukken worden gemaakt die overeenkomen met het certificaat en de persoonlijke sleutel op uw IoT Edge apparaat. Als dat niet het geval is, volgt u de stappen in [een IOT edge apparaat registreren in IOT hub](how-to-register-device.md).

1. Voer Power shell uit als beheerder op het IoT Edge-apparaat.

2. Gebruik de opdracht [initialiseren-IoTEdge](reference-windows-scripts.md#initialize-iotedge) om de IOT Edge runtime op uw computer te configureren.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Als u het IoTEdgeSecurityDaemon.ps1 script op uw apparaat hebt gedownload voor offline of installatie van een specifieke versie, moet u ervoor zorgen dat u naar de lokale kopie van het script verwijst.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Geef de volgende informatie op wanneer u daarom wordt gevraagd:

   * **IotHubHostName**: de hostnaam van de IOT-hub waarmee het apparaat verbinding maakt. Bijvoorbeeld `{IoT hub name}.azure-devices.net`.
   * **DeviceID**: de id die u hebt ingevoerd tijdens het registreren van het apparaat.
   * **X509IdentityCertificate**: absoluut pad naar een identiteits certificaat op het apparaat. Bijvoorbeeld `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: het absolute pad naar het bestand met de persoonlijke sleutel voor het gegeven identiteits certificaat. Bijvoorbeeld `C:\path\identity_key.pem`.

Wanneer u een apparaat hand matig inricht, kunt u extra para meters gebruiken om het proces te wijzigen, met inbegrip van:

* Direct verkeer om een proxy server te passeren
* Een specifieke edgeAgent-container installatie kopie declareren en referenties opgeven als deze zich in een persoonlijk REGI ster bevinden

Zie [Power shell-scripts voor IOT Edge met Windows-containers](reference-windows-scripts.md)voor meer informatie over deze aanvullende para meters.

## <a name="offline-or-specific-version-installation-optional"></a>Installatie van offline of specifieke versie (optioneel)

De stappen in deze sectie zijn bedoeld voor scenario's die niet worden gedekt door de standaard installatie stappen. Dit kan het volgende omvatten:

* IoT Edge offline installeren
* Een release Candi date-versie installeren
* Installeer een andere versie dan de nieuwste

Tijdens de installatie worden drie bestanden gedownload:

* Een Power shell-script, dat de installatie-instructies bevat
* Microsoft Azure IoT Edge cab, dat de IoT Edge Security daemon (iotedged), Moby container engine en Moby CLI bevat
* Installatie programma voor het Visual C++ Redistributable-pakket (VC runtime)

Als uw apparaat offline is tijdens de installatie, of als u een specifieke versie van IoT Edge wilt installeren, kunt u deze bestanden van tevoren downloaden naar het apparaat. Wanneer het tijd is om te installeren, plaatst u het installatie script in de map met de gedownloade bestanden. Het installatie programma controleert eerst de Directory en downloadt alleen de onderdelen die niet worden gevonden. Als alle bestanden offline beschikbaar zijn, kunt u installeren zonder Internet verbinding.

1. Zie [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases)voor de nieuwste IOT Edge installatie bestanden samen met vorige versies.

2. Zoek de versie die u wilt installeren en down load de volgende bestanden van het gedeelte **assets** van de release opmerkingen op uw IOT-apparaat:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab van 1,1-release kanaal.

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

## <a name="update-iot-edge"></a>IoT Edge bijwerken

Gebruik de `Update-IoTEdge` opdracht om de beveiligings-daemon bij te werken. Het script haalt automatisch de nieuwste versie van de beveiligings-daemon op.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

Als u de Update-IoTEdge opdracht uitvoert, wordt de beveiligings-daemon van uw apparaat verwijderd en bijgewerkt, samen met de twee runtime container installatie kopieën. Het bestand config. yaml wordt op het apparaat bewaard, evenals gegevens van de Moby-container engine. Het houden van de configuratie gegevens betekent dat u de gegevens van de connection string of de Device Provisioning Service voor uw apparaat niet opnieuw hoeft op te geven tijdens het update proces.

Als u wilt bijwerken naar een specifieke versie van de Security daemon, zoekt u de versie van 1,1-release kanaal dat u wilt richten op basis van [IOT Edge releases](https://github.com/Azure/azure-iotedge/releases). Down load het **Microsoft-Azure-IoTEdge.cab** bestand in die versie. Vervolgens gebruikt u de `-OfflineInstallationPath` para meter om naar de locatie van het lokale bestand te verwijzen. Bijvoorbeeld:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>De `-OfflineInstallationPath` para meter zoekt naar een bestand met de naam **Microsoft-Azure-IoTEdge.cab** in de opgegeven map. Wijzig de naam van het bestand om het structuur achtervoegsel te verwijderen als er een bestaat.

Als u een apparaat offline wilt bijwerken, zoekt u de versie die u wilt instellen in [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases). Down load de *IoTEdgeSecurityDaemon.ps1* -en *Microsoft-Azure-IoTEdge.cab* -bestanden in die versie. Het is belang rijk dat u het Power shell-script uit dezelfde versie gebruikt als het CAB-bestand dat u gebruikt, omdat de functionaliteit wordt gewijzigd zodat de functies in elke release worden ondersteund.

Als het CAB-bestand dat u hebt gedownload een architectuur achtervoegsel heeft, wijzigt u de naam van het bestand in alleen **Microsoft-Azure-IoTEdge.cab**.

Als u wilt bijwerken met offline onderdelen, [punt bron](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) het lokale exemplaar van het Power shell-script. Vervolgens gebruikt u de `-OfflineInstallationPath` para meter als onderdeel van de `Update-IoTEdge` opdracht en geeft u het absolute pad naar de bestands directory op. Bijvoorbeeld:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Voor meer informatie over Update opties gebruikt u de opdracht `Get-Help Update-IoTEdge -full` of raadpleegt u [Power shell-scripts voor IOT Edge met Windows-containers](reference-windows-scripts.md).

## <a name="uninstall-iot-edge"></a>IoT Edge verwijderen

Als u de IoT Edge-installatie wilt verwijderen van uw apparaat, gebruikt u de volgende opdrachten.

Als u de IoT Edge-installatie wilt verwijderen van uw Windows-apparaat, gebruikt u de opdracht [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) van een beheer-Power shell-venster. Met deze opdracht wordt de IoT Edge runtime verwijderd, samen met uw bestaande configuratie en de Moby-Engine gegevens.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Voor meer informatie over verwijderings opties gebruikt u de opdracht `Get-Help Uninstall-IoTEdge -full` .

## <a name="next-steps"></a>Volgende stappen

Ga door met het [implementeren van IOT Edge-modules](how-to-deploy-modules-portal.md) voor meer informatie over het implementeren van modules op uw apparaat.
