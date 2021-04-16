---
title: Apparaat inrichten met een virtuele TPM op linux-VM - Azure IoT Edge
description: Een gesimuleerde TPM op een Linux-VM gebruiken om Azure Device Provisioning Service te testen voor Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 79fe8acd06084c58b0cf9b47bf93e933c648510c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481987"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Een apparaat met IoT Edge maken en inrichten met een TPM in Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

In dit artikel wordt beschreven hoe u automatische inrichting op een Linux-IoT Edge test met behulp van een Trusted Platform Module (TPM). U kunt automatisch apparaten Azure IoT Edge met [Device Provisioning Service.](../iot-dps/index.yml) Als u niet bekend bent met het proces van automatische inrichting, bekijkt u het overzicht voor [inrichting](../iot-dps/about-iot-dps.md#provisioning-process) voordat u verdergaat.

De taken zijn als volgt:

1. Maak een virtuele Linux-machine (VM) in Hyper-V met een gesimuleerde Trusted Platform Module (TPM) voor hardwarebeveiliging.
1. Maak een exemplaar van IoT Hub Device Provisioning Service (DPS).
1. Maak een afzonderlijke inschrijving voor het apparaat.
1. Installeer de IoT Edge runtime en verbind het apparaat met IoT Hub.

> [!TIP]
> In dit artikel wordt beschreven hoe u DPS-inrichting test met behulp van een TPM-simulator, maar veel ervan is van toepassing op fysieke TPM-hardware, zoals [de Infineon OPTIGA &trade; TPM,](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board)een Azure Certified for IoT-apparaat.
>
> Als u een fysiek apparaat gebruikt, kunt u doorgaan naar de sectie Inrichtingsgegevens [ophalen](#retrieve-provisioning-information-from-a-physical-device) van een fysiek apparaat in dit artikel.

## <a name="prerequisites"></a>Vereisten

* Een Windows-ontwikkelmachine [met Hyper-V ingeschakeld.](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) In dit artikel wordt Windows 10 Ubuntu Server-VM wordt uitgevoerd.
* Een actief IoT Hub.

> [!NOTE]
> TPM 2.0 is vereist bij het gebruik van TPM Attestation met DPS en kan alleen worden gebruikt voor het maken van afzonderlijke, niet groepsinschrijvingen.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Een virtuele Linux-machine maken met een virtuele TPM

In deze sectie maakt u een nieuwe virtuele Linux-machine op Hyper-V. U configureert deze virtuele machine met een gesimuleerde TPM om te testen hoe automatische inrichting werkt met IoT Edge.

### <a name="create-a-virtual-switch"></a>Een virtuele switch maken

Met een virtuele switch kan uw virtuele machine verbinding maken met een fysiek netwerk.

1. Open Hyper-V-manager op uw Windows-computer.

2. Selecteer **virtual** Switch Manager in **het menu Acties.**

3. Kies een **externe** virtuele switch en selecteer **vervolgens Virtuele switch maken.**

4. Geef de nieuwe virtuele switch een naam, bijvoorbeeld **EdgeSwitch.** Zorg ervoor dat het verbindingstype is ingesteld op **Extern netwerk** en selecteer **ok.**

5. In een pop-up wordt u gewaarschuwd dat de netwerkverbinding kan worden onderbroken. Selecteer **Ja** ter bevestiging.

Als er fouten optreden tijdens het maken van de nieuwe virtuele switch, moet u ervoor zorgen dat er geen andere switches gebruikmaken van de ethernetadapter en dat andere switches dezelfde naam niet gebruiken.

### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Download een schijfafbeeldingsbestand voor uw virtuele machine en sla het lokaal op. Bijvoorbeeld [Ubuntu-server 18.04.](http://releases.ubuntu.com/18.04/) Zie ondersteunde systemen voor IoT Edge over ondersteunde [Azure IoT Edge besturingssystemen.](support.md)

2. Selecteer in Hyper-V-manager opnieuw **Actie**  >  **Nieuwe**  >  **virtuele machine** in het menu Acties. 

3. Voltooi de **wizard Nieuwe virtuele machine** met de volgende specifieke configuraties:

   1. **Generatie opgeven:** selecteer **Generatie 2.** Virtuele machines van de tweede generatie hebben geneste virtualisatie ingeschakeld, wat vereist is om deze IoT Edge op een virtuele machine uit te voeren.
   2. **Netwerken configureren:** stel de waarde van **Verbinding in** op de virtuele switch die u in de vorige sectie hebt gemaakt.
   3. **Installatieopties:** selecteer **Een besturingssysteem installeren vanuit** een opstartbaar installatiebestand en blader naar het schijfinstallatiebestand dat u lokaal hebt opgeslagen.

4. Selecteer **Voltooien** in de wizard om de virtuele machine te maken.

Het kan enkele minuten duren om de nieuwe VM te maken.

### <a name="enable-virtual-tpm"></a>Virtuele TPM inschakelen

Zodra de virtuele machine is gemaakt, opent u de instellingen om de virtuele TPM (Trusted Platform Module) in teschakelen waarmee u het apparaat automatisch kunt inrichten.

1. Klik in Hyper-V-manager met de rechtermuisknop op de VM en selecteer **Instellingen.**

2. Navigeer naar **Beveiliging**.

3. Schakel beveiligd **opstarten inschakelen uit.**

4. Schakel **Inschakelen Trusted Platform Module** in.

5. Klik op **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>De virtuele machine starten en TPM-gegevens verzamelen

Bouw op de virtuele machine een hulpprogramma dat u  kunt gebruiken om de registratie-id en goedkeuringssleutel van het apparaat **op te halen.**

1. Start uw VM in Hyper-V-manager en maak er verbinding mee.

1. Volg de aanwijzingen in de virtuele machine om het installatieproces te voltooien en de machine opnieuw op te starten.

1. Meld u aan bij uw VM [](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) en volg de stappen in Een Linux-ontwikkelomgeving instellen om de Azure IoT Device SDK voor C te installeren en te bouwen.

   >[!TIP]
   >In de loop van dit artikel kopieert en plakt u op de virtuele machine, wat niet eenvoudig is via de Hyper-V Manager-verbindingstoepassing. Mogelijk wilt u eenmaal verbinding maken met de virtuele machine via Hyper-V-manager om het IP-adres op te halen. Voer eerst `sudo apt install net-tools` uit en vervolgens `hostname -I` . Vervolgens kunt u het IP-adres gebruiken om verbinding te maken via SSH: `ssh <username>@<ipaddress>` .

1. Voer de volgende opdrachten uit om het SDK-hulpprogramma te bouwen waarmee de inrichtingsgegevens van uw apparaat worden opgehaald uit de TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. In het uitvoervenster worden de **registratie-id** en goedkeuringssleutel **van het apparaat weergegeven.** Kopieer deze waarden voor later gebruik wanneer u een afzonderlijke inschrijving voor uw apparaat maakt.

Zodra u uw registratie-id en goedkeuringssleutel hebt, gaat u verder met de sectie [De registratie-id IoT Hub Device Provisioning Service](#set-up-the-iot-hub-device-provisioning-service)

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Inrichtingsgegevens ophalen van een fysiek apparaat

Als u een fysiek IoT Edge gebruikt in plaats van een virtuele machine, bouwt u een hulpprogramma dat u kunt gebruiken om de inrichtingsgegevens van het apparaat op te halen.

1. Volg de stappen in [Een Linux-ontwikkelomgeving instellen om](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) de Azure IoT Device SDK voor C te installeren en te bouwen.

1. Voer de volgende opdrachten uit om het SDK-hulpprogramma te bouwen waarmee de inrichtingsgegevens van uw apparaat worden opgehaald van het TPM-apparaat.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Kopieer de waarden voor **Registratie-id** en **Goedkeuringssleutel**. U gebruikt deze waarden om een afzonderlijke inschrijving voor uw apparaat in DPS te maken.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>De IoT Hub Device Provisioning Service

Maak een nieuw exemplaar van de IoT Hub Device Provisioning Service in Azure en koppel deze aan uw IoT-hub. U kunt de instructies volgen in [De IoT Hub DPS instellen.](../iot-dps/quick-setup-auto-provision.md)

Nadat de Device Provisioning Service is uitgevoerd, kopieert u de waarde van **Id-bereik** op de overzichtspagina. U gebruikt deze waarde wanneer u de IoT Edge configureert.

## <a name="create-a-dps-enrollment"></a>Een DPS-inschrijving maken

Haal de inrichtingsgegevens op van uw virtuele machine en gebruik deze om een afzonderlijke inschrijving te maken in Device Provisioning Service.

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om de status van een initiële **apparaat-dubbel te declaren.** In de apparaat dubbel kunt u tags instellen om apparaten te groepen op metrische gegevens die u nodig hebt in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt om automatische [implementaties te maken.](how-to-deploy-at-scale.md)

> [!TIP]
> In de Azure CLI kunt [](/cli/azure/iot/dps/enrollment) u een inschrijving maken en de vlag edge **gebruiken** om op te geven dat een apparaat een IoT Edge is.

1. [Navigeer in Azure Portal](https://portal.azure.com)naar uw exemplaar van IoT Hub Device Provisioning Service.

2. Selecteer **onder Instellingen** de optie **Inschrijvingen beheren.**

3. Selecteer **Afzonderlijke inschrijving toevoegen en** voltooi de volgende stappen om de inschrijving te configureren:  

   1. Selecteer **bij Mechanisme** de optie **TPM.**

   2. Geef de **goedkeuringssleutel en** **registratie-id op** die u hebt gekopieerd van uw virtuele machine.

      > [!TIP]
      > Als u een fysiek TPM-apparaat gebruikt, moet u de goedkeuringssleutel **bepalen.** Deze sleutel is uniek voor elke TPM-chip en wordt verkregen van de TPM-chipfabrikant die ermee is gekoppeld. U kunt een unieke **registratie-id** voor uw TPM-apparaat afleiden door bijvoorbeeld een SHA-256-hash van de goedkeuringssleutel te maken.

   3. Geef indien nodig een id op voor uw apparaat. Als u geen apparaat-id op geeft, wordt de registratie-id gebruikt.

   4. Selecteer **Waar om** aan te geven dat deze virtuele machine een IoT Edge is.

   5. Kies het gekoppelde IoT Hub u uw apparaat wilt verbinden of selecteer **Koppeling naar nieuwe IoT Hub.** U kunt meerdere hubs kiezen en het apparaat wordt toegewezen aan een van deze hubs volgens het geselecteerde toewijzingsbeleid.

   6. Voeg indien nodig een tagwaarde toe aan de status **van de** initiële apparaat-dubbel. U kunt tags gebruiken voor doelgroepen van apparaten voor module-implementatie. Zie Deploy IoT Edge modules at scale (Modules op schaal [IoT Edge implementeren) voor meer informatie.](how-to-deploy-at-scale.md)

   7. Selecteer **Opslaan**.

Nu er een inschrijving voor dit apparaat bestaat, kan de IoT Edge het apparaat automatisch inrichten tijdens de installatie.

## <a name="install-the-iot-edge-runtime"></a>De IoT Edge-runtime installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen worden uitgevoerd in containers en bieden u de mogelijkheid om extra containers op het apparaat te implementeren, zodat u code aan de rand kunt uitvoeren. Installeer de IoT Edge runtime op uw virtuele machine.

Volg de stappen in [De Azure IoT Edge runtime installeren](how-to-install-iot-edge.md)en ga vervolgens terug naar dit artikel om het apparaat in terichten.

## <a name="configure-the-device-with-provisioning-information"></a>Het apparaat configureren met inrichtingsgegevens

Zodra de runtime op uw apparaat is geïnstalleerd, configureert u het apparaat met de informatie die wordt gebruikt om verbinding te maken met Device Provisioning Service en IoT Hub.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Weet uw **DPS-id-bereik** en **apparaatregistratie-id** die in de vorige secties zijn verzameld.

1. Open het configuratiebestand op het IoT Edge apparaat.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Zoek de sectie inrichtingsconfiguraties van het bestand. Maak commentaar van de regels voor TPM-inrichting en zorg ervoor dat andere inrichtingsregels als commentaar zijn opgenomen.

   De `provisioning:` regel mag geen voorgaande witruimte hebben en geneste items moeten worden ingesprongen door twee spaties.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. Werk de waarden van `scope_id` en bij met uw `registration_id` DPS- en apparaatgegevens.

1. U kunt eventueel de regels of gebruiken om het gedrag van de inrichting van uw apparaat `always_reprovision_on_startup` `dynamic_reprovisioning` te configureren. Als een apparaat bij het opstarten opnieuw wordt ingericht, probeert het altijd eerst in terichten met DPS en vervolgens terug te vallen op de inrichtingsback-up als dat mislukt. Als een apparaat is ingesteld op dynamisch opnieuw in te stellen, wordt IoT Edge opnieuw opgestart en opnieuw in te stellen als er een herinrichtingsgebeurtenis wordt gedetecteerd. Zie concepten voor het opnieuw [IoT Hub van apparaten voor meer informatie.](../iot-dps/concepts-device-reprovision.md)

1. Sla het bestand op en sluit het.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Uw **DPS-id-bereik en** **apparaatregistratie-id kennen** die in de vorige secties zijn verzameld.

1. Open het configuratiebestand op het IoT Edge apparaat.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Zoek de sectie inrichtingsconfiguraties van het bestand. Maak commentaar van de regels voor TPM-inrichting en zorg ervoor dat eventuele andere inrichtingsregels als commentaar worden opgenomen.

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. Werk de waarden van `id_scope` en bij met uw `registration_id` DPS- en apparaatgegevens.

1. Zoek eventueel de sectie modus voor automatisch opnieuw inbouwen van het bestand. Gebruik de parameter om het inrichtingsgedrag van uw apparaat te configureren `auto_reprovisioning_mode` op `Dynamic` , of `AlwaysOnStartup` `OnErrorOnly` . Zie concepten voor het opnieuw [IoT Hub van apparaten voor meer informatie.](../iot-dps/concepts-device-reprovision.md)

1. Sla het bestand op en sluit het.
:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>Toegang IoT Edge tot de TPM

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

De IoT Edge runtime moet toegang hebben tot de TPM om uw apparaat automatisch in te kunnenrichten.

U kunt TPM toegang geven tot de IoT Edge runtime door de systeeminstellingen te overschrijven, zodat de service hoofdbevoegdheden `iotedge` heeft. Als u de servicebevoegdheden niet wilt verhogen, kunt u ook de volgende stappen gebruiken om handmatig TPM-toegang te bieden.

1. Zoek het bestandspad naar de TPM-hardwaremodule op uw apparaat en sla het op als een lokale variabele.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Maak een nieuwe regel die de IoT Edge runtime toegang geeft tot tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Open het regelsbestand.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopieer de volgende toegangsgegevens naar het regelbestand.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   ```

5. Sla het bestand op en sluit het af.

6. Activeer het udev-systeem om de nieuwe regel te evalueren.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Controleer of de regel is toegepast.

   ```bash
   ls -l /dev/tpm0
   ```

   Geslaagde uitvoer wordt als volgt weergegeven:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Als u niet ziet dat de juiste machtigingen zijn toegepast, start u de computer opnieuw op om udev te vernieuwen.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
De IoT Edge is afhankelijk van een TPM-service die brokertoegang tot de TPM van een apparaat biedt. Deze service moet toegang hebben tot de TPM om uw apparaat automatisch in te kunnenrichten.

U kunt toegang tot de TPM verlenen door de systeeminstellingen te overschrijven, zodat de `aziottpm` service hoofdbevoegdheden heeft. Als u de servicebevoegdheden niet wilt verhogen, kunt u ook de volgende stappen gebruiken om handmatig TPM-toegang te verlenen.

1. Zoek het bestandspad naar de TPM-hardwaremodule op uw apparaat en sla het op als een lokale variabele.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Maak een nieuwe regel die de runtime-IoT Edge toegang geeft tot tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Open het regelbestand.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopieer de volgende toegangsgegevens naar het regelbestand.

   ```input
   # allow aziottpm access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0600"
   ```

5. Sla het bestand op en sluit het af.

6. Activeer het udev-systeem om de nieuwe regel te evalueren.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Controleer of de regel is toegepast.

   ```bash
   ls -l /dev/tpm0
   ```

   Geslaagde uitvoer wordt als volgt weergegeven:

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Als u niet ziet dat de juiste machtigingen zijn toegepast, start u de computer opnieuw op om udev te vernieuwen.
:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>Start de IoT Edge en controleer of de installatie is geslaagd

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Start de IoT Edge opnieuw op, zodat alle configuratiewijzigingen die u op het apparaat hebt aangebracht, worden opgehaald.

   ```bash
   sudo systemctl restart iotedge
   ```

Controleer of de runtime IoT Edge wordt uitgevoerd.

   ```bash
   sudo systemctl status iotedge
   ```

Daemonlogboeken bekijken.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Als er inrichtingsfouten optreden, kan het zijn dat de configuratiewijzigingen nog niet van kracht zijn. Probeer de daemon IoT Edge opnieuw te starten.

   ```bash
   sudo systemctl daemon-reload
   ```

Of start de virtuele machine opnieuw op om te zien of de wijzigingen van kracht worden bij een nieuwe start.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Pas de configuratiewijzigingen toe die u op het apparaat hebt aangebracht.

   ```bash
   sudo iotedge config apply
   ```

Controleer of de runtime IoT Edge wordt uitgevoerd.

   ```bash
   sudo iotedge system status
   ```

Daemonlogboeken bekijken.

   ```cmd/sh
   sudo iotedge system logs
   ```

Als er inrichtingsfouten optreden, kan het zijn dat de configuratiewijzigingen nog niet van kracht zijn. Start de daemon IoT Edge opnieuw.

   ```bash
   sudo systemctl daemon-reload
   ```

Of start de virtuele machine opnieuw op om te zien of de wijzigingen van kracht worden bij een nieuwe start.
:::moniker-end
<!-- end 1.2 -->

Als de runtime is gestart, kunt u naar uw IoT Hub en zien dat uw nieuwe apparaat automatisch is ingericht. Uw apparaat is nu klaar om de modules IoT Edge uitvoeren.

Lijst met modules die worden uitgevoerd.

```cmd/sh
iotedge list
```

U kunt controleren of de afzonderlijke inschrijving die u in Device Provisioning Service hebt gemaakt, is gebruikt. Navigeer naar uw Device Provisioning Service-exemplaar in Azure Portal. Open de inschrijvingsgegevens voor de afzonderlijke inschrijving die u hebt gemaakt. U ziet dat de status van de inschrijving is **toegewezen** en dat de apparaat-id wordt vermeld.

## <a name="next-steps"></a>Volgende stappen

Met het DPS-inschrijvingsproces kunt u de apparaat-id en apparaattweetags tegelijk instellen tijdens het inrichten van het nieuwe apparaat. U kunt deze waarden gebruiken voor afzonderlijke apparaten of groepen apparaten met behulp van automatisch apparaatbeheer. Meer informatie over [het implementeren en controleren IoT Edge modules op](how-to-deploy-at-scale.md) schaal met behulp van de Azure Portal of azure [CLI.](how-to-deploy-cli-at-scale.md)
