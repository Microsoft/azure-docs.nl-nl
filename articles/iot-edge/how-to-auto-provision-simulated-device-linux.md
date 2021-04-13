---
title: Apparaat inrichten met een virtuele TPM op Linux VM-Azure IoT Edge
description: Een gesimuleerde TPM op een virtuele Linux-machine gebruiken om de Azure Device Provisioning Service voor Azure IoT Edge te testen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ca16099cffc22a19c2ee35b00ae6f1bcbe2977a7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312396"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Een IoT Edge apparaat maken en inrichten met een TPM in Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

In dit artikel wordt beschreven hoe u automatische inrichting op een Linux IoT Edge apparaat kunt testen met behulp van een Trusted Platform Module (TPM). U kunt Azure IoT Edge apparaten automatisch inrichten met de [Device Provisioning Service](../iot-dps/index.yml). Als u niet bekend bent met het proces van automatische inrichting, bekijkt u het overzicht voor [inrichting](../iot-dps/about-iot-dps.md#provisioning-process) voordat u verdergaat.

De taken zijn als volgt:

1. Een virtuele Linux-machine (VM) maken in Hyper-V met een gesimuleerde Trusted Platform Module (TPM) voor hardware-beveiliging.
1. Maak een instantie van IoT Hub Device Provisioning Service (DPS).
1. Een afzonderlijke inschrijving voor het apparaat maken.
1. Installeer de IoT Edge runtime en verbind het apparaat met IoT Hub.

> [!TIP]
> In dit artikel wordt beschreven hoe u DPS Provisioning test met een TPM-Simulator, maar veel van deze is van toepassing op fysieke TPM-hardware, zoals [INFINEON OPTIGA &trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), een Azure Certified voor IOT-apparaat.
>
> Als u een fysiek apparaat gebruikt, kunt u door gaan naar de sectie [inrichtings gegevens ophalen van een fysiek apparaat](#retrieve-provisioning-information-from-a-physical-device) in dit artikel.

## <a name="prerequisites"></a>Vereisten

* Een Windows-ontwikkel computer waarop [Hyper-V is ingeschakeld](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). In dit artikel wordt gebruikgemaakt van Windows 10 waarop een Ubuntu-Server-VM wordt uitgevoerd.
* Een actieve IoT Hub.

> [!NOTE]
> TPM 2,0 is vereist voor het gebruik van TPM-Attestation met DPS en kan alleen worden gebruikt voor het maken van afzonderlijke, niet-groeps registraties.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Een virtuele Linux-machine maken met een virtuele TPM

In deze sectie maakt u een nieuwe virtuele Linux-machine in Hyper-V. U configureert deze virtuele machine met een gesimuleerde TPM om te testen hoe automatische inrichting werkt met IoT Edge.

### <a name="create-a-virtual-switch"></a>Een virtuele switch maken

Met een virtuele switch kan de virtuele machine verbinding maken met een fysiek netwerk.

1. Open Hyper-V-beheer op de Windows-computer.

2. Selecteer **Virtual Switch Manager** in het menu **acties** .

3. Kies een **externe** virtuele switch en selecteer **virtuele switch maken**.

4. Geef de nieuwe virtuele switch een naam, bijvoorbeeld **EdgeSwitch**. Zorg ervoor dat het verbindings type is ingesteld op **extern netwerk** en selecteer **OK**.

5. In een pop-upvenster wordt u gewaarschuwd dat de netwerk verbinding mogelijk wordt verstoord. Selecteer **Ja** ter bevestiging.

Als er fouten worden weer geven tijdens het maken van de nieuwe virtuele switch, moet u ervoor zorgen dat er geen andere switches gebruikmaken van de Ethernet-adapter en dat er geen andere switches dezelfde naam gebruiken.

### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Down load een schijf kopie bestand dat u voor de virtuele machine wilt gebruiken en sla het lokaal op. Bijvoorbeeld [Ubuntu server 18,04](http://releases.ubuntu.com/18.04/). Zie [Azure IOT Edge-ondersteunde systemen](support.md)voor meer informatie over de ondersteunde besturings systemen voor IOT edge apparaten.

2. In Hyper-V-beheer opnieuw, selecteer **actie**  >  **nieuwe**  >  **virtuele machine** in het menu **acties** .

3. Voltooi de **wizard Nieuwe virtuele machine** met de volgende specifieke configuraties:

   1. **Generatie opgeven**: Selecteer **generatie 2**. Voor virtuele machines van de 2e generatie is geneste virtualisatie ingeschakeld, wat vereist is om IoT Edge uit te voeren op een virtuele machine.
   2. **Netwerken configureren**: Stel de waarde in van **verbinding** met de virtuele switch die u hebt gemaakt in de vorige sectie.
   3. **Installatie opties**: Selecteer **een besturings systeem installeren vanaf een opstartbaar installatie kopie bestand** en blader naar het schijf kopie bestand dat u lokaal hebt opgeslagen.

4. Selecteer in de wizard **volt ooien** om de virtuele machine te maken.

Het kan een paar minuten duren voordat de nieuwe VM is gemaakt.

### <a name="enable-virtual-tpm"></a>Virtuele TPM inschakelen

Zodra de VM is gemaakt, opent u de instellingen om de Virtual trusted platform module (TPM) in te scha kelen waarmee u het apparaat automatisch kunt inrichten.

1. Klik in Hyper-V-beheer met de rechter muisknop op de virtuele machine en selecteer **instellingen**.

2. Navigeer naar **Beveiliging**.

3. **Schakel beveiligd opstarten inschakelen** uit.

4. **Schakel trusted platform module in**.

5. Klik op **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>De virtuele machine starten en TPM-gegevens verzamelen

Bouw in de virtuele machine een hulp programma dat u kunt gebruiken om de **registratie-id** en **goedkeurings sleutel** van het apparaat op te halen.

1. Start de virtuele machine in Hyper-V-beheer en maak er verbinding mee.

1. Volg de aanwijzingen in de virtuele machine om het installatie proces te volt ooien en de computer opnieuw op te starten.

1. Meld u aan bij uw virtuele machine en volg de stappen in [een Linux-ontwikkel omgeving instellen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) om de Azure IOT Device SDK voor C te installeren en te bouwen.

   >[!TIP]
   >In het kader van dit artikel kopieert en plakt u op de virtuele machine. Dit is niet eenvoudig via de verbindings toepassing Hyper-V-beheer. U kunt het beste verbinding maken met de virtuele machine via Hyper-V-beheer om het IP-adres op te halen. Voer de eerste keer uit `sudo apt install net-tools` en klik vervolgens `hostname -I` . Vervolgens kunt u het IP-adres gebruiken om verbinding te maken via SSH: `ssh <username>@<ipaddress>` .

1. Voer de volgende opdrachten uit om het SDK-hulp programma te bouwen dat de inrichtings gegevens van uw apparaat ophaalt uit de TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. In het uitvoer venster worden de **registratie-id** en de **goedkeurings sleutel** van het apparaat weer gegeven. Kopieer deze waarden voor later gebruik wanneer u een afzonderlijke registratie voor uw apparaat maakt.

Zodra u de registratie-ID en goedkeurings sleutel hebt ingesteld, gaat u verder met de sectie [instellen van de IOT hub Device Provisioning Service](#set-up-the-iot-hub-device-provisioning-service)

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Inrichtings gegevens ophalen van een fysiek apparaat

Als u een fysiek IoT Edge apparaat gebruikt in plaats van een virtuele machine, bouwt u een hulp programma dat u kunt gebruiken om de inrichtings gegevens van het apparaat op te halen.

1. Volg de stappen in [een Linux-ontwikkel omgeving instellen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) om de Azure IOT Device SDK voor C te installeren en te bouwen.

1. Voer de volgende opdrachten uit om het SDK-hulp programma te bouwen dat de inrichtings gegevens van uw apparaat ophaalt van het TPM-apparaat.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Kopieer de waarden voor de **registratie-id** en **goedkeurings sleutel**. U gebruikt deze waarden voor het maken van een afzonderlijke inschrijving voor uw apparaat in DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>De IoT Hub Device Provisioning Service instellen

Maak een nieuw exemplaar van de IoT Hub Device Provisioning Service in Azure en koppel deze aan uw IoT-hub. U kunt de instructies voor [het instellen van de IOT hub DPS](../iot-dps/quick-setup-auto-provision.md)volgen.

Nadat u de Device Provisioning Service hebt uitgevoerd, kopieert u de waarde van **id-bereik** van de pagina overzicht. U gebruikt deze waarde bij het configureren van de IoT Edge runtime.

## <a name="create-a-dps-enrollment"></a>Een DPS-inschrijving maken

Haal de inrichtings gegevens op van de virtuele machine en gebruik deze om een afzonderlijke registratie in Device Provisioning Service te maken.

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om een **eerste dubbele toestand** van het apparaat te declareren. In het dubbele apparaat kunt u Tags instellen om apparaten te groeperen op elke gewenste metrische waarde in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt voor het maken van [automatische implementaties](how-to-deploy-at-scale.md).

> [!TIP]
> In de Azure CLI kunt u een [inschrijving](/cli/azure/ext/azure-iot/iot/dps/enrollment) maken en de vlag voor **rand ingeschakeld** gebruiken om aan te geven dat een apparaat een IOT edge apparaat is.

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw exemplaar van IOT hub Device Provisioning Service.

2. Selecteer onder **instellingen** de optie **inschrijvingen beheren**.

3. Selecteer **Individuele inschrijving toevoegen** en voer de volgende stappen uit om de registratie te configureren:  

   1. Selecteer voor **mechanisme** **TPM**.

   2. Geef de **goedkeurings sleutel** en **registratie-id** op die u van de virtuele machine hebt gekopieerd.

      > [!TIP]
      > Als u een fysiek TPM-apparaat gebruikt, moet u de **goedkeurings sleutel** bepalen, die uniek is voor elke TPM-chip en die is verkregen van de fabrikant van de TPM-chip die eraan is gekoppeld. U kunt een unieke **registratie-id** voor uw TPM-apparaat afleiden door bijvoorbeeld een SHA-256-hash van de goedkeurings sleutel te maken.

   3. Geef een ID op voor uw apparaat als u wilt. Als u geen apparaat-ID opgeeft, wordt de registratie-ID gebruikt.

   4. Selecteer **waar** om te declareren dat deze virtuele machine een IOT edge apparaat is.

   5. Kies de gekoppelde IoT Hub waarmee u uw apparaat wilt verbinden, of selecteer **koppelen aan nieuw IOT hub**. U kunt meerdere hubs kiezen en het apparaat wordt toegewezen aan een van deze op basis van het geselecteerde toewijzings beleid.

   6. Voeg indien gewenst een tag-waarde toe aan de **eerste dubbele toestand** van het apparaat. U kunt tags gebruiken om groepen apparaten te richten op het implementeren van modules. Zie [IOT Edge-modules op schaal implementeren](how-to-deploy-at-scale.md)voor meer informatie.

   7. Selecteer **Opslaan**.

Nu een inschrijving voor dit apparaat bestaat, kan de IoT Edge runtime automatisch het apparaat inrichten tijdens de installatie.

## <a name="install-the-iot-edge-runtime"></a>De IoT Edge-runtime installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen worden in containers uitgevoerd en bieden u de mogelijkheid om extra containers op het apparaat te implementeren, zodat u code aan de rand kunt uitvoeren. Installeer de IoT Edge runtime op uw virtuele machine.

Volg de stappen in [install the Azure IOT Edge runtime](how-to-install-iot-edge.md)en ga vervolgens terug naar dit artikel om het apparaat in te richten.

## <a name="configure-the-device-with-provisioning-information"></a>Het apparaat configureren met inrichtings gegevens

Zodra de runtime op uw apparaat is geïnstalleerd, configureert u het apparaat met de informatie die wordt gebruikt om verbinding te maken met Device Provisioning Service en IoT Hub.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Ken het DPS **-id-bereik** en de **registratie-id** van het apparaat die in de vorige secties zijn verzameld.

1. Open het configuratie bestand op het IoT Edge-apparaat.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Zoek de sectie inrichtings configuraties van het bestand. Verwijder de opmerkingen over de regels voor TPM-inrichting en zorg ervoor dat alle andere inrichtings regels worden uitgeoefend.

   De `provisioning:` regel mag geen voor gaande witruimte hebben en geneste items moeten worden inge sprongen met twee spaties.

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

1. De waarden van `scope_id` en `registration_id` met uw DPS en apparaatgegevens bijwerken.

1. Gebruik eventueel de `always_reprovision_on_startup` `dynamic_reprovisioning` regels of om het herinrichtings gedrag van uw apparaat te configureren. Als een apparaat is ingesteld op het opnieuw inrichten bij het opstarten, wordt altijd geprobeerd eerst met DPS in te richten en vervolgens terug te vallen naar de inrichtings back-up als dat mislukt. Als een apparaat is ingesteld op dynamisch opnieuw inrichten, wordt IoT Edge opnieuw opgestart en wordt het opnieuw ingericht als er een herinrichtings gebeurtenis wordt gedetecteerd. Zie IoT Hub voor het opnieuw [inrichten van apparaten](../iot-dps/concepts-device-reprovision.md)voor meer informatie.

1. Sla het bestand op en sluit het.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Ken het DPS **-id-bereik** en de **registratie-id** van het apparaat die in de vorige secties zijn verzameld.

1. Open het configuratie bestand op het IoT Edge-apparaat.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Zoek de sectie inrichtings configuraties van het bestand. Verwijder de opmerkingen over de regels voor TPM-inrichting en zorg ervoor dat alle andere inrichtings regels worden uitgeoefend.

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

1. De waarden van `id_scope` en `registration_id` met uw DPS en apparaatgegevens bijwerken.

1. U kunt eventueel ook de sectie modus automatisch opnieuw inrichten van het bestand vinden. Gebruik de `auto_reprovisioning_mode` para meter om het herinrichtings gedrag van uw apparaat te configureren voor `Dynamic` `AlwaysOnStartup` of, of `OnErrorOnly` . Zie IoT Hub voor het opnieuw [inrichten van apparaten](../iot-dps/concepts-device-reprovision.md)voor meer informatie.

1. Sla het bestand op en sluit het.
:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>IoT Edge toegang tot de TPM geven

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

De IoT Edge runtime moet toegang hebben tot de TPM om uw apparaat automatisch in te richten.

U kunt TPM-toegang verlenen aan de IoT Edge-runtime door de systeem instellingen te overschrijven zodat de `iotedge` service bevoegdheden heeft voor het hoofd niveau. Als u de service bevoegdheden niet wilt verhogen, kunt u ook de volgende stappen gebruiken om de toegang tot de TPM hand matig te bieden.

1. Zoek het bestandspad naar de TPM hardware-module op het apparaat en sla het bestand op als een lokale variabele.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Maak een nieuwe regel waarmee de IoT Edge runtime toegang krijgt tot tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Open het regel bestand.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopieer de volgende toegangs gegevens naar het regel bestand.

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

   De uitvoer ziet er als volgt uit:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Als u niet ziet dat de juiste machtigingen zijn toegepast, start u de computer opnieuw op om udev te vernieuwen.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
De IoT Edge runtime is afhankelijk van een TPM-service die de toegang tot de TPM van een apparaat in de Broker heeft. Deze service moet toegang hebben tot de TPM om uw apparaat automatisch in te richten.

U kunt toegang krijgen tot de TPM door de systeem instellingen te overschrijven zodat de `aziottpm` service bevoegdheden heeft voor het hoofd niveau. Als u de service bevoegdheden niet wilt verhogen, kunt u ook de volgende stappen gebruiken om de toegang tot de TPM hand matig te bieden.

1. Zoek het bestandspad naar de TPM hardware-module op het apparaat en sla het bestand op als een lokale variabele.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Maak een nieuwe regel waarmee de IoT Edge runtime toegang krijgt tot tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Open het regel bestand.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopieer de volgende toegangs gegevens naar het regel bestand.

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

   De uitvoer ziet er als volgt uit:

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Als u niet ziet dat de juiste machtigingen zijn toegepast, start u de computer opnieuw op om udev te vernieuwen.
:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>IoT Edge opnieuw opstarten en controleren of de installatie is voltooid

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Start de IoT Edge-runtime opnieuw zodat alle configuratie wijzigingen die u op het apparaat hebt aangebracht, worden opgehaald.

   ```bash
   sudo systemctl restart iotedge
   ```

Controleer of de IoT Edge runtime wordt uitgevoerd.

   ```bash
   sudo systemctl status iotedge
   ```

Raadpleeg daemon-Logboeken.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Als er inrichtings fouten worden weer gegeven, is het mogelijk dat de configuratie wijzigingen nog niet van kracht zijn. Probeer de IoT Edge-daemon opnieuw op te starten.

   ```bash
   sudo systemctl daemon-reload
   ```

Of probeer de virtuele machine opnieuw op te starten om te controleren of de wijzigingen van kracht worden op een nieuwe start.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
De configuratie wijzigingen Toep assen die u hebt aangebracht op het apparaat.

   ```bash
   sudo iotedge config apply
   ```

Controleer of de IoT Edge runtime wordt uitgevoerd.

   ```bash
   sudo iotedge system status
   ```

Raadpleeg daemon-Logboeken.

   ```cmd/sh
   sudo iotedge system logs
   ```

Als er inrichtings fouten worden weer gegeven, is het mogelijk dat de configuratie wijzigingen nog niet van kracht zijn. Probeer de IoT Edge-daemon opnieuw op te starten.

   ```bash
   sudo systemctl daemon-reload
   ```

Of probeer de virtuele machine opnieuw op te starten om te controleren of de wijzigingen van kracht worden op een nieuwe start.
:::moniker-end
<!-- end 1.2 -->

Als de runtime is gestart, kunt u naar uw IoT Hub gaan en zien dat het nieuwe apparaat automatisch is ingericht. Uw apparaat is nu klaar om IoT Edge-modules uit te voeren.

Een lijst met actieve modules weer geven.

```cmd/sh
iotedge list
```

U kunt controleren of de afzonderlijke registratie die u hebt gemaakt in Device Provisioning Service is gebruikt. Navigeer naar het Device Provisioning service-exemplaar in het Azure Portal. Open de inschrijvings gegevens voor de afzonderlijke inschrijving die u hebt gemaakt. U ziet dat de status van de registratie is **toegewezen** en dat de apparaat-id wordt vermeld.

## <a name="next-steps"></a>Volgende stappen

Met het registratie proces DPS kunt u de apparaat-ID en de dubbele Tags van het apparaat instellen op hetzelfde moment als u het nieuwe apparaat inricht. U kunt deze waarden gebruiken om afzonderlijke apparaten of groepen apparaten te richten met behulp van automatische Apparaatbeheer. Meer informatie over [het implementeren en bewaken van IOT Edge modules op schaal met behulp van de Azure Portal](how-to-deploy-at-scale.md) of [met behulp van Azure cli](how-to-deploy-cli-at-scale.md).
