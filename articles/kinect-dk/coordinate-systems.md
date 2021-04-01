---
title: Azure Kinect DK-coördinaten systemen
description: Beschrijving van het coördinaten systeem van Azure Kinect DK dat is gekoppeld aan Azure DK Sens oren
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, sensor, SDK, diepte camera, tof, principes, prestaties, ongeldigheid
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277198"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Azure Kinect DK-coördinaten systemen

In dit artikel worden de conventies beschreven die worden gebruikt voor 2D-en 3D-coördinaten systemen.  Er zijn afzonderlijke coördinaten systemen gekoppeld aan het apparaat van elke sensor en de [kalibratie functies](use-calibration-functions.md) waarmee punten tussen de apparaten kunnen worden getransformeerd. Met de [transformatie functies](use-image-transformation.md) worden volledige afbeeldingen tussen coördinaten systemen getransformeerd.  

## <a name="2d-coordinate-systems"></a>2D-coördinaten systemen

 Zowel de diepte-als kleuren camera's zijn gekoppeld aan een onafhankelijk 2D-coördinaten systeem. Een coördinaat van [x, y] wordt weer gegeven in eenheden van pixels waarbij *x* een bereik van 0 tot breedte-1 en *y* bereiken van 0 tot hoogte-1. De breedte en hoogte zijn afhankelijk van de gekozen modus waarin diepte en kleuren camera's worden gebruikt. De pixel coördinaat `[0,0]` komt overeen met de linkerbovenhoek van de afbeelding. Pixel coördinaten kunnen deel uitmaken van de coördinaten van subpixel.

Het 2D-coördinaten systeem is 0-gecentreerd, dat wil zeggen, de coördinaat van de subpixel `[0.0, 0.0]` vertegenwoordigt het midden en `[0.5,0.5]` de rechter benedenhoek van de pixel, zoals hieronder weer gegeven.

   ![2D-coördinaten systeem](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>3D-coördinaten systemen

Elke camera, de versnellings meter en de gyroscope, zijn gekoppeld aan een onafhankelijk 3D-coördinaten ruimte systeem.

Punten in de 3D-coördinaten systemen worden weer gegeven als metrische [X, Y, Z]-triplets coördinaat met eenheden in millimeters.

### <a name="depth-and-color-camera"></a>Diepte en kleuren camera

De oorsprong `[0,0,0]` bevindt zich op het brand punt van de camera. Het coördinaten systeem is zodanig gericht dat de positieve X-as wijst, de positieve Y-as naar beneden en de positieve punten van de Z-as naar voren.

De diepte camera wordt 6 graden omlaag gekanteld van de kleuren camera, zoals hieronder wordt weer gegeven. 

Er worden twee verlichting gebruikt door de diepte camera. De verlichtings modus die wordt gebruikt in de smalle weergave modi (NFOV) is afgestemd op de diepte van de camera, waardoor de lamper niet gekanteld is. De lamper die in de WFOV-modus (Wide Field-of-View) wordt gebruikt, is een extra 1,3 graden omlaag gekanteld ten opzichte van de diepte camera.

![conventies voor 3D-coördinaat](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>Gyroscope en versnellings meter

De oorsprong van gyroscope `[0,0,0]` is gelijk aan de oorsprong van de diepte camera. De oorsprong van de versnellings meter valt samen met de fysieke locatie. Zowel de versnellings meter als de gyroscope-coördinaten worden rechts handig. De positieve X-as van het coördinaten systeem achterwaarts, de positieve Y-as wijst naar links en de positieve Z-as omlaag, zoals hieronder wordt weer gegeven.

![IMU-coördinaten systeem](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over de Azure Kinect sensor SDK](about-sensor-sdk.md)