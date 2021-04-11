---
title: Media Encoder Standard indelingen en codecs-Azure
description: Dit artikel bevat een overzicht van Media Encoder Standard indelingen en codecs.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: 9055c4c1af14c9ecc8fbdeb621c30ac6ebe5afc4
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108799"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder Standard Formats and Codecs (Indelingen en codecs voor Media Encoder Standard)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecteer de versie van Media Services die u gebruikt:"]
> * [Versie 2](media-services-media-encoder-standard-formats.md)
> * [Versie 3](../latest/encode-media-encoder-standard-formats-reference.md)

Dit document bevat een lijst met de meest voorkomende indelingen voor het importeren en exporteren van bestanden die u kunt gebruiken met Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Invoer van container/bestands indelingen
| Bestandsindelingen (bestandsextensies) | Ondersteund |
| --- | --- |
| FLV (met H.264- en AAC-codecs) (.flv) |Yes |
| MXF (.mxf) |Yes |
| GXF    (.gxf) |Yes |
| MPEG2-PS, MPEG2-TS 3GP (.ts, PS, .3gp, .3gpp, mpg) |Yes |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Yes |
| AVI (niet-gecomprimeerd, 8-bits/10 bits) (.avi) |Yes |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Yes |
| [Microsoft Digital Video Recording(DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (dvr-ms) |Yes |
| Matroska/WebM (.mkv) |Yes |
| WAVE/WAV (.wav) |Yes |
| QuickTime (.mov) |Yes |

> [!NOTE]
> Hierboven vindt u een lijst van de meer frequent voorkomende bestandsextensies. Media Encoder Standard ondersteunt veel andere (bijvoorbeeld:. m2ts,. mpeg2video,. qt). Als u een bestand probeert te coderen en er een fout bericht wordt weer gegeven over de indeling die niet wordt ondersteund, geeft u [hier](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/)uw feedback.
> 
> 

### <a name="audio-formats-in-input-containers"></a>Audio-indelingen in invoer containers
Media Encoder Standard ondersteunt het uitvoeren van de volgende audio-indelingen in invoer containers:

* MXF-, GXF-en QuickTime-bestanden met audio sporen met Interleaved stereo of 5,1 voor beelden

of

* MXF-, GXF- en QuickTime-bestanden waar de audio wordt uitgevoerd als afzonderlijke PCM-sporen, maar de kanaaltoewijzing (in stereo of 5.1) kan worden afgeleid van de metagegevens van het bestand

## <a name="input-video-codecs"></a>Video-codecs invoeren
| Video-codecs invoeren | Ondersteund |
| --- | --- |
| AVC 8-bits/10-bits, maximaal 4:2:2, inclusief AVCIntra |8-bits 4:2:0 en 4:2:2 |
| Avid DNxHD (in MXF) |Yes |
| DVCPro/DVCProHD (in MXF) |Yes |
| Digitale video (DV) (in AVI-bestanden) |Yes |
| JPEG 2000 |Yes |
| MPEG-2 (maximaal 422-profiel en hoog niveau, inclusief varianten zoals XDCAM, XDCAM HD XDCAM IMX, CableLabs® en D10) |Maximaal 422-profiel |
| MPEG-1 |Yes |
| VC-1/WMV9 |Yes |
| Canopus HQ/HQX |No |
| MPEG-4 Part 2 |Yes |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |
| YUV420 ongecomprimeerd of mezzanine |Yes |
| Apple ProRes 422 |Yes |
| Apple ProRes 422 LT |Yes |
| Apple ProRes 422 HQ |Yes |
| Apple ProRes Proxy |Yes |
| Apple ProRes 4444 |Yes |
| Apple ProRes 4444 XQ |Yes |
| HEVC/H. 265| Hoofd-en hoofd-10 (&#42;) profielen<br/>Ondersteuning voor hoofd-10-profielen is bedoeld voor 8bit 4:2:0-inhoud. |

## <a name="input-audio-codecs"></a>Audio-codecs invoeren
| Codecs audio-invoer | Ondersteund |
| --- | --- |
| AAC (AAC-LC, AAC-HE en AAC-HEv2; tot. 5.1) |Yes |
| MPEG Layer 2 |Yes |
| MP3 (MPEG-1 Audio Layer 3) |Yes |
| Windows Media Audio |Yes |
| WAV/PCM |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Yes |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |
| AMR (Adaptive Multi-Rate) |Yes |
| AES (SMPTE 331M en 302M, AES3-2003) |No |
| Dolby® E |No |
| Dolby® Digital (AC3) |No |
| Dolby® Digital Plus (E-AC3) |No |

## <a name="output-formats-and-codecs"></a>Uitvoer indelingen en codecs
De volgende tabel geeft een lijst van de codecs en bestands indelingen die worden ondersteund voor het exporteren.

| Bestandsindeling | Videocodec | Audiocodec |
| --- | --- | --- |
| MP4 <br/><br/>(inclusief multi-bitrate MP4-containers) |H. 264 (High-, Main-en baseline-profielen) |AAC-LC, hij-AAC v1, HE-AAC v2 |
| MPEG2-TS |H. 264 (High-, Main-en baseline-profielen) |AAC-LC, hij-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Inhoud op aanvraag coderen met Azure Media Services](media-services-encode-asset.md)

[Coderen met Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)
