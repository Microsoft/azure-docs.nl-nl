---
title: Live streamen met Azure Media Services om multi-bitrate streams te maken | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u een kanaal instelt dat een single-bitrate Live Stream ontvangt van een on-premises encoder en vervolgens Live encoding uitvoert voor een adaptieve bitrate stroom met Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: b9b4cd54375a13da95259e27da680255f785df45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013207"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Live streamen met Azure Media Services om multi-bitrate streams te maken

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Vanaf 12 mei 2018 krijgen Live kanalen niet langer ondersteuning voor het RTP/MPEG-2 Trans Port stream opname protocol. Migreer van RTP/MPEG-2 naar RTMP-of gefragmenteerde MP4 (Smooth Streaming)-opname protocollen.

## <a name="overview"></a>Overzicht
In Azure Media Services (AMS) vertegenwoordigt een **kanaal** een pijp lijn voor het verwerken van live streaming-inhoud. Een **kanaal** ontvangt Live-invoer stromen op een van de volgende twee manieren:

* Een on-premises Live Encoder verzendt een stream met één bitsnelheid naar het kanaal dat is ingeschakeld voor het uitvoeren van Live code ring met Media Services in een van de volgende indelingen: RTMP of Smooth Streaming (gefragmenteerde MP4). Het kanaal codeert de inkomende single-bitrate stream vervolgens live naar een (adaptieve) multi-bitrate videostream. Desgevraagd levert Media Services de stream aan klanten.
* Een on-premises Live Encoder verzendt een multi-bitrate **RTMP** -of **Smooth streaming** (gefragmenteerde MP4) naar het kanaal dat niet is ingeschakeld voor het uitvoeren van Live code ring met AMS. De opgenomen streams passeren door **kanaal** s zonder verdere verwerking. Deze methode wordt **Pass-Through** genoemd. U kunt de volgende Live coderings Programma's gebruiken die multi-bitrate Smooth Streaming uitvoeren: MediaExcel, Ateme, denk aan communicatie, Envivio, Cisco en elementair. De volgende Live coderings Programma's uitvoeren RTMP: [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), Haivision, Teradek encoder.  Een live coderingsprogramma kan ook een stream met één bitsnelheid verzenden naar een kanaal dat niet is ingeschakeld voor Live Encoding, maar dit wordt niet aanbevolen. Desgevraagd levert Media Services de stream aan klanten.

  > [!NOTE]
  > Het gebruik van een Pass-Through-methode is de voordeligste manier om live streamen uit te voeren.
  > 
  > 

Als u begint met de release van Media Services 2,10, kunt u, wanneer u een kanaal maakt, opgeven op welke manier u wilt dat uw kanaal de invoer stroom ontvangt en of u wilt dat het kanaal Live code ring van uw stream uitvoert. U hebt hiervoor twee opties:

* **Geen** – geef deze waarde op als u van plan bent een on-premises Live Encoder te gebruiken waarmee een stream voor meerdere bitsnelheden (een Pass-Through-stroom) wordt uitgevoerd. In dit geval wordt de inkomende stroom door gegeven aan de uitvoer zonder code ring. Dit is het gedrag van een kanaal vóór 2,10 release.  Zie [live streamen met on-premises encoders die multi-bitrate streams maken](media-services-live-streaming-with-onprem-encoders.md)voor meer gedetailleerde informatie over het werken met kanalen van dit type.
* **Standaard** : Kies deze waarde als u Media Services wilt gebruiken om uw live stream met één bitsnelheid te coderen naar een multi-bitrate stream. Houd er rekening mee dat er sprake is van een facturerings effect voor Live code ring en u moet er rekening mee houden dat het gebruik van een live coderings kanaal in de status ' running ' in rekening wordt gebracht.  Het is raadzaam om uw actieve kanalen direct te stoppen nadat uw live streaming-gebeurtenis is voltooid om extra kosten per uur te voor komen.

