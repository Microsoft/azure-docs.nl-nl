---
title: Windows-vergelijking van Azure Kinect DK
description: Hardware-en software verschillen tussen Azure Kinect DK en Kinect voor Windows v2
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect, Windows, v2, Azure Kinect, Comparison, SDK, verschillen, hardware, software
ms.openlocfilehash: 0a8d399370f354524858bdd658ffd65c0494dd4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "87031572"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Vergelijking van Azure Kinect en Kinect Windows v2

De hardware-en software development kits van Azure Kinect DK hebben verschillen van Kinect voor Windows v2. Bestaande Kinect voor Windows v2-toepassingen werken niet rechtstreeks met Azure Kinect DK en moeten poort van de nieuwe SDK gebruiken.  

## <a name="hardware"></a>Hardware

In de volgende tabel worden de belangrijkste verschillen tussen de Azure Kinect Development Kit en Kinect voor Windows v2 vermeld.

| Functie | Type | Azure Kinect DK | Kinect voor Windows v2 |
| ------- | ---- | --------------- | --------------------- |
| **Audio** | Details  | 7-Mic-cirkel vormige matrix | 4-Mic, lineaire, gefaseerde matrix |
| **Bewegings sensor** | Details | 3-as Gyro voor de 3-as | versnellings meter van 3 assen |
| **RGB-camera**    | Details | 3840 x 2160 PX @30 fps | 1920 x 1080 PX @30 fps |
| **Diepte camera**  | Methode   | Tijdstip van de vlucht | Tijdstip van de vlucht |
|                   | Oplossing | 640 x 576 PX @30 fps | 512 x 424 PX bij 30 fps |
|                   |            | 512 x 512 PX @30 fps |                       |
|                   |            | 1024x1024 PX @15 fps |                       |
| **Connectiviteit** | Gegevens | USB 3.1 gen 1 met type USB-C  | USB 3,1 gen 1|
|  | Stroom | Externe PSU of USB-C | Externe PSU |
|  | Synchronisatie | RGB-& diepte intern, extern apparaat-naar-apparaat| RGB-& diepte alleen intern |
| **Kunde** | Dimensies | 103 x 39 x 126 mm | 249 x 66 x 67 mm |
|  | Kansdichtheids | 440 g | 970 g |
| | Muur | Een 1/4-20-UNC. Vier interne schroef punten | Een 1/4-20-UNC |

Meer informatie vindt u in het document [Azure KINECT DK-hardware](hardware-specification.md) .

## <a name="sensor-access"></a>Sensor toegang

De volgende tabel bevat een vergelijking van de toegangs mogelijkheden op laag niveau voor de sensor.

| **Functionaliteit**| **Azure Kinect** | **Kinect voor Windows** | **Opmerkingen** |
|---------|---------|------------|---------|
| **Diepga** | ✔️ | ✔️ |    |   |
| **FO** | ✔️ | ✔️ |  |
| **Kleur** | ✔️ | ✔️ | Kleur indeling ondersteunt verschillen, Azure Kinect DK ondersteunt de volgende camera besturings elementen: bloot stelling, witbalans, helderheid, contrast, verzadiging, scherpte en controle |
| **Audio** | ✔️ | ✔️ | Azure Kinect DK mics wordt geopend via Speech SDK of Windows native API |
| **IMU** | ✔️ |  | Azure Kinect DK heeft een volledige IMU voor 6 assen en Kinect voor Windows biedt alleen 1-as |
| **Kalibratie gegevens** | ✔️ | ✔️ | Kalibratie van OpenCV-compatibel camera model |
| **Diepte: interne RGB-synchronisatie** | ✔️ | ✔️ |  |
| **Externe synchronisatie**| ✔️|  | Azure Kinect DK staat Programmeer bare vertraging toe voor externe synchronisatie |
| **Toegang delen met meerdere clients** | | ✔️ | De Azure Kinect sensor SDK is afhankelijk van WinUSB/libUSB om toegang te krijgen tot het apparaat en er is geen service geïmplementeerd om het delen van apparaten met meerdere processen mogelijk te maken |
| **Hulp programma voor stream record/afspelen** | ✔️ | ✔️ | Azure Kinect DK maakt gebruik van een open-source Matroska-implementatie op basis van een container |

## <a name="features"></a>Functies

De functieset van Azure Kinect SDK wijkt af van Kinect voor Windows v2, zoals hieronder wordt beschreven:

| **Kinect v2-functie** | **Kinect v2-gegevens type** | **Azure Kinect SDK/Service** |
|--------|--------|------|
| Sensor gegevens toegang |DepthFrame| [Sensor-SDK-installatie kopieën ophalen](retrieve-images.md) 
| |InfraredFrame | [Sensor-SDK-installatie kopieën ophalen](retrieve-images.md) 
| | ColorFrame | [Sensor-SDK-installatie kopieën ophalen](retrieve-images.md) | 
| | AudioBeamFrame |Momenteel niet ondersteund 
| Bijhouden van hoofd tekst | BodyFrame | Body tracking SDK |
| | BodyIndexFrame | Body tracking SDK  |
| Toewijzing van coördinaten|CoordinateMapper| [Sensor SDK-afbeeldings transformaties](use-image-transformation.md) |
|Gezichts tracking | FaceFrame | [Cognitive Services: Face](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    Spraak herkenning    |    N.v.t.                      |    [Cognitive Services: spraak](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>Volgende stappen

[Kinect voor Windows-ontwikkelaars pagina's](https://developer.microsoft.com/windows/kinect)
