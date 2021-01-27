---
title: Meta gegevens schema voor uitvoer Azure Media Services
description: Dit artikel geeft een overzicht van Azure Media Services v3 uitvoer van meta gegevens schema.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1a27db81866760bff56c900a908162f356c01a66
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895200"
---
# <a name="output-metadata"></a>Uitvoermetagegevens

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Een coderings taak is gekoppeld aan een invoer element (of activa) waarvoor u bepaalde coderings taken wilt uitvoeren. U kunt bijvoorbeeld een MP4-bestand coderen naar H. 264 MP4 Adaptive bitrate sets; Maak een miniatuur. Maak overlays. Wanneer een taak is voltooid, wordt een uitvoer activum geproduceerd.  Het uitvoer element bevat video, audio, miniaturen en andere bestanden. Het uitvoer activum bevat ook een bestand met meta gegevens over het uitvoer element. De naam van het JSON-bestand met meta gegevens heeft de volgende indeling: `<source_file_name>_manifest.json` (bijvoorbeeld `BigBuckBunny_manifest.json` ). U moet zoeken naar een * _metadata.jsen een query uitvoeren op de filepath teken reeks in om de bron bestandsnaam te vinden (zonder afkap ping).

Media Services de invoer assets niet preventief om meta gegevens te genereren. Invoer meta gegevens worden alleen gegenereerd als een artefact wanneer een invoer element in een taak wordt verwerkt. Dit artefact wordt daarom geschreven naar de uitvoer Asset. Er worden verschillende hulpprogram ma's gebruikt voor het genereren van meta gegevens voor invoer assets en uitvoer assets. Daarom heeft de invoer meta gegevens een iets ander schema dan de uitvoer meta gegevens.

In dit artikel worden de elementen en typen van het JSON-schema beschreven waarop de meta gegevens van de uitvoer ( &lt; source_file_name &gt;_manifest.js) is gebaseerd. <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

Aan het eind van dit artikel vindt u de volledige schema code en het JSON-voor beeld.  

## <a name="assetfile"></a>AssetFile

Verzameling van AssetFile-vermeldingen voor de coderings taak.  

| Naam | Beschrijving |
| --- | --- |
| **Bronnen** |Verzameling van invoer/bron media bestanden, die zijn verwerkt om deze AssetFile te maken.<br />Voorbeeld: `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|Elk fysiek AssetFile kan in de waarde nul bevatten of meer Video's worden getraceerd in een geschikte container indeling. <br />Zie [VideoTracks](#videotracks). |
| **AudioTracks**|Elk fysiek AssetFile kan in de waarde nul of meer audio tracks Interleaved in een geschikte container indeling bevatten. Dit is de verzameling van al deze audio sporen.<br /> Zie [AudioTracks](#audiotracks)voor meer informatie. |
| **Naam**<br />Vereist |De bestands naam voor het Media-activum. <br /><br />Voorbeeld: `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **Grootte**<br />Vereist |Grootte van het activa bestand in bytes. <br /><br />Voorbeeld: `"Size": 32414631`|
| **Duur**<br />Vereist |Duur van het afspelen van inhoud. Zie de [iso8601](https://www.iso.org/iso-8601-date-and-time-format.html) -indeling voor meer informatie. <br /><br />Voorbeeld: `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

Elk fysiek AssetFile kan in de waarde nul bevatten of meer Video's worden getraceerd in een geschikte container indeling. Het **VideoTracks** -element vertegenwoordigt een verzameling van alle video tracks.  

| Naam | Beschrijving |
| --- | --- |
| **Id**<br /> Vereist |Op nul gebaseerde index van deze video track. **Opmerking:**  Deze **id** is niet noodzakelijkerwijs de TrackID die in een MP4-bestand wordt gebruikt. <br /><br />Voorbeeld: `"Id": 1`|
| **FourCC**<br />Vereist | Een code van de video-codec die wordt gerapporteerd door ffmpeg.  <br /><br />Voorbeeld: `"FourCC": "avc1"`|
| **Profiel** |H264-Profiel (alleen van toepassing op H264-codec).  <br /><br />Voorbeeld: `"Profile": "High"` |
| **Afvlakking** |Niveau H264 (alleen van toepassing op H264-codec).  <br /><br />Voorbeeld: `"Level": "3.2"`|
| **Breedte**<br />Vereist |Breedte van gecodeerde video in pixels.  <br /><br />Voorbeeld: `"Width": "1280"`|
| **Hoogte**<br />Vereist |Versleutelde video hoogte in pixels.  <br /><br />Voorbeeld: `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />Vereist|Teller voor hoogte-breedte verhouding video weergave.  <br /><br />Voorbeeld: `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominator**<br />Vereist |Noemer van hoogte-breedte verhouding video weergave.  <br /><br />Voorbeeld: `"DisplayAspectRatioDenominator": 9.0`|
| **Framesnelheid**<br />Vereist |Gemeten video frame frequentie in. 3F-indeling.  <br /><br />Voorbeeld: `"Framerate": 29.970`|
| **Bitsnelheid**<br />Vereist |De gemiddelde bitsnelheid van video in bits per seconde, zoals berekend op basis van de AssetFile. Telt alleen de elementaire stream Payload en bevat niet de verpakkings belasting.  <br /><br />Voorbeeld: `"Bitrate": 3551567`|
| **TargetBitrate**<br />Vereist |De gemiddelde bitrate van het doel voor dit video spoor, zoals aangevraagd via de voor instelling voor versleuteling, in bits per seconde. <br /><br />Voorbeeld: `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTracks 

Elk fysiek AssetFile kan in de waarde nul of meer audio tracks Interleaved in een geschikte container indeling bevatten. Het **AudioTracks** -element vertegenwoordigt een verzameling van al deze audio sporen.  

| Naam  | Beschrijving |
| --- | --- |
| **Id**<br />Vereist  |Op nul gebaseerde index van dit audio spoor. **Opmerking:**  Dit is niet noodzakelijkerwijs de TrackID die in een MP4-bestand wordt gebruikt.  <br /><br />Voorbeeld: `"Id": 2`|
| **Videocodec**  |Teken reeks van codec audio track.  <br /><br />Voorbeeld: `"Codec": "aac"`|
| **Taal**|Voorbeeld: `"Language": "eng"`|
| **Kanalen**<br />Vereist|Aantal audio kanalen.  <br /><br />Voorbeeld: `"Channels": 2`|
| **SamplingRate**<br />Vereist |Audio sampling frequentie in samples/sec of Hz.  <br /><br />Voorbeeld: `"SamplingRate": 48000`|
| **Bitsnelheid**<br />Vereist |De gemiddelde bitsnelheid van audio in bits per seconde, zoals berekend op basis van de AssetFile. Telt alleen de elementaire stream Payload en bevat niet de verpakkings belasting.  <br /><br />Voorbeeld: `"Bitrate": 128041`|

## <a name="json-schema-example"></a>Voor beeld van JSON-schema

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

[Een taak invoer maken op basis van een HTTPS-URL](job-input-from-http-how-to.md)
