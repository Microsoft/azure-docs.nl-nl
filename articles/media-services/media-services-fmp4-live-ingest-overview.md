---
title: Azure Media Services gefragmenteerde MP4 Live opname-specificatie | Microsoft Docs
description: Deze specificatie beschrijft het protocol en de indeling voor gefragmenteerde op MP4 gebaseerde Live-streaming opname voor Azure Media Services. In dit document worden ook aanbevolen procedures beschreven voor het bouwen van uiterst redundante en robuuste Live opname mechanismen.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 7323ae611431e1d91fd1a8471914be388fcc4712
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92019508"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure Media Services gefragmenteerde MP4 Live opname-specificatie 

Deze specificatie beschrijft het protocol en de indeling voor gefragmenteerde op MP4 gebaseerde Live-streaming opname voor Azure Media Services. Media Services biedt een service voor live streamen die klanten in realtime in real time kunnen gebruiken om live-gebeurtenissen te streamen en inhoud te verzenden met Azure als Cloud platform. In dit document worden ook aanbevolen procedures beschreven voor het bouwen van uiterst redundante en robuuste Live opname mechanismen.

## <a name="1-conformance-notation"></a>1. de notatie voor conformiteit
De sleutel woorden ' moet ', ' mag niet ', ' vereist ', ' "mag niet ', ' moet ', ' is ', ' mag niet ', ' is ', ' is ' en ' optioneel ' in dit document moeten worden geïnterpreteerd zoals beschreven in RFC 2119.

## <a name="2-service-diagram"></a>2. service diagram
In het volgende diagram ziet u de architectuur op hoog niveau van de live streaming-service in Media Services:

1. Met een live coderings programma worden live feeds gepusht naar kanalen die zijn gemaakt en ingericht via de Azure Media Services SDK.
1. Kanalen, Program ma's en streaming-eind punten in Media Services omgaan met alle functies van live streaming, waaronder opname, opmaak, Cloud-DVR, beveiliging, schaal baarheid en redundantie.
1. Klanten kunnen er ook voor kiezen om een Azure Content Delivery Network-laag te implementeren tussen het streaming-eind punt en de client eindpunten.
1. Client-eind punten streamen van het streaming-eind punt met behulp van HTTP-protocollen voor adaptief streamen. Voor beelden zijn onder andere micro soft Smooth Streaming, dynamisch adaptief streamen via HTTP (DASH of MPEG-DASH) en Apple HTTP Live Streaming (HLS).

