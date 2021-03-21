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
ms.openlocfilehash: 7aada042254a2794602002614df51646be90c7cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013139"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder Standard Formats and Codecs (Indelingen en codecs voor Media Encoder Standard)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecteer de versie van Media Services die u gebruikt:"]
> * [Versie 2](media-services-media-encoder-standard-formats.md)
> * [Versie 3](../latest/media-encoder-standard-formats.md)

Dit document bevat een lijst met de meest voorkomende indelingen voor het importeren en exporteren van bestanden die u kunt gebruiken met Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Invoer van container/bestands indelingen
| Bestandsindelingen (bestandsextensies) | Ondersteund |
| --- | --- |
| FLV (met H.264- en AAC-codecs) (.flv) |Ja |
| MXF (.mxf) |Ja |
| GXF    (.gxf) |Ja |
| MPEG2-PS, MPEG2-TS 3GP (.ts, PS, .3gp, .3gpp, mpg) |Ja |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Ja |
| AVI (niet-gecomprimeerd, 8-bits/10 bits) (.avi) |Ja |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Ja |
| [Microsoft Digital Video Recording(DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (dvr-ms) |Ja |
| Matroska/WebM (.mkv) |Ja |
| WAVE/WAV (.wav) |Ja |
| QuickTime (.mov) |Ja |

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
| Avid DNxHD (in MXF) |Ja |
| DVCPro/DVCProHD (in MXF) |Ja |
| Digitale video (DV) (in AVI-bestanden) |Ja |
| JPEG 2000 |Ja |
| MPEG-2 (maximaal 422-profiel en hoog niveau, inclusief varianten zoals XDCAM, XDCAM HD XDCAM IMX, CableLabs® en D10) |Maximaal 422-profiel |
| MPEG-1 |Ja |
| VC-1/WMV9 |Ja |
| Canopus HQ/HQX |Nee |
| MPEG-4 Part 2 |Ja |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ja |
| YUV420 ongecomprimeerd of mezzanine |Ja |
| Apple ProRes 422 |Ja |
| Apple ProRes 422 LT |Ja |
| Apple ProRes 422 HQ |Ja |
| Apple ProRes Proxy |Ja |
| Apple ProRes 4444 |Ja |
| Apple ProRes 4444 XQ |Ja |
| HEVC/H. 265| Hoofd-en hoofd-10 (&#42;) profielen<br/>Ondersteuning voor hoofd-10-profielen is bedoeld voor 8bit 4:2:0-inhoud. |

## <a name="input-audio-codecs"></a>Audio-codecs invoeren
| Codecs audio-invoer | Ondersteund |
| --- | --- |
| AAC (AAC-LC, AAC-HE en AAC-HEv2; tot. 5.1) |Ja |
| MPEG Layer 2 |Ja |
| MP3 (MPEG-1 Audio Layer 3) |Ja |
| Windows Media Audio |Ja |
| WAV/PCM |Ja |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Ja |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Ja |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ja |
| AMR (Adaptive Multi-Rate) |Ja |
| AES (SMPTE 331M en 302M, AES3-2003) |Nee |
| Dolby® E |Nee |
| Dolby® Digital (AC3) |Nee |
| Dolby® Digital Plus (E-AC3) |Nee |

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
