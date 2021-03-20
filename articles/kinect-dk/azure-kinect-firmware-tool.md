---
title: Azure Kinect-firmwarehulpprogramma
description: Meer informatie over het uitvoeren van query's en het bijwerken van de firmware van een apparaat met het hulp programma Azure Kinect firmware.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, firmware, update
ms.openlocfilehash: f8b89751d27fb5a4b18d635f45f63f4f36bd05f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277310"
---
# <a name="azure-kinect-dk-firmware-tool"></a>Hulp programma Azure Kinect DK-firmware

Het hulp programma Azure Kinect-firmware kan worden gebruikt om de firmware van het apparaat van Azure Kinect DK te doorzoeken en bij te werken.

## <a name="list-connected-devices"></a>Verbonden apparaten weer geven

U kunt een lijst met verbonden apparaten ophalen met behulp van de-l-optie.  `AzureKinectFirmwareTool.exe -l`

```console
 == Azure Kinect DK Firmware Tool ==
Found 2 connected devices:
0: Device "000036590812"
1: Device "000274185112"
```

## <a name="check-device-firmware-version"></a>Controleer de versie van de firmware van het apparaat

U kunt de huidige firmware versies van het eerste aangesloten apparaat controleren met behulp van de optie-q, bijvoorbeeld `AzureKinectFirmwareTool.exe -q` .

```console
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000036590812
Current Firmware Versions:
    RGB camera firmware:      1.5.92
    Depth camera firmware:    1.5.66
    Depth config file:        6109.7
    Audio firmware:           1.5.14
    Build Config:             Production
    Certificate Type:         Microsoft
```

Als er meer dan één apparaat is gekoppeld, kunt u opgeven welk apparaat u wilt doorzoeken door het volledige serie nummer toe te voegen aan de opdracht, zoals:

`AzureKinectFirmwareTool.exe -q 000036590812`

## <a name="update-device-firmware"></a>Firmware van apparaat bijwerken

Het meest voorkomende gebruik van dit hulp programma is het bijwerken van de firmware van het apparaat. Voer de update uit door het hulp programma aan te roepen met behulp van de `-u` optie. Het kan enkele minuten duren voordat een firmware-update is uitgevoerd, afhankelijk van de firmware bestanden die moeten worden bijgewerkt.

Zie [Azure Kinect firmware update](update-device-firmware.md)(Engelstalig) voor stapsgewijze instructies voor het bijwerken van firmware.  

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

Als er meer dan één apparaat is gekoppeld, kunt u opgeven welk apparaat u wilt doorzoeken door het volledige serie nummer toe te voegen aan de opdracht.

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin 000036590812`

## <a name="reset-device"></a>Apparaat opnieuw instellen

Een gekoppelde Azure Kinect DK kan opnieuw worden ingesteld met behulp van de optie-r als u het apparaat in een bekende status moet ontvangen.

Als er meer dan één apparaat is gekoppeld, kunt u opgeven welk apparaat u wilt doorzoeken door het volledige serie nummer toe te voegen aan de opdracht.

`AzureKinectFirmwareTool.exe -r 000036590812`

## <a name="inspect-firmware"></a>Firmware inspecteren

Door firmware te controleren, kunt u de versie gegevens van een firmware-bin-bestand ophalen voordat u een echt apparaat bijwerkt.

`AzureKinectFirmwareTool.exe -i firmware\AzureKinectDK_Fw_1.5.926614.bin`

```console
 == Azure Kinect DK Firmware Tool ==
Loading firmware package ..\tools\updater\firmware\AzureKinectDK_Fw_1.5.926614.bin.
File size: 1228844 bytes
This package contains:
  RGB camera firmware:      1.5.92
  Depth camera firmware:    1.5.66
  Depth config files: 6109.7 5006.27
  Audio firmware:           1.5.14
  Build Config:             Production
  Certificate Type:         Microsoft
  Signature Type:           Microsoft
```

## <a name="firmware-update-tool-options"></a>Opties firmware-update programma

```console
 == Azure Kinect DK Firmware Tool ==
* Usage Info *
    AzureKinectFirmwareTool.exe <Command> <Arguments>

Commands:
    List Devices: -List, -l
    Query Device: -Query, -q
        Arguments: [Serial Number]
    Update Device: -Update, -u
        Arguments: <Firmware Package Path and FileName> [Serial Number]
    Reset Device: -Reset, -r
        Arguments: [Serial Number]
    Inspect Firmware: -Inspect, -i
        Arguments: <Firmware Package Path and FileName>

    If no Serial Number is provided, the tool will just connect to the first device.

Examples:
    AzureKinectFirmwareTool.exe -List
    AzureKinectFirmwareTool.exe -Update c:\data\firmware.bin 0123456
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Stapsgewijze instructies voor het bijwerken van de firmware van een apparaat](update-device-firmware.md)