![opname stroom][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. bitstream-indeling – ISO 14496-12 gefragmenteerde MP4
De telegram indeling voor live streaming-opname die in dit document wordt besproken, is gebaseerd op [ISO-14496-12]. Zie [[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)voor een gedetailleerde beschrijving van gefragmenteerde MP4-indeling en-extensies voor video-on-demand-bestanden en opname van live streamen.

### <a name="live-ingest-format-definitions"></a>Definities van Live opname-indelingen
In de volgende lijst worden speciale opmaak definities beschreven die van toepassing zijn op Live-opname in Azure Media Services:

1. De vakken **ftyp**, **Live server manifest** en **Moov** moeten bij elke aanvraag (http post) worden verzonden. Deze vakken moeten aan het begin van de stroom worden verzonden en telkens wanneer het coderings programma opnieuw verbinding moet maken om de stroom opname te hervatten. Zie sectie 6 in [1] voor meer informatie.
1. Sectie 3.3.2 in [1] definieert een optioneel vak met de naam **StreamManifestBox** voor Live-opname. Als gevolg van de routerings logica van de Azure load balancer, is het gebruik van dit vak afgeschaft. Het vak mag niet aanwezig zijn bij het opnemen in Media Services. Als dit vak aanwezig is, wordt het door Media Services op de achtergrond genegeerd.
1. Het vak **TrackFragmentExtendedHeaderBox** gedefinieerd in 3.2.3.2 in [1] moet voor elk fragment aanwezig zijn.
1. Versie 2 van het vak **TRACKFRAGMENTEXTENDEDHEADERBOX** moet worden gebruikt voor het genereren van media segmenten met identieke url's in meerdere data centers. Het veld fragment index is vereist voor de failover van meerdere data centers op basis van op index gebaseerde streaming-indelingen, zoals Apple HLS en MPEG-DASH op basis van index. Als u failover tussen meerdere data centers mogelijk wilt maken, moet de fragment index worden gesynchroniseerd over verschillende coderings Programma's en met 1 worden verhoogd voor elk volgend media fragment, zelfs bij het opnieuw opstarten of mislukken van een encoder.
1. De sectie 3.3.6 in [1] definieert een box met de naam **MovieFragmentRandomAccessBox** (**mfra**) die aan het einde van de live opname kan worden verzonden om aan het kanaal end-of-stream (EOS) aan te geven. Als gevolg van de opname logica van Media Services, is het gebruik van EOS afgeschaft en wordt het vak **mfra** voor Live opname niet verzonden. Als deze is verzonden, worden deze door Media Services op de achtergrond genegeerd. Als u de status van het opname punt opnieuw wilt instellen, raden we u aan om het gebruik van het [kanaal opnieuw](/rest/api/media/operations/channel#reset_channels)in te stellen. We raden u ook aan om [programma stoppen](/rest/api/media/operations/program#stop_programs) te gebruiken om een presentatie en stroom te beëindigen.
1. De duur van de MP4-fragment moet constant zijn, om de client manifesten te reduceren. Een constante MP4-fragment duur verbetert ook het downloaden van de client met behulp van herhalings codes. De duur kan schommelen om de niet-integere frame snelheden te compenseren.
1. De duur van de MP4-fragment moet tussen 2 en 6 seconden liggen.
1. De time Stamps en indexen van MP4-fragmenten (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` en `fragment_index` ) moeten in oplopende volg orde arriveren. Hoewel Media Services robuust is voor dubbele fragmenten, heeft het beperkte mogelijkheid om fragmenten te wijzigen op basis van de media tijdlijn.

## <a name="4-protocol-format--http"></a>4. Protocol-indeling – HTTP
In ISO gefragmenteerde op MP4 gebaseerde Live-opname voor Media Services gebruikt een standaard langlopende HTTP POST-aanvraag voor het verzenden van gecodeerde media gegevens die in gefragmenteerde MP4-indeling naar de service zijn verpakt. Elk HTTP POST-bericht verzendt een volledig gefragmenteerde MP4 Bitstream (stream), beginnend vanaf het begin met koptekst vakken (**ftyp**, **Live server-manifest Box** en **Moov** ), en gaat verder met een reeks fragmenten (**moof** -en **mdat** -vakken). Zie voor de URL-syntaxis voor de HTTP POST-aanvraag sectie 9,2 in [1]. Een voor beeld van de POST-URL is: 

`http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)`

### <a name="requirements"></a>Vereisten
Hier volgen de gedetailleerde vereisten:

1. Het coderings programma moet de uitzending starten door een HTTP POST-aanvraag te verzenden met een lege "Body" (lengte van nul) door gebruik te maken van dezelfde opname-URL. Op deze manier kan het coderings programma snel detecteren of het live opname-eind punt geldig is, en als er verificatie of andere voor waarden vereist zijn. Per HTTP-protocol kan de server een HTTP-antwoord niet terugsturen totdat de volledige aanvraag, inclusief de hoofd tekst, is ontvangen. Gezien het langdurige karakter van een live-gebeurtenis, zonder deze stap, kan het coderings programma mogelijk geen fouten detecteren totdat alle gegevens zijn verzonden.
1. Het coderings programma moet eventuele fouten of authenticatie problemen verwerken vanwege (1). Als (1) met een 200-antwoord slaagt, kunt u door gaan.
1. Het coderings programma moet een nieuwe HTTP POST-aanvraag met de gefragmenteerde MP4-stroom starten. De payload moet beginnen met de koptekst vakken, gevolgd door fragmenten. Houd er rekening mee dat de vakken **ftyp**, **Live server manifest** en **Moov** (in deze volg orde) moeten worden verzonden met elke aanvraag, zelfs als het coderings programma opnieuw moet worden verbonden, omdat de vorige aanvraag vóór het einde van de stroom is beëindigd. 
1. Het coderings programma moet gesegmenteerde overdrachts codering gebruiken voor het uploaden, omdat het onmogelijk is om de volledige lengte van de inhoud van de live-gebeurtenis te voors pellen.
1. Wanneer de gebeurtenis zich voordoet, moet het coderings programma, na het verzenden van het laatste fragment, de gesegmenteerde overdrachts coderings bericht sequentie zonder problemen beëindigen (de meeste HTTP-client stacks worden automatisch verwerkt). Het coderings programma moet wachten tot de service de laatste reactie code heeft geretourneerd en vervolgens de verbinding verbreken. 
1. Het coderings programma mag het `Events()` zelfstandige naam woord niet gebruiken, zoals beschreven in 9,2 in [1] voor Live opname in Media Services.
1. Als de HTTP POST-aanvraag wordt beëindigd of een time-out optreedt met een TCP-fout vóór het einde van de stroom, moet het coderings programma een nieuwe POST-aanvraag uitgeven met een nieuwe verbinding en de voor gaande vereisten volgen. Daarnaast moet het coderings programma de vorige twee MP4-fragmenten opnieuw verzenden voor elk spoor in de stroom en wordt hervat zonder een onderbreking in de media tijdlijn te introduceren. Als u de laatste twee MP4-fragmenten opnieuw verzendt voor elk spoor, zorgt u ervoor dat er geen gegevens verloren gaan. Met andere woorden, als een stroom zowel een audio-als een video track bevat en de huidige POST-aanvraag mislukt, moet het coderings programma opnieuw verbinding maken en de laatste twee fragmenten opnieuw verzenden voor het audio spoor, dat eerder is verzonden en de laatste twee fragmenten voor het video spoor, die eerder zijn verzonden, om ervoor te zorgen dat er geen gegevens verloren gaan. Het coderings programma moet een "Forward" buffer van media fragmenten onderhouden, die wordt opnieuw verzonden wanneer er opnieuw verbinding wordt gemaakt.

## <a name="5-timescale"></a>5. tijd schaal
[[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) hierin wordt het gebruik van de tijd schaal voor **SmoothStreamingMedia** (sectie 2.2.2.1), **StreamElement** (Section 2.2.2.3), **StreamFragmentElement** (Section 2.2.2.6) en **LiveSMIL** (Section 2.2.7.3.1) beschreven. Als de tijdschaal waarde niet aanwezig is, is de gebruikte standaard waarde 10.000.000 (10 MHz). Hoewel de specificatie van de Smooth Streaming-indeling geen gebruik van andere tijdschaal waarden blokkeert, gebruiken de meeste implementaties van encoders deze standaard waarde (10 MHz) voor het genereren van Smooth Streaming opname gegevens. Als gevolg van de functie voor [dynamische verpakking van Azure media](./previous/media-services-dynamic-packaging-overview.md) , raden we u aan een 90-kHz tijd schaal te gebruiken voor video-streams en 44,1 khz of 48,1 kHz voor audio-streams. Als er verschillende tijdschaal waarden worden gebruikt voor verschillende stromen, moet de tijd schaal van het stroom niveau worden verzonden. Zie [[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)voor meer informatie.     

## <a name="6-definition-of-stream"></a>6. definitie van stream
Stream is de basis eenheid van de bewerking in Live opname voor het opstellen van live-presentaties, het verwerken van streaming-failover en redundantie scenario's. Stream is gedefinieerd als één unieke, gefragmenteerde MP4-bitstream die een enkel spoor of meerdere sporen kan bevatten. Een volledige live-presentatie kan een of meer streams bevatten, afhankelijk van de configuratie van de Live coderings Programma's. In de volgende voor beelden ziet u verschillende opties voor het gebruik van stromen voor het samen stellen van een volledige live-presentatie.

**Voorbeeld:** 

Een klant wil een live streamen-presentatie maken die de volgende audio/video-bitrates bevat:

Video – 3000 kbps, 1500 kbps, 750 kbps

Audio-128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>Optie 1: alle sporen in één stream
Met deze optie worden alle audio-en video tracks gegenereerd door één coderings programma en worden deze vervolgens in één gefragmenteerde MP4-bitstream gebundeld. De gefragmenteerde MP4 bitstream wordt vervolgens verzonden via één HTTP POST-verbinding. In dit voor beeld is er slechts één stroom voor deze live-presentatie.

![Stromen-één track][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Optie 2: elk nummer in een afzonderlijke stroom
Met deze optie wordt één track in elk fragment MP4 bitstream geplaatst en worden alle streams via afzonderlijke HTTP-verbindingen gepost. Dit kan worden gedaan met één coderings programma of met meerdere coderings Programma's. De live-opname ziet er als volgt uit dat deze live presentatie bestaat uit vier streams.

![Stromen-afzonderlijke trajecten][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Optie 3: het audio spoor met de laagste bitsnelheid video track in één stream bundelen
In deze optie kiest de klant voor het bundelen van het audio spoor met het video spoor met de laagste bitsnelheid in één fragment MP4 bitstream, en laat u de andere twee video tracks als afzonderlijke streams. 

![Streams-audio-en video tracks][image4]

### <a name="summary"></a>Samenvatting
Dit is een volledig overzicht van alle mogelijke opname opties voor dit voor beeld. Het is belang rijk dat elke groep tracks in stromen wordt ondersteund door Live opname. Klanten en coderings leveranciers kunnen hun eigen implementaties kiezen op basis van technische complexiteit, coderings capaciteit en redundantie-en failover-overwegingen. In de meeste gevallen is er echter slechts één audio track voor de hele live presentatie. Het is dus belang rijk om te zorgen voor de healthiness van de opname stroom die het audio spoor bevat. Deze overweging resulteert vaak in het plaatsen van het audio spoor in een eigen stroom (zoals in optie 2) of het bundelen hiervan met het video spoor met de laagste bitsnelheid (zoals in optie 3). Voor een betere redundantie en fout tolerantie kunt u ook hetzelfde audio spoor verzenden in twee verschillende streams (optie 2 met redundante audio tracks) of het audio spoor bundelen met ten minste twee van de video tracks met de laagste bitsnelheid (optie 3 met audio gebundeld in ten minste twee video stromen) wordt sterk aanbevolen voor Live-opname in Media Services.

## <a name="7-service-failover"></a>7. failover van service
Gezien de aard van live streamen is een goede failover-ondersteuning van cruciaal om de beschik baarheid van de service te garanderen. Media Services is ontworpen voor het afhandelen van verschillende soorten fouten, waaronder netwerk fouten, Server fouten en opslag problemen. Bij gebruik in combi natie met de juiste failover-logica van de Live Encoder-kant kunnen klanten een zeer betrouw bare service voor live streamen vanuit de Cloud krijgen.

In deze sectie bespreken we scenario's voor de failover van een service. In dit geval treedt de fout ergens in de service op en wordt het systeem als een netwerk fout gemanifesteerd. Hier volgen enkele aanbevelingen voor de implementatie van het coderings programma voor het verwerken van failover van de service:

1. Gebruik een time-out van 10 seconden voor het tot stand brengen van de TCP-verbinding. Als de verbinding tot stand kan worden gebracht, neemt de bewerking af en probeert u het opnieuw. 
1. Gebruik een korte time-out voor het verzenden van de segment berichten van de HTTP-aanvraag. Als de duur van het doel-MP4-fragment N seconden is, gebruikt u een time-out voor verzenden tussen N en 2 N seconden; Als de duur van de MP4-fragment bijvoorbeeld 6 seconden is, gebruikt u een time-out van 6 tot 12 seconden. Als er een time-out optreedt, stelt u de verbinding opnieuw in, opent u een nieuwe verbinding en hervat u de stroom opname op de nieuwe verbinding. 
1. Onderhoud een roulerende buffer met de laatste twee fragmenten voor elk spoor die zijn geslaagd en volledig naar de service zijn verzonden.  Als de HTTP POST-aanvraag voor een stroom wordt beëindigd of een time-out voorafgaat aan het einde van de stroom, opent u een nieuwe verbinding en begint u met een andere HTTP POST-aanvraag, verzendt u de stroom headers opnieuw, verzendt u de laatste twee fragmenten opnieuw voor elk nummer en hervat u de stroom zonder een onderbreking in de media tijdlijn te introduceren. Dit vermindert de kans op gegevens verlies.
1. Het is raadzaam om het coderings programma niet het aantal nieuwe pogingen te beperken om een verbinding tot stand te brengen of streaming te hervatten nadat een TCP-fout is opgetreden.
1. Na een TCP-fout:
  
    a. De huidige verbinding moet worden gesloten en er moet een nieuwe verbinding worden gemaakt voor een nieuwe HTTP POST-aanvraag.

    b. De nieuwe URL voor HTTP POST moet hetzelfde zijn als de URL voor de eerste POST.
  
    c. Het nieuwe HTTP-bericht moet stroom headers (**ftyp**-, **Live server manifest Box**-en **Moov** -vakken) bevatten dat identiek is aan de stroom headers in de eerste post.
  
    d. De laatste twee geverzendde fragmenten voor elk nummer moeten opnieuw worden verzonden en streaming moet worden hervat zonder een onderbreking in de media tijdlijn te introduceren. De time Stamps van de MP4-fragmenten moeten continu toenemen, zelfs via HTTP POST-aanvragen.
1. Het coderings programma moet de HTTP POST-aanvraag beëindigen als er geen gegevens worden verzonden tegen een snelheid die afloopt met de duur van de MP4-fragment.  Een HTTP POST-aanvraag waarmee geen gegevens worden verzonden, kan verhinderen dat Media Services snel verbinding maakt met het coderings programma in het geval van een service-update. Daarom moet de HTTP POST voor sparse-tracks (AD signalering) kort worden gestaakt, zodra het verspreide fragment wordt verzonden.

## <a name="8-encoder-failover"></a>8. encoder-failover
Failover van coderings programma is het tweede type failover-scenario dat moet worden geadresseerd voor een end-to-end Live streaming-levering. In dit scenario treedt de fout situatie op de encoder-kant op. 

![encoder-failover][image5]

De volgende verwachtingen zijn van toepassing op het live opname-eind punt wanneer er een failover voor het coderings programma plaatsvindt:

1. Er moet een nieuw encoder-exemplaar worden gemaakt om de streaming voort te zetten, zoals wordt geïllustreerd in het diagram (stream for 3000k video, met streepjes lijn).
1. Het nieuwe coderings programma moet dezelfde URL voor HTTP POST-aanvragen gebruiken als het mislukte exemplaar.
1. De POST-aanvraag van het nieuwe coderings programma moet dezelfde gefragmenteerde MP4-koptekst vakken bevatten als het mislukte exemplaar.
1. De nieuwe encoder moet correct worden gesynchroniseerd met alle andere coderings Programma's die voor dezelfde live presentatie worden uitgevoerd om gesynchroniseerde audio/video-voor beelden met uitgelijnde fragment grenzen te genereren.
1. De nieuwe stroom moet semantisch gelijkwaardig zijn aan de vorige stroom en kan worden gewijzigd op basis van de header-en fragment niveaus.
1. De nieuwe encoder moet proberen gegevens verlies te minimaliseren. De `fragment_absolute_time` en `fragment_index` van de media fragmenten moeten toenemen van het punt waar de encoder voor het laatst is gestopt. De `fragment_absolute_time` en `fragment_index` moet doorlopend worden uitgebreid, maar het is niet toegestaan om, indien nodig, een oncontinuïteit in te voeren. Media Services negeert fragmenten die het al hebben ontvangen en verwerkt. het is dus beter om de fout aan de zijde van het opnieuw verzenden van fragmenten te verduidelijken dan om in de media tijdlijn de tijd te laten verzorgen. 

## <a name="9-encoder-redundancy"></a>9. encoder-redundantie
Voor bepaalde kritieke Live gebeurtenissen die nog hogere Beschik baarheid en kwaliteit vereisen, is het raadzaam om actief-actief redundante coderings Programma's te gebruiken voor een naadloze failover zonder verlies van gegevens.

![encoder redundantie][image6]

Zoals in dit diagram wordt geïllustreerd, pushen twee groepen code ring twee kopieën van elke stream tegelijk naar de Live-service. Dit installatie programma wordt ondersteund omdat Media Services dubbele fragmenten op basis van de stream-ID en de tijds tempel van het fragment kan filteren. De resulterende Live Stream en Archive is een enkele kopie van alle stromen die de best mogelijke aggregatie van de twee bronnen zijn. Een voor beeld: in een hypothetisch extreem geval geldt dat als er één coderings programma is (dit hoeft niet hetzelfde te zijn) wordt uitgevoerd op een bepaald moment voor elke stroom, de resulterende live stream van de service doorlopend zonder gegevens verlies. 

De vereisten voor dit scenario zijn bijna hetzelfde als de vereisten in het geval van ' encoder failover ', met uitzonde ring dat de tweede set encoders op hetzelfde moment wordt uitgevoerd als de primaire coderings Programma's.

## <a name="10-service-redundancy"></a>10. service redundantie
Voor een zeer redundante wereld wijde distributie moet u soms over een back-up over meerdere regio's beschikken om regionale rampen te kunnen afhandelen. Door uitbrei ding van de topologie ' encoder redundantie ' kunnen klanten ervoor kiezen een redundante service-implementatie te hebben in een andere regio die is verbonden met de tweede set coderings Programma's. Klanten kunnen ook samen werken met een Content Delivery Network Provider voor het implementeren van een globale Traffic Manager vóór de twee service-implementaties om naadloos client verkeer te routeren. De vereisten voor de encoders zijn gelijk aan die van de ' encoder redundantion '. De enige uitzonde ring hierop is dat de tweede set coderings Programma's moet verwijzen naar een ander live opname-eind punt. In het volgende diagram ziet u deze instellingen:

![Service redundantie][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. speciale typen opname-indelingen
In deze sectie worden speciale typen Live opname-indelingen beschreven die zijn ontworpen voor het verwerken van specifieke scenario's.

### <a name="sparse-track"></a>Sparse track
Bij het leveren van een live streamen-presentatie met een krachtige client ervaring is het vaak nodig om tijd gesynchroniseerde gebeurtenissen te verzenden of in band te reageren met de hoofd media gegevens. Een voor beeld hiervan is dynamische Live AD-invoeging. Dit type gebeurtenis signalering wijkt af van gewone audio-en video-streaming vanwege de aard van de verspreiding. Met andere woorden, de signaal gegevens worden doorgaans niet voortdurend herhaald en het interval kan moeilijk worden voor speld. Het concept van sparse track is ontworpen om in-band-signalerings gegevens op te nemen en uit te zenden.

De volgende stappen zijn een aanbevolen implementatie voor opname van sparse track:

1. Maak een afzonderlijke gefragmenteerde MP4-bitstream die alleen sparse tracks bevat zonder audio-en video-tracks.
1. In het **manifest van de live server** zoals gedefinieerd in sectie 6 in [1], gebruikt u de para meter *parentTrackName* om de naam van het bovenliggende spoor op te geven. Zie sectie 4.2.1.2.1.2 in [1] voor meer informatie.
1. In het **vak Live server-manifest** moet **manifestOutput** zijn ingesteld op **True**.
1. Gezien de zeldzame aard van de signaal gebeurtenis, wordt het volgende aangeraden:
   
    a. Aan het begin van de live-gebeurtenis verzendt het coderings programma de oorspronkelijke koptekst vakken naar de service, waardoor de service de sparse track in het client manifest kan registreren.
   
    b. Het coderings programma moet de HTTP POST-aanvraag beëindigen wanneer er geen gegevens worden verzonden. Een langlopende HTTP-POST waarmee geen gegevens worden verzonden, kan verhinderen dat Media Services snel verbinding maakt met het coderings programma in het geval van een service-update of het opnieuw opstarten van de server. In dergelijke gevallen wordt de media server tijdelijk geblokkeerd in een receive-bewerking op de socket.
   
    c. Gedurende de tijd dat er geen signaal gegevens beschikbaar zijn, moet het coderings programma de HTTP POST-aanvraag sluiten. Terwijl de POST-aanvraag actief is, moet het coderings programma gegevens verzenden.

    d. Bij het verzenden van sparse fragmenten kan het coderings programma een expliciete content-length-header instellen, als deze beschikbaar is.

    e. Wanneer sparse fragmenten met een nieuwe verbinding worden verzonden, moet het coderings programma beginnen met het verzenden van de koptekst vakken, gevolgd door de nieuwe fragmenten. Dit geldt voor situaties waarin failover plaatsvindt tussen en de nieuwe verspreide verbinding wordt tot stand gebracht met een nieuwe server die de sparse track niet eerder heeft gezien.

    f. Het verspreide spoor fragment wordt beschikbaar voor de client wanneer het bijbehorende bovenliggende spoor fragment met een gelijke of grotere time stamp-waarde beschikbaar wordt gesteld aan de client. Als het sparse fragment bijvoorbeeld een tijds tempel heeft van t = 1000, wordt ervan uitgegaan dat nadat de client ' video ' heeft gezien (ervan uitgaande dat de naam van het bovenliggende spoor ' video ' is) fragment tempel 1000 of hoger, de sparse fragment t = 1000 kan worden gedownload. Houd er rekening mee dat het werkelijke signaal kan worden gebruikt voor een andere positie in de presentatie tijdlijn voor het desbetreffende doel. In dit voor beeld is het mogelijk dat het sparse fragment van t = 1000 een XML-nettolading heeft, wat betekent dat voor het invoegen van een AD een positie van een paar seconden later is.

    g. De payload van sparse track fragmenten kunnen zich in verschillende indelingen bevinden (zoals XML, tekst of binair), afhankelijk van het scenario.

### <a name="redundant-audio-track"></a>Redundante audio track
In een typisch HTTP-adaptief streaming-scenario (bijvoorbeeld Smooth Streaming of streepje) is er vaak maar één audio track in de hele presentatie. In tegens telling tot video tracks, waarvoor meerdere kwaliteits niveaus gelden voor de client, kan het audio spoor een Single Point of Failure als de opname van de stroom die het audio spoor bevat, is verbroken. 

Om dit probleem op te lossen, ondersteunt Media Services Live opname van redundante audio sporen. Het idee is dat hetzelfde audio spoor meerdere keren in verschillende stromen kan worden verzonden. Hoewel de service het audio spoor slechts eenmaal in het client manifest registreert, kan het gebruik maken van redundante audio nummers als back-ups voor het ophalen van audio fragmenten als het primaire audio spoor problemen heeft. Voor het opnemen van redundante audio sporen moet de encoder het volgende doen:

1. Maak hetzelfde audio spoor in meerdere fragment-MP4-bitstreams. De redundante audio tracks moeten semantisch gelijkwaardig zijn, met dezelfde fragment tempels en kunnen worden gewijzigd op het niveau van de header en het fragment.
1. Zorg ervoor dat de vermelding ' Audio ' in het manifest van Live server (sectie 6 in [1]) hetzelfde is voor alle redundante audio sporen.

De volgende implementatie wordt aanbevolen voor redundante audio sporen:

1. Elk uniek audio spoor verzenden in een stroom. U kunt ook een redundante stroom verzenden voor elk van deze audio track stromen, waarbij de tweede stroom verschilt van de eerste alleen door de id in de HTTP POST-URL: {Protocol}://{Server-adres}/{Publishing punt pad}/streams ({Identifier}).
1. Gebruik afzonderlijke streams om de twee laagste video-bitsnelheden te verzenden. Elk van deze stromen moet ook een kopie van elk uniek audio spoor bevatten. Als er bijvoorbeeld meerdere talen worden ondersteund, moeten deze streams audio sporen voor elke taal bevatten.
1. Gebruik afzonderlijke server exemplaren (encoder) voor het coderen en verzenden van de redundante stromen vermeld in (1) en (2). 

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png