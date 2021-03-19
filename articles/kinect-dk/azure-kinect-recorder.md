---
title: Azure Kinect DK-recorder
description: Meer informatie over het vastleggen van gegevens stromen van de sensor-SDK naar een bestand met behulp van de Azure Kinect-recorder.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, record, Play, Reader, Matroska, MKV, streams, depth, RGB, camera, Color, IMU, audio
ms.openlocfilehash: 5fb6895d4102956a991c67ffab836a26b7a3abb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277302"
---
# <a name="azure-kinect-dk-recorder"></a>Azure Kinect DK-recorder

In dit artikel wordt beschreven hoe u het `k4arecorder` opdracht regel hulpprogramma kunt gebruiken voor het vastleggen van gegevens stromen van de sensor-SDK naar een bestand.

>[!NOTE]
>Audio wordt niet opgenomen in azure Kinect-recorder.

## <a name="recorder-options"></a>Opties voor recorder

De `k4arecorder` heeft verschillende opdracht regel argumenten om het uitvoer bestand en de opname modus op te geven.

Opnamen worden opgeslagen in de [indeling Matroska. MKV](record-file-format.md). De opname maakt gebruik van meerdere video tracks voor kleur en diepte en ook aanvullende informatie, zoals kalibratie van de camera en meta gegevens.

```console
k4arecorder [options] output.mkv

 Options:
  -h, --help              Prints this help
  --list                  List the currently connected K4A devices
  --device                Specify the device index to use (default: 0)
  -l, --record-length     Limit the recording to N seconds (default: infinite)
  -c, --color-mode        Set the color sensor mode (default: 1080p), Available options:
                            3072p, 2160p, 1536p, 1440p, 1080p, 720p, 720p_NV12, 720p_YUY2, OFF
  -d, --depth-mode        Set the depth sensor mode (default: NFOV_UNBINNED), Available options:
                            NFOV_2X2BINNED, NFOV_UNBINNED, WFOV_2X2BINNED, WFOV_UNBINNED, PASSIVE_IR, OFF
  --depth-delay           Set the time offset between color and depth frames in microseconds (default: 0)
                            A negative value means depth frames will arrive before color frames.
                            The delay must be less than 1 frame period.
  -r, --rate              Set the camera frame rate in Frames per Second
                            Default is the maximum rate supported by the camera modes.
                            Available options: 30, 15, 5
  --imu                   Set the IMU recording mode (ON, OFF, default: ON)
  --external-sync         Set the external sync mode (Master, Subordinate, Standalone default: Standalone)
  --sync-delay            Set the external sync delay off the master camera in microseconds (default: 0)
                            This setting is only valid if the camera is in Subordinate mode.
  -e, --exposure-control  Set manual exposure value (-11 to 1) for the RGB camera (default: auto exposure)
```

## <a name="record-files"></a>Record bestanden

Voorbeeld 1. Record depth NFOV unbinned (640x576) modus, RGB 1080p bij 30 fps met IMU.
Druk op **CTRL-C** toetsen om de opname te stoppen.

```
k4arecorder.exe output.mkv
```

Voorbeeld 2. Record WFOV non-binning (1MP), RGB 3072p bij 15 fps zonder IMU, gedurende 10 seconden.

```
k4arecorder.exe -d WFOV_UNBINNED -c 3072p -r 15 -l 10 --imu OFF output.mkv
```

Voor beeld 3. Registreer WFOV 2x2 binning bij 30 fps gedurende 5 seconden en bespaar op uitvoer. MKV.

```
k4arecorder.exe -d WFOV_2X2BINNED -c OFF --imu OFF -l 5 output.mkv
```

>[!TIP]
>U kunt [Azure Kinect Viewer](azure-kinect-viewer.md) gebruiken om de besturings elementen voor de RGB-camera te configureren vóór de opname (bijvoorbeeld om hand matig witbalans in te stellen).

## <a name="verify-recording"></a>Opname controleren

U kunt het bestand output. MKV openen met de [Azure Kinect-Viewer](azure-kinect-viewer.md).

Voor het extra heren van sporen of het weer geven van bestands gegevens, zoals de hulpprogram ma's die `mkvinfo` beschikbaar zijn als onderdeel van de [MKVToolNix](https://mkvtoolnix.download/) Toolkit.

## <a name="next-steps"></a>Volgende stappen

[Recorder gebruiken met externe gesynchroniseerde eenheden](record-external-synchronized-units.md)