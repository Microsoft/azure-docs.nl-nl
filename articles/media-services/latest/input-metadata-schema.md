---
title: Azure Media Services v3 invoer schema voor meta gegevens
description: Dit artikel bevat een overzicht van Azure Media Services v3 invoer van meta gegevens schema.
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
ms.openlocfilehash: 0acb882410d103cf6f6c34bbecf2006094437b04
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102634682"
---
# <a name="input-metadata"></a>Invoermetagegevens

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Een coderings taak is gekoppeld aan een invoer element (of activa) waarvoor u bepaalde coderings taken wilt uitvoeren.  Wanneer een taak is voltooid, wordt een uitvoer activum geproduceerd. Het uitvoer element bevat video, audio, miniaturen, manifest en andere bestanden. 

Het uitvoer activum bevat ook een bestand met meta gegevens over de invoer Asset. De naam van het JSON-bestand met meta gegevens heeft een wille keurige ID en mag niet worden gebruikt om de invoer activa te identificeren waarvan het uitvoer activum deel uitmaakt. Als u het element van de invoer wilt identificeren, gebruikt u het `Uri` veld (Zie [andere onderliggende elementen](#other-child-elements)voor meer informatie).  

Media Services de invoer assets niet preventief om meta gegevens te genereren. Invoer meta gegevens worden alleen gegenereerd als een artefact wanneer een invoer element in een taak wordt verwerkt. Dit artefact wordt daarom geschreven naar de uitvoer Asset. Er worden verschillende hulpprogram ma's gebruikt voor het genereren van meta gegevens voor invoer assets en uitvoer assets. Daarom heeft de invoer meta gegevens een iets ander schema dan de uitvoer meta gegevens.

In dit artikel worden de elementen en typen van het JSON-schema beschreven waarop de invoer metada ( &lt; asset_id &gt;_metadata.js) is gebaseerd. Zie [uitvoer van meta gegevens](output-metadata-schema.md)voor informatie over het bestand dat meta gegevens bevat over het uitvoer element.  

Aan het einde van dit artikel vindt u het voor beeld van een JSON-schema.  

## <a name="assetfile"></a>AssetFile  

Bevat een verzameling AssetFile-elementen voor de coderings taak.  

> [!NOTE]
> De volgende vier onderliggende elementen moeten in een reeks worden weer gegeven.

| Naam  | Beschrijving |
| --- | --- | 
| **VideoTracks**|Elk fysiek-activa bestand kan nul of meer video tracks bevatten die Interleaved zijn in een geschikte container indeling. Zie [VideoTracks](#videotracks)voor meer informatie. |
| **AudioTracks**|Elk fysiek-activa bestand kan nul of meer audio tracks Interleaved in een geschikte container indeling bevatten. Zie [AudioTracks](#audiotracks) voor meer informatie. |
| **Metagegevens**  |Meta gegevens van Asset file worden weer gegeven als key\value-teken reeksen. <br />Bijvoorbeeld: `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>Andere onderliggende elementen

| Naam | Beschrijving |
| --- | --- |
| **Naam**<br />Vereist |Bestands naam van het activum. <br /><br />Voorbeeld: `"Name": "Ignite-short.mp4"` |
| **Uri**<br />Vereist |De URL waar het invoer element zich bevindt. Gebruik het `Uri` veld in plaats van id om de input-Asset bij te horen waarvan het uitvoer element deel uitmaakt.|
| **Grootte**<br />Vereist |Grootte van het activa bestand in bytes.  <br /><br />Voorbeeld: `"Size": 75739259`|
| **Duur**<br />Vereist |Duur van het afspelen van inhoud. <br /><br />Bijvoorbeeld: `"Duration": "PT1M10.304S"`. |
| **NumberOfStreams**<br />Vereist |Aantal streams in het activa bestand.  <br /><br />Voorbeeld: `"NumberOfStreams": 2`|
| **FormatNames**<br />Vereist |Indelings namen.  <br /><br />Voorbeeld: `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **FormatVerboseName**<br /> Vereist |Uitgebreide namen opmaken. <br /><br />Voorbeeld: `"FormatVerboseName": "QuickTime / MOV"` |
| **StartTime** |Begin tijd van inhoud.  <br /><br />Voorbeeld: `"StartTime": "PT0S"` |
| **OverallBitRate** |De gemiddelde bitrate van het activa bestand in bits per seconde.  <br /><br />Voorbeeld: `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| Naam | Beschrijving |
| --- | --- |
| **FourCC**<br />Vereist |Een code van de video-codec die wordt gerapporteerd door ffmpeg.<br /><br />Voorbeeld: `"FourCC": "avc1" | "hev1" | "hvc1"` |
| **Profiel** |Profiel van video spoor. <br /><br />Voorbeeld: `"Profile": "Main"`|
| **Afvlakking** |Niveau van de video track. <br /><br />Voorbeeld: `"Level": "3.2"`|
| **PixelFormat** |Pixel indeling van video track. <br /><br />Voorbeeld: `"PixelFormat": "yuv420p"`|
| **Breedte**<br />Vereist |Breedte van gecodeerde video in pixels. <br /><br />Voorbeeld: `"Width": "1280"`|
| **Hoogte**<br />Vereist |Versleutelde video hoogte in pixels.<br /><br />Voorbeeld: `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />Vereist |Teller voor hoogte-breedte verhouding video weergave.<br /><br />Voorbeeld: `"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominator**<br />Vereist |Noemer van hoogte-breedte verhouding video weergave. <br /><br />Voorbeeld: `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |Teller voor sample aspect verhouding van video. <br /><br />Voorbeeld: `"SampleAspectRatioNumerator": 1.0`|
| **SampleAspectRatioDenominator**|Voorbeeld: `"SampleAspectRatioDenominator": 1.0`|
| **Snelheid**<br />Vereist |Gemeten video frame frequentie in. 3F-indeling. <br /><br />Voorbeeld: `"FrameRate": 29.970`|
| **Bitsnelheid** |De gemiddelde bitsnelheid van video in bits per seconde, zoals berekend op basis van het activa bestand. Alleen de nettolading van de elementaire stream wordt geteld en de verpakkings overhead is niet opgenomen. <br /><br />Voorbeeld: `"Bitrate": 8421583`|
| **HasBFrames** |Video track van het aantal B-frames. <br /><br />Voorbeeld: `"HasBFrames": 2`|
| **Metagegevens** |Algemene sleutel/waarde-teken reeksen die kunnen worden gebruikt voor het opslaan van verschillende gegevens. <br />Bekijk het volledige voor beeld aan het einde van het artikel. |
| **Id**<br />Vereist |Op nul gebaseerde index van dit audio-of video spoor.<br /><br /> Deze **id** is niet noodzakelijkerwijs de TrackID die in een MP4-bestand wordt gebruikt. <br /><br />Voorbeeld: `"Id": 2`|
| **Videocodec** |Video track-codec teken reeks. <br /><br />Voorbeeld: `"Codec": "h264 | hev1"`|
| **CodecLongName** |De lange naam van de audio-of video track-codec. <br /><br />Voorbeeld: `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **Videocodec** |Video track-codec teken reeks. <br /><br />Voorbeeld: `"Codec": "h264 | hev1"`|
| **Negatieve**<br />Vereist |Tijd basis.<br /><br />Voorbeeld: `"TimeBase": "1/30000"`|
| **NumberOfFrames** |Aantal frames (aanwezig voor video tracks). <br /><br />Voorbeeld: `"NumberOfFrames": 2107`|
| **StartTime** |Start tijd bijhouden.<br /><br />Voorbeeld: `"StartTime": "PT0.033S"` |
| **Duur** |Houd de duur bij. <br /><br />Voorbeeld: `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTracks

| Naam  | Beschrijving |
| --- | --- | 
| **SampleFormat** |Voorbeeld indeling. <br /><br />Voorbeeld: `"SampleFormat": "fltp"`|
| **ChannelLayout** |Kanaal indeling. <br /><br />Voorbeeld: `"ChannelLayout": "stereo"`|
| **Kanalen**<br />Vereist |Aantal (0 of meer) audio kanalen. <br /><br />Voorbeeld: `"Channels": 2`|
| **SamplingRate**<br />Vereist |Audio sampling frequentie in samples/sec of Hz. <br /><br />Voorbeeld: `"SamplingRate": 48000`|
| **Bitsnelheid** |De gemiddelde bitsnelheid van audio in bits per seconde, zoals berekend op basis van het Asset-bestand. Alleen de nettolading van de elementaire stream wordt geteld en de pakket belasting is niet inbegrepen in dit aantal. <br /><br />Voorbeeld: `"Bitrate": 192080`|
| **Metagegevens** |Algemene sleutel/waarde-teken reeksen die kunnen worden gebruikt voor het opslaan van verschillende gegevens.  <br />Bekijk het volledige voor beeld aan het einde van het artikel. |
| **Id**<br />Vereist |Op nul gebaseerde index van dit audio-of video spoor.<br /><br /> Dit is niet noodzakelijkerwijs het TrackID dat wordt gebruikt in een MP4-bestand. <br /><br />Voorbeeld: `"Id": 1`|
| **Videocodec** |Video track-codec teken reeks. <br /><br />Voorbeeld: `"Codec": "aac"`|
| **CodecLongName** |De lange naam van de audio-of video track-codec. <br /><br />Voorbeeld: `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **Negatieve**<br />Vereist |Tijd basis.<br /><br />Voorbeeld: `"TimeBase": "1/48000"` |
| **NumberOfFrames** |Aantal frames (aanwezig voor video tracks). <br /><br />Voorbeeld: `"NumberOfFrames": 3294`|
| **StartTime** |Start tijd bijhouden. Zie [iso8601](https://www.iso.org/iso-8601-date-and-time-format.html)voor meer informatie. <br /><br />Voorbeeld: `"StartTime": "PT0S"` |
| **Duur** |Houd de duur bij. <br /><br />Voorbeeld: `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>Metagegevens

| Naam | Beschrijving |
| --- | --- |
| **prestatie**<br />Vereist |De sleutel in het sleutel/waarde-paar. |
| **value**<br /> Vereist |De waarde in het sleutel/waarde-paar. |

## <a name="schema-example"></a>Voor beeld van schema

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

[Uitvoermetagegevens](output-metadata-schema.md)
