---
title: Video's bijsnijden met Media Encoder Standard-Azure | Microsoft Docs
description: Bijsnijden is het proces van het selecteren van een rechthoekig venster binnen het video frame en het coderen van alleen de pixels in het venster. In dit artikel wordt beschreven hoe u Video's bijsnijdt met Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 6a15e57884f85e2d56b77c3e5eec1267133fe96d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016726"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Video’s bijsnijden met Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

U kunt Media Encoder Standard (MES) gebruiken om uw invoer video bij te snijden. Bijsnijden is het proces van het selecteren van een rechthoekig venster binnen het video frame en het coderen van alleen de pixels in het venster. Het volgende diagram illustreert het proces.

![Een video bijsnijden](./media/media-services-crop-video/media-services-crop-video01.png)

Stel, u hebt als invoer een video met een resolutie van 1920 pixels (verhouding van 16:9 hoogte), maar heeft zwarte balken aan de linkerkant en rechts, zodat alleen een 4:3-venster of 1440x1080 pixels actieve video bevatten. U kunt MES gebruiken om de zwarte balken te bijsnijden of te bewerken en de regio 1440x1080 te coderen.

Bijsnijden in MES is een pre-verwerkings fase, waardoor de bijsnijd parameters in de voor instelling voor versleuteling van toepassing zijn op de oorspronkelijke invoer video. Encoding is een volgende fase en de instellingen voor breedte/hoogte zijn van toepassing op de *vooraf verwerkte* video en niet op de oorspronkelijke video. Bij het ontwerpen van uw voor instelling moet u het volgende doen: (a) Selecteer de snij parameters op basis van de oorspronkelijke invoer video en (b) Selecteer uw coderings instellingen op basis van de bijgesneden video. Als u de versleutelings instellingen niet overeenkomt met de bijgesneden video, is de uitvoer niet zoals verwacht.

In het [volgende](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) onderwerp ziet u hoe u een coderings taak met mes maakt en hoe u een aangepaste voor instelling voor de coderings taak opgeeft. 

## <a name="creating-a-custom-preset"></a>Een aangepaste voor instelling maken
In het voor beeld dat in het diagram wordt weer gegeven:

1. De oorspronkelijke invoer is 1920
2. Het moet worden bijgesneden tot een uitvoer van 1440x1080, die in het invoer kader is gecentreerd
3. Dit betekent een X-verschuiving van (1920 – 1440)/2 = 240 en een Y-verschuiving van nul
4. De breedte en hoogte van de rechthoek voor bijsnijden zijn respectievelijk 1440 en 1080
5. In de fase versleutelen is de vraag om drie lagen te produceren, respectievelijk oplossingen 1440x1080, 960x720 en 480x360

### <a name="json-preset"></a>JSON-voor instelling

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Crop": {
          "X": 240,
          "Y": 0,
          "Width": 1440,
          "Height": 1080
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1440,
          "Height": 1080,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1250,
          "MaxBitrate": 1250,
          "BufferWindow": "00:00:05",
          "Width": 480,
          "Height": 360,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="restrictions-on-cropping"></a>Beperkingen bij bijsnijden
De functie voor bijsnijden is bedoeld om hand matig te zijn. U moet uw invoer video in een geschikt bewerkings programma laden waarmee u interessante frames kunt selecteren, de cursor kunt positioneren om de verplaatsings rechthoeken te bepalen, om te bepalen welke coderings voorinstelling is afgestemd op die specifieke video, enzovoort. Deze functie is niet bedoeld voor het inschakelen van dingen als: automatische detectie en verwijdering van Black letterbox/pillarbox-randen in uw invoer video.

De volgende beperkingen zijn van toepassing op de functie voor bijsnijden. Als niet aan deze voor waarden wordt voldaan, kan de versleutelings taak mislukken of wordt er een onverwachte uitvoer gegenereerd.

1. De co-coördinaten en de grootte van de rechthoek voor bijsnijden moeten binnen de invoer video passen
2. Zoals hierboven vermeld, moet de breedte & hoogte in de instellingen voor code ring overeenkomen met de bijgesneden video
3. Bijsnijden is van toepassing op Video's die zijn vastgelegd in de modus Liggend (dat wil zeggen niet van toepassing op Video's die zijn opgenomen met een smartphone die verticaal of in de modus Staand is opgeslagen)
4. Werkt het beste met progressieve video die is vastgelegd met vier kante pixels

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Volgende stap
Zie Azure Media Services leer trajecten om meer te weten te komen over fantastische functies die worden aangeboden door AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
