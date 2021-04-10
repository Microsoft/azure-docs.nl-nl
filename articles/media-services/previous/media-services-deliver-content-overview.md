---
title: Inhoud aan klanten leveren
description: In dit onderwerp vindt u een overzicht van wat er is betrokken bij het leveren van uw inhoud met Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 1ad89345a2779766fde4559758e61dff92023741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016607"
---
# <a name="deliver-content-to-customers"></a>Inhoud leveren aan klanten

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Wanneer u uw streaming-of video-on-demand inhoud levert aan klanten, is het doel om video van hoge kwaliteit te leveren aan verschillende apparaten onder verschillende netwerk omstandigheden.

Voor dit doel kunt u het volgende doen:

* Codeer uw stroom naar een video stroom met een multi-bitrate (Adaptive bitrate). Dit zorgt voor de kwaliteit en netwerk omstandigheden.
* Gebruik Microsoft Azure Media Services [dynamische verpakking](media-services-dynamic-packaging-overview.md) om uw stroom dynamisch opnieuw te verpakken in verschillende protocollen. Dit zorgt ervoor dat streaming op verschillende apparaten wordt uitgevoerd. Media Services ondersteunt de levering van de volgende Adaptive Bitrate Streaming technologieën: <br/>
    * **Http live streaming** (HLS)-Voeg ' (Format = M3U8-AAPL) ' toe aan het '/manifest-gedeelte van de URL om de bron server voor streaming te laten terugkeren naar de HLS-inhoud voor gebruik op systeem eigen **Apple IOS** -apparaten (Zie [Locators](#locators) en [url's](#URLs)) voor meer informatie.
    * **MPEG-Dash** -add ' (Format = mpd-time-KVP) ' pad naar het '/manifest-gedeelte van de URL om de bron server voor streaming te laten terugkeren naar een MPEG-Dash (Zie [Locators](#locators) en [url's](#URLs)),
    * **Smooth streaming**.

>[!NOTE]
>Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben. 

Dit artikel bevat een overzicht van belang rijke concepten voor het leveren van inhoud.

Zie [bekende problemen](media-services-deliver-content-overview.md#known-issues)als u bekende problemen wilt controleren.

## <a name="dynamic-packaging"></a>Dynamische verpakking
Met de dynamische verpakking die Media Services biedt, kunt u uw adaptieve bitrate MP4 of Smooth Streaming gecodeerde inhoud leveren in streaming-indelingen die worden ondersteund door Media Services (MPEG-DASH, HLS, Smooth Streaming,) zonder dat ze opnieuw moeten worden ingepakt in deze streaming-indelingen. We raden u aan uw inhoud met dynamische pakketten te leveren.

Als u gebruik wilt maken van dynamische pakketten, moet u uw mezzanine-bestand (bron) coderen in een set Adaptive-bitrate MP4-bestanden of adaptieve bitrate Smooth Streaming-bestanden.

Met dynamische verpakking slaat u de bestanden op in één opslag indeling en betaalt u deze. Media Services bouwt en onderhoudt de juiste reactie op basis van uw aanvragen.

Dynamische pakketten zijn beschikbaar voor standaard-en Premium-streaming-eind punten. 

Zie [dynamische verpakking](media-services-dynamic-packaging-overview.md)voor meer informatie.

## <a name="filters-and-dynamic-manifests"></a>Filters en dynamische manifesten
U kunt filters voor uw assets definiëren met Media Services. Deze filters zijn regels aan de server zijde waarmee uw klanten zaken kunnen doen, zoals het afspelen van een specifieke sectie van een video of het opgeven van een subset van audio-en video weergaven die het apparaat van uw klant kan verwerken (in plaats van alle vertoningen die aan de Asset zijn gekoppeld). Dit filter wordt bereikt via *dynamische manifesten* die worden gemaakt wanneer uw klant een video op basis van een of meer opgegeven filters wil streamen.

Zie [filters en dynamische manifesten](media-services-dynamic-manifest-overview.md)voor meer informatie.

## <a name="locators"></a><a name="locators"></a>Locators
Als u uw gebruiker een URL wilt geven die kan worden gebruikt om uw inhoud te streamen of te downloaden, moet u uw asset eerst publiceren door een Locator te maken. Een Locator biedt een toegangs punt voor toegang tot de bestanden in een Asset. Media Services ondersteunt twee typen locators:

* OnDemandOrigin-Locators. Deze worden gebruikt voor het streamen van media (bijvoorbeeld MPEG-DASH, HLS of Smooth Streaming) of het progressief downloaden van bestanden.
* URL-Locators voor Shared Access Signature (SAS). Deze worden gebruikt om media bestanden te downloaden naar uw lokale computer.

Een *toegangs beleid* wordt gebruikt voor het definiëren van de machtigingen (zoals lezen, schrijven en lijst) en de duur waarvoor een client toegang heeft voor een bepaalde Asset. Houd er rekening mee dat de lijst machtiging (AccessPermissions. List) niet moet worden gebruikt bij het maken van een OnDemandOrigin-Locator.

Locators hebben verval datums. In de Azure Portal wordt een verval datum van 100 jaar in de toekomst ingesteld voor Locators.

> [!NOTE]
> Als u de Azure Portal hebt gebruikt voor het maken van Locators vóór maart 2015, zijn deze Locators na twee jaar ingesteld op verlopen.
> 
> 

Als u de vervaldatum van een locator wilt bijwerken, gebruikt u [REST](/rest/api/media/operations/locator#update_a_locator)- of [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator)-API's. Wanneer u de vervaldatum van een SAS-locator bijwerkt, wordt de URL gewijzigd.

Locators zijn niet ontworpen voor het beheren van toegangs beheer per gebruiker. U kunt verschillende toegangs rechten verlenen aan afzonderlijke gebruikers met behulp van DRM-oplossingen (Digital Rights Management). Zie [media beveiligen](/previous-versions/azure/dn282272(v=azure.100))voor meer informatie.

Wanneer u een Locator maakt, kan er een vertraging van 30 seconden optreden vanwege de vereiste processen voor opslag en doorgifte in Azure Storage.

## <a name="adaptive-streaming"></a>Adaptief streamen
Met adaptieve bitrate technologieën kunnen video speler-toepassingen netwerk omstandigheden bepalen en een aantal bitrates selecteren. Wanneer de netwerk communicatie wordt gedegradeerd, kan de client een lagere bitsnelheid selecteren, zodat het afspelen kan worden voortgezet met een lagere video kwaliteit. Wanneer de netwerk omstandigheden verbeteren, kan de client overschakelen naar een hogere bitrate met verbeterde video kwaliteit. Azure Media Services ondersteunt de volgende Adaptive bitrate technologieën: HTTP Live Streaming (HLS), Smooth Streaming en MPEG-DASH.

Als u gebruikers wilt voorzien van streaming-Url's, moet u eerst een OnDemandOrigin-Locator maken. Het maken van de Locator geeft u het basispad dat de inhoud bevat die u wilt streamen. Als u deze inhoud echter wilt streamen, moet u dit pad verder aanpassen. Als u een volledige URL naar het streaming-manifest bestand wilt maken, moet u de pad-waarde van de Locator en de naam van het manifest bestand (bestandsnaam. ISM) samen voegen. Voeg vervolgens **/manifest** en de juiste indeling (indien nodig) toe aan het pad van de Locator.

> [!NOTE]
> U kunt ook uw inhoud streamen via een TLS-verbinding. Om dit te doen, moet u ervoor zorgen dat uw streaming-Url's beginnen met HTTPS. Houd er rekening mee dat AMS op dit moment geen ondersteuning biedt voor TLS met aangepaste domeinen.  
> 

U kunt alleen streamen via TLS als het streaming-eind punt van waaruit u uw inhoud levert, is gemaakt na 10 september 2014. Als uw streaming-Url's zijn gebaseerd op de streaming-eind punten die zijn gemaakt na 10 september 2014, bevat de URL ' streaming.mediaservices.windows.net '. Streaming-Url's die "origin.mediaservices.windows.net" (de oude indeling) bevatten, bieden geen ondersteuning voor TLS. Als uw URL de oude indeling heeft en u via TLS wilt kunnen streamen, maakt u een nieuw streaming-eind punt. Gebruik Url's op basis van het nieuwe streaming-eind punt om uw inhoud te streamen via TLS.

## <a name="streaming-url-formats"></a><a name="URLs"></a>Streaming-URL-indelingen

### <a name="mpeg-dash-format"></a>MPEG-DASH-indeling
{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = mpd-time-KVP)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) v4-indeling
{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = M3U8-AAPL)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Indeling van Apple HTTP Live Streaming (HLS) v3
{streaming-eindpunt naam-Media Services-account naam}. streaming. Media Services. Windows. net/{Locator-ID} bestands (Format = M3U8-AAPL-v3)

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = M3U8-AAPL-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Apple HTTP Live Streaming-indeling (HLS) met filter voor alleen audio
Audio-alleen tracks worden standaard opgenomen in het HLS-manifest. Dit is vereist voor de Apple Store-certificering voor mobiele netwerken. Als een client niet voldoende band breedte heeft of verbinding heeft via een 2G-verbinding, worden de afspeel switches alleen naar audio gestuurd. Zo kunt u het streamen van inhoud zonder buffering houden, maar er is geen video. In sommige gevallen is het mogelijk dat het bufferen van spelers de voor keur heeft voor alleen audio. Als u het audio schema alleen wilt verwijderen, voegt u **alleen audio toe = False** aan de URL.

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = M3U8-AAPL-v3, alleen audio = false)

Zie voor meer informatie [dynamische manifest compositie ondersteuning en HLS uitvoer extra functies](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Smooth Streaming indeling
{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest

Voorbeeld:

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Smooth Streaming 2,0-manifest (verouderd manifest)
De Smooth Streaming manifest-indeling bevat standaard de herhalingstag (r-tag). Sommige spelers bieden echter geen ondersteuning voor de r-tag. Clients met deze spelers kunnen een indeling gebruiken waarmee de r-tag wordt uitgeschakeld:

{streaming-eindpunt naam-Media Services-account naam}. streaming. Media Services. Windows. net/{Locator-ID} bestands (Format = fmp4-V20)

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)`

## <a name="progressive-download"></a>Progressieve down load
Met progressief downloaden kunt u beginnen met het afspelen van media voordat het hele bestand is gedownload. U kunt de bestanden van ISM * (ismv, ISMA, ismt en ismc) niet progressief downloaden.

Als u inhoud progressief wilt downloaden, gebruikt u het OnDemandOrigin-type Locator. In het volgende voor beeld wordt de URL weer gegeven die is gebaseerd op het OnDemandOrigin type Locator:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

U moet alle door opslag versleutelde assets ontsleutelen die u wilt streamen van de oorspronkelijke service voor progressief downloaden.

## <a name="download"></a>Downloaden
Als u uw inhoud wilt downloaden naar een client apparaat, moet u een SAS-Locator maken. De SAS-Locator geeft u toegang tot de Azure Storage-container waarin uw bestand zich bevindt. Als u de download-URL wilt maken, moet u de bestands naam tussen de host en de SAS-hand tekening insluiten.

In het volgende voor beeld wordt de URL weer gegeven die is gebaseerd op de SAS-Locator:

`https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D`

De volgende overwegingen zijn van toepassing:

* U moet alle door opslag versleutelde assets ontsleutelen die u wilt streamen van de oorspronkelijke service voor progressief downloaden.
* Een down load die niet binnen 12 uur is voltooid, mislukt.

## <a name="streaming-endpoints"></a>Streaming-eindpunten

Een streaming-eind punt vertegenwoordigt een streaming-service die inhoud rechtstreeks kan leveren aan een client speler of een CDN (Content Delivery Network) voor verdere distributie. De uitgaande stroom van een streaming-eindpunt service kan een live stream of een video-on-demand-asset in uw Media Services-account zijn. Er zijn twee soorten streaming-eind punten, **Standard** en **Premium**. Zie [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Overzicht van streaming-eindpunten) voor meer informatie.

>[!NOTE]
>Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben. 

## <a name="known-issues"></a>Bekende problemen
### <a name="changes-to-smooth-streaming-manifest-version"></a>Wijzigingen in Smooth Streaming manifest versie
Vóór de release van de juli 2016-service: wanneer activa die zijn geproduceerd door Media Encoder Standard, Media Encoder Premium Workflow of de eerdere Azure Media Encoder gestreamd zijn met behulp van dynamische pakketten, wordt het resulterende Smooth Streaming-manifest in overeenstemming gebracht met versie 2,0. In versie 2,0 maken de fragment duur geen gebruik van de zogenaamde REPEAT-Tags (' r '). Bijvoorbeeld:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" n="0" />
        <c d="2000" />
        <c d="2000" />
        <c d="2000" />
    </StreamIndex>
</SmoothStreamingMedia>
```

In de service release van juli 2016 voldoet het gegenereerde Smooth Streaming-manifest aan versie 2,2, met een fragment duur met behulp van herhalings codes. Bijvoorbeeld:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" r="4" />
    </StreamIndex>
</SmoothStreamingMedia>
```

Sommige van de verouderde Smooth Streaming-clients bieden mogelijk geen ondersteuning voor de herhalings codes en kunnen het manifest niet laden. Als u dit probleem wilt verhelpen, kunt u de verouderde manifest indelings parameter gebruiken **(Format = fmp4-V20)** of uw client bijwerken naar de meest recente versie, die herhaalde Tags ondersteunt. Zie [Smooth Streaming 2,0](media-services-deliver-content-overview.md#fmp4_v20)voor meer informatie.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Verwante onderwerpen
[Media Services Locators na Rolling Storage-sleutels bijwerken](media-services-roll-storage-access-keys.md)