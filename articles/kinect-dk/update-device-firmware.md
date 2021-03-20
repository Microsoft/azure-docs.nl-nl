---
title: Azure Kinect DK-firmware bijwerken
description: Meer informatie over het bijwerken van de firmware van het Azure Kinect DK-apparaat met het hulp programma Azure Kinect firmware.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, firmware, update, herstel
ms.openlocfilehash: 71557798a584635356cc47fd7c4a0309df4d018a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94356220"
---
# <a name="update-azure-kinect-dk-firmware"></a>Azure Kinect DK-firmware bijwerken

Dit document bevat richt lijnen voor het bijwerken van de firmware van een apparaat in azure Kinect DK.

Firmware wordt niet automatisch bijgewerkt met Azure Kinect DK. U kunt het [hulp programma Azure Kinect-firmware](azure-kinect-firmware-tool.md) gebruiken om firmware hand matig bij te werken naar de meest recente beschik bare versie.

## <a name="prepare-for-firmware-update"></a>Firmware-update voorbereiden

1. [SDK downloaden](sensor-sdk-download.md).
2. Installeer de SDK.
3. Ga naar de installatie locatie van de SDK onder (SDK-installatie locatie) \tools\:

    - AzureKinectFirmwareTool.exe
    - Een firmware. bin-bestand in de map firmware, zoals *AzureKinectDK_Fw_1.5.926614. bin*.

4. Verbind uw apparaat met de host van de PC en sluit het ook uit.

> [!IMPORTANT]
> Zorg ervoor dat de USB-en energie voeding verbonden is tijdens de firmware-update. Als u een verbinding tijdens de update verwijdert, wordt de status van de firmware beschadigd.

## <a name="update-device-firmware"></a>Firmware van apparaat bijwerken

1. Open een opdracht prompt in de map (SDK-installatie locatie) \tools\
2. Firmware bijwerken met het hulp programma Azure Kinect-firmware

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    Voorbeeld:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. Wacht tot de firmware-update is voltooid. Het kan een paar minuten duren, afhankelijk van de grootte van de afbeelding.

### <a name="verify-device-firmware-version"></a>De firmware versie van het apparaat controleren

1. Controleer of de firmware is bijgewerkt.

    `AzureKinectFirmwareTool.exe -q`

2. Bekijk het volgende voor beeld.

    ```console
       >AzureKinectFirmwareTool.exe -q
       == Azure Kinect DK Firmware Tool ==
        Device Serial Number: 000805192412
        Current Firmware Versions:
        RGB camera firmware:      1.6.102
        Depth camera firmware:    1.6.75
        Depth config file:        6109.7
        Audio firmware:           1.6.14
        Build Config:             Production
        Certificate Type:         Microsoft
    ```

3. Als de bovenstaande uitvoer wordt weer geven, wordt de firmware bijgewerkt.

4. Na de firmware-update kunt u [Azure Kinect Viewer](azure-kinect-viewer.md) uitvoeren om te controleren of alle Sens oren werken zoals verwacht.

## <a name="troubleshooting"></a>Problemen oplossen

Firmware-updates kunnen om verschillende redenen mislukken. Wanneer een firmware-update mislukt, voert u de volgende stappen uit:

1. Probeer de opdracht firmware-update een tweede keer uit te voeren.

2. Controleer of het apparaat nog steeds is verbonden door de firmware-versie te doorzoeken.        AzureKinectFirmareTool.exe

3. Als dat niet het geval is, volgt u de stappen voor het [herstellen](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) van de firmware van de fabriek en probeert u het opnieuw.

Ga voor meer problemen naar [micro soft-ondersteunings pagina's](./index.yml)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Hulp programma Azure Kinect-firmware](azure-kinect-firmware-tool.md)