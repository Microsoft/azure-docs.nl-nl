---
title: Voor instellingen voor taken voor Media Encoder Standard (MES) | Microsoft Docs
description: In het onderwerp vindt u een overzicht van de door de service gedefinieerde voor instellingen voor Media Encoder Standard (MES).
author: IngridAtMicrosoft
manager: femila
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 74e557aa02f5ab83828262209653bf8a78fdd2d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011643"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Voor beeld van voor instellingen voor Media Encoder Standard (MES)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

**Media Encoder Standard** definieert een reeks vooraf gedefinieerde instellingen voor systeem codering die u kunt gebruiken bij het maken van coderings taken. Het is raadzaam om de voor instelling ' adaptieve streaming ' te gebruiken als u een video wilt coderen voor streamen met Media Services. Wanneer u deze voor instelling opgeeft, wordt door Media Encoder Standard [automatisch een bitrate voor een bitsnelheid gegenereerd](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Aangepaste voor instellingen maken op basis van voor beelden
Media Services volledig ondersteunt het aanpassen van alle waarden in voor instellingen om te voldoen aan uw specifieke behoeften en vereisten voor de code ring. Als u een vooraf ingestelde code ring wilt aanpassen, moet u beginnen met een van de onderstaande systeem voorinstellingen die in deze sectie zijn opgenomen als een sjabloon voor uw aangepaste configuratie. Zie het onderwerp [Media Encoder Standard schema](media-services-mes-schema.md) voor uitleg over wat elk-element in deze voor instellingen betekent en de geldige waarden voor elk element.  
  
> [!NOTE]
>  Wanneer u een voor instelling voor 4 KB-code ringen gebruikt, moet u het `S3` gereserveerde eenheids type ophalen. Zie [How to scale encoding](./media-services-scale-media-processing-overview.md)(Engelstalig) voor meer informatie.  

#### <a name="video-rotation-default-setting-in-presets"></a>Standaard instelling voor het draaien van video in voor instellingen:
Wanneer u werkt met Media Encoder Standard, wordt de rotatie van de video standaard ingeschakeld. Als uw video is vastgelegd op een mobiel apparaat in de modus Staand, worden deze voor waarden door deze voor waarden naar de liggende modus geroteerd vóór de code ring.
 
## <a name="available-presets"></a>Beschik bare voor instellingen: 

 [H264 multiple bitrate 1080P Audio 5,1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) produceert een set met 8 GOP terug-afgevulde MP4-bestanden, variërend van 6000 kbps tot 400 kbps en AAC 5,1-audio.  
  
 [H264 multiple bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) produceert een set van 8 GOP terug-afgevulde MP4-bestanden, variërend van 6000 kbps tot 400 kbps en stereo AAC-audio.  
  
 [H264 multiple bitrate 16x9 voor IOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) produceert een set van 8 GOP terug-afgevulde MP4-bestanden, variërend van 8500 kbps tot 200 kbps en stereo AAC-audio.  
  
 [H264 multiple bitrate 16X9 SD Audio 5,1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) produceert een set van 5 GOP terug-afgevulde MP4-bestanden, variërend van 1900 kbps tot 400 kbps en AAC 5,1-audio.  
  
 [H264 multiple bitrate 16X9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) produceert een set van 5 GOP terug-afgevulde MP4-bestanden, variërend van 1900 kbps tot 400 kbps en stereo AAC-audio.  
  
 [H264 multiple bitrate van 4.000% 5,1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) produceert een set van 12 GOP terug-bestanden met de namen, variërend van 20000 kbps tot 1000 kbps en AAC 5,1-audio.  
  
 [H264 meerdere bitrate 4.000](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) produceert een set van 12 GOP terug-afgevulde MP4-bestanden, variërend van 20000 kbps tot 1000 kbps en stereo AAC-audio.  
  
 [H264 multiple bitrate 4x3 voor IOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) produceert een set van 8 GOP terug-afgevulde MP4-bestanden, variërend van 8500 kbps tot 200 kbps en stereo AAC-audio.  
  
 [H264 multiple bitrate 4X3 SD Audio 5,1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) produceert een set van 5 GOP terug-afgevulde MP4-bestanden, variërend van 1600 kbps tot 400 kbps en AAC 5,1-audio.  
  
 [H264 multiple bitrate 4X3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) produceert een set van 5 GOP terug-afgevulde MP4-bestanden, variërend van 1600 kbps tot 400 kbps en stereo AAC-audio.  
  
 [H264 multiple bitrate 720P Audio 5,1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) produceert een set van 6 GOP terug-afgevulde MP4-bestanden, variërend van 3400 kbps tot 400 kbps en AAC 5,1-audio.  
  
 [H264 multiple bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) produceert een set van 6 GOP terug-afgevulde MP4-bestanden, variërend van 3400 kbps tot 400 kbps en stereo AAC-audio.  
  
 [H264 single bitrate 1080P Audio 5,1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) produceert één MP4-bestand met een Bitrate van 6750 kbps en AAC 5,1-audio.  
  
 [H264 single bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) produceert één MP4-bestand met een Bitrate van 6750 kbps en stereo AAC-audio.  
  
 [H264 single bitrate 1 KB-audio 5,1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) produceert één MP4-bestand met een Bitrate van 18000 kbps en AAC 5,1-audio.  
  
 [H264 single-bitrate 4.000](media-services-mes-preset-H264-Single-Bitrate-4K.md) produceert één MP4-bestand met een Bitrate van 18000 kbps en stereo AAC-audio.  
  
 [H264 single bitrate 4X3 SD Audio 5,1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) produceert één MP4-bestand met een Bitrate van 1800 kbps en AAC 5,1-audio.  
  
 [H264 single bitrate 4X3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) produceert één MP4-bestand met een Bitrate van 1800 kbps en stereo AAC-audio.  
  
 [H264 single bitrate 16X9 SD Audio 5,1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) produceert één MP4-bestand met een Bitrate van 2200 kbps en AAC 5,1-audio.  
  
 [H264 single bitrate 16X9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) produceert één MP4-bestand met een Bitrate van 2200 kbps en stereo AAC-audio.  
  
 [H264 single bitrate 720P Audio 5,1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) produceert één MP4-bestand met een Bitrate van 4500 kbps en AAC 5,1-audio.  
  
 [H264 single bitrate 720p voor Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) -voor instelling produceert één MP4-bestand met een Bitrate van 2000 kbps en stereo AAC.  
  
 [H264 single bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) produceert één MP4-bestand met een Bitrate van 4500 kbps en stereo AAC-audio.  
  
 [H264 single bitrate SD van hoge kwaliteit voor Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) produceert één MP4-bestand met een Bitrate van 500 Kbps en stereo AAC-audio.  
  
 [H264 single bitrate SD van de lage kwaliteit voor Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) produceert één MP4-bestand met een Bitrate van 56 kbps en stereo AAC-audio.  
  
 Zie voor meer informatie met betrekking tot Media Services coderings Programma's een [code ring op aanvraag met Azure Media Services](./media-services-encode-asset.md).
