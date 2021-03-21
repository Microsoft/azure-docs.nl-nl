---
title: Automatisch Windows-apparaten inrichten met DPS-Azure IoT Edge | Microsoft Docs
description: Een gesimuleerd apparaat op uw Windows-machine gebruiken om het automatisch inrichten van apparaten te testen voor Azure IoT Edge met Device Provisioning Service
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 11279c552cb599c24b72473d6574175450ca7ab0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200854"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Een gesimuleerd IoT Edge-apparaat maken en inrichten met een virtuele TPM in Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge apparaten kunnen automatisch worden ingericht met behulp van de [Device Provisioning Service](../iot-dps/index.yml) , net zoals apparaten die niet Edge-ingeschakeld zijn. Als u niet bekend bent met het proces van automatische inrichting, bekijkt u het overzicht voor [inrichting](../iot-dps/about-iot-dps.md#provisioning-process) voordat u verdergaat.

DPS ondersteunt symmetrische sleutel attest voor IoT Edge-apparaten in zowel individuele inschrijving als groeps inschrijving. Als u bij het inschrijven van de groep de optie ' is IoT Edge apparaat ' moet worden ingesteld op True voor symmetrische-sleutel attest, worden alle apparaten die zijn geregistreerd onder die registratie groep als IoT Edge-apparaten gemarkeerd.

In dit artikel leest u hoe u automatisch inrichten op een gesimuleerd IoT Edge apparaat kunt testen met de volgende stappen:

* Maak een instantie van IoT Hub Device Provisioning Service (DPS).
* Maak een gesimuleerd apparaat op uw Windows-computer met een gesimuleerde Trusted Platform Module (TPM) voor hardware-beveiliging.
* Een afzonderlijke inschrijving voor het apparaat maken.
* Installeer de IoT Edge runtime en verbind het apparaat met IoT Hub.

> [!TIP]
> In dit artikel wordt beschreven hoe u het automatisch inrichten kunt testen met behulp van TPM-Attestation op virtuele apparaten, maar dit is ook van toepassing bij gebruik van fysieke TPM-hardware.

## <a name="prerequisites"></a>Vereisten

* Een Windows-ontwikkel computer. In dit artikel wordt gebruikgemaakt van Windows 10.
* Een actieve IoT Hub.

> [!NOTE]
> TPM 2,0 is vereist voor het gebruik van TPM-Attestation met DPS en kan alleen worden gebruikt voor het maken van afzonderlijke, niet-groeps registraties.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>De IoT Hub Device Provisioning Service instellen

Maak een nieuw exemplaar van de IoT Hub Device Provisioning Service in Azure en koppel deze aan uw IoT-hub. U kunt de instructies voor [het instellen van de IOT hub DPS](../iot-dps/quick-setup-auto-provision.md)volgen.

Nadat u de Device Provisioning Service hebt uitgevoerd, kopieert u de waarde van **id-bereik** van de pagina overzicht. U gebruikt deze waarde bij het configureren van de IoT Edge runtime.

