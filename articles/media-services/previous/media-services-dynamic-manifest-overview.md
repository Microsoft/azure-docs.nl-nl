---
title: Filters en dynamische manifesten | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u filters maakt, zodat uw client deze kan gebruiken om specifieke secties van een stroom te streamen. Media Services maakt dynamische manifesten om deze selectief streaming te archiveren.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 96f0c4d4ea7c32e2b58807204ef45b75feae7132
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727325"
---
# <a name="filters-and-dynamic-manifests"></a>Filters en dynamische manifesten

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecteer de versie van Media Services die u gebruikt:"]
> * [Versie 2](media-services-dynamic-manifest-overview.md)
> * [Versie 3](../latest/filters-dynamic-manifest-overview.md)

Met ingang van 2,17 versie Media Services kunt u filters voor uw assets definiëren. Deze filters zijn regels aan de server zijde waarmee uw klanten dingen kunnen doen: alleen een sectie van een video afspelen (in plaats van de volledige video afspelen), of alleen een subset van audio-en video weergaven opgeven die het apparaat van de klant kan verwerken (in plaats van alle vertoningen die zijn gekoppeld aan de Asset). Dit filter van uw assets wordt bereikt via de **dynamische manifesten** die worden gemaakt op de aanvraag van uw klant om een video te streamen op basis van opgegeven filter (s).

In dit onderwerp worden algemene scenario's besproken waarin het gebruik van filters nuttig is voor uw klanten en koppelingen naar onderwerpen waarin wordt uitgelegd hoe u via een programma filters maakt.

## <a name="overview"></a>Overzicht
Bij het leveren van uw inhoud aan klanten (het streamen van Live-gebeurtenissen of video op aanvraag) is het doel om een video van hoge kwaliteit te leveren aan verschillende apparaten onder verschillende netwerk omstandigheden. Ga als volgt te werk om deze doel stelling te behaalen:

