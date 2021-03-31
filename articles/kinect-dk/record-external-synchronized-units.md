---
title: Azure Kinect-recorder gebruiken met externe gesynchroniseerde apparaten
description: Meer informatie over het vastleggen van gegevens van apparaten die zijn geconfigureerd voor externe synchronisatie met behulp van de Azure Kinect-recorder.
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, sensor, viewer, externe synchronisatie, fase vertraging, diepte, RGB, camera, audio kabel, recorder
ms.openlocfilehash: 052f6f1ac9f90e764de25d1d4d1b25b3d50a848d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277273"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>Azure Kinect-recorder gebruiken met externe gesynchroniseerde apparaten

Dit artikel bevat richt lijnen voor het vastleggen van gegevens externe synchronisatie geconfigureerde apparaten met de [Azure Kinect-recorder](azure-kinect-recorder.md) .

## <a name="prerequisites"></a>Vereisten

- [Stel meerdere Azure KINECT DK-eenheden in voor externe synchronisatie](https://support.microsoft.com/help/4494429).

## <a name="external-synchronization-constraints"></a>Externe synchronisatie beperkingen

- Het hoofd apparaat kan geen synchronisatie IN de kabel verbonden hebben.
- Het hoofd apparaat moet een RGB-camera streamen om synchronisatie mogelijk te maken.
- Alle eenheden moeten gebruikmaken van dezelfde camera configuratie (frame snelheid en resolutie).
- Alle eenheden moeten dezelfde firmware van het apparaat uitvoeren (firmware-instructies[bijwerken](update-device-firmware.md) ).
- Alle onderliggende apparaten moeten voorafgaand aan het hoofd apparaat worden gestart.
- Dezelfde belichtings waarde moet worden ingesteld op alle apparaten.
- De *vertraagde hoofd* instelling van elke onderliggend niveau is relatief ten opzichte van het hoofd apparaat.

## <a name="record-when-each-unit-has-a-host-pc"></a>Record wanneer elke eenheid een host-PC heeft

In het onderstaande voor beeld heeft elk apparaat een eigen toegewezen host-PC.
Het is raadzaam om apparaten te verbinden met toegewezen Pc's om problemen met de USB-band breedte en CPU/GPU-gebruik te voor komen.

### <a name="subordinate-1"></a>Ondergeschikte-1

1. Recorder instellen voor de eerste eenheid

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. Apparaat begint te wachten

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>Onderliggend-2

1. Registratie instellen voor de tweede eenheid

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. Apparaat begint te wachten

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>Master

1. Beginnen met opnemen op het hoofd

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. Wachten tot de opname is voltooid

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>Vastleggen wanneer meerdere eenheden zijn verbonden met een computer met één host

U kunt meerdere Azure Kinect DKs hebben aangesloten op één host-PC. Dit kan echter zeer veeleisend zijn voor USB-band breedte en host compute. De vraag verminderen:

- Elk apparaat verbinden met een eigen USB-hostcontroller.
- Een krachtige GPU hebben die de diepte-engine voor elk apparaat kan verwerken.
- Neem alleen vereiste Sens oren op en gebruik een lagere frame snelheid.

Eerst de onderliggende apparaten en het hoofd model altijd starten.

## <a name="subordinate-1"></a>Ondergeschikte-1

1. Opname starten op onderliggend niveau

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. Het apparaat wordt in de wacht stand gezet

## <a name="master"></a>Master

1. Master-apparaat starten

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. De opname volt ooien

## <a name="playing-recording"></a>Opnamen afspelen

U kunt de [Azure Kinect-Viewer](azure-kinect-viewer.md) gebruiken voor het afspelen van opnamen.



## <a name="tips"></a>Tips

- Gebruik hand matige belichting voor het vastleggen van gesynchroniseerde camera's. De automatische bloot stelling van een RGB-camera kan invloed hebben op de tijd synchronisatie.
- Als onderliggend apparaat opnieuw wordt opgestart, gaat de synchronisatie verloren.
- Sommige [camera modi](hardware-specification.md#depth-camera-supported-operating-modes) ondersteunen 15 fps Max. U kunt het beste geen modi/frame snelheden tussen apparaten mengen
- Wanneer u meerdere eenheden verbindt met één PC, kan de USB-band breedte eenvoudig worden verzadigd. u kunt overwegen om afzonderlijke host-PC'S per apparaat te gebruiken. Let ook op CPU/GPU compute.
- Schakel de microfoon en IMU uit als ze niet nodig zijn om de betrouw baarheid te verbeteren.

Zie [probleem oplossing](troubleshooting.md) voor alle problemen

## <a name="see-also"></a>Zie ook

- [Externe synchronisatie instellen](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- [Azure Kinect-recorder](azure-kinect-recorder.md) voor de instellingen van de recorder en aanvullende informatie.
- [Azure Kinect Viewer](azure-kinect-viewer.md) voor het afspelen van opnamen of het instellen van RGB-camera-eigenschappen die niet beschikbaar zijn via de recorder.
- [Azure Kinect-hulp programma](azure-kinect-firmware-tool.md) voor het bijwerken van de firmware van het apparaat.