> [!TIP]
> Als u een fysiek TPM-apparaat gebruikt, moet u de **goedkeurings sleutel** bepalen, die uniek is voor elke TPM-chip en die is verkregen van de fabrikant van de TPM-chip die eraan is gekoppeld. U kunt een unieke **registratie-id** voor uw TPM-apparaat afleiden door bijvoorbeeld een SHA-256-hash van de goedkeurings sleutel te maken.
>
> Volg de instructies in het artikel de registratie van [apparaten beheren met Azure Portal](../iot-dps/how-to-manage-enrollments.md) om uw inschrijving in DPS te maken en ga vervolgens verder met de sectie [de IOT Edge runtime installeren](#install-the-iot-edge-runtime) in dit artikel om door te gaan.

## <a name="simulate-a-tpm-device"></a>TPM-apparaat simuleren

Maak een gesimuleerd TPM-apparaat op uw Windows-ontwikkel computer. Haal de **registratie-id** en **goedkeurings sleutel** voor uw apparaat op en gebruik deze om een afzonderlijke inschrijvings vermelding in DPS te maken.

Wanneer u een inschrijving in DPS maakt, hebt u de mogelijkheid om een **eerste dubbele toestand** van het apparaat te declareren. Op het apparaat dubbele kunt u Tags instellen om apparaten te groeperen op elke gewenste metrische waarde in uw oplossing, zoals regio, omgeving, locatie of apparaattype. Deze tags worden gebruikt voor het maken van [automatische implementaties](how-to-deploy-at-scale.md).

Kies de SDK-taal die u wilt gebruiken voor het maken van het gesimuleerde apparaat en volg de stappen totdat u de afzonderlijke inschrijving hebt gemaakt.

Wanneer u de afzonderlijke inschrijving maakt, selecteert u **waar** om te declareren dat het gesimuleerde TPM-apparaat op uw Windows-ontwikkel computer een **IOT edge apparaat** is.

> [!TIP]
> In de Azure CLI kunt u een [inschrijving](/cli/azure/ext/azure-iot/iot/dps/enrollment) of een [registratie groep](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) maken en de vlag voor **rand ingeschakeld** gebruiken om op te geven dat een apparaat, of groep apparaten, een IOT edge apparaat is.

Gesimuleerd apparaat en afzonderlijke registratie handleidingen:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Nadat u de afzonderlijke inschrijving hebt gemaakt, slaat u de waarde van de **registratie-id** op. U gebruikt deze waarde bij het configureren van de IoT Edge runtime.

## <a name="install-the-iot-edge-runtime"></a>De IoT Edge-runtime installeren

De IoT Edge-runtime wordt op alle IoT Edge-apparaten geïmplementeerd. De onderdelen worden in containers uitgevoerd en bieden u de mogelijkheid om extra containers op het apparaat te implementeren, zodat u code aan de rand kunt uitvoeren. Installeer de IoT Edge runtime op het apparaat waarop de gesimuleerde TPM wordt uitgevoerd.

Volg de stappen in [install the Azure IOT Edge runtime](how-to-install-iot-edge.md)en ga vervolgens terug naar dit artikel om het apparaat in te richten.

> [!TIP]
> Houd het venster dat de TPM-Simulator uitvoert tijdens uw installatie en testen.

## <a name="configure-the-device-with-provisioning-information"></a>Het apparaat configureren met inrichtings gegevens

Zodra de runtime op uw apparaat is geïnstalleerd, configureert u het apparaat met de informatie die wordt gebruikt om verbinding te maken met Device Provisioning Service en IoT Hub.

1. Ken het DPS **-id-bereik** en de **registratie-id** van het apparaat die in de vorige secties zijn verzameld.

1. Open een Power shell-venster in de beheerders modus. Zorg ervoor dat u een AMD64-sessie van Power shell gebruikt bij het installeren van IoT Edge, niet in Power shell (x86).

1. Met de opdracht **Deploy-IoTEdge** wordt gecontroleerd of de Windows-computer een ondersteunde versie heeft, wordt de functie containers ingeschakeld, waarna de Moby-runtime en de IOT Edge-runtime worden gedownload. De opdracht wordt standaard ingesteld op het gebruik van Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Op dit moment kunnen IoT-kern apparaten automatisch opnieuw worden opgestart. Windows 10-of Windows Server-apparaten vragen u mogelijk om opnieuw op te starten. Als dit het geval is, start u het apparaat nu opnieuw op. Zodra het apparaat klaar is, voert u Power shell als beheerder opnieuw uit.

1. Met de opdracht **Initialize-IoTEdge** configureert u de IoT Edge-runtime op uw machine. De opdracht wordt standaard ingesteld op handmatig inrichten met Windows-containers. Gebruik de `-Dps` markering om de Device Provisioning Service te gebruiken in plaats van hand matig inrichten.

   Vervang de waarden van de tijdelijke aanduidingen voor `{scope_id}` en `{registration_id}` door de gegevens die u eerder hebt verzameld.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Geslaagde installatie controleren

Als de runtime is gestart, kunt u naar uw IoT Hub gaan en IoT Edge modules op het apparaat implementeren. Gebruik de volgende opdrachten op het apparaat om te controleren of de runtime is geïnstalleerd en is gestart.  

Controleer de status van de IoT Edge-service.

```powershell
Get-Service iotedge
```

Bekijk service Logboeken in de afgelopen vijf minuten.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Een lijst met actieve modules weer geven.

```powershell
iotedge list
```

## <a name="next-steps"></a>Volgende stappen

Met het inschrijvings proces voor Device Provisioning Service kunt u de apparaat-ID en de dubbele Tags van het apparaat instellen op hetzelfde moment als u het nieuwe apparaat inricht. U kunt deze waarden gebruiken om afzonderlijke apparaten of groepen apparaten te richten met behulp van automatische Apparaatbeheer. Meer informatie over [het implementeren en bewaken van IOT Edge modules op schaal met behulp van de Azure Portal](how-to-deploy-at-scale.md) of [met Azure cli](how-to-deploy-cli-at-scale.md)
