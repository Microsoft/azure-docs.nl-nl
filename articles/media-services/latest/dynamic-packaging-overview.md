---
title: Dynamische pakketten in Azure Media Services v3
description: Dit artikel geeft een overzicht van dynamische pakketten in Azure Media Services.
author: myoungerman
manager: femila
services: media-services
ms.service: media-services
ms.workload: media
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 4e396841231659c27f199a7353565c5d69e02877
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061992"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Dynamische pakketten in Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Microsoft Azure Media Services kunnen worden gebruikt voor het coderen van veel mediabronbestandsindelingen. Deze worden geleverd via verschillende streamingprotocollen, met of zonder inhoudsbeveiliging, om alle grote apparaten te bereiken (zoals iOS- en Android-apparaten). Deze clients werken met verschillende protocollen. Zo vereist iOS dat stromen worden geleverd in HLS-indeling (HTTP Live Streaming), en Android-apparaten ondersteunen zowel HLS als MPEG DASH.

In Media Services vertegenwoordigt een [streaming-eindpunt](streaming-endpoint-concept.md) (origin) een dynamische service (just-in-time) die u uw live en on-demand-inhoud rechtstreeks kan leveren aan een client-speler-app. De service maakt gebruik van een van de algemene protocollen voor het streamen van media die worden vermeld in de volgende sectie. *Dynamische pakketten* is een functie die standaard wordt geleverd op alle streaming-eindpunten.

## <a name="to-prepare-your-source-files-for-delivery"></a>Voorbereiding van de bronbestanden voor levering

U moet uw mezzaninebestand (bronbestand) [coderen](encoding-concept.md) in een set multi-bitrate MP4-bestanden (ISO Base Media 14496-12) om gebruik te maken van dynamische pakketten. U moet een [asset](assets-concept.md) hebben met de gecodeerde MP4- en streaming-configuratiebestanden die nodig zijn voor dynamische pakketten van Media Services. Vanuit deze set MP4-bestanden kunt u dynamische pakketten gebruiken voor het leveren van video via de protocollen voor streaming media die hieronder worden beschreven.

Dynamische pakketten van Azure Media Services ondersteunen alleen video- en audiobestanden in de MP4-containerindeling. Audiobestanden moeten ook worden gecodeerd in een MP4-container wanneer alternatieve codecs, zoals Dolby, worden gebruikt.  

