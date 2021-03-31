---
title: Trans formaties van de Azure Kinect sensor SDK gebruiken
description: Meer informatie over het gebruik van de functies van de Azure Kinect sensor SDK-afbeeldings transformatie.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.topic: conceptual
ms.date: 06/26/2019
keywords: kinect, azure, sensor, SDK, coördinaten systeem, kalibratie, project, unproject, trans formatie, RGB-d, Point-Cloud
ms.openlocfilehash: df7f2aa13c0e9c0241494e96e720b30f3ff1d8a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277202"
---
# <a name="use-azure-kinect-sensor-sdk-image-transformations"></a>Trans formaties van de Azure Kinect sensor SDK gebruiken

Volg de specifieke functies voor het gebruiken en transformeren van afbeeldingen tussen gecoördineerde camera systemen in uw Azure Kinect DK.

## <a name="k4a_transformation-functions"></a>k4a_transformation functies

 Alle functies die worden voorafgegaan door *k4a_transformation* worden toegepast op hele installatie kopieën. Ze vereisen dat de transformatie ingang [k4a_transformation_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__transformation__t.html) verkregen via [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10) en niet is toegewezen via [k4a_transformation_destroy ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7d3ecaae66f26c1a89da9042b1bc6d44.html#ga7d3ecaae66f26c1a89da9042b1bc6d44). U kunt ook verwijzen naar het voor beeld van SDK- [trans formatie](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation) dat laat zien hoe u de drie functies in dit onderwerp kunt gebruiken.

De volgende functies worden behandeld:

* [`k4a_transformation_depth_image_to_color_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)
* [`k4a_transformation_depth_image_to_color_camera_custom()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)
* [`k4a_transformation_color_image_to_depth_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)
* [`k4a_transformation_depth_image_to_point_cloud()`](
https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)

### <a name="k4a_transformation_depth_image_to_color_camera"></a>k4a_transformation_depth_image_to_color_camera

#### <a name="overview"></a>Overzicht

 Met de functie [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) wordt de diepte kaart getransformeerd van het gezichts punt van de diepte camera tot het oogpunt van de kleuren camera. Deze functie is ontworpen om zogenaamde RGB-D-afbeeldingen te maken, waarbij D een extra afbeeldings kanaal is dat de diepte waarde vastlegt. Zoals in de afbeelding hieronder wordt weer gegeven, zien de kleuren afbeelding en de uitvoer van [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) eruit alsof ze zijn genomen uit hetzelfde gezichts punt, dat wil zeggen het gezichts punt van de kleuren camera.

   ![Afbeeldings transformatie](./media/how-to-guides/image-transformation.png)

#### <a name="implementation"></a>Implementatie

 Deze transformatie functie is ingewik kelder dan alleen gewoon [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) aan te roepen voor elke pixel. Er wordt een drie hoekige mesh gebogen van de geometrie van de diepte camera naar de geometrie van de kleuren camera. Het drie hoekje wordt gebruikt om te voor komen dat gaten in de getransformeerde diepte afbeelding worden gegenereerd. Een Z-buffer zorgt ervoor dat occlusions correct worden afgehandeld. GPU-versnelling is standaard ingeschakeld voor deze functie.

#### <a name="parameters"></a>Parameters

 Invoer parameters zijn de transformatie-ingang en een diepte afbeelding. De diepte van de afbeelding moet overeenkomen met de ```depth_mode``` opgegeven bij het maken van de transformatie koppeling. Als de transformatie koppeling bijvoorbeeld is gemaakt met behulp van de 1024x1024 ```K4A_DEPTH_MODE_WFOV_UNBINNED``` -modus, moet de resolutie van de diepte afbeelding 1024x1024 pixels zijn. De uitvoer is een getransformeerde diepte afbeelding die door de gebruiker moet worden toegewezen via het aanroepen van [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). De resolutie van de getransformeerde diepte afbeelding moet overeenkomen met de ```color_resolution``` opgegeven bij het maken van de transformatie koppeling. Als de kleur resolutie bijvoorbeeld is ingesteld op `K4A_COLOR_RESOLUTION_1080P` , moet de uitvoer afbeelding 1920 pixels zijn. De uitvoer installatie kopie stride is ingesteld op `width * sizeof(uint16_t)` , omdat de afbeelding 16-bits diepte waarden opslaat.

### <a name="k4a_transformation_depth_image_to_color_camera_custom"></a>k4a_transformation_depth_image_to_color_camera_custom

#### <a name="overview"></a>Overzicht

 Met de functie [k4a_transformation_depth_image_to_color_camera_custom ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb) wordt de diepte kaart en een aangepaste afbeelding van het gezichts punt van de diepte camera getransformeerd tot het oogpunt van de kleuren camera. Als uitbrei ding van [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)is deze functie ontworpen voor het produceren van een bijbehorende aangepaste installatie kopie waarvoor elke pixel overeenkomt met de bijbehorende pixel coördinaten van de kleuren camera, in aanvulling op de getransformeerde diepte afbeelding.

#### <a name="implementation"></a>Implementatie

 Deze transformatie functie produceert de getransformeerde diepte afbeelding op dezelfde manier als [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Als u de aangepaste afbeelding wilt transformeren, biedt deze functie opties voor het gebruik van lineaire interpolatie of de dichtstbijzijnde buur interpolatie. Met lineaire interpolatie kunt u nieuwe waarden maken in de getransformeerde aangepaste afbeelding. Door gebruik te maken van de dichtstbijzijnde buur interpolatie voor komt u dat waarden die niet aanwezig zijn in de oorspronkelijke afbeelding, worden weer gegeven in de uitvoer afbeelding, maar dit resulteert in minder vloeiende afbeelding. De aangepaste installatie kopie moet één kanaal 8-bits of 16-bits zijn. GPU-versnelling is standaard ingeschakeld voor deze functie.

#### <a name="parameters"></a>Parameters

 Invoer parameters zijn de transformatie-ingang, een diepte afbeelding, een aangepaste installatie kopie en het interpolatie type. De diepte afbeelding en aangepaste afbeeldings resolutie moeten overeenkomen met de `depth_mode` opgegeven bij het maken van de transformatie-ingang. Als de transformatie koppeling bijvoorbeeld is gemaakt met behulp van de 1024x1024 `K4A_DEPTH_MODE_WFOV_UNBINNED` -modus, moet de resolutie van de diepte afbeelding en de aangepaste afbeelding 1024x1024 pixels zijn. De `interpolation_type` moet ofwel `K4A_TRANSFORMATION_INTERPOLATION_TYPE_LINEAR` of zijn `K4A_TRANSFORMATION_INTERPOLATION_TYPE_NEAREST` . De uitvoer is een getransformeerde diepte afbeelding en een getransformeerde aangepaste installatie kopie die door de gebruiker moet worden toegewezen via het aanroepen van [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). De resolutie van de getransformeerde diepte afbeelding en getransformeerde aangepaste afbeelding moeten overeenkomen met de `color_resolution` opgegeven bij het maken van de transformatie koppeling. Als de kleur resolutie bijvoorbeeld is ingesteld op `K4A_COLOR_RESOLUTION_1080P` , moet de uitvoer afbeelding 1920 pixels zijn. De uitvoer diepte van de afbeelding stride is ingesteld op `width * sizeof(uint16_t)` , omdat de afbeelding 16-bits diepte waarden opslaat. De aangepaste invoer afbeelding en getransformeerde aangepaste installatie kopie moeten een indeling hebben `K4A_IMAGE_FORMAT_CUSTOM8` of moeten `K4A_IMAGE_FORMAT_CUSTOM16` overeenkomen met de juiste afbeeldings stride. 

### <a name="k4a_transformation_color_image_to_depth_camera"></a>k4a_transformation_color_image_to_depth_camera

#### <a name="overview"></a>Overzicht

 Met de functie [k4a_transformation_color_image_to_depth_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29) wordt de kleuren afbeelding getransformeerd van het gezichts punt van de kleuren camera naar het gezichts punt van de diepte camera (zie afbeelding hierboven). Het kan worden gebruikt voor het genereren van RGB-D-afbeeldingen.

#### <a name="implementation"></a>Implementatie

 Voor elke pixel van de diepte kaart gebruikt de functie de diepte waarde van de pixel voor het berekenen van de corresponderende subpixel coördinaat in de kleuren afbeelding. Vervolgens wordt de kleur waarde bij deze coördinaat in de kleuren afbeelding gezocht. BiLinear interpolatie wordt uitgevoerd in de kleuren afbeelding om de kleur waarde op de precisie van de subpixel te verkrijgen. Een pixel zonder een diep gaande Lees bewerking is toegewezen aan een BGRA-waarde `[0,0,0,0]` in de uitvoer afbeelding. GPU-versnelling is standaard ingeschakeld voor deze functie. Omdat deze methode gaten in de getransformeerde kleuren afbeelding produceert en occlusions niet verwerkt, wordt aangeraden de functie [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) te gebruiken.

#### <a name="parameters"></a>Parameters

De invoer parameters zijn de transformatie-ingang, een diepte afbeelding en een kleuren afbeelding. De resoluties van diepte-en kleuren afbeeldingen moeten overeenkomen met de depth_mode en color_resolution opgegeven bij het maken van de transformatie koppeling. De uitvoer is een getransformeerde kleuren afbeelding die door de gebruiker moet worden toegewezen via het aanroepen van [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). De resolutie van de getransformeerde kleuren afbeelding moet overeenkomen met de depth_resolution die is opgegeven bij het maken van de transformatie koppeling. In de uitvoer installatie kopie worden 4 8-bits waarden opgeslagen voor BGRA voor elke pixel. Daarom is de stride van de afbeelding ```width * 4 * sizeof(uint8_t)``` . De volg orde van de gegevens is pixel Interleaved, dat wil zeggen, blauw waarde/pixel 0, groene waarde-pixel 0, rode waarde-pixel 0, Alfa waarde-pixel 0, blauw waarde-pixel 1, enzovoort.

### <a name="k4a_transformation_depth_image_to_point_cloud"></a>k4a_transformation_depth_image_to_point_cloud

#### <a name="overview"></a>Overzicht

Met de functie [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) wordt een 2D-diepte kaart die is gemaakt door een camera omgezet in een 3D-punt-Cloud in het coördinaten systeem van dezelfde camera. De camera kan de diepte of kleuren camera zijn.

#### <a name="implementation"></a>Implementatie

 De functie geeft gelijkwaardige resultaten voor het uitvoeren van [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) voor elke pixel, maar is een efficiëntere aanpak. Bij het aanroepen van [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10), wordt een zogenaamde XY-opzoek tabel voor de x-en y-schaal voor elke afbeelding pixel vooraf berekend. Wanneer u [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)aanroept, krijgen we de 3D x-coördinaat van een pixel te vermenigvuldigen met de X-schaal factor van de pixel met de Z-coördinaat van de pixel. De 3D Y-coördinaat wordt vergelijkbaar berekend met vermenigvuldiging met de Y-schaal factor. In het [voor beeld van de snelle punt-Cloud](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud) van de SDK ziet u hoe de xy-tabel wordt berekend. Gebruikers kunnen de voorbeeld code volgen om hun eigen versie van deze functie te implementeren, bijvoorbeeld om de GPU-pijp lijn te versnellen.

#### <a name="parameters"></a>Parameters

 De invoer parameters zijn de transformatie-ingang, een camera aanduiding en een diepte afbeelding. Als de camera aanduiding is ingesteld op depth, moet de resolutie van de diepte afbeelding overeenkomen met de depth_mode die is opgegeven bij het maken van de transformatie koppeling. Als de aanduiding is ingesteld op de kleuren camera, moet de oplossing overeenkomen met de resolutie van de gekozen color_resolution. De output-para meter is een XYZ-afbeelding die door de gebruiker moet worden toegewezen via het aanroepen van [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). De XYZ-afbeeldings resolutie moet overeenkomen met de resolutie van de toewijzing van de invoer diepte. We slaan drie ondertekende 16-bits coördinaat waarden op in millimeters voor elke pixel. De XYZ-afbeelding stride wordt daarom ingesteld op `width * 3 * sizeof(int16_t)` . De gegevens volgorde is pixel Interleaved, dat wil zeggen X-coördinaat – pixel 0, Y-coördinaat – pixel 0, Z-coördinaat – pixel 0, X-coördinaat – pixel 1 enzovoort. Als een pixel niet naar 3D kan worden geconverteerd, wijst de functie de waarden `[0,0,0]` toe aan de pixel.

## <a name="samples"></a>Voorbeelden

[Voor beeld van trans formatie](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u de functies van de Azure Kinect sensor SDK-afbeeldings transformatie kunt gebruiken, hebt u ook meer informatie over

>[!div class="nextstepaction"]
>[Kalibratie functies van de Azure Kinect sensor SDK](use-calibration-functions.md)

U kunt ook controleren

[Systemen coördineren](coordinate-systems.md)
