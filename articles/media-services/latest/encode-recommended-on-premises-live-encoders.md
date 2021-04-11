---
title: Live streaming encoders die worden aanbevolen door Media Services
description: Meer informatie over on-premises coderings Programma's voor live streamen aanbevolen door Media Services
services: media-services
keywords: code ring; encoders; media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: media-services
ms.openlocfilehash: 8210efe9c17a6edcb18fe114b7f6165d8cbd8360
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281866"
---
# <a name="verified-on-premises-live-streaming-encoders"></a>Geverifieerde on-premises live streaming encoders

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In Azure Media Services vertegenwoordigt een [live gebeurtenis](/rest/api/media/liveevents) (kanaal) een pijp lijn voor het verwerken van live-streaming-inhoud. De live-gebeurtenis ontvangt Live invoer stromen op een van de volgende twee manieren.

* Een on-premises Live Encoder verzendt een gegevensgestuurde multi-bitrate RTMP-of Smooth Streaming (gefragmenteerde MP4)-stream naar de live gebeurtenis die niet is ingeschakeld voor het uitvoeren van Live code ring met Media Services. De opgenomen streams passeren Live gebeurtenissen zonder verdere verwerking. Deze methode wordt **Pass-Through** genoemd. We raden u aan om met het Live coderings programma multi-bitrate streams te verzenden in plaats van een single-bitrate stream naar een Pass-through live-gebeurtenis om Adaptive Bitrate Streaming toe te staan aan de client. 

    Als u multi-bitrate streams gebruikt voor de Pass-through live-gebeurtenis, moeten de grootte van de video-GOP terug en de video fragmenten op verschillende bitsnelheden worden gesynchroniseerd om onverwacht gedrag bij het afspelen te voor komen.

  > [!TIP]
  > Het gebruik van een Pass-Through-methode is de voordeligste manier om live streamen uit te voeren.
 
* Een on-premises Live Encoder verzendt een stream met één bitsnelheid naar de live gebeurtenis die is ingeschakeld voor het uitvoeren van Live code ring met Media Services in een van de volgende indelingen: RTMP of Smooth Streaming (gefragmenteerde MP4). De live-gebeurtenis voert vervolgens Live encoding uit van de inkomende single-bitrate stream naar een multi-bitrate-video stroom (adaptief).

In dit artikel worden de geverifieerde on-premises live streaming encoders beschreven. De verificatie wordt uitgevoerd via self-verificatie of klantverificatie van de leverancier. Microsoft Azure Media Services voert geen volledige of rigoureuze tests van elk coderings programma uit en wordt niet voortdurend opnieuw gecontroleerd op updates. Zie [uw on-premises encoder controleren](encode-on-premises-encoder-partner.md) voor instructies voor het controleren van uw on-premises Live coderings programma

Zie voor gedetailleerde informatie over Live encoding met Media Services [live streamen met Media Services v3](stream-live-streaming-concept.md).

## <a name="encoder-requirements"></a>Encoder vereisten

Encoders moeten TLS 1,2 ondersteunen bij het gebruik van HTTPS-of RTMP-protocollen.

## <a name="live-encoders-that-output-rtmp"></a>Live coderings Programma's die RTMP uitvoeren

Media Services raadt het gebruik aan van een van de volgende live-encoders met RTMP als uitvoer. De ondersteunde URL-schema's zijn `rtmp://` of `rtmps://` .

Bij het streamen via RTMP controleert u de instellingen voor de firewall en/of proxy om te zien of de uitgaande TCP-poorten 1935 en 1936 open zijn.<br/><br/>
Bij het streamen via RTMPS controleert u de instellingen voor de firewall en/of proxy om te zien of de uitgaande TCP-poorten 2935 en 2936 open zijn.

> [!NOTE]
> Encoders moeten TLS 1,2 ondersteunen bij het gebruik van de RTMP-protocollen.

- Adobe Flash Media Live Encoder 3.2
- [Anti-Digital](http://www.antixdigital.com/) Live StreamZ (voorheen SelenioFlex Live)
- [Blackmagic ATEM mini-en ATEM mini PRO](https://www.blackmagicdesign.com/products/atemmini)
- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- Elementair Live (versie 2.14.15 en hoger)
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Helden 7 en held 8
- Haivision KB
- Haivision Makito X HEVC
- [Restream.io](https://restream.io/)
- OBS Studio
- [Streamlabs IB](https://streamlabs.com/)
- [Switcher Studio (iOS)](https://www.switcherstudio.com/)
- Telestream-Wirecast (versie 13.0.2 of hoger vanwege de TLS 1,2-vereiste)
- Telestream-Wirecast S (alleen RTMP wordt ondersteund. Geen RTMP-ondersteuning vanwege een gebrek aan TLS 1.2 +)
- Teradek Slice 756
- VMIX
- xStream

> [!WARNING]
> De bovenstaande lijst met encoders is slechts een lijst met aanbevelingen. Coderings Programma's worden niet door micro soft getest of gevalideerd op een continue basis en updates of belang rijke wijzigingen kunnen worden geïntroduceerd door coderings leveranciers of open-source projecten die de compatibiliteit kunnen verstoren. 

## <a name="live-encoders-that-output-fragmented-mp4-smooth-streaming-ingest"></a>Live coderings Programma's die gefragmenteerde MP4 uitvoeren (Smooth Streaming opname)

Media Services raadt u aan om een van de volgende Live coderings Programma's te gebruiken met multi-bitrate Smooth Streaming (gefragmenteerde MP4) als uitvoer. De ondersteunde URL-schema's zijn `http://` of `https://` .

> [!NOTE]
> Encoders moeten TLS 1,2 ondersteunen bij het gebruik van HTTPS-protocollen.

- Ateme TITAN Live
- [Anti-Digital](http://www.antixdigital.com/) Live StreamZ (voorheen SelenioFlex Live)
- Cisco Digital Media Encoder 2200
- Elementaire Live (versie 2.14.15 en hoger vanwege de TLS 1,2-vereiste)
- Envivio 4Caster C4 Gen III 
- [Ffmpeg](https://www.ffmpeg.org)
- Media Excel Hero Live en Hero 4K (UHD/HEVC)

> [!TIP]
>  Als u live-gebeurtenissen in meerdere talen wilt streamen (bijvoorbeeld een Engels audio nummer en één Spaans audio spoor), kunt u dit doen met de media Excel live encoder die is geconfigureerd voor het verzenden van live feeds naar een Pass-through live-gebeurtenis.

> [!WARNING]
> De bovenstaande lijst met encoders is slechts een lijst met aanbevelingen. Coderings Programma's worden niet door micro soft getest of gevalideerd met behulp van een continue basis en ondersteuning of fouten kunnen worden geïntroduceerd door de leveranciers van het coderings programma of door open-source projecten die de compatibiliteit op elk gewenst moment verstoren. 

## <a name="configuring-on-premises-live-encoder-settings"></a>Instellingen voor on-premises Live coderings Programma's configureren

Zie voor meer informatie over welke instellingen geldig zijn voor uw live-gebeurtenis type [vergelijking van live gebeurtenis typen](live-event-types-comparison-reference.md).

### <a name="playback-requirements"></a>Vereisten voor afspelen

Als u de inhoud wilt afspelen, moet zowel een audio-als een video stroom aanwezig zijn. Afspelen van de stream alleen video wordt niet ondersteund.

### <a name="configuration-tips"></a>Configuratietips

- Gebruik indien mogelijk een vaste internetverbinding.
- Wanneer u de vereisten voor de band breedte bepaalt, verdubbelt u de streaming bitrates. Hoewel het niet verplicht is, kunt u met deze eenvoudige regel de impact van de netwerk congestie verminderen.
- Wanneer u software encoders gebruikt, moet u overbodige Program ma's sluiten.
- Het wijzigen van de encoder configuratie nadat deze is gestart, heeft negatieve gevolgen voor de gebeurtenis. Configuratie wijzigingen kunnen ertoe leiden dat de gebeurtenis Insta Biel wordt. 
- Test en valideer nieuwere versies van coderings software altijd voor een voortdurende compatibiliteit met Azure Media Services. Micro soft valideert encoders in deze lijst niet opnieuw en de meeste validaties worden door de software leveranciers rechtstreeks als een ' zelf certificerings instantie ' uitgevoerd.
- Zorg ervoor dat u uw gebeurtenis in ruime tijd kunt instellen. Voor grootschalige gebeurtenissen raden we u aan om de installatie een uur voor uw gebeurtenis te starten.
- De H. 264 video-en AAC-LC-audio-codec uitvoer gebruiken.
- Beknopte oplossingen en frame snelheden voor het type live-evenement dat u uitzendt (bijvoorbeeld 60fps wordt momenteel afgewezen.)
- Zorg ervoor dat er sprake is van een belang rijke frame of GOP terug tijdelijke uitlijning over de video-eigenschappen.
- Zorg ervoor dat er een unieke stroom naam is voor elke video kwaliteit.
- Gebruik strikte CBR-code ring aanbevolen voor optimale communicatie prestaties.

> [!IMPORTANT]
> Bekijk de fysieke toestand van de machine (CPU/geheugen/etc.) als het uploaden van fragmenten naar de Cloud CPU-en i/o-bewerkingen vergt. Als u instellingen in het coderings programma wijzigt, moet u er zeker van zijn dat de kanalen/live-gebeurtenis opnieuw worden ingesteld om de wijziging door te voeren.

## <a name="see-also"></a>Zie ook

[Live streamen met Media Services v3](stream-live-streaming-concept.md)

## <a name="next-steps"></a>Volgende stappen

[Uw coderings programma verifiëren](encode-on-premises-encoder-partner.md)