> [!TIP]
> Eén manier om de MP4- en streamingconfiguratiebestanden op te halen is door [uw mezzanine-bestand te coderen met Media Services](#encode-to-adaptive-bitrate-mp4s). 

Om video's in de gecodeerde asset beschikbaar te maken voor clients om af te spelen, moet u een [streaming-locator](streaming-locators-concept.md) maken en streaming-URL's bouwen. Vervolgens ontvangt u op basis van de indeling die is opgegeven in het streaming-client-manifest (HLS, DASH of Smooth Streaming) de stream in het protocol dat u hebt gekozen.

Hierdoor hoeft u voor slechts één opslagindeling de bestanden op te slaan en hiervoor te betalen. De Media Services-service bouwt en levert de juiste reactie op basis van aanvragen van een client.

Als u van plan bent om uw inhoud te beschermen met Media Services dynamische versleuteling, raadpleegt u [Streaming protocols and encryption types](drm-content-protection-concept.md#streaming-protocols-and-encryption-types) (streaming-protocollen en versleutelingstypen).

### <a name="hls-protocol"></a>HLS-protocol

Uw streaming-client kan de volgende HLS-indelingen opgeven:

|Protocol|Voorbeeld|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|

> [!NOTE]
> In de vorige richtlijnen van Apple wordt aanbevolen dat bij de terugval voor netwerken met lage bandbreedte alleen audio zou kunnen worden gestreamd.  Op dit moment genereert de Media Services-encoder automatisch een nummer met alleen audio.  In de Apple-richtlijnen wordt nu vermeld dat het nummer met alleen audio *niet* moet worden opgenomen. Dit geldt met name voor de Apple TV-distributie.  Om te voorkomen dat de speler standaard een nummer met alleen audio kan maken, raden we u aan de tag audio-only=false te gebruiken in de URL waardoor weergave met alleen audio in HLS wordt verwijderd. U kunt ook gewoon HLS-V3 gebruiken. Bijvoorbeeld `http://host/locator/asset.ism/manifest(format=m3u8-aapl,audio-only=false)`.

### <a name="mpeg-dash-protocol"></a>MPEG-DASH-protocol

Uw streaming-client kan de volgende MPEG-DASH-indelingen opgeven:

|Protocol|Voorbeeld|
|---|---|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |

### <a name="smooth-streaming-protocol"></a>Smooth Streaming-protocol

Uw streaming-client kan de volgende Smooth Streaming-indelingen opgeven:

|Protocol|Opmerkingen/voorbeelden| 
|---|---|
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|
|Smooth Streaming 2.0 (verouderd manifest)|De Smooth Streaming manifest-indeling bevat standaard de herhalingstag (r-tag). Sommige spelers bieden echter geen ondersteuning voor de `r-tag`. Clients met deze spelers kunnen een indeling gebruiken waarmee de r-tag wordt uitgeschakeld:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> Smooth Streaming vereist dat zowel audio als video aanwezig is in de stroom.

## <a name="on-demand-streaming-workflow"></a>Werkstroom voor streaming on demand

De volgende stappen tonen een algemene Media Services-streaming-werkstroom waarbij dynamische pakketten worden gebruikt samen met de Standaard Encoder in Azure Media Services.

1. [Upload een invoerbestand](job-input-from-http-how-to.md), zoals een MP4-, QuickTime-/MOV-bestand of andere ondersteunde bestandsindeling. Dit bestand wordt ook wel het mezzanine- of bronbestand genoemd. Zie [indelingen die worden ondersteund door de Standard-encoder](media-encoder-standard-formats.md)voor een lijst met ondersteunde indelingen.
1. [Codeer](#encode-to-adaptive-bitrate-mp4s) uw mezzanine-bestand in een H.264/AAC MP4-set met adaptieve bitsnelheid.

    Als u al gecodeerde bestanden hebt en alleen de bestanden wilt kopiëren en streamen, gebruikt u: De API’s [CopyVideo](/rest/api/media/transforms/createorupdate#copyvideo) en [CopyAudio](/rest/api/media/transforms/createorupdate#copyaudio). Als gevolg daarvan zal een nieuw MP4-bestand met een streamingmanifest (.ism-bestand) worden gemaakt.
1. Publiceer de uitvoer-asset die de MP4-set met adaptieve bitsnelheid bevat. U publiceert door het maken van een [streaming-locator](streaming-locators-concept.md).
1. Bouw URL's die zijn gericht op verschillende indelingen (HLS, MPEG-DASH en Smooth Streaming). Het *streaming-eindpunt* zorgt voor het aanbieden van het juiste manifest en de juiste aanvragen voor al deze verschillende indelingen.
    
In het volgende diagram ziet u de werkstroom voor streaming on demand met dynamische pakketten.

![Diagram van een werkstroom voor streaming on demand met dynamische pakketten](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Het downloadpad is aanwezig in de bovenstaande afbeelding om te laten zien dat u een MP4-bestand rechtstreeks via het *streaming-eindpunt* (origin) kunt downloaden (u geeft het downloadbare [streaming-beleid](streaming-policy-concept.md) op in de streaming-locator).<br/>Het dynamische pakket wijzigt het bestand niet. U kunt eventueel de API's van Azure Blob Storage gebruiken als u een MP4 rechtstreeks wilt openen voor progressief downloaden als u de functies voor het *streaming-eindpunt* wilt omzeilen. 

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Coderen naar MP4's met adaptieve bitsnelheid

In de volgende artikelen ziet u voorbeelden van [het coderen van een video met Media Services](encoding-concept.md):

* [Coderen vanuit een HTTPS-URL met behulp van ingebouwde voorinstellingen](job-input-from-http-how-to.md).
* [Een lokaal bestand coderen met behulp van ingebouwde voorinstellingen](job-input-from-local-file-how-to.md).
* [Een aangepaste voorinstelling bouwen voor de vereisten van uw specifieke scenario of apparaat](customize-encoder-presets-how-to.md).

Zie de lijst met [indelingen en codecs](media-encoder-standard-formats.md) voor Standard Encoder.

## <a name="live-streaming-workflow"></a>Werkstroom voor live streamen

Een livegebeurtenis kan worden ingesteld op een *pass-through* (een on-premises live-encoder verzendt een stroom met meerdere bitsnelheden) of *live-codering* (een on-premises live-encoder verzendt een stream met één bitsnelheid). 

Hier volgt een algemene werkstroom voor live streamen met *dynamische pakketten*:

1. Maak een [livegebeurtenis](live-events-outputs-concept.md).
1. Haal de opname-URL op en configureer uw on-premises encoder voor het gebruik van de URL om de bijdragefeed te verzenden.
1. Haal de preview-URL op en gebruik deze om te controleren of de input van de encoder wordt ontvangen.
1. Maak een nieuwe asset.
1. Maak een live-uitvoer en gebruik de assetnaam die u hebt gemaakt.<br />De live uitvoer archiveert de stroom naar de asset.
1. Maak een streaming-locator met de ingebouwde streaming-beleidstypen.<br />Als u van plan bent om uw inhoud te versleutelen, raadpleegt u het [Overzicht van inhoudsbeveiliging](drm-content-protection-concept.md).
1. Maak een lijst van de paden op de streaming-locator om de te gebruiken URL's te krijgen.
1. Haal de hostnaam op voor het streaming-eindpunt van waaruit u wilt streamen.
1. Bouw URL's die zijn gericht op verschillende indelingen (HLS, MPEG-DASH en Smooth Streaming). Het *streaming-eindpunt* zorgt voor het aanbieden van het juiste manifest en de juiste aanvragen voor de verschillende indelingen.

Dit diagram toont de werkstroom voor live streamen met *dynamische pakketten*:

![Diagram van een werkstroom voor pass-through-codering met dynamische pakketten](./media/live-streaming/pass-through.svg)

Zie [Overzicht van live streamen](live-streaming-overview.md) voor informatie over live streamen in Media Services v3.

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Video-codecs die worden ondersteund door dynamische pakketten

Dynamische pakketten ondersteunen videobestanden in de bestandsindeling MP4-container en bevatten video die is gecodeerd met [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC of AVC1) of [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 of hvc1).

> [!NOTE]
> Resoluties van maximaal 4.000 en framesnelheden van maximaal 60 frames per seconde zijn getest met *dynamische pakketten*.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Audio-codecs die worden ondersteund door dynamische pakketten

Dynamische pakketten ondersteunen ook audiobestanden die zijn opgeslagen in de bestandsindeling MP4-container die gecodeerde audiostroom bevat in een van de volgende codecs:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 of HE-AAC v2). 
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 of E-AC3).  De gecodeerde audio moet worden opgeslagen in de MP4-containerindeling om met dynamische pakketten te kunnen werken.
* Dolby Atmos

   Het streamen van Dolby Atmos-inhoud wordt ondersteund voor standaarden zoals het MPEG-DASH-protocol met CSF (Common Streaming Format) of CMAF (Common Media Application Format) gefragmenteerde MP4, en via HLS (HTTP Live Streaming) met CMAF.
* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   DTS-codecs die worden ondersteund door de pakketindelingen DASH-CSF, DASH-CMAF, HLS-M2TS en HLS-CMAF zijn:  

    * DTS Digital Surround (DTSC)
    * DTS-HD High Resolution en DTS-HD Master Audio (DTSH)
    * DTS Express (DTSE)
    * DTS-HD Lossless (no core) (DTSL)

Dynamische pakketten ondersteunen meerdere audiosporen met DASH of HLS (versie 4 of later) voor streaming-assets die meerdere audiosporen met meerdere codecs en talen bevatten.

Voor alle bovenstaande audiocodecs moet de gecodeerde audio worden opgeslagen in de MP4-containerindeling om met dynamische pakketten te kunnen werken. De service biedt geen ondersteuning voor raw-bestandsindelingen van elementaire streams in Blob Storage (het volgende wordt bijvoorbeeld niet ondersteund: .DTS, .AC3.) 

Alleen bestanden met de extensie .mp4 of .mp4a worden ondersteund voor audiopakketten. 

### <a name="limitations"></a>Beperkingen

#### <a name="ios-limitation-on-aac-51-audio"></a>iOS-beperking op AAC 5.1-audio

Apple iOS-apparaten ondersteunen geen 5.1 AAC-audiocodec. Audio met meerdere kanalen moet worden gecodeerd met Dolby Digital- of Dolby Digital Plus-codecs.

Zie [HLS authoring specification for Apple devices](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices) (HLS-ontwerpspecificatie voor Apple-apparaten) voor gedetailleerde informatie.

> [!NOTE]
> Media Services biedt geen ondersteuning voor codering van Dolby Digital, Dolby Digital Plus of Dolby Digital Plus met Dolby Atmos multi-channel audio-indelingen.

#### <a name="dolby-digital-audio"></a>Dolby Digital-audio

Dynamische pakketten van Media Services ondersteunen momenteel geen bestanden die [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital)-audio (AC3) bevatten, omdat dit door Dolby als verouderd wordt beschouwd.

## <a name="manifests"></a>Manifesten

In *dynamische pakketten* van Media Services worden de streaming-clientmanifesten voor HLS, MPEG-DASH en Smooth Streaming dynamisch gegenereerd op basis van de indelingsselector in de URL.  

Een manifestbestand bevat streaming-metagegevens zoals het type spoor (audio, video of tekst), de naam van het spoor, de begin- en eindtijd, de bitsnelheid (kwaliteiten), de spoortalen, het presentatievenster (sliding window met vaste duur) en de videocodec (FourCC). Het instrueert de speler ook om het volgende fragment op te halen door informatie te geven over de volgende afspeelbare videofragmenten die beschikbaar zijn en hun locatie. Fragmenten (of segmenten) zijn de werkelijke 'chunks' met video-inhoud.

### <a name="examples"></a>Voorbeelden

#### <a name="hls"></a>HLS

Hier volgt een voor beeld van een HLS-manifestbestand, ook wel een HLS-hoofdafspeellijst genoemd: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

Hier volgt een voorbeeld van een MPEG-DASH-manifestbestand, ook wel een MPEG-DASH MPD (Media Presentation Description) genoemd:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>Smooth Streaming

Hier volgt een voorbeeld van een Smooth Streaming-manifestbestand:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>Naamgeving van sporen in het manifest

Als er in het .ism-bestand een audiospoornaam wordt opgegeven, voegt Media Services een `Label`-element binnen een `AdaptationSet` toe om de textuurgegevens voor het specifieke audiospoor op te geven. Een voorbeeld van het uitgevoerde DASH-manifest:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

De speler kan het `Label`-element gebruiken om weer te geven in de gebruikersinterface ervan.

### <a name="signaling-audio-description-tracks"></a>Signalering audiobeschrijvingssporen

U kunt een commentaarspoor toevoegen aan uw video om visueel gehandicapten te helpen de video-opname te volgen door naar de gesproken tekst te luisteren. U moet een audiospoor in het manifest aangeven als audiobeschrijving. Voeg hiervoor de parameters 'accessibility' en 'role' toe aan het .ism-bestand. Het is uw verantwoordelijkheid om deze parameters op de juiste wijze in te stellen om een audiospoor te signaleren als audiobeschrijving. Voeg bijvoorbeeld `<param name="accessibility" value="description" />` en `<param name="role" value="alternate"` toe aan het .ism-bestand voor een specifiek audiospoor. 

Zie voor meer informatie het voorbeeld [Beschrijvend audiospoor signaleren](signal-descriptive-audio-howto.md).

#### <a name="smooth-streaming-manifest"></a>Smooth Streaming-manifest

Als u een Smooth Streaming-stroom afspeelt, bevat het manifest waarden in de attributen `Accessibility` en `Role` voor dat audiospoor. Er wordt bijvoorbeeld `Role="alternate" Accessibility="description"` toegevoegd in het `StreamIndex`-element om aan te geven dat het een audiobeschrijving is.

#### <a name="dash-manifest"></a>DASH-manifest

Voor een DASH-manifest worden de volgende twee elementen toegevoegd om de audiobeschrijving te signaleren:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS-afspeellijst

Voor HLS v7 en hoger `(format=m3u8-cmaf)` bevat de afspeellijst `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` wanneer het audiobeschrijvingsspoor wordt gesignaleerd.

#### <a name="example"></a>Voorbeeld

Zie [Audiobeschrijvingssporen signaleren](signal-descriptive-audio-howto.md) voor meer informatie.

## <a name="dynamic-manifest"></a>Dynamisch manifest

Als u het aantal sporen, indelingen, bitsnelheden en presentatietijdvensters wilt beheren dat naar spelers wordt verzonden, kunt u dynamische filtering gebruiken met de dynamische pakketten van Media Services. Zie [Manifesten vooraf filteren met dynamische pakketten](filters-dynamic-manifest-overview.md) voor meer informatie.

## <a name="dynamic-encryption"></a>Dynamische versleuteling

U kunt *dynamische versleuteling* gebruiken om uw live of on-demand inhoud te versleutelen met AES-128 of een van de drie voornaamste DRM-systemen (Digital Rights Management): Microsoft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM-licenties aan geautoriseerde clients. Zie [dynamische versleuteling](drm-content-protection-concept.md) voor meer informatie.

> [!NOTE]
> Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="more-information"></a>Meer informatie

Ga naar de [Azure Media Services-community](media-services-community.md) om verschillende manieren te bekijken om vragen te stellen, feedback te geven en updates over Media Services te krijgen.

## <a name="need-help"></a>Hebt u hulp nodig?

U kunt een ondersteuningsticket openen door te navigeren naar [Nieuwe ondersteuningsaanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

[Video's uploaden, coderen en streamen](stream-files-tutorial-with-api.md)
