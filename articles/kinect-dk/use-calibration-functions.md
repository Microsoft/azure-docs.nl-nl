---
title: De functies van Azure Kinect-kalibratie gebruiken
description: Meer informatie over het gebruik van de kalibratie functies voor Azure Kinect DK.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, sensor, SDK, coördinaten systeem, kalibratie, functies, camera, intrinsiek, extrinsic, project, unproject, trans formatie, RGB-d, Point-Cloud
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277289"
---
# <a name="use-azure-kinect-calibration-functions"></a>De functies van Azure Kinect-kalibratie gebruiken

Met de kalibratie functies kunnen punten worden getransformeerd tussen de coördinaten systemen van elke sensor op het Azure Kinect-apparaat. Toepassingen waarvoor conversie van hele installatie kopieën nodig is, kunnen profiteren van de versnelde bewerkingen die beschikbaar zijn in [transformatie functies](use-image-transformation.md).

## <a name="retrieve-calibration-data"></a>Kalibratie gegevens ophalen

Het is nood zakelijk om de kalibratie van het apparaat op te halen om de coördinaten systeem transformaties uit te voeren. De kalibratie gegevens worden opgeslagen in het [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) -gegevens type. Het wordt via de functie [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78)van het apparaat opgehaald. De kalibratie gegevens zijn niet alleen specifiek voor elk apparaat, maar ook voor de besturings modus van de camera's. [K4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) vereist daarom de `depth_mode` `color_resolution` para meters en als invoer.

### <a name="opencv-compatibility"></a>OpenCV-compatibiliteit

De kalibratie parameters zijn compatibel met [OpenCV](https://opencv.org/). Zie ook [OpenCV-documentatie](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c)voor meer informatie over de para meters voor de individuele camera kalibratie. Zie ook [OpenCV-compatibiliteit voor beeld](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility) van de SDK die de conversie demonstreert tussen het [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) type en de bijbehorende OpenCV-gegevens structuren.

## <a name="coordinate-transformation-functions"></a>Transformatie functies coördineren

In de afbeelding hieronder ziet u de verschillende coördinaten systemen van Azure Kinect en de functies die ertussen moeten worden geconverteerd. We laten het 3D-coördinaten stelsel van gyroscope en acceleratie-out weg om de afbeelding eenvoudig te houden.

   ![Trans formatie coördineren](./media/how-to-guides/coordinate-transformation.png)

Opmerking over lens vervorming: 2D-coördinaten verwijzen altijd naar de vervormde afbeelding in de SDK. Het [voor beeld](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort) van een niet-verstoring van de SDK illustreert de installatie kopie. Over het algemeen worden 3D-punten nooit beïnvloed door lens vervalsing.

### <a name="convert-between-3d-coordinate-systems"></a>Converteren tussen 3D-coördinaten systemen

Met de functie [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) wordt een 3D-punt van het bron coördinaten systeem geconverteerd naar een 3D-punt van het doel coördinaten systeem met behulp van de extrinsic-kalibratie van de camera. Bron en doel kunnen worden ingesteld op een van de vier 3D-coördinaten systemen, dat wil zeggen, kleur camera, diepte camera, gyroscope of versnellings meter. Als de bron en het doel identiek zijn, wordt het niet-gewijzigde invoer-3D-punt geretourneerd als uitvoer.

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>Converteren tussen 2D-en 3D-coördinaten systemen

Met de functie [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) wordt een 3D-punt van het bron coördinaten systeem geconverteerd naar een coördinaat van een 2D-pixel van de doel camera. Deze functie wordt vaak aangeduid als project-functie. Hoewel de bron kan worden ingesteld op een van de vier 3D-coördinaten systemen, moet het doel de diepte of kleuren camera zijn. Als de bron en het doel verschillend zijn, wordt het 3D-invoer punt naar het 3D-coördinaten systeem van de doel camera geconverteerd met behulp van [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). Zodra het 3D-punt is weer gegeven in het coördinaten systeem van de doel camera, worden de bijbehorende 2D-pixel coördinaten berekend met behulp van de ingebouwde kalibratie van de doel camera. Als een 3D-punt buiten het zicht bare gebied van de doel camera valt, wordt de geldige waarde ingesteld op 0.

Met de functie [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) wordt een coördinaat van een 2D-pixel van de bron camera geconverteerd naar een 3D-punt van het coördinaten systeem van de doel camera. De bron moet een kleuren-of diepte camera zijn. Het doel kan worden ingesteld op een van de vier 3D-coördinaten systemen. Naast de coördinaat van de 2D-pixels, is de diepte waarde van de pixel (in millimeters) in de afbeelding van de bron camera vereist als invoer voor de functie, een manier om de diepte waarde in de geometrie van de kleuren camera te gebruiken om de functie [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)te kunnen afleiden. De functie berekent de 3D-Ray-voor loop van het middel punt van de bron camera tot en met de opgegeven pixel coördinaat met behulp van de ingebouwde kalibratie van de bron camera. De diepte waarde wordt vervolgens gebruikt om de exacte locatie van het 3D-punt op deze Ray te vinden. Deze bewerking wordt vaak de functie unproject genoemd. Als de bron-en doel camera verschillend zijn, transformeert de functie het 3D-punt naar het coördinaten systeem van het doel via [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). Als een coördinaat van een 2D-pixel buiten het zicht bare gebied van de bron camera valt, wordt de geldige waarde ingesteld op 0.

### <a name="converting-between-2d-coordinate-systems"></a>Converteren tussen 2D-coördinaten systemen

Met de functie [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) wordt een coördinaat van een 2D-pixel van de bron camera geconverteerd naar een coördinaat van een 2D-pixel van de doel camera. De bron en het doel moeten worden ingesteld op een kleuren-of diepte camera. Voor de functie is de diepte waarde van de pixel (in millimeters) in de bron camera-afbeelding als invoer vereist. een manier om de diepte waarde in de geometrie van de kleuren camera te gebruiken, is het gebruik van de functie [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Het roept [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) aan om te converteren naar een 3D-punt van het bron camera systeem. Vervolgens roept het [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) aan om te converteren naar een coördinaat van een 2D-pixel van de afbeelding van de doel camera. De geldige waarde is ingesteld op 0, als [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded) of [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) een ongeldig resultaat retourneert.

## <a name="related-samples"></a>Verwante voor beelden

- [Voor beeld van OpenCV-compatibiliteit](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [Voor beeld van onvervalsing](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [Voor beeld van Fast Point-Cloud](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>Volgende stappen

Nu u weet wat camera kalibraties zijn, kunt u ook leren hoe u
>[!div class="nextstepaction"]
>[Apparaatsynchronisatie vastleggen](capture-device-synchronization.md)

U kunt ook controleren

[Systemen coördineren](coordinate-systems.md)