* Codeer uw stroom naar een video stroom met een multi-bitrate ([Adaptive bitrate](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) (dit zorgt voor de kwaliteit en netwerk omstandigheden) en 
* gebruik Media Services [dynamische verpakking](media-services-dynamic-packaging-overview.md) om uw stroom dynamisch opnieuw te verpakken in verschillende protocollen (dit zorgt ervoor dat streaming op verschillende apparaten wordt uitgevoerd). Media Services ondersteunt de levering van de volgende Adaptive Bitrate Streaming technologieën: HTTP Live Streaming (HLS), Smooth Streaming en MPEG DASH. 

### <a name="manifest-files"></a>Manifest bestanden
Wanneer u een Asset codeert voor Adaptive Bitrate Streaming, wordt er een **manifest** bestand (afspeel lijst) gemaakt (het bestand is gebaseerd op tekst of XML). Het **manifest** bestand bevat streaming meta gegevens zoals: het type spoor (audio, video of tekst), de naam van het spoor, de begin-en eind tijd, de bitsnelheid (kwaliteiten), de bijgehouden talen, het presentatie venster (sliding window van de vaste duur), de videocodec (FourCC). Het geeft ook de speler de opdracht het volgende fragment op te halen door informatie te verstrekken over de volgende Speel bare video fragmenten die beschikbaar zijn en hun locatie. Fragmenten (of segmenten) zijn de daad werkelijke chunks van een video-inhoud.

Hier volgt een voor beeld van een manifest bestand: 

```xml
<?xml version="1.0" encoding="UTF-8"?>    
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

<StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
<QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
<QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
<QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
<QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
<QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
<QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
<QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
<QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

<c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
</StreamIndex>


<StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
<QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

<c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


<StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
<QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

<c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

</SmoothStreamingMedia>
```

### <a name="dynamic-manifests"></a>Dynamische manifesten
Er zijn [scenario's](media-services-dynamic-manifest-overview.md#scenarios) waarbij uw client meer flexibiliteit nodig heeft dan is beschreven in het manifest bestand van het standaard activum. Bijvoorbeeld:

* Apparaat specifiek: alleen de opgegeven vertoningen en/of opgegeven taal sporen leveren die worden ondersteund door het apparaat dat wordt gebruikt voor het afspelen van de inhoud (' weergave filters '). 
* Verminder het manifest om een subclip van een live-gebeurtenis weer te geven ("subclip filtering").
* Het begin van een video bijsnijden ("een video bijsnijden").
* Stel Presentation Window (DVR) in om een beperkte lengte van het DVR-venster op te geven in de speler ("presentatie venster aanpassen").

Media Services biedt **dynamische manifesten** op basis van vooraf gedefinieerde [filters](media-services-dynamic-manifest-overview.md#filters)om deze flexibiliteit te krijgen.  Wanneer u de filters hebt gedefinieerd, kunnen de clients deze gebruiken om een specifieke vertoning of subfragmenten van uw video te streamen. Er worden filter (s) in de streaming-URL opgegeven. Filters kunnen worden toegepast op Adaptive Bitrate Streaming protocollen die worden ondersteund door [dynamische pakketten](media-services-dynamic-packaging-overview.md): HLS, MPEG-DASH en Smooth streaming. Bijvoorbeeld:

MPEG DASH-URL met filter

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)`

Smooth Streaming URL met filter

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)`


Zie overzicht van het [leveren van inhoud](media-services-deliver-content-overview.md)voor meer informatie over het leveren van uw inhoud en het bouwen van streaming-url's.

> [!NOTE]
> Houd er rekening mee dat dynamische manifesten de Asset en het standaard manifest voor die Asset niet wijzigen. Uw client kan ervoor kiezen om een stroom met of zonder filters aan te vragen. 
> 
> 

### <a name="filters"></a><a id="filters"></a>Filters
Er zijn twee soorten Asset-filters: 

* Globale filters (kunnen worden toegepast op alle assets in het Azure Media Services account, hebben een levens duur van het account) en 
* Lokale filters (kunnen alleen worden toegepast op een Asset waarmee het filter is gekoppeld bij het maken, een levens duur van de Asset). 

Globale en lokale filter typen hebben precies dezelfde eigenschappen. Het belangrijkste verschil tussen de twee is voor welke scenario's het type bestander geschikter is. Globale filters zijn algemeen geschikt voor apparaatprofielen (weergave filters) waarin lokale filters kunnen worden gebruikt om een specifiek activum te bijsnijden.

## <a name="common-scenarios"></a><a id="scenarios"></a>Algemene scenario's
Zoals eerder is vermeld, kunt u bij het leveren van uw inhoud aan klanten (Live-gebeurtenissen of video-on-demand) het doel bieden een video van hoge kwaliteit te leveren aan verschillende apparaten onder verschillende netwerk omstandigheden. Daarnaast hebt u mogelijk andere vereisten voor het filteren van uw assets en het gebruik van **dynamische manifest** s. De volgende secties geven een kort overzicht van verschillende filter scenario's.

* Geef alleen een subset van audio-en video weergaven op die door bepaalde apparaten kunnen worden verwerkt (in plaats van alle vertoningen die zijn gekoppeld aan de Asset). 
* Alleen een sectie van een video afspelen (in plaats van de hele video af te spelen).
* Wijzig het DVR-presentatie venster.

## <a name="rendition-filtering"></a>Vertoning filteren
U kunt ervoor kiezen om uw asset te coderen naar meerdere coderings profielen (H. 264 baseline, H. 264 High, AACL, AACH, Dolby Digital Plus) en meerdere kwaliteits bitrates. Niet alle client apparaten ondersteunen echter wel de profielen en bitrates van uw asset. Oudere Android-apparaten ondersteunen bijvoorbeeld alleen H. 264 Baseline + AACL. Het verzenden van hogere bitrates naar een apparaat dat de voor delen niet kan verkrijgen, de band breedte en de berekening van het apparaat verspilt. Een dergelijk apparaat moet alle opgegeven informatie decoderen, zodat het alleen kan worden geschaald voor weer gave.

Met het dynamische manifest kunt u apparaatprofielen maken, zoals Mobile, console, HD/SD, enzovoort en de sporen en kwaliteiten opnemen die u wilt delen van elk profiel.

![Voor beeld van vertonings filter][renditions2]

In het volgende voor beeld werd een encoder gebruikt voor het coderen van een mezzanine-asset in zeven ISO Mp4's-video weergaven (van 180p naar 1080p). De versleutelde Asset kan dynamisch worden verpakt in een van de volgende streaming-protocollen: HLS, Smooth en MPEG DASH.  Boven aan het diagram wordt het HLS-manifest voor het activum zonder filters weer gegeven (dit bevat alle zeven weer gaven).  In de linkerbenedenhoek wordt het HLS-manifest weer gegeven waarop een filter met de naam ' Ott ' is toegepast. Het filter ' Ott ' geeft aan dat alle bitsnelheden onder 1Mbps moeten worden verwijderd, wat heeft geleid tot de laagste twee kwaliteits niveaus die in het antwoord worden verwijderd. In de rechter benedenhoek wordt het HLS-manifest weer gegeven waarop een filter met de naam ' Mobile ' is toegepast. Met het filter ' Mobiel ' geeft u de weer gaven van de oplossing groter dan 720p, waardoor de twee 1080p-weer gaven worden verwijderd.

![Vertoning filteren][renditions1]

## <a name="removing-language-tracks"></a>Taal sporen verwijderen
Uw assets kunnen meerdere audio talen bevatten, zoals Engels, Spaans, Frans, enzovoort. Normaal gesp roken worden met de Player-beheer Programma's standaard audio bijgehouden selectie en beschik bare audio sporen per gebruiker geselecteerd. Het is lastig om dergelijke Sdk's voor de speler te ontwikkelen, maar er zijn verschillende implementaties vereist voor apparaatspecifieke speler-frameworks. Op sommige platforms zijn de Api's van de speler beperkt en bevatten ze geen functie voor audio selectie, waarbij gebruikers de standaard audio track niet kunnen selecteren of wijzigen. Met Asset filters kunt u het gedrag beheren door filters te maken die alleen gewenste audio talen bevatten.

![Taal sporen filteren][language_filter]

## <a name="trimming-start-of-an-asset"></a>Begin van een Asset bijsnijden
In de meest live streaming-gebeurtenissen voeren Opera tors enkele tests uit vóór de werkelijke gebeurtenis. Ze kunnen bijvoorbeeld een pastel bevatten zoals deze voor het begin van de gebeurtenis: ' het programma wordt binnenkort gestart '. Als het programma wordt gearchiveerd, worden de test-en pastel gegevens ook gearchiveerd en opgenomen in de presentatie. Deze informatie mag echter niet worden weer gegeven aan de clients. Met het dynamische manifest kunt u een begin tijd filter maken en de ongewenste gegevens verwijderen uit het manifest.

![Bijsnijden starten][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Subfragmenten (weer gaven) maken op basis van een Live-Archief
Veel live-gebeurtenissen zijn langdurig actief en live archief bevat mogelijk meerdere gebeurtenissen. Nadat de live-gebeurtenis is beëindigd, willen broadcasters mogelijk het Live Archief opsplitsen in de start-en stop sequenties van het logische programma. Vervolgens publiceert u deze virtuele Program ma's afzonderlijk zonder te verwerken van het Live Archief en niet voor het maken van afzonderlijke assets (waarbij het voor deel van de bestaande fragmenten in de cache in de Cdn's niet wordt opgehaald). Voor beelden van dergelijke virtuele Program ma's zijn de kwart alen van een voetbal-of basketbal spel, innings in honkbal of afzonderlijke evenementen van elk sport programma.

Met het dynamische manifest kunt u filters maken met behulp van begin-en eind tijden en virtuele weer gaven maken boven aan uw live-archief. 

![Filter subfragment][subclip_filter]

Gefilterde activa:

![Ski][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Presentation Window aanpassen (DVR)
Azure Media Services biedt momenteel een circulair archief waarbij de duur tussen 5 minuten en 25 uur kan worden geconfigureerd. Het filteren van manifesten kan worden gebruikt voor het maken van een voortschrijdend DVR-venster boven aan het archief, zonder dat er media worden verwijderd. Er zijn veel scenario's waarbij broadcasters een beperkt DVR-venster willen aanbieden om met de Live Edge te verplaatsen en tegelijkertijd een groter archiverings venster te gebruiken. Een omroep gebruiker kan de gegevens uit het DVR-venster gebruiken om clips te markeren, of ze willen mogelijk verschillende DVR-Vensters voor verschillende apparaten opgeven. De meeste mobiele apparaten verwerken bijvoorbeeld geen grote DVR-Windows (u kunt een DVR-venster van 2 minuten hebben voor mobiele apparaten en één uur voor desktop-clients).

![DVR-venster][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff aanpassen (Live positie)
Manifest filtering kan worden gebruikt om enkele seconden van de Live rand van een actief programma te verwijderen. Door middel van filteren kunnen zenders de presentatie bekijken op het publicatie punt van de preview-versie en een advertentie invoegen voordat de kijkers de stroom ontvangen (met 30 seconden). Broadcasters kunnen vervolgens deze advertenties naar hun client raamwerks pushen, zodat ze de informatie ontvangen en verwerken vóór de aankondigings mogelijkheid.

Naast de advertentie ondersteuning, kan de LiveBackoff-instelling worden gebruikt voor het aanpassen van de positie van de kijkers, zodat de gebruiker de Live Edge kan blijven gebruiken in plaats van een HTTP 404-of 412-fout.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Meerdere regels in één filter combi neren
U kunt meerdere filter regels combi neren in één filter. U kunt bijvoorbeeld een ' bereik regel ' definiëren om pastels uit een live archief te verwijderen en ook beschik bare bitrates te filteren. Wanneer u meerdere filter regels toepast, is het eind resultaat het snij punt van alle regels.

![meerdere regels][multiple-rules]

## <a name="create-filters-programmatically"></a>Filters maken via een programma
In het volgende artikel worden Media Services entiteiten besproken die betrekking hebben op filters. In dit artikel ziet u ook hoe u via programma code filters kunt maken.  

[Maak filters met rest-api's](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Meerdere filters combi neren (filter samenstelling)
U kunt ook meerdere filters combi neren in één URL. 

In het volgende scenario ziet u waarom u mogelijk filters wilt combi neren:

1. U moet uw video kwaliteiten filteren voor mobiele apparaten, zoals Android of iPAD (om de video kwaliteiten te beperken). Als u de ongewenste kwaliteiten wilt verwijderen, maakt u een algemeen filter dat geschikt is voor de apparaatprofielen. Zoals eerder in dit artikel wordt vermeld, kunnen globale filters worden gebruikt voor al uw assets onder hetzelfde Media Services-account zonder verdere koppeling. 
2. U wilt ook de begin-en eind tijd van een activum bijsnijden. U kunt dit doen door een lokaal filter te maken en de begin-en eind tijd in te stellen. 
3. U wilt beide filters combi neren (zonder combi natie, moet u kwaliteits filters aan het filter Bijsnijden toevoegen, waardoor het filter gebruik moeilijker wordt).

Als u filters wilt combi neren, moet u de namen van de filters instellen op de URL van het manifest of de afspeel lijst met punt komma's als scheidings teken. We gaan ervan uit dat u een filter hebt met de naam *MyMobileDevice* dat de kwaliteiten filtert en dat u een andere benoemde *MyStartTime* hebt om een specifieke begin tijd in te stellen. U kunt ze als volgt combi neren:

`http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)`

U kunt Maxi maal drie filters combi neren. 

Zie [deze](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog voor meer informatie.

## <a name="know-issues-and-limitations"></a>Bekende problemen en beperkingen
* Dynamische manifesten werkt in GOP terug-grenzen (keyframes), waardoor het verkleinen van GOP terug nauw keurig is. 
* U kunt dezelfde filter naam gebruiken voor lokale en globale filters. Lokale filters hebben een hogere prioriteit en overschrijven globale filters.
* Als u een filter bijwerkt, kan het tot twee minuten duren voordat het streaming-eind punt de regels heeft vernieuwd. Als de inhoud is bediend met enkele filters (en in de cache opgeslagen in proxy's en CDN-caches), kunnen deze filters worden bijgewerkt als gevolg van fouten in de speler. Het is raadzaam om de cache te wissen na het bijwerken van het filter. Als deze optie niet mogelijk is, kunt u overwegen een ander filter te gebruiken.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Overzicht van het leveren van inhoud aan klanten](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
