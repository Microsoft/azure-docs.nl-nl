---
title: De Azure Kinect sensor SDK gebruiken voor het vastleggen van de bestands indeling
description: Meer informatie over het gebruik van de Azure Kinect sensor SDK vastgelegde bestands indeling.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: reference
keywords: kinect, azure, sensor, SDK, depth, RGB, record, afspelen, Matroska, MKV
ms.openlocfilehash: f4fa14b0841cb76b2ba191310ecbca312d29f805
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654588"
---
# <a name="use-azure-kinect-sensor-sdk-to-record-file-format"></a>De Azure Kinect sensor SDK gebruiken voor het vastleggen van de bestands indeling

Om sensor gegevens vast te leggen, wordt de container indeling Matroska (. MKV) gebruikt, zodat meerdere sporen kunnen worden opgeslagen met behulp van een breed scala aan codecs. Het opname bestand bevat sporen voor het opslaan van kleur, diepte, IR-installatie kopieën en IMU.

Details op laag niveau van de MKV-container indeling kunt u vinden op de [Matroska-website](https://www.matroska.org/index.html).

| Naam bijhouden | Codec-indeling                          |
|------------|---------------------------------------|
| Kleur      | Mode-Dependent (MJPEG, NV12 of YUY2) |
| DIEPGA      | b16g (16-bits grijs waarde, big-endian)   |
| IR         | b16g (16-bits grijs waarde, big-endian)   |
| IMU        | Aangepaste structuur, zie de [voorbeeld structuur IMU](record-file-format.md#imu-sample-structure) hieronder. |

## <a name="using-third-party-tools"></a>Hulpprogram ma's van derden gebruiken

Hulpprogram ma's zoals `ffmpeg` of de `mkvinfo` opdracht uit de [MKVToolNix](https://mkvtoolnix.download/) Toolkit kunnen worden gebruikt voor het weer geven en ophalen van gegevens uit de opname bestanden.

Met de volgende opdracht wordt bijvoorbeeld het diepte spoor geëxtraheerd als een reeks 16-bits PNGs naar dezelfde map:

```
ffmpeg -i output.mkv -map 0:1 -vsync 0 depth%04d.png
```

De `-map 0:1` para meter extraheert de tracerings index 1, die voor de meeste opnamen dieper is. Als de opname geen kleuren spoor bevat, `-map 0:0` wordt gebruikt.

De `-vsync 0` para meter forceert ffmpeg om frames te extra heren in plaats van een frame snelheid van 30 fps, 15 fps of 5 fps te zoeken.

## <a name="imu-sample-structure"></a>Voorbeeld structuur van IMU

Als IMU gegevens uit het bestand worden opgehaald zonder de API voor afspelen te gebruiken, zijn de gegevens in binaire vorm.
De structuur van de IMU-gegevens vindt u hieronder. Alle velden zijn little endian.

| Veld                        | Type     |
|------------------------------|----------|
| Time Stamp van versnellings meter (μs) | uint64   |
| Acceleratie gegevens (x, y, z) | float [3] |
| Gyroscope-tijds tempel (μs)     | uint64   |
| Gyroscope-gegevens (x, y, z)     | float [3] |

## <a name="identifying-tracks"></a>Identificerende sporen

Het kan nodig zijn om te identificeren welk spoor kleur, diepte, IR, enzovoort bevat. Het identificeren van de sporen is nodig bij het werken met hulpprogram ma's van derden om een Matroska-bestand te lezen.
Nummers volgen variëren op basis van de camera modus en de set ingeschakelde tracks. Tags worden gebruikt om de betekenis van elk spoor te identificeren.

De lijst met tags hieronder zijn gekoppeld aan een specifiek Matroska-element en kan worden gebruikt om de bijbehorende track of bijlage op te zoeken.

Deze tags kunnen worden weer gegeven met hulpprogram ma's zoals `ffmpeg` en `mkvinfo` .
De volledige lijst met tags wordt weer gegeven op de pagina [record en afspelen](record-playback-api.md) .

| Tagnaam             | Label doel             | Label waarde             |
|----------------------|------------------------|-----------------------|
| K4A_COLOR_TRACK      | Kleur spoor            | Matroska bijhouden    |
| K4A_DEPTH_TRACK      | Diepte spoor            | Matroska bijhouden    |
| K4A_IR_TRACK         | IR-spoor               | Matroska bijhouden    |
| K4A_IMU_TRACK        | IMU-spoor              | Matroska bijhouden    |
| K4A_CALIBRATION_FILE | Kalibratie bijlage | Bestands naam van bijlage   |

## <a name="next-steps"></a>Volgende stappen

[Opnemen en afspelen](record-playback-api.md)