> [!NOTE]
> In dit onderwerp worden kenmerken van kanalen beschreven die zijn ingeschakeld voor het uitvoeren van Live encoding (**standaard** coderings type). Zie [live streamen met on-premises encoders die multi-bitrate streams maken](media-services-live-streaming-with-onprem-encoders.md)voor meer informatie over het werken met kanalen die niet zijn ingeschakeld voor het uitvoeren van Live code ring.
> 
> Zorg ervoor dat u de sectie [overwegingen](media-services-manage-live-encoder-enabled-channels.md#Considerations) bekijkt.
> 
> 

## <a name="billing-implications"></a>Facturerings implicaties
Een live coderings kanaal begint met factureren zodra de status overgangen via de API wordt uitgevoerd.   U kunt ook de status weer geven in de Azure Portal of in het hulp programma Azure Media Services Verkenner ( https://aka.ms/amse) .

In de volgende tabel ziet u hoe kanaal statussen worden toegewezen aan facturerings statussen in de API en Azure Portal. De statussen zijn iets anders tussen de API en de portal UX. Zodra een kanaal zich in de status ' running ' bevindt via de API of in de status ' gereed ' of ' streaming ' in de Azure Portal, wordt de facturering actief.
Als u het kanaal niet meer wilt factureren, moet u het kanaal stoppen via de API of in de Azure Portal.
U bent zelf verantwoordelijk voor het stoppen van uw kanalen wanneer u klaar bent met het Live encoding-kanaal.  Als u een coderings kanaal niet stopt, wordt de facturering voortgezet.

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Kanaal statussen en hoe deze worden toegewezen aan de facturerings modus
De huidige status van een kanaal. Mogelijke waarden zijn:

* **Gestopt**. Dit is de begin status van het kanaal nadat het is gemaakt (tenzij automatisch starten is geselecteerd in de portal.) Er vindt geen facturering plaats in deze status. In deze status kunnen de kanaal eigenschappen worden bijgewerkt, maar streaming is niet toegestaan.
* Wordt **gestart**. Het kanaal wordt gestart. Er vindt geen facturering plaats in deze status. Er zijn geen updates of streaming toegestaan tijdens deze status. Als er een fout optreedt, keert het kanaal terug naar de status gestopt.
* **Wordt uitgevoerd**. Het kanaal kan live streams verwerken. Het is nu een facturerings verbruik. U moet het kanaal stoppen om verdere facturering te voor komen. 
* Wordt **gestopt**. Het kanaal wordt gestopt. Er vindt geen facturering plaats in deze tijdelijke status. Er zijn geen updates of streaming toegestaan tijdens deze status.
* **Verwijderen**. Het kanaal wordt verwijderd. Er vindt geen facturering plaats in deze tijdelijke status. Er zijn geen updates of streaming toegestaan tijdens deze status.

In de volgende tabel ziet u hoe kanaal statussen worden toegewezen aan de facturerings modus. 

| Kanaal status | UI-indica toren van portal | Is het facturerings abonnement? |
| --- | --- | --- |
| Starten |Starten |Nee (tijdelijke status) |
| Wordt uitgevoerd |Gereed (geen actieve Program ma's)<br/>of<br/>Streaming (ten minste één programma dat wordt uitgevoerd) |JA |
| Stoppen |Stoppen |Nee (tijdelijke status) |
| Gestopt |Gestopt |Nee |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatisch afsluiten voor ongebruikte kanalen
Vanaf 25 januari 2016 Media Services de implementatie van een update die automatisch een kanaal stopt (met Live encoding ingeschakeld) nadat het is uitgevoerd in een ongebruikte status gedurende een lange periode. Dit geldt voor kanalen zonder actieve Program Ma's en die gedurende een lange periode geen invoer bijdrage hebben ontvangen.

De drempel waarde voor een ongebruikte periode is een nominale tijd van 12 uur, maar kan worden gewijzigd.

## <a name="live-encoding-workflow"></a>Live Encoding werk stroom
Het volgende diagram vertegenwoordigt een werk stroom voor live streamen waarbij een kanaal een enkele bitrate stroom ontvangt in een van de volgende protocollen: RTMP of Smooth Streaming; vervolgens wordt de stroom gecodeerd naar een multi-bitrate stream. 

![Live-werkstroom][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Algemeen scenario voor live streamen
Hieronder volgen de algemene stappen voor het maken van veelvoorkomende toepassingen voor live streamen.

> [!NOTE]
> De maximum aanbevolen duur van een live gebeurtenis is momenteel acht uur.
>
> Er is een facturerings effect voor Live code ring en u moet er rekening mee houden dat het gebruik van een live coderings kanaal in de status ' running ' per uur in rekening wordt gebracht. Het is raadzaam om uw actieve kanalen direct te stoppen nadat uw live streaming-gebeurtenis is voltooid om extra kosten per uur te voor komen. 

1. Sluit een videocamera aan op een computer. Start en configureer een on-premises Live coderings programma dat een **enkele** bitrate stroom kan uitvoeren in een van de volgende protocollen: RTMP of Smooth streaming. 

    Deze stap kan ook worden uitgevoerd nadat u uw kanaal hebt gemaakt.
2. Maak en start een kanaal. 
3. Haal de URL voor opnemen voor het kanaal op. 

    De URL voor opnemen wordt gebruikt door het live coderingsprogramma om de stream naar het kanaal te verzenden.
4. Haal de voorbeeld-URL voor het kanaal op. 

    Gebruik deze URL om te controleren of de livestream goed door het kanaal wordt ontvangen.
5. Een programma maken. 

    Wanneer u de Azure Portal gebruikt, wordt er ook een Asset gemaakt door een programma te maken. 

    Bij het gebruik van .NET SDK of de REST moet u een Asset maken en opgeven dat deze asset moet worden gebruikt bij het maken van een programma. 
6. Publiceer de Asset die aan het programma is gekoppeld.   

    >[!NOTE]
    >Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Het streaming-eindpunt van waar u inhoud wilt streamen, moet de status **Wordt uitgevoerd** hebben. 

7. Start het programma wanneer u klaar bent om te streamen en te archiveren.
8. Het live coderingsprogramma kan desgewenst een signaal ontvangen dat een advertentie moet worden gestart. De advertentie wordt ingevoegd in de uitvoerstream.
9. Stop het programma als u het streamen wilt stoppen en de gebeurtenis wilt archiveren.
10. Verwijder het programma (en verwijder desgewenst de asset).   

> [!NOTE]
> Het is belang rijk dat u niet vergeet om een Live Encoding kanaal te stoppen. Houd er rekening mee dat er een facturerings effect is voor Live encoding en dat u er rekening mee moet houden dat een live coderings kanaal in de status ' running ' wordt gebruikt om facturerings kosten in rekening te brengen.  Het is raadzaam om uw actieve kanalen direct te stoppen nadat uw live streaming-gebeurtenis is voltooid om extra kosten per uur te voor komen. 
> 
> 

## <a name="channels-input-ingest-configurations"></a><a id="channel"></a>Configuratie van de invoer van het kanaal (opname)
### <a name="ingest-streaming-protocol"></a><a id="Ingest_Protocols"></a>Opname stroomsgewijze Protocol
Als het **coderings type** is ingesteld op **Standard**, geldige opties zijn:

* **RTMP** met één bitsnelheid
* **Gefragmenteerde MP4** met enkele bitsnelheid (Smooth streaming)

#### <a name="single-bitrate-rtmp"></a><a id="single_bitrate_RTMP"></a>Single-bitrate RTMP;
Overwegingen:

* De binnenkomende stroom kan geen multi-bitrate video bevatten
* De video stroom moet een gemiddelde bitsnelheid hebben van minder dan 15 Mbps
* De audio stroom moet een gemiddelde bitsnelheid hebben van minder dan 1 Mbps
* Hier volgen de ondersteunde codecs:
* MPEG-4 AVC/H. 264 video
* Basis lijn, hoofd, hoog profiel (8-bits 4:2:0)
* High-10-Profiel (10-bits 4:2:0)
* High 422-Profiel (10-bits 4:2:2)
* MPEG-2 AAC-LC-audio
* Mono, stereo, surround (5,1, 7,1)
* sampling frequentie van 44,1 kHz
* ADTS-verpakking met MPEG-2-stijl
* De aanbevolen encoders zijn onder andere:
* [Telestream-Wirecast](media-services-configure-wirecast-live-encoder.md)
* Flash Media Live Encoder

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Single-bitrate Fragmented MP4 (Smooth Streaming);
Typische use-case:

Gebruik on-premises Live coderings Programma's van leveranciers als ' elementair Technologies ', Ericsson, Ateme, Envivio om de invoer stroom via het open Internet te verzenden naar een nabijgelegen Azure-Data Center.

Overwegingen:

Hetzelfde als voor de [enkele bitrate RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Andere overwegingen
* U kunt het invoerprotocol niet wijzigen terwijl het kanaal of de gekoppelde programma's worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u afzonderlijke kanalen voor elk invoerprotocol.
* De maximale resolutie voor de inkomende video stroom is 1920 en Maxi maal 60 velden per seconde indien geïnterlinieerd of 30 frames per seconde als progressieve.

### <a name="ingest-urls-endpoints"></a>Opname-Url's (eind punten)
Een kanaal biedt een invoer eindpunt (opname-URL) dat u opgeeft in het Live coderings programma, zodat het coderings programma streams naar uw kanalen kan pushen.

U kunt de opname-Url's ophalen zodra u een kanaal hebt gemaakt. Om deze Url's op te halen, hoeft het kanaal niet te worden **uitgevoerd** . Wanneer u klaar bent om gegevens naar het kanaal te pushen, moet deze de status **wordt uitgevoerd** hebben. Zodra het kanaal begint met het opnemen van gegevens, kunt u een voor beeld van de stroom bekijken via de voor beeld-URL.

U hebt de mogelijkheid om gefragmenteerde MP4 (Smooth Streaming) live stream via een TLS-verbinding op te nemen. Zorg ervoor dat u de opname-URL naar HTTPS bijwerkt voor opname via TLS. AMS biedt momenteel geen ondersteuning voor TLS met aangepaste domeinen.  

### <a name="allowed-ip-addresses"></a>Toegestane IP-adressen
U kunt de IP-adressen definiëren die video naar dit kanaal mogen publiceren. Toegestane IP-adressen kunnen worden opgegeven als een enkel IP-adres (bijvoorbeeld 10.0.0.1), een IP-bereik met een IP-adres en een CIDR-subnetmasker (bijvoorbeeld 10.0.0.1/22) of een IP-bereik met een IP-adres en een decimaal subnetmasker met punten (bijvoorbeeld ' 10.0.0.1 (255.255.252.0) ').

Als geen IP-adressen zijn opgegeven en er geen regeldefinitie bestaat, zijn er geen IP-adressen toegestaan. Als u IP-adres(sen) wilt toestaan, maakt u een regel en stelt u 0.0.0.0/0 in.

## <a name="channel-preview"></a>Channel Preview
### <a name="preview-urls"></a>Preview-URL's
Kanalen bieden een preview-eind punt (Preview-URL) die u gebruikt om uw stroom te bekijken en te valideren vóór verdere verwerking en levering.

U kunt de voor beeld-URL ophalen wanneer u het kanaal maakt. Om de URL op te halen, hoeft het kanaal niet te worden **uitgevoerd** .

Zodra het kanaal begint met het opnemen van gegevens, kunt u een voor beeld van de stroom bekijken.

> [!NOTE]
> De voorbeeld stroom kan momenteel alleen worden geleverd in gefragmenteerde MP4-indeling (Smooth Streaming), ongeacht het opgegeven invoer type.  U kunt een speler die wordt gehost in de Azure Portal gebruiken om uw stream te bekijken.
> 
> 

### <a name="allowed-ip-addresses"></a>Toegestane IP-adressen
U kunt de IP-adressen definiëren die verbinding mogen maken met het eind punt van de preview. Als er geen IP-adressen zijn opgegeven, is een IP-adres toegestaan. Toegestane IP-adressen kunnen worden opgegeven als een enkel IP-adres (bijvoorbeeld 10.0.0.1), een IP-bereik met een IP-adres en een CIDR-subnetmasker (bijvoorbeeld 10.0.0.1/22) of een IP-bereik met een IP-adres en een decimaal subnetmasker met punten (bijvoorbeeld ' 10.0.0.1 (255.255.252.0) ').

## <a name="live-encoding-settings"></a>Instellingen voor Live code ring
In deze sectie wordt beschreven hoe de instellingen voor de Live Encoder binnen het kanaal kunnen worden aangepast wanneer het **coderings type** van een kanaal is ingesteld op **standaard**.

> [!NOTE]
> Uw bijdrager mag alleen één audio spoor bevatten: opname van meerdere audio sporen wordt momenteel niet ondersteund. Bij het uitvoeren van Live code ring met [on-premises Live](media-services-live-streaming-with-onprem-encoders.md)-code ringen kunt u een invoer voor bijdragen verzenden in het Smooth streaming-protocol met meerdere audio tracks.
> 
> 

### <a name="ad-marker-source"></a>Bron van AD-markering
U kunt de bron voor advertentie markeringen opgeven. De standaard waarde is **API**, waarmee wordt aangegeven dat de Live coderings Programma's binnen het kanaal moeten worden geluisterd naar een asynchrone **ad-markerings-API**.

### <a name="cea-708-closed-captions"></a>Ondertiteling van CEA 708
Een optionele vlag die het Live coderings programma vertelt dat alle CEA 708 bijschriften die zijn Inge sloten in de binnenkomende video, worden genegeerd. Wanneer de vlag is ingesteld op False (standaard), detecteert het coderings programma CEA 708-gegevens in de video-uitvoer stromen.

#### <a name="index"></a>Index
U kunt het beste in een Single Program Transport Stream (share point Team Services) verzenden. Als de invoer stroom meerdere Program ma's bevat, de Live Encoder in het kanaal parseert de tabel programma toewijzing (PMT) in de invoer, identificeert de invoer met de naam van het stream type van MPEG-2 AAC ADTS of AC-3-systeem-A-of AC-3-systeem-B, MPEG-2-privé-PES of MPEG-1-audio of MPEG-2-audio De op nul gebaseerde index wordt vervolgens gebruikt om het n-e-item in die rang schikking op te halen.

#### <a name="language"></a>Taal
De taal-id van de audio stroom, die voldoet aan ISO 639-2, zoals ENG. Als deze niet aanwezig is, is de standaard waarde UND (niet gedefinieerd).

### <a name="system-preset"></a><a id="preset"></a>Systeem vooraf ingesteld
Hiermee geeft u de voor instelling op die door de Live Encoder binnen dit kanaal moet worden gebruikt. Op dit moment is de enige toegestane waarde **Default720p** (standaard).

Met **Default720p** wordt de video in de volgende 6 lagen gecodeerd.

#### <a name="output-video-stream"></a>Video stroom voor uitvoer

| Bitsnelheid | Breedte | Hoogte | MaxFPS | Profiel | Naam van de uitvoer stroom |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Hoog |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Hoog |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Hoog |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Hoog |Video_512x288_850kbps |
| 550 |384 |216 |30 |Hoog |Video_384x216_550kbps |
| 200 |340 |192 |30 |Hoog |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Audio stroom voor uitvoer

Audio wordt gecodeerd met stereo AAC-LC om 128 kbps, sampling frequentie van 48 kHz.

## <a name="signaling-advertisements"></a>Advertisements voor Signa lering
Wanneer uw kanaal Live Encoding ingeschakeld, hebt u een onderdeel in uw pijp lijn dat video verwerkt en kan het worden bewerkt. U kunt een signaal voor het kanaal geven om pastels en/of advertenties in de uitgaande Adaptive bitrate-stroom in te voegen. Pastels zijn stilstaande installatie kopieën die u kunt gebruiken om de invoer van live feeds in bepaalde gevallen te bedekken (bijvoorbeeld tijdens een commerciële afbreek periode). Reclame signalen, zijn tijdgesynchroniseerde signalen die u in de uitgaande stroom insluit om de video speler een speciale actie te geven, bijvoorbeeld om naar een advertentie op het juiste tijdstip te scha kelen. Zie deze [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) voor een overzicht van het SCTE-35-signalerings mechanisme dat hiervoor wordt gebruikt. Hieronder ziet u een typisch scenario dat u kunt implementeren in uw live-gebeurtenis.

1. Laat uw viewers een installatie kopie van vóór de gebeurtenis ontvangen voordat de gebeurtenis wordt gestart.
2. Laat uw kijkers een installatie kopie na de gebeurtenis ontvangen nadat de gebeurtenis is beëindigd.
3. Laat uw viewers een fout afbeelding ontvangen als er een probleem is tijdens de gebeurtenis (bijvoorbeeld stroom storingen in de stadion).
4. Een AD-afbeelding verzenden om de Live Event feed tijdens een commerciële afbreek periode te verbergen.

Hieronder vindt u de eigenschappen die u kunt instellen bij het Signa leren van advertenties. 

### <a name="duration"></a>Duur
De duur, in seconden, van de commerciële grens. Dit moet een positieve waarde zijn die niet gelijk is aan nul om de commerciële grens te starten. Wanneer er een commerciële ondertekening wordt uitgevoerd en de duur is ingesteld op nul met de CueId die overeenkomt met de on-continue commerciële inbraak, wordt die afbreek bewerking geannuleerd.

### <a name="cueid"></a>CueId
Een unieke ID voor de commerciële grens die door downstream-toepassingen moet worden gebruikt om de juiste actie (s) uit te voeren. Moet een positief geheel getal zijn. U kunt deze waarde instellen op elk wille keurig positief geheel getal of een upstream-systeem gebruiken om de hint-Id's bij te houden. Zorg ervoor dat de Id's van positieve gehele getallen worden genormaliseerd voordat ze via de API worden verzonden.

### <a name="show-slate"></a>Pastel weer geven
Optioneel. Hiermee wordt de Live Encoder gesignaleerd om over te scha kelen naar de standaard afbeelding van de [kleur](media-services-manage-live-encoder-enabled-channels.md#default_slate) tijdens een commerciële uitsplitsing en de feed voor inkomende video te verbergen. Audio wordt ook gedempt tijdens pastel. De standaard waarde is **False**. 

De afbeelding die wordt gebruikt, is de waarde die is opgegeven via de eigenschap activa-id van de standaard kleur op het moment dat het kanaal wordt gemaakt. De pastel wordt uitgerekt zodat deze overeenkomt met de grootte van de weergave afbeelding. 

## <a name="insert-slate--images"></a>Afbeelding in pastel invoegen
Het Live coderings programma binnen het kanaal kan worden gesignaleerd om over te scha kelen naar een pastel afbeelding. Het kan ook worden gesignaleerd om een voortdurende pastel te beëindigen. 

Het Live coderings programma kan worden geconfigureerd om over te scha kelen naar een pastel afbeelding en het binnenkomende video signaal te verbergen in bepaalde situaties, bijvoorbeeld tijdens een AD-break. Als een dergelijke pastel niet is geconfigureerd, wordt de invoer video niet gemaskeerd tijdens die AD-afbreek periode.

### <a name="duration"></a>Duur
De duur van de pastel in seconden. Dit moet een positieve waarde zijn die niet gelijk is aan nul om de pastel te starten. Als er sprake is van een voortdurende pastel en er een duur van nul is opgegeven, wordt die voortdurende slag beëindigd.

### <a name="insert-slate-on-ad-marker"></a>Pastel invoegen in advertentie markering
Als deze optie is ingesteld op True, wordt met deze instelling het Live coderings programma geconfigureerd voor het invoegen van een pastel afbeelding tijdens een AD-out. De standaardwaarde is waar. 

### <a name="default-slate-asset-id"></a><a id="default_slate"></a>Standaard activum-id

Optioneel. Hiermee geeft u de activum-id op van het Media Services activum dat de afbeelding van de pastel bevat. De standaard waarde is null. 


> [!NOTE] 
> Voordat u het kanaal maakt, moet de afbeelding pastel met de volgende beperkingen worden geüpload als een toegewezen activum (er moeten geen andere bestanden in deze asset zijn). Deze installatie kopie wordt alleen gebruikt wanneer het Live coderings programma een pastel invoegt als gevolg van een AD-break of expliciet is gesignaleerd om een pastel in te voegen. Er is momenteel geen optie voor het gebruik van een aangepaste installatie kopie wanneer het Live coderings programma de status ' invoer signaal verloren ' invoert. U kunt [hier](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel)stemmen voor deze functie.

* Maxi maal 1920 in oplossing.
* Maxi maal 3 MB groot.
* De bestands naam moet een extensie *. jpg hebben.
* De afbeelding moet worden geüpload naar een activum als de enige AssetFile in dat activum en deze AssetFile moet worden gemarkeerd als het primaire bestand. De Asset kan niet worden versleuteld met de opslag.

Als de **standaard activum-id** niet is opgegeven en **pastel invoegen op de AD-markering** is ingesteld op **True**, wordt er een standaard Azure Media Services afbeelding gebruikt om de invoer video stroom te verbergen. Audio wordt ook gedempt tijdens pastel. 

## <a name="channels-programs"></a>Program ma's van het kanaal
Een kanaal is gekoppeld aan programma's waarmee u het publiceren en opslaan van segmenten in een live stream kunt beheren. Kanalen beheren programma's. De kanaal-en programma relatie lijkt veel op traditionele media, waarbij een kanaal een constante stroom inhoud heeft en een programma wordt afgestemd op een bepaalde time-outgebeurtenis op dat kanaal.

U kunt het aantal uren opgeven dat u de opgenomen inhoud voor het programma wilt behouden door de lengte voor **Archiefvenster** in te stellen. Deze waarde kan worden ingesteld van minimaal 5 minuten tot maximaal 25 uur. De lengte van het archief venster bepaalt ook het maximum aantal keren dat clients vanaf de huidige Live positie terug op tijd kunnen zoeken. Programma's kunnen in de opgegeven tijdsduur worden uitgevoerd, maar de inhoud die achter de lengte van het venster valt, wordt altijd verwijderd. De waarde van deze eigenschap bepaalt ook hoe lang de clientmanifesten kunnen groeien.

Elk programma is gekoppeld aan een Asset dat de gestreamde inhoud opslaat. Een Asset wordt toegewezen aan een blok-BLOB-container in het Azure Storage-account en de bestanden in de Asset worden opgeslagen als blobs in die container. Als u het programma wilt publiceren zodat uw klanten de stroom kunnen bekijken, moet u een OnDemand-Locator voor de gekoppelde Asset maken. Met deze locator kunt u een streaming-URL maken die u aan uw clients kunt leveren.

Een kanaal ondersteunt Maxi maal drie gelijktijdig actieve Program ma's, zodat u meerdere archieven van dezelfde binnenkomende stream kunt maken. Hierdoor kunt u verschillende onderdelen van een gebeurtenis naar behoefte publiceren en archiveren. Zo kan het voor uw bedrijf nodig zijn zes uur van een programma te archiveren, maar alleen de laatste tien minuten uit te zenden. Hiervoor moet u twee gelijktijdig actieve programma's maken. Het ene programma wordt ingesteld om zes uur van de gebeurtenis te archiveren, maar het programma wordt niet gepubliceerd. Het andere programma wordt ingesteld om tien minuten te archiveren en dit programma wordt wel gepubliceerd.

Gebruik bestaande programma's niet voor nieuwe gebeurtenissen. In plaats daarvan maakt en start u een nieuw programma voor elke gebeurtenis, zoals wordt beschreven in de sectie toepassingen voor live streamen Program meren.

Start het programma wanneer u klaar bent om te streamen en te archiveren. Stop het programma als u het streamen wilt stoppen en de gebeurtenis wilt archiveren. 

Als u de gearchiveerde inhoud wilt verwijderen, stopt en verwijdert u het programma en verwijdert u vervolgens de gekoppelde asset. Een asset kan niet worden verwijderd als deze wordt gebruikt door een programma. U moet eerst het programma verwijderen. 

Zelfs na het stoppen en verwijderen van het programma kunnen gebruikers de gearchiveerde inhoud als video op aanvraag streamen, mits u de asset niet hebt verwijderd.

Als u de gearchiveerde inhoud wilt behouden maar deze niet langer voor streaming beschikbaar mag zijn, verwijdert u de streaming-locator.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Een miniatuur preview van een live feed ophalen
Als Live Encoding is ingeschakeld, kunt u nu een preview-versie van de live-feed krijgen wanneer het kanaal wordt bereikt. Dit kan een waardevol hulp middel zijn om te controleren of uw live feed het kanaal daad werkelijk bereikt. 

## <a name="channel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>Kanaal statussen en hoe statussen worden toegewezen aan de facturerings modus
De huidige status van een kanaal. Mogelijke waarden zijn:

* **Gestopt**. Dit is de begin status van het kanaal nadat het is gemaakt. In deze status kunnen de kanaal eigenschappen worden bijgewerkt, maar streaming is niet toegestaan.
* Wordt **gestart**. Het kanaal wordt gestart. Er zijn geen updates of streaming toegestaan tijdens deze status. Als er een fout optreedt, keert het kanaal terug naar de status gestopt.
* **Wordt uitgevoerd**. Het kanaal kan live streams verwerken.
* Wordt **gestopt**. Het kanaal wordt gestopt. Er zijn geen updates of streaming toegestaan tijdens deze status.
* **Verwijderen**. Het kanaal wordt verwijderd. Er zijn geen updates of streaming toegestaan tijdens deze status.

In de volgende tabel ziet u hoe kanaal statussen worden toegewezen aan de facturerings modus. 

| Kanaal status | UI-indica toren van portal | Gefactureerd? |
| --- | --- | --- |
| Starten |Starten |Nee (tijdelijke status) |
| Wordt uitgevoerd |Gereed (geen actieve Program ma's)<br/>of<br/>Streaming (ten minste één programma dat wordt uitgevoerd) |Ja |
| Stoppen |Stoppen |Nee (tijdelijke status) |
| Gestopt |Gestopt |Nee |

> [!NOTE]
> Op dit moment is het begin gemiddelde van het kanaal ongeveer 2 minuten, maar het kan Maxi maal 20 minuten duren. Het opnieuw instellen van het kanaal kan tot vijf minuten duren.
> 
> 

## <a name="considerations"></a><a id="Considerations"></a>Overwegingen
* Wanneer een kanaal met een **standaard** coderings type een verlies van invoer bron/bijdrage levert, wordt dit door de bron video/audio vervangen door een fout melding en stilte. Het kanaal blijft een pastel verzenden totdat de invoer/contribut-feed wordt hervat. Het is raadzaam dat een live kanaal langer dan 2 uur niet in een dergelijke status achterblijft. Na dat punt wordt het gedrag van het kanaal op de invoer opnieuw verbinding niet gegarandeerd, ongeacht het gedrag van de opdracht reset. U moet het kanaal stoppen, verwijderen en een nieuw bestand maken.
* U kunt het invoerprotocol niet wijzigen terwijl het kanaal of de gekoppelde programma's worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u afzonderlijke kanalen voor elk invoerprotocol.
* Telkens wanneer u het Live coderings programma opnieuw configureert, roept u de methode **Reset** op het kanaal aan. Voordat u het kanaal opnieuw instelt, moet u het programma stoppen. Nadat u het kanaal opnieuw hebt ingesteld, start u het programma opnieuw.
* Een kanaal kan alleen worden gestopt wanneer het de status actief heeft en alle Program ma's op het kanaal zijn gestopt.
* Standaard kunt u Maxi maal 5 kanalen toevoegen aan uw Media Services-account. Dit is een zacht quotum voor alle nieuwe accounts. Zie [quota's en beperkingen](media-services-quotas-and-limitations.md)voor meer informatie.
* U kunt het invoerprotocol niet wijzigen terwijl het kanaal of de gekoppelde programma's worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u afzonderlijke kanalen voor elk invoerprotocol.
* U wordt alleen gefactureerd wanneer de status van uw kanaal **actief** is. Raadpleeg [deze](media-services-manage-live-encoder-enabled-channels.md#states) sectie voor meer informatie.
* De maximum aanbevolen duur van een live gebeurtenis is momenteel acht uur. 
* Zorg ervoor dat het streaming-eind punt van waaruit u inhoud wilt streamen, de status **wordt uitgevoerd** heeft.
* De vooraf ingestelde code ring maakt gebruik van het begrip ' maximale frame frequentie ' van 30 fps. Dus als de invoer 60fps/59.94 i is, worden de invoer frames verwijderd/ongedaan gemaakt op 30/29.97 fps. Als de invoer is 50fps/50i, worden de invoer frames verwijderd/de omlaced naar 25 fps. Als de invoer 25 fps is, blijft de uitvoer op 25 fps.
* Vergeet niet om uw kanalen te stoppen wanneer u klaar bent. Als u dat niet doet, wordt de facturering voortgezet.

## <a name="known-issues"></a>Bekende problemen
* De start tijd van het kanaal is verbeterd tot een gemiddelde van 2 minuten, maar het kan nog Maxi maal 20 minuten duren voordat de vraag is toegenomen.
* Pastel afbeeldingen moeten voldoen aan de beperkingen die [hier](media-services-manage-live-encoder-enabled-channels.md#default_slate)worden beschreven. Als u probeert een kanaal te maken met een standaard kleur die groter is dan 1920, wordt de aanvraag uiteindelijk afgemeld.
* Nog een keer.... Vergeet niet om uw kanalen te stoppen wanneer u klaar bent met streamen. Als u dat niet doet, wordt de facturering voortgezet.

## <a name="need-help"></a>Hulp nodig?

U kunt een ondersteunings ticket openen door te navigeren naar de [nieuwe ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Volgende stap

Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Verwante onderwerpen
[Live Streaming-gebeurtenissen met Azure Media Services leveren](media-services-overview.md)

[Kanalen maken die een live code ring van een afzonderlijke bitrate uitvoeren op een adaptieve bitrate stroom met de portal](media-services-portal-creating-live-encoder-enabled-channel.md)

[Kanalen maken voor het uitvoeren van Live code ring vanuit een afzonderlijke bitrate voor een adaptieve bitrate stroom met de .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Kanalen beheren met REST API](/rest/api/media/operations/channel)

[Media Services concepten](media-services-concepts.md)

[Azure Media Services gefragmenteerde MP4 Live opname-specificatie](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png
