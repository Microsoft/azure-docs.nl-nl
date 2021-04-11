---
title: Miniaturen genereren met behulp van Media Encoder Standard REST
description: In dit artikel wordt beschreven hoe u REST kunt gebruiken om een Asset te coderen en tegelijkertijd miniaturen te genereren met behulp van Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 88e19a3153c5a0368bc35d07b5e525e51b9b92da
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281891"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>Miniaturen genereren met behulp van coderings standaard met REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

U kunt Media Encoder Standard gebruiken om een of meer miniaturen te genereren op basis van uw invoer video in [JPEG](https://en.wikipedia.org/wiki/JPEG)-, [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)-of [BMP](https://en.wikipedia.org/wiki/BMP_file_format) -afbeeldings bestands indelingen.

## <a name="recommended-reading-and-practice"></a>Aanbevolen Lees-en praktijk

Het is raadzaam dat u bekend raakt met aangepaste trans formaties door [te lezen hoe u codeert met een aangepaste transform-rest](encode-custom-preset-rest-how-to.md).

## <a name="thumbnail-parameters"></a>Miniatuur parameters

U moet de volgende para meters instellen:

- **Start** : de positie in de invoer video van waaruit u miniaturen wilt gaan genereren. De waarde kan de ISO 8601-indeling hebben (bijvoorbeeld PT05S om 5 seconden te beginnen) of een aantal frames (bijvoorbeeld 10 om te beginnen bij het tiende frame) of een relatieve waarde voor de stroom duur (bijvoorbeeld 10% om te beginnen bij 10% van de duur van de stroom). Biedt ook ondersteuning voor een macro {best}, waarmee wordt aangegeven dat het coderings programma de beste miniatuur van de eerste paar seconden van de video selecteert en slechts één miniatuur produceert, ongeacht welke andere instellingen voor stap en bereik zijn. De standaard waarde is macro {best}.
- **stap** -de intervallen waarop miniaturen worden gegenereerd. De waarde kan de ISO 8601-indeling hebben (bijvoorbeeld elke vijf seconden voor één afbeelding) of een aantal frames (bijvoorbeeld 30 voor de ene afbeelding elke 30 frames) of een relatieve waarde voor de stroom duur (bijvoorbeeld 10% voor één afbeelding elke 10% van de duur van de stroom). De stap waarde is van invloed op de eerst gegenereerde miniatuur, die mogelijk niet exact hetzelfde is als de begin tijd van de voor instelling voor transformeren. Dit wordt veroorzaakt door de encoder, waarmee wordt geprobeerd de beste miniatuur te selecteren tussen de begin tijd en de positie van de stap vanaf de begin tijd als de eerste uitvoer. Omdat de standaard waarde 10% is, betekent dit dat als de stroom een lange duur heeft, de eerste gegenereerde miniatuur mogelijk ver weg is van de tijd die is opgegeven tijdens het starten. Probeer een redelijke waarde voor stap te selecteren als de eerste miniatuur naar verwachting dicht bij de begin tijd is of stel de bereik waarde in op 1 als er tijdens de begin tijd slechts één miniatuur nodig is.
- **bereik** : de positie ten opzichte van de vooraf ingestelde begin tijd van de trans formatie in de invoer video waarbij het genereren van miniaturen moet worden gestopt. De waarde kan de ISO 8601-indeling hebben (bijvoorbeeld PT5M30S om 5 minuten en 30 seconden vanaf de begin tijd te stoppen), of een aantal frames (bijvoorbeeld 300 om tijdens het starten op het frame 300th te stoppen. Als deze waarde 1 is, betekent dit dat er slechts één miniatuur tijdens de start tijd wordt geproduceerd) of een relatieve waarde aan de stroom duur (bijvoorbeeld 50% om de helft van de stroom duur vanaf de begin tijd te stoppen). De standaard waarde is 100%, wat betekent dat de gegevens aan het einde van de stroom moeten worden gestopt.
- **lagen** : een verzameling uitvoer afbeeldings lagen die moeten worden geproduceerd door de encoder.

## <a name="example-of-a-single-png-file-preset"></a>Voor beeld van een standaard instelling voor één PNG-bestand

De volgende JSON-standaard instelling kan worden gebruikt voor het maken van een PNG-bestand met enkele uitvoer van de eerste paar seconden van de invoer video, waarbij het coderings programma een beste poging doet bij het zoeken naar een interessant frame. Houd er rekening mee dat de afmetingen van de uitvoer afbeelding zijn ingesteld op 100%, wat betekent dat ze overeenkomen met de afmetingen van de invoer video. Houd er ook rekening mee dat de instelling ' notatie ' in ' uitvoer ' vereist is om te voldoen aan het gebruik van ' PngLayers ' in de sectie ' codecs '.  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Voor beeld van een standaard instelling voor een ' reeks JPEG-afbeeldingen '

De volgende JSON-standaard instelling kan worden gebruikt voor het produceren van een set van 10 afbeeldingen met tijds tempels van 5%, 15%,..., 95% van de invoer tijdlijn, waarbij de grootte van de afbeelding is ingesteld op één kwar taal van de invoer video.

### <a name="json-preset"></a>JSON-voor instelling

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Voor beeld van een definitie van een installatie kopie met een specifieke tijds tempel

De volgende JSON-standaard instelling kan worden gebruikt om één JPEG-afbeelding te maken met de 30-seconde van de invoer video. Deze vooraf ingesteld verwacht dat de invoer video meer dan 30 seconden lang is (anders mislukt de taak).

### <a name="json-preset"></a>JSON-voor instelling

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "00:00:30",
      "Step": "1",
      "Range": "1",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Voor beeld van een ' miniaturen met verschillende resoluties '

De volgende vooraf ingestelde voor instelling kan worden gebruikt om miniaturen te genereren met verschillende resoluties in één taak. In het voor beeld, op posities 5%, 15%,..., 95% van de invoer tijdlijn, genereert het coderings programma twee installatie kopieën: één op 100% van de video resolutie voor invoer en de andere op 50%.

Let op het gebruik van de macro {resolution} in de bestands naam; Hiermee wordt aangegeven dat het coderings programma de breedte en hoogte moet gebruiken die u hebt opgegeven in de sectie encoding van de voor instelling tijdens het genereren van de bestands naam van de uitvoer installatie kopieën. Dit helpt u ook om onderscheid te maken tussen de verschillende installatie kopieën.

### <a name="json-preset"></a>JSON-voor instelling

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "100%",
  "Height": "100%"
},
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "50%",
  "Height": "50%"
}

      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
      "Format": {
"Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Voor beeld van het genereren van een miniatuur tijdens het coderen

Hoewel al deze voor beelden hebben besproken hoe u een coderings taak kunt verzenden die alleen installatie kopieën produceert, kunt u ook video-en audio codering combi neren met het genereren van miniaturen. De volgende JSON-standaard instelling vertelt coderings norm voor het genereren van een miniatuur tijdens het coderen.

### <a name="json-preset"></a>JSON-voor instelling

Zie dit artikel voor meer informatie over [het](../previous/media-services-mes-schema.md) schema.

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": "true",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 4500,
          "MaxBitrate": 4500,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cabac",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"

        }
      ],
      "Type": "H264Video"
    },
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    },
    {
      "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen
[Miniaturen genereren met behulp van .NET](transform-generate-thumbnails-dotnet-how-to.md)