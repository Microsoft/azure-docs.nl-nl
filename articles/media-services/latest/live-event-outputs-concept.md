---
title: Concepten voor livegebeurtenissen en live-uitvoer
description: In dit onderwerp vindt u een overzicht van livegebeurtenissen en live-uitvoer in Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: 44ab9e4ff83fec2ddfbd1cb44f503298d12789d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766295"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Livegebeurtenissen en live-uitvoer in Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services kunt u livegebeurtenissen leveren aan uw klanten in de Azure-cloud. Als u uw live streaminggebeurtenissen in Media Services v3 wilt instellen, moet u de concepten begrijpen die in dit artikel worden besproken.

> [!TIP]
> Voor klanten die migreren vanuit Media Services v2-API's, vervangt de **entiteit voor livegebeurtenissen** **Channel** in v2 en vervangt **live** uitvoer **programma**.

## <a name="live-events"></a>Livegebeurtenissen

[Livegebeurtenissen](/rest/api/media/liveevents) zijn verantwoordelijk voor het opnemen en verwerken van de live videofeeds. Wanneer u een livegebeurtenis maakt, wordt er een primair en secundair invoereindpunt gemaakt dat u kunt gebruiken om een livesignaal te verzenden vanaf een externe encoder. De externe live-encoder verzendt de bijdragefeed naar dat invoer-eindpunt met behulp van het [RTMP-](https://www.adobe.com/devnet/rtmp.html) of [Smooth Streaming-invoerprotocol](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (gefragmenteerd MP4). Voor het RTMP-opnameprotocol kan de inhoud worden verzonden in het duidelijke ( ) of veilig worden versleuteld `rtmp://` op de kabel( `rtmps://` ). Voor het Smooth Streaming opnameprotocol zijn de ondersteunde URL-schema's `http://` of `https://` .  

## <a name="live-event-types"></a>Typen livegebeurtenissen

Een [livegebeurtenis](/rest/api/media/liveevents) kan worden ingesteld op een *pass-through* (een on-premises live-encoder verzendt een stream met meerdere bitrates) of *live* codering (een on-premises live-encoder verzendt een stream met één bitsnelheid). De typen worden ingesteld tijdens het maken met [liveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None:** een on-premises live-encoder verzendt een stream met meerdere bitrates. De opgenomen stream passeert de livegebeurtenis zonder verdere verwerking. Wordt ook wel de pass-through-modus genoemd.
* **LiveEventEncodingType.Standard:** een on-premises live-encoder verzendt een single-bitrate stream naar de livegebeurtenis en Media Services meerdere bitrate streams maakt. Als de bijdragefeed een resolutie van 720p of hoger heeft, codeert de **default720p-voorinstelling** een set van 6 resolutie/bitrates-paren.
* **LiveEventEncodingType.Premium1080p:** een on-premises live-encoder verzendt een single-bitrate stream naar de livegebeurtenis en Media Services meerdere bitrate streams maakt. Met de standaardinstelling Default1080p wordt de uitvoerset met resolution/bitrates-paren opgegeven.

### <a name="pass-through"></a>Pass-through

![pass-through livegebeurtenis met Media Services voorbeelddiagram](./media/live-streaming/pass-through.svg)

Wanneer u de **pass-through** livegebeurtenis gebruikt, vertrouwt u op uw on-premises live-encoder om een videostream met meerdere bitrates te genereren en deze te verzenden als bijdragefeed voor de livegebeurtenis (met behulp van RTMP of gefragmenteerd MP4-protocol). De livegebeurtenis voert vervolgens de binnenkomende videostreams uit zonder verdere verwerking. Een dergelijke pass-through livegebeurtenis is geoptimaliseerd voor langlopende livegebeurtenissen of 24x365 lineair live streamen. Wanneer u dit type livegebeurtenis maakt, geeft u Geen op (LiveEventEncodingType.None).

U kunt de bijdragefeed verzenden bij een resolutie tot maximaal 4K en een framesnelheid van 60 frames per seconde, met ofwel H.264/AVC- of H.265/HEVC-videocodecs, en AAC (AAC-LC, HE-AACv1 of HE-AACv2)-audiocodec. Zie Vergelijking van [typen livegebeurtenissen voor meer informatie.](live-event-types-comparison-reference.md)

> [!NOTE]
> Het gebruik van een pass-through-methode is de voordeligste manier om live te streamen wanneer u meerdere gebeurtenissen gedurende een lange periode doet en u al hebt geïnvesteerd in on-premises coderingen. Zie [Prijsdetails.](https://azure.microsoft.com/pricing/details/media-services/)
>

Zie het .NET-codevoorbeeld voor het maken van een pass-through livegebeurtenis in [livegebeurtenis met DVR](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/4a436376e77bad57d6cbfdc02d7df6c615334574/Live/LiveEventWithDVR/Program.cs#L214).

### <a name="live-encoding"></a>Live Encoding  

![live codering met Media Services voorbeelddiagram](./media/live-streaming/live-encoding.svg)

Wanneer u live codering met Media Services gebruikt, configureert u uw on-premises live-encoder voor het verzenden van één bitrate video als bijdragefeed voor de livegebeurtenis (met behulp van RTMP of Fragmented-Mp4-protocol). Vervolgens stelt u een livegebeurtenis in, zodat deze de binnenkomende [single-bitrate](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)stream codeert naar een videostream met meerdere bitrates en de uitvoer beschikbaar maakt voor levering om apparaten af te spelen via protocollen zoals MPEG-DASH, HLS en Smooth Streaming.

Wanneer u live codering gebruikt, kunt u de bijdragefeed alleen verzenden bij resoluties tot 1080p resolutie met een framesnelheid van 30 frames per seconde, met H.264/AVC-videocodec en AAC (AAC-LC, HE-AACv1 of HE-AACv2) audiocodec. Houd er rekening mee dat pass-through livegebeurtenissen resoluties van maximaal 4.000 frames per seconde kunnen ondersteunen. Zie Vergelijking van [typen livegebeurtenissen voor meer informatie.](live-event-types-comparison-reference.md)

De resoluties en bitrates in de uitvoer van de live-encoder worden bepaald door de vooraf ingestelde. Als u een **Standaard** live-encoder (LiveEventEncodingType.Standard) gebruikt, geeft de standaardinstelling *Default720p* een set van zes resolutie/bit-snelheidsparen op, van 720p bij 3,5 Mbps omlaag naar 192p bij 200 kbps. Als u anders een Live Encoder **Premium1080p** (LiveEventEncodingType.Premium1080p) gebruikt, geeft de standaardinstelling *Default1080p* een set van zes resolutie/bit-snelheidsparen op, die van 1080p op 3,5 Mbps omlaag gaat naar 180p bij 200 kbps. Zie [Systeemwaarden](live-event-types-comparison-reference.md#system-presets) voor meer informatie.

> [!NOTE]
> Als u de vooraf ingestelde live codering moet aanpassen, opent u een ondersteuningsticket via Azure Portal. Geef de gewenste tabel met resolutie en bitrates op. Controleer of er slechts één laag op 720p is (als u een voorinstelling voor een Standard live-encoder aanvraagt) of op 1080p (als u een vooraf ingestelde voor een Live Encoder premium1080p aanvraagt) en 6 lagen.

## <a name="creating-live-events"></a>Livegebeurtenissen maken

### <a name="options"></a>Opties

Wanneer u een livegebeurtenis maakt, kunt u de volgende opties opgeven:

* U kunt de livegebeurtenis een naam en een beschrijving geven.
* Cloudcoderen omvat pass-through (geen cloudcoderen), Standard (maximaal 720p) of Premium (tot 1080p). Voor Standard- en Premium-codering kunt u de stretch-modus van de gecodeerde video kiezen.
  * Geen: respecteert strikt de uitvoerresolutie die is opgegeven in de vooraf ingestelde codering zonder rekening te houden met de pixel-aspectverhouding of de beeldverhouding van de invoervideo.
  * AutoSize: overschrijven de uitvoerresolutie en wijzigt deze in de breedteverhouding van de weergave van de invoer, zonder opvulling. Als de invoer bijvoorbeeld 1920x1080 is en de coderingsvoorinstelling vraagt om 1280x1280, wordt de waarde in de voorinstelling overgeslagen en wordt de uitvoer op 1280x720, waarbij de verhouding van het invoeraspect van 16:9 wordt behouden.
  * AutoFit: pads de uitvoer (met letterbox of pijler vak) om te houden aan de uitvoer resolutie, terwijl ervoor te zorgen dat de actieve videoregio in de uitvoer dezelfde hoogte-breedteverhouding als de invoer heeft. Als de invoer bijvoorbeeld 1920x1080 is en de coderingsvoorinstelling vraagt om 1280x1280, is de uitvoer 1280x1280, die een binnenste rechthoek van 1280x720 bevat met een aspectverhouding van 16:9, met regio's van de pijlervak 280 pixels breed aan de linkerkant en rechts.
* Streamingprotocol (momenteel worden de RTMP- en Smooth Streaming-protocollen ondersteund). U kunt de protocoloptie niet wijzigen terwijl de livegebeurtenis of de bijbehorende live-uitvoer wordt uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u een afzonderlijke livegebeurtenis voor elk streamingprotocol.
* Invoer-id die een wereldwijd unieke id is voor de invoerstroom van de livegebeurtenis.
* Statisch hostnaam voorvoegsel dat geen bevat (in dat geval een willekeurige 128-bits hextische tekenreeks wordt gebruikt), naam van livegebeurtenis gebruiken of aangepaste naam gebruiken.  Wanneer u ervoor kiest om een klantnaam te gebruiken, is deze waarde het voorvoegsel Aangepaste hostnaam.
* U kunt de end-to-endlatentie tussen de live-uitzending en het afspelen verminderen door het interval voor het invoersleutelframe in te stellen( dit is de duur (in seconden) van elk mediasegment in de HLS-uitvoer. De waarde moet een geheel getal zijn dat niet nul is in het bereik van 0,5 tot 20 seconden.  De waarde wordt standaard ingesteld op 2 seconden als *geen van* de invoer- of uitvoersleutelframeintervallen zijn ingesteld. Het interval voor sleutelframes is alleen toegestaan voor pass-through-gebeurtenissen.
* Wanneer u de gebeurtenis maakt, kunt u deze instellen op autostart. Wanneer autostart is ingesteld op true, wordt de livegebeurtenis gestart nadat deze is gemaakt. De facturering begint zodra de livegebeurtenis wordt uitgevoerd. U moet expliciet Stoppen aanroepen voor de resource van de livegebeurtenis om verdere facturering te stoppen. U kunt de gebeurtenis ook starten wanneer u klaar bent om te streamen.

> [!NOTE]
> De maximale framesnelheid is 30 fps voor zowel Standard- als Premium-codering.

## <a name="standby-mode"></a>StandBy-modus

Wanneer u een livegebeurtenis maakt, kunt u deze instellen op stand-bymodus. Terwijl de gebeurtenis zich in de stand-bymodus, kunt u de beschrijving, het statische hostnaam voorvoegsel en de toegangsinstellingen voor invoer en preview beperken.  De StandBy-modus is nog steeds een factureerbare modus, maar is anders geprijsd dan wanneer u een livestream start.

Zie Livegebeurtenissen en [facturering voor meer informatie.](live-event-states-billing-concept.md)

* IP-beperkingen voor de opname en voorbeeldweergave. U kunt de IP-adressen definiëren die een video mogen opnemen in deze livegebeurtenis. Toegestane IP-adressen kunnen worden opgegeven als één IP-adres (bijvoorbeeld 10.0.0.1), een IP-adresbereik met een IP-adres en een CIDR-subnetmasker (bijvoorbeeld 10.0.0.1/22) of een IP-adresbereik met een IP-adres en een decimaal subnetmasker met punten (bijvoorbeeld , ' 10.0.0.1(255.255.252.0)').
<br/><br/>
Als geen IP-adressen zijn opgegeven en er geen regeldefinitie bestaat, zijn er geen IP-adressen toegestaan. Als u IP-adres(sen) wilt toestaan, maakt u een regel en stelt u 0.0.0.0/0 in.<br/>De IP-adressen moeten een van de volgende indelingen hebben: IpV4-adres met 4 cijfers of een CIDR-adresbereik.
<br/><br/>
Als u bepaalde IP-adressen in uw eigen firewalls wilt inschakelen of invoer voor uw livegebeurtenissen wilt beperken tot Azure IP-adressen, downloadt u een JSON-bestand van DE IP-adresbereiken van [Azure Datacenter.](https://www.microsoft.com/download/details.aspx?id=41653) Selecteer de sectie Details op de pagina **voor meer informatie** over dit bestand.

* Wanneer u de gebeurtenis maakt, kunt u ervoor kiezen om livetranscripties in te zetten. Livetranscriptie is standaard uitgeschakeld. Lees Livetranscriptie voor meer informatie [over livetranscriptie.](live-event-live-transcription-how-to.md)

### <a name="naming-rules"></a>Naamgevingsregels

* De maximale naam van de livegebeurtenis is 32 tekens.
* De naam moet dit [regex-patroon](/dotnet/standard/base-types/regular-expression-language-quick-reference) volgen: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

Zie ook [Naamconvents voor streaming-eindpunten.](stream-streaming-endpoint-concept.md#naming-convention)

> [!TIP]
> Om de uniekheid van de naam van uw livegebeurtenis te garanderen, kunt u een GUID genereren en vervolgens alle afbreekstree sluiten en haakjes (indien aanwezig) verwijderen. De tekenreeks is uniek voor alle livegebeurtenissen en de lengte is gegarandeerd 32.

## <a name="live-event-ingest-urls"></a>URL's voor opnemen van livegebeurtenissen

Zodra de livegebeurtenis is gemaakt, kunt u URL's voor opnemen krijgen die u aan de live on-premises encoder verstrekt. De live-encoder gebruikt deze URL's voor het invoeren van een live-stream. Zie Aanbevolen [on-premises live coderingen voor meer informatie.](encode-recommended-on-premises-live-encoders.md)

>[!NOTE]
> Vanaf de API-release van 2020-05-01 worden 'vanity'-URL's statische hostnamen genoemd (useStaticHostname: true)


> [!NOTE]
> Als u wilt dat een opname-URL statisch en voorspelbaar is voor gebruik in een hardware-encoder-installatie, stelt u de eigenschap **useStaticHostname** in op true en stelt u bij elke aanmaak de eigenschap **accessToken** in op dezelfde GUID. 

### <a name="example-liveevent-and-liveeventinput-configuration-settings-for-a-static-non-random-ingest-rtmp-url"></a>Voorbeeld van een LiveEvent- en LiveEventInput-configuratie-instellingen voor een statische (niet-willekeurige) OPNAME RTMP-URL.

```csharp
             LiveEvent liveEvent = new LiveEvent(
                    location: mediaService.Location,
                    description: "Sample LiveEvent from .NET SDK sample",
                    // Set useStaticHostname to true to make the ingest and preview URL host name the same. 
                    // This can slow things down a bit. 
                    useStaticHostname: true,

                    // 1) Set up the input settings for the Live event...
                    input: new LiveEventInput(
                        streamingProtocol: LiveEventInputProtocol.RTMP,  // options are RTMP or Smooth Streaming ingest format.
                                                                         // This sets a static access token for use on the ingest path. 
                                                                         // Combining this with useStaticHostname:true will give you the same ingest URL on every creation.
                                                                         // This is helpful when you only want to enter the URL into a single encoder one time for this Live Event name
                        accessToken: "acf7b6ef-8a37-425f-b8fc-51c2d6a5a86a",  // Use this value when you want to make sure the ingest URL is static and always the same. If omitted, the service will generate a random GUID value.
                        accessControl: liveEventInputAccess, // controls the IP restriction for the source encoder.
                        keyFrameIntervalDuration: "PT2S" // Set this to match the ingest encoder's settings
                    ),
```

* Niet-statische hostnaam

    Een niet-statische hostnaam is de standaardmodus in Media Services v3 bij het maken van een **LiveEvent.** U kunt de livegebeurtenis iets sneller toewijzen, maar de opname-URL die u nodig hebt voor uw hardware of software voor live codering wordt gerandomiseerd. De URL wordt gewijzigd als u de livegebeurtenis stopt/start. Niet-statische hostnamen zijn alleen nuttig in scenario's waarin een eindgebruiker wil streamen met behulp van een app die zeer snel een livegebeurtenis moet krijgen en een dynamische opname-URL geen probleem is.

    Als een client-app geen opname-URL vooraf hoeft te genereren voordat de livegebeurtenis wordt gemaakt, Media Services het toegangsken voor de livegebeurtenis automatisch genereren.

* Statische hostnamen 

    De statische hostnaammodus heeft de voorkeur van de meeste operators die hun live coderingshardware of -software vooraf willen configureren met een RTMP-opname-URL die nooit verandert bij het maken of stoppen/starten van een specifieke livegebeurtenis. Deze operators willen een voorspellende RTMP-opname-URL die na een periode niet verandert. Dit is ook zeer nuttig wanneer u een statische RTMP-opname-URL naar de configuratie-instellingen van een hardware-coderingsapparaat wilt pushen, zoals de BlackMagic Atem Mini Pro, of vergelijkbare hulpprogramma's voor hardwarecoderen en productie. 

    > [!NOTE]
    > In de Azure Portal de statische hostnaam-URL '*Statisch hostnaam voorvoegsel '* genoemd.

    Als u deze modus in de API wilt opgeven, stelt u in `useStaticHostName` op tijdens het maken `true` (standaard is `false` ). Wanneer is ingesteld op true, geeft de het eerste deel van de hostnaam op die is toegewezen aan de `useStaticHostname` `hostnamePrefix` livegebeurtenisvoorbeeld en opname-eindpunten. De uiteindelijke hostnaam is een combinatie van dit voorvoegsel, de naam van het mediaserviceaccount en een korte code voor het Azure Media Services datacentrum.

    Om een willekeurig token in de URL te voorkomen, moet u tijdens het maken ook uw eigen toegangs token `LiveEventInput.accessToken` () doorgeven.  Het toegangsken moet een geldige GUID-tekenreeks zijn (met of zonder de afbreekstree streepjes). Zodra de modus is ingesteld, kan deze niet worden bijgewerkt.

    Het toegangsken moet uniek zijn in uw Azure-regio en Media Services account. Als uw app gebruik moet maken van een statische URL voor de opname van hostnamen, is het raadzaam om altijd een nieuwe GUID-instantie te maken voor gebruik met een specifieke combinatie van regio, Media Services-account en livegebeurtenis.

    Gebruik de volgende API's om de statische hostnaam-URL in te stellen en stel het toegangsken in op een geldige GUID (bijvoorbeeld `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` ).  

    |Taal|Statische hostnaam-URL inschakelen|Toegangstoken instellen|
    |---|---|---|
    |REST|[properties.useStaticHostname](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.useStaticHostname](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--use-static-hostname](/cli/azure/ams/live-event#az_ams_live_event_create)|[--access-token](/cli/azure/ams/live-event#optional-parameters)|
    |.NET|[LiveEvent.useStaticHostname](/dotnet/api/microsoft.azure.management.media.models.liveevent.usestatichostname?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Management_Media_Models_LiveEvent_UseStaticHostname)|[LiveEventInput.AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>Naamgevingsregels voor URL voor live opname

* De *willekeurige* tekenreeks hieronder is een 128-bits hexadecimaal getal (bestaande uit 32 tekens, van 0-9 en a-f).
* *uw toegangs token:* de geldige GUID-tekenreeks die u hebt ingesteld bij het gebruik van de instelling voor de statische hostnaam. Bijvoorbeeld `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *streamnaam:* hiermee wordt de naam van de stream voor een specifieke verbinding aangegeven. De waarde van de streamnaam wordt meestal toegevoegd door de live-encoder die u gebruikt. U kunt de live-encoder configureren voor het gebruik van een naam om de verbinding te beschrijven, bijvoorbeeld : 'video1_audio1', 'video2_audio1', 'stream'.

#### <a name="non-static-hostname-ingest-url"></a>Opname-URL voor niet-statische hostnaam

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="static-hostname-ingest-url"></a>Opname-URL voor statische hostnaam

In de volgende paden betekent ofwel de naam die is opgegeven voor de gebeurtenis of de aangepaste naam die wordt gebruikt `<live-event-name>` bij het maken van de livegebeurtenis.

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>PREVIEW-URL voor livegebeurtenissen

Zodra de livegebeurtenis de bijdragefeed ontvangt, kunt u het preview-eindpunt gebruiken om een voorbeeld te bekijken en te valideren dat u de livestream ontvangt voordat u verder publiceert. Nadat u hebt gecontroleerd of de preview-stream goed is, kunt u de livegebeurtenis gebruiken om de livestream beschikbaar te maken voor levering via een of meer (vooraf gemaakte) streaming-eindpunten. Maak hiervoor een nieuwe [live-uitvoer op](/rest/api/media/liveoutputs) de livegebeurtenis.

> [!IMPORTANT]
> Zorg ervoor dat de video naar de preview-URL stroomt voordat u doorgaat.

## <a name="live-event-long-running-operations"></a>Langdurige bewerkingen voor livegebeurtenissen

Zie Langlopende [bewerkingen voor meer informatie.](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Live-uitvoer

Zodra de stream naar de livegebeurtenis stroomt, kunt u de streaminggebeurtenis starten door een [asset,](/rest/api/media/assets) [live uitvoer](/rest/api/media/liveoutputs)en [streaming-locator te maken.](/rest/api/media/streaminglocators) Met live-uitvoer wordt de stream gearchiveerd en beschikbaar gemaakt voor kijkers via het [streaming-eindpunt](/rest/api/media/streamingendpoints).  

Zie Using a cloud DVR (Een cloud-DVR gebruiken) voor gedetailleerde [informatie over live-uitvoer.](live-event-cloud-dvr-time-how-to.md)

## <a name="live-event-output-questions"></a>Vragen over uitvoer van livegebeurtenissen

Zie het [artikel met vragen over de uitvoer van livegebeurtenissen.](questions-collection.md#live-streaming)
