---
title: Windows-apparaten automatisch inrichten met DPS - Azure IoT Edge | Microsoft Docs
description: Een gesimuleerd apparaat op uw Windows-computer gebruiken om automatische apparaatinrichting te testen voor Azure IoT Edge Device Provisioning Service
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 82bd027773a5759caee19228f56ba4b3dfe8c2cf
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482004"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Een gesimuleerd apparaat IoT Edge met een virtuele TPM in Windows maken en inrichten

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge kunnen automatisch worden ingericht met [Device Provisioning Service,](../iot-dps/index.yml) net als apparaten die niet aan de rand zijn ingeschakeld. Als u niet bekend bent met het proces van automatische inrichting, bekijkt u het overzicht voor [inrichting](../iot-dps/about-iot-dps.md#provisioning-process) voordat u verdergaat.

DPS ondersteunt attestation met symmetrische sleutels IoT Edge apparaten in zowel afzonderlijke inschrijving als groepsinschrijving. Als u voor groepsinschrijving de optie 'is IoT Edge device' in de attestation met symmetrische sleutels incheckt, worden alle apparaten die zijn geregistreerd onder die registratiegroep gemarkeerd als IoT Edge apparaten.

In dit artikel wordt beschreven hoe u automatische inrichting op een gesimuleerd apparaat IoT Edge testen met de volgende stappen:

* Maak een exemplaar van IoT Hub Device Provisioning Service (DPS).
* Maak een gesimuleerd apparaat op uw Windows-computer met een gesimuleerde Trusted Platform Module (TPM) voor hardwarebeveiliging.
* Maak een afzonderlijke inschrijving voor het apparaat.
* Installeer de IoT Edge runtime en verbind het apparaat met IoT Hub.

> [!TIP]
> In dit artikel wordt beschreven hoe u automatisch inrichten kunt testen met behulp van TPM-attestation op virtuele apparaten, maar veel ervan is ook van toepassing wanneer u fysieke TPM-hardware gebruikt.

## <a name="prerequisites"></a>Vereisten

* Een Windows-ontwikkelmachine. In dit artikel wordt gebruikgemaakt Windows 10.
* Een actief IoT Hub.

> [!NOTE]
> TPM 2.0 is vereist bij het gebruik van TPM Attestation met DPS en kan alleen worden gebruikt voor het maken van afzonderlijke, niet groepsinschrijvingen.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>De IoT Hub Device Provisioning Service

Maak een nieuw exemplaar van de IoT Hub Device Provisioning Service in Azure en koppel deze aan uw IoT-hub. U kunt de instructies in [Set up the IoT Hub DPS (Dps instellen) volgen.](../iot-dps/quick-setup-auto-provision.md)

Nadat u Device Provisioning Service hebt uitgevoerd, kopieert u de waarde van **Id-bereik** op de overzichtspagina. U gebruikt deze waarde wanneer u de IoT Edge configureert.

> [!TIP]
> Als u een fysiek TPM-apparaat gebruikt, moet u de goedkeuringssleutel **bepalen.** Deze sleutel is uniek voor elke TPM-chip en wordt verkregen van de TPM-chipfabrikant die ermee is gekoppeld. U kunt een unieke **registratie-id** voor uw TPM-apparaat afleiden door bijvoorbeeld een SHA-256-hash van de goedkeuringssleutel te maken.
>
> Volg de instructies in het artikel Apparaatinschrijvingen beheren met [Azure Portal](../iot-dps/how-to-manage-enrollments.md) om uw inschrijving in DPS te maken en ga vervolgens verder met de sectie De [IoT Edge-runtime](#install-the-iot-edge-runtime) installeren in dit artikel om door te gaan.

## <a name="simulate-a-tpm-device"></a>TPM-apparaat simuleren

Maak een gesimuleerd TPM-apparaat op uw Windows-ontwikkelmachine. Haal de **registratie-id** **en goedkeuringssleutel** voor uw apparaat op en gebruik deze om een afzonderlijke inschrijvingsinvoer in DPS te maken.

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om de status van een initiële **apparaat-dubbel te declaren.** In de apparaat dubbel kunt u tags instellen om apparaten te groepen op metrische gegevens die u nodig hebt in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt om automatische [implementaties te maken.](how-to-deploy-at-scale.md)

Kies de SDK-taal die u wilt gebruiken om het gesimuleerde apparaat te maken en volg de stappen totdat u de afzonderlijke inschrijving maakt.

Wanneer u de afzonderlijke inschrijving  maakt, selecteert u Waar om aan te geven dat het gesimuleerde TPM-apparaat op uw Windows-ontwikkelmachine een IoT Edge **is.**

> [!TIP]
> In de Azure CLI kunt [](/cli/azure/iot/dps/enrollment) u een [](/cli/azure/iot/dps/enrollment-group) inschrijving of een registratiegroep maken en de vlag Edge **gebruiken** om op te geven dat een apparaat of groep apparaten een IoT Edge is.

Gesimuleerde apparaat- en afzonderlijke inschrijvingshandleidingen:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Nadat u de afzonderlijke inschrijving hebt aanmaken, moet u de waarde van de **registratie-id opslaan.** U gebruikt deze waarde wanneer u de IoT Edge configureert.

## <a name="install-the-iot-edge-runtime"></a>De IoT Edge-runtime installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen worden uitgevoerd in containers en bieden u de mogelijkheid om extra containers op het apparaat te implementeren, zodat u code aan de rand kunt uitvoeren. Installeer de IoT Edge runtime op het apparaat met de gesimuleerde TPM.

Volg de stappen in [De Azure IoT Edge runtime installeren](how-to-install-iot-edge.md)en ga vervolgens terug naar dit artikel om het apparaat in terichten.

> [!TIP]
> Houd het venster waarin de TPM-simulator wordt uitgevoerd geopend tijdens de installatie en het testen.

## <a name="configure-the-device-with-provisioning-information"></a>Het apparaat configureren met inrichtingsgegevens

Zodra de runtime op uw apparaat is geïnstalleerd, configureert u het apparaat met de informatie die wordt gebruikt om verbinding te maken met Device Provisioning Service en IoT Hub.

1. Weet uw **DPS-id-bereik** en **apparaatregistratie-id** die in de vorige secties zijn verzameld.

1. Open een PowerShell-venster in de beheerdersmodus. Zorg ervoor dat u een AMD64-sessie van PowerShell gebruikt bij het installeren van IoT Edge, niet powershell (x86).

1. De **opdracht Deploy-IoTEdge** controleert of uw Windows-computer een ondersteunde versie heeft, schakelt de functie containers in en downloadt vervolgens de moby-runtime en de IoT Edge runtime. De opdracht wordt standaard ingesteld op het gebruik van Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Op dit moment kunnen IoT Core-apparaten automatisch opnieuw worden opgestart. Windows 10 of Windows Server-apparaten kunt u vragen opnieuw op te starten. Als dat het zo is, start u het apparaat nu opnieuw op. Zodra uw apparaat gereed is, moet u PowerShell opnieuw uitvoeren als beheerder.

1. Met de opdracht **Initialize-IoTEdge** configureert u de IoT Edge-runtime op uw machine. De opdracht wordt standaard ingesteld op handmatig inrichten met Windows-containers. Gebruik de `-Dps` vlag om Device Provisioning Service te gebruiken in plaats van handmatig in terichten.

   Vervang de tijdelijke aanduidingen voor `{scope_id}` en door de gegevens die u eerder hebt `{registration_id}` verzameld.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Controleren of de installatie is geslaagd

Als de runtime is gestart, kunt u naar uw IoT Hub en beginnen met het implementeren IoT Edge modules op uw apparaat. Gebruik de volgende opdrachten op uw apparaat om te controleren of de runtime is geïnstalleerd en gestart.  

Controleer de status van de IoT Edge-service.

```powershell
Get-Service iotedge
```

Bekijk de servicelogboeken van de afgelopen vijf minuten.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lijst met modules die worden uitgevoerd.

```powershell
iotedge list
```

## <a name="next-steps"></a>Volgende stappen

Met het registratieproces van Device Provisioning Service kunt u de apparaat-id en apparaattweetags instellen op hetzelfde moment dat u het nieuwe apparaat inrichten. U kunt deze waarden gebruiken voor afzonderlijke apparaten of groepen apparaten met behulp van automatisch apparaatbeheer. Meer informatie over [het implementeren en bewaken IoT Edge modules op schaal](how-to-deploy-at-scale.md) met behulp van de Azure Portal of azure [CLI](how-to-deploy-cli-at-scale.md)
