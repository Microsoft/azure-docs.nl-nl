---
title: Live streamen met on-premises coderings Programma's die multi-bitrate streams maken-Azure | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u een kanaal instelt dat een multi-bitrate Live Stream ontvangt van een on-premises encoder.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 316372f091833519f0479d07355d2845c82743b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014839"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Werken met kanalen die een multi-bitrate live stream van on-premises encoders ontvangen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Vanaf 12 mei 2018 krijgen Live kanalen niet langer ondersteuning voor het RTP/MPEG-2 Trans Port stream opname protocol. Migreer van RTP/MPEG-2 naar RTMP-of gefragmenteerde MP4 (Smooth Streaming)-opname protocollen.

## <a name="overview"></a>Overzicht
In Azure Media Services vertegenwoordigt een *kanaal* een pijp lijn voor het verwerken van live-streaming-inhoud. Een kanaal ontvangt Live-invoer stromen op een van de volgende twee manieren:

* Een on-premises Live coderings programma verzendt een multi-bitrate RTMP-of Smooth Streaming (gefragmenteerde MP4)-stream naar het kanaal dat niet is ingeschakeld voor het uitvoeren van Live code ring met Media Services. De opgenomen streams passeren kanalen zonder verdere verwerking. Deze methode wordt *Pass-Through* genoemd. Een Live Encoder kan ook een stream met één bitsnelheid verzenden naar een kanaal dat niet is ingeschakeld voor Live encoding, maar dit wordt niet aangeraden. Media Services de stroom levert aan klanten die deze aanvragen.

  > [!NOTE]
  > Het gebruik van een Pass-Through-methode is de voordeligste manier om live streamen uit te voeren.


* Een on-premises Live Encoder verzendt een stream met één bitsnelheid naar het kanaal dat is ingeschakeld voor het uitvoeren van Live code ring met Media Services in een van de volgende indelingen: RTMP of Smooth Streaming (gefragmenteerde MP4). Het kanaal voert vervolgens Live encoding uit van de inkomende single-bitrate stream naar een multi-bitrate-video stroom (adaptief). Media Services de stroom levert aan klanten die deze aanvragen.

Als u begint met de release van Media Services 2,10, kunt u, wanneer u een kanaal maakt, opgeven hoe u wilt dat uw kanaal de invoer stroom ontvangt. U kunt ook opgeven of u wilt dat het kanaal Live code ring van uw stream uitvoert. U hebt hiervoor twee opties:

* **Pass** through: geef deze waarde op als u van plan bent een on-premises Live coderings programma te gebruiken dat een multi-bitrate stroom (een Pass-Through-stroom) als uitvoer heeft. In dit geval wordt de inkomende stroom door gegeven aan de uitvoer zonder code ring. Dit is het gedrag van een kanaal vóór de 2,10-release. In dit artikel vindt u informatie over het werken met kanalen van dit type.
* **Live encoding**: Kies deze waarde als u Media Services wilt gebruiken om uw live stream met één bitsnelheid te coderen naar een multi-bitrate stream. Als u een live coderings kanaal in een **actieve** status laat staan, worden facturerings kosten in rekening gebracht. We raden u aan uw actieve kanalen direct te stoppen nadat uw live-streaming-gebeurtenis is voltooid om extra kosten per uur te voor komen. Media Services de stroom levert aan klanten die deze aanvragen.

> [!NOTE]
> In dit artikel worden kenmerken van kanalen beschreven die niet zijn ingeschakeld voor het uitvoeren van Live code ring. Zie voor meer informatie over het werken met kanalen die geschikt zijn voor het uitvoeren van Live code ring, [live streamen met Azure Media Services om multi-bitrate streams te maken](media-services-manage-live-encoder-enabled-channels.md).
>
>Zie [Aanbevolen voor on-premises coderings](media-services-recommended-encoders.md)Programma's voor meer informatie over het aanbevolen on-premises coderings Programma's.

Het volgende diagram vertegenwoordigt een live-streaming werk stroom die gebruikmaakt van een on-premises Live coderings programma voor multi-bitrate RTMP-of gefragmenteerde MP4-stromen (Smooth Streaming) als uitvoer.

![Live-werkstroom][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Algemeen scenario voor live streamen
In de volgende stappen worden de taken beschreven die worden uitgevoerd bij het maken van veelvoorkomende toepassingen voor live streamen.

1. Sluit een videocamera aan op een computer. Start en configureer een on-premises Live coderings programma met een multi-bitrate RTMP-of gefragmenteerde MP4-stroom (Smooth Streaming) als uitvoer. Zie [Azure Media Services RTMP-ondersteuning en live coderingsprogramma's](https://go.microsoft.com/fwlink/?LinkId=532824) voor meer informatie.

    U kunt deze stap ook uitvoeren nadat u uw kanaal hebt gemaakt.
2. Een kanaal maken en starten.

3. Haal de kanaal opname-URL op.

    Het Live coderings programma gebruikt de opname-URL om de stream naar het kanaal te verzenden.
4. De voor beeld-URL van de kanaal ophalen.

    Gebruik deze URL om te controleren of de livestream goed door het kanaal wordt ontvangen.
5. Een programma maken.

    Wanneer u de Azure Portal gebruikt, wordt er ook een Asset gemaakt door een programma te maken.

    Wanneer u de .NET SDK of de REST gebruikt, moet u een Asset maken en opgeven dat deze asset moet worden gebruikt bij het maken van een programma.
6. Publiceer het activum dat is gekoppeld aan het programma.   

    >[!NOTE]
    >Wanneer uw Azure Media Services-account wordt gemaakt, wordt er een **standaard** streaming-eind punt aan uw account toegevoegd met de status **gestopt** . Het streaming-eindpunt van waar u inhoud wilt streamen, moet de status **Wordt uitgevoerd** hebben.

7. Start het programma wanneer u klaar bent om te streamen en te archiveren.

8. Het live coderingsprogramma kan desgewenst een signaal ontvangen dat een advertentie moet worden gestart. De advertentie wordt ingevoegd in de uitvoerstream.

9. Stop het programma als u het streamen wilt stoppen en de gebeurtenis wilt archiveren.

10. Verwijder het programma (en verwijder desgewenst de Asset).     

## <a name="description-of-a-channel-and-its-related-components"></a><a id="channel"></a>Beschrijving van een kanaal en de bijbehorende onderdelen
### <a name="channel-input-ingest-configurations"></a><a id="channel_input"></a>Kanaal-invoer (opname) configuraties
#### <a name="ingest-streaming-protocol"></a><a id="ingest_protocols"></a>Opname stroomsgewijze Protocol
Media Services ondersteunt het opnemen van live feeds door multi-bitrate gefragmenteerde MP4 en multi-bitrate RTMP als streaming protocollen te gebruiken. Wanneer het protocol RTMP opname streaming is geselecteerd, worden er twee opname-(invoer) eind punten gemaakt voor het kanaal:

* **Primaire URL**: Hiermee geeft u de volledig gekwalificeerde URL op van het primaire RTMP opname-eind punt van het kanaal.
* **Secundaire URL** (optioneel): Hiermee geeft u de volledig gekwalificeerde URL op van het secundaire RTMP opname-eind punt van het kanaal.

Gebruik de secundaire URL als u de duurzaamheid en fout tolerantie van uw opname stroom wilt verbeteren (evenals de failover van het coderings programma en fout tolerantie), met name voor de volgende scenario's:

- Eén coderings programma voor het dubbel pushen naar zowel de primaire als de secundaire Url's:

    Het belangrijkste doel van dit scenario is om meer flexibiliteit te geven aan netwerk schommelingen en-Jitters. Sommige RTMP-coderings Programma's verwerken de verbinding met het netwerk niet goed. Wanneer een netwerk wordt losgekoppeld, wordt de versleuteling mogelijk gestopt en worden de gebufferde gegevens niet verzonden wanneer er opnieuw verbinding wordt gemaakt. Dit leidt tot onderbrekingen en gegevens verlies. Netwerk verbindingen kunnen zich voordoen als gevolg van een beschadigd netwerk of onderhoud aan de Azure-zijde. Primaire/secundaire Url's verminderen de netwerk problemen en bieden een bewaakt upgrade proces. Telkens wanneer een geplande netwerk verbinding wordt verbroken, beheert Media Services de primaire en secundaire verbroken verbindingen en vertraagde verbreking van de twee. Encoders hebben dan tijd om gegevens te blijven verzenden en opnieuw verbinding te maken. De volg orde van de verbreken van de verbinding kan wille keurig zijn, maar er is altijd een vertraging tussen de primaire/secundaire of secundaire/primaire Url's. In dit scenario is het coderings programma nog steeds de Single Point of Failure.

- Meerdere coderings Programma's, waarbij elk coderings programma naar een toegewezen punt pusht:

    Dit scenario biedt zowel coderings programma als reredundantie van opname. In dit scenario encoder1 pushes naar de primaire URL en encoder2 pushes naar de secundaire URL. Wanneer een coderings programma mislukt, kan het andere coderings programma gegevens blijven verzenden. Gegevens redundantie kan worden gehandhaafd omdat Media Services de primaire en secundaire Url's niet tegelijk verbreekt. In dit scenario wordt ervan uitgegaan dat coderings Programma's zijn gesynchroniseerd en exact dezelfde gegevens opgeven.  

- Meerdere coderings Programma's die dubbel pushen naar zowel de primaire als de secundaire Url's:

    In dit scenario pushen beide Codeer gegevens naar zowel de primaire als de secundaire URL. Dit biedt de beste betrouw baarheid en fout tolerantie, evenals gegevens redundantie. Dit scenario kan beide coderings fouten verdragen en de verbinding verbreken, zelfs als één encoder niet meer werkt. Hierbij wordt ervan uitgegaan dat coderings Programma's zijn gesynchroniseerd en exact dezelfde gegevens opgeven.  

Zie [Azure Media Services RTMP-ondersteuning en live coderings](https://go.microsoft.com/fwlink/?LinkId=532824)Programma's voor meer informatie over RTMP Live coderings Programma's.

#### <a name="ingest-urls-endpoints"></a>Opname-Url's (eind punten)
Een kanaal biedt een invoer eindpunt (opname-URL) dat u opgeeft in het Live coderings programma, zodat het coderings programma streams naar uw kanalen kan pushen.   

U kunt de reopname-Url's ophalen wanneer u het kanaal maakt. U kunt deze Url's alleen ophalen als het kanaal niet de status **actief** heeft. Wanneer u klaar bent om te beginnen met het pushen van gegevens naar het kanaal, moet het kanaal de status **actief** hebben. Nadat het kanaal is begonnen met het opnemen van gegevens, kunt u een voor beeld van de stream bekijken via de URL van de preview-versie.

U kunt een gefragmenteerde MP4 (Smooth Streaming) live stream opnemen via een TLS-verbinding. Zorg ervoor dat u de opname-URL naar HTTPS bijwerkt voor opname via TLS. Op dit moment kunt u geen RTMP via TLS opnemen.

#### <a name="keyframe-interval"></a><a id="keyframe_interval"></a>Interval voor keyframe
Wanneer u een on-premises Live coderings programma gebruikt voor het genereren van een multi-bitrate stream, geeft het keyframe de duur van de groep afbeeldingen (GOP terug) aan, zoals wordt gebruikt door dat externe coderings programma. Nadat het kanaal deze inkomende stroom heeft ontvangen, kunt u uw Live Stream leveren aan client Play-toepassingen in een van de volgende indelingen: Smooth Streaming, dynamisch adaptief streamen via HTTP (DASH) en HTTP Live Streaming (HLS). Wanneer u live streamen uitvoert, wordt HLS altijd dynamisch verpakt. Standaard berekent Media Services de HLS segment-verhouding (fragmenten per segment) automatisch op basis van het interval van het keyframe dat is ontvangen van het Live coderings programma.

In de volgende tabel ziet u hoe de duur van het segment wordt berekend:

| Interval voor keyframe | HLS segment-verpakkings verhouding (FragmentsPerSegment) | Voorbeeld |
| --- | --- | --- |
| Minder dan of gelijk aan 3 seconden |3:1 |Als KeyFrameInterval (of GOP terug) 2 seconden is, is de standaard verhouding van het segment van het HLS 3 op 1. Hiermee maakt u een HLS-segment van 6 seconden. |
| 3 tot 5 seconden |2:1 |Als KeyFrameInterval (of GOP terug) 4 seconden is, is de standaard verhouding van het segment van het HLS-pakket 2 tot 1. Hiermee maakt u een HLS-segment van 8 seconden. |
| Langer dan 5 seconden |1:1 |Als KeyFrameInterval (of GOP terug) 6 seconden is, is de standaard verhouding van het segment van het HLS 1 en 1. Hiermee maakt u een HLS-segment van 6 seconden. |

U kunt de verhouding van fragmenten per segment wijzigen door de uitvoer van het kanaal te configureren en FragmentsPerSegment in ChannelOutputHls in te stellen.

U kunt ook de waarde voor het interval van het keyframe wijzigen door de eigenschap KeyFrameInterval in te stellen op ChannelInput. Als u KeyFrameInterval expliciet instelt, wordt de HLS-segment verpakkings ratio FragmentsPerSegment berekend volgens de eerder beschreven regels.  

Als u zowel KeyFrameInterval als FragmentsPerSegment expliciet hebt ingesteld, gebruikt Media Services de waarden die u hebt ingesteld.

#### <a name="allowed-ip-addresses"></a>Toegestane IP-adressen
U kunt de IP-adressen definiëren die video naar dit kanaal mogen publiceren. Een toegestaan IP-adres kan worden opgegeven als een van de volgende:

* Eén IP-adres (bijvoorbeeld 10.0.0.1)
* Een IP-bereik dat gebruikmaakt van een IP-adres en een CIDR-subnetmasker (bijvoorbeeld 10.0.0.1/22)
* Een IP-bereik dat gebruikmaakt van een IP-adres en een decimaal subnetmasker met punten (bijvoorbeeld 10.0.0.1 (255.255.252.0))

Als er geen IP-adressen zijn opgegeven en er geen regel definitie is, is geen IP-adres toegestaan. Als u IP-adres(sen) wilt toestaan, maakt u een regel en stelt u 0.0.0.0/0 in.

### <a name="channel-preview"></a>Channel Preview
#### <a name="preview-urls"></a>Preview-URL's
Kanalen bieden een preview-eind punt (Preview-URL) die u gebruikt om uw stroom te bekijken en te valideren vóór verdere verwerking en levering.

U kunt de voor beeld-URL ophalen wanneer u het kanaal maakt. U kunt de URL alleen ophalen als het kanaal niet de status **actief** heeft. Wanneer het kanaal begint met het opnemen van gegevens, kunt u een voor beeld van de stroom bekijken.

Op dit moment kan de voorbeeld stroom alleen worden geleverd in gefragmenteerde MP4-indeling (Smooth Streaming), ongeacht het opgegeven invoer type. U kunt de [Smooth streaming Health Monitor](https://playready.directtaps.net/smoothstreaming/) Player gebruiken om de vloeiende stroom te testen. U kunt ook een speler gebruiken die wordt gehost in de Azure Portal om uw stroom weer te geven.

#### <a name="allowed-ip-addresses"></a>Toegestane IP-adressen
U kunt de IP-adressen definiëren die verbinding mogen maken met het eind punt van de preview. Als er geen IP-adressen zijn opgegeven, is elk IP-adres toegestaan. Een toegestaan IP-adres kan worden opgegeven als een van de volgende:

* Eén IP-adres (bijvoorbeeld 10.0.0.1)
* Een IP-bereik dat gebruikmaakt van een IP-adres en een CIDR-subnetmasker (bijvoorbeeld 10.0.0.1/22)
* Een IP-bereik dat gebruikmaakt van een IP-adres en een decimaal subnetmasker met punten (bijvoorbeeld 10.0.0.1 (255.255.252.0))

### <a name="channel-output"></a>Kanaal-uitvoer
Zie de sectie [interval voor keyframe](#keyframe_interval) voor meer informatie over kanaal uitvoer.

### <a name="channel-managed-programs"></a>Door kanalen beheerde Program ma's
Een kanaal is gekoppeld aan Program ma's die u kunt gebruiken voor het beheren van het publiceren en opslaan van segmenten in een live stream. Kanalen beheren Program ma's. De kanaal-en programma relatie is vergelijkbaar met traditionele media, waarbij een kanaal een constante stroom inhoud heeft en een programma wordt afgestemd op een bepaalde time-outgebeurtenis op dat kanaal.

U kunt het aantal uren opgeven dat u de opgenomen inhoud voor het programma wilt behouden door de lengte voor **Archiefvenster** in te stellen. Deze waarde kan worden ingesteld van minimaal 5 minuten tot maximaal 25 uur. De lengte van het archief venster bepaalt ook het maximum aantal keren dat clients vanaf de huidige Live positie terug op tijd kunnen zoeken. Programma's kunnen in de opgegeven tijdsduur worden uitgevoerd, maar de inhoud die achter de lengte van het venster valt, wordt altijd verwijderd. De waarde van deze eigenschap bepaalt ook hoe lang de clientmanifesten kunnen groeien.

Elk programma is gekoppeld aan een Asset waarin de gestreamde inhoud wordt opgeslagen. Een Asset wordt toegewezen aan een blok-BLOB-container in het Azure-opslag account en de bestanden in de Asset worden opgeslagen als blobs in die container. Als u het programma wilt publiceren zodat uw klanten de stroom kunnen bekijken, moet u een OnDemand-Locator voor de gekoppelde Asset maken. Met deze Locator kunt u een streaming-URL maken die u aan uw clients kunt leveren.

Een kanaal ondersteunt Maxi maal drie gelijktijdig actieve Program ma's, zodat u meerdere archieven van dezelfde binnenkomende stream kunt maken. U kunt zo nodig andere onderdelen van een gebeurtenis publiceren en archiveren. Stel bijvoorbeeld dat uw zakelijke vereiste is om 6 uur van een programma te archiveren, maar alleen de laatste tien minuten uit te zenden. Hiervoor moet u twee gelijktijdig actieve programma's maken. Eén programma is ingesteld om zes uur van de gebeurtenis te archiveren, maar het programma wordt niet gepubliceerd. Het andere programma is ingesteld op archiveren gedurende tien minuten en dit programma wordt gepubliceerd.

Gebruik bestaande programma's niet voor nieuwe gebeurtenissen. Maak in plaats daarvan een nieuw programma voor elke gebeurtenis. Start het programma wanneer u klaar bent om te streamen en te archiveren. Stop het programma als u het streamen wilt stoppen en de gebeurtenis wilt archiveren.

Als u de gearchiveerde inhoud wilt verwijderen, stopt en verwijdert u het programma en verwijdert u vervolgens de gekoppelde Asset. Een Asset kan niet worden verwijderd als een programma dit gebruikt. Het programma moet eerst worden verwijderd.

Zelfs nadat u het programma hebt gestopt en verwijderd, kunnen gebruikers de gearchiveerde inhoud als een video op aanvraag streamen, totdat u het activum verwijdert. Als u de gearchiveerde inhoud wilt behouden, maar deze niet beschikbaar wilt maken voor streaming, verwijdert u de streaming-Locator.

## <a name="channel-states-and-billing"></a><a id="states"></a>Kanaal status en facturering
Mogelijke waarden voor de huidige status van een kanaal zijn:

* **Gestopt**: dit is de begin status van het kanaal nadat het is gemaakt. In deze status kunnen de kanaal eigenschappen worden bijgewerkt, maar streaming is niet toegestaan.
* **Starten**: het kanaal wordt gestart. Er zijn geen updates of streaming toegestaan tijdens deze status. Als er een fout optreedt, keert het kanaal terug naar de status **gestopt** .
* **Uitvoeren**: het kanaal kan live streams verwerken.
* **Stoppen**: het kanaal wordt gestopt. Er zijn geen updates of streaming toegestaan tijdens deze status.
* **Verwijderen**: het kanaal wordt verwijderd. Er zijn geen updates of streaming toegestaan tijdens deze status.

In de volgende tabel ziet u hoe kanaal statussen worden toegewezen aan de facturerings modus.

| Kanaal status | UI-indica toren van portal | Gefactureerd? |
| --- | --- | --- |
| **Starten** |**Starten** |Nee (tijdelijke status) |
| **Wordt uitgevoerd** |**Gereed** (geen actieve Program ma's)<p><p>of<p>**Streaming** (ten minste één programma dat wordt uitgevoerd) |Ja |
| **Stoppen** |**Stoppen** |Nee (tijdelijke status) |
| **Gestopt** |**Gestopt** |Nee |

## <a name="closed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Ondertiteling en AD-invoeging
De volgende tabel bevat de ondersteunde standaarden voor ondertiteling en het invoegen van advertenties.

| Standard | Notities |
| --- | --- |
| CEA-708 en EIA-608 (708/608) |CEA-708 en EIA-608 zijn closed captioning-standaarden voor de Verenigde Staten en Canada.<p><p>Bijschriften worden momenteel alleen ondersteund als deze in de gecodeerde invoer stroom worden uitgevoerd. U moet een Live Media Encoder gebruiken waarmee u 608-of 708-bijschriften in de gecodeerde stroom kunt invoegen die naar Media Services worden verzonden. Media Services levert de inhoud met Inge sloten bijschriften aan uw viewers. |
| TTML binnen. ismt (Smooth Streaming tekst sporen) |Met Media Services dynamische pakketten kunnen uw clients inhoud streamen in een van de volgende indelingen: DASH, HLS of Smooth Streaming. Als u echter gefragmenteerde MP4 (Smooth Streaming) opneemt met bijschriften in. ismt (Smooth Streaming tekst sporen), kunt u de stroom alleen aan Smooth Streaming-clients leveren. |
| SCTE-35 |SCTE-35 is een digitaal signalerings systeem dat wordt gebruikt voor het aanroepen van invoeging van advertenties. Downstream-ontvangers gebruiken het signaal om reclame uit te splitsen in de stroom voor de toegewezen tijd. SCTE-35 moet worden verzonden als een sparse track in de invoer stroom.<p><p>Op dit moment is de enige ondersteunde invoer stroom indeling die AD-signalen heeft, gefragmenteerde MP4 (Smooth Streaming). De enige ondersteunde uitvoer indeling is ook Smooth Streaming. |

## <a name="considerations"></a><a id="considerations"></a>Overwegingen
Wanneer u een on-premises Live coderings programma gebruikt om een multi-bitrate stroom naar een kanaal te verzenden, gelden de volgende beperkingen:

* Zorg ervoor dat u voldoende vrije Internet connectiviteit hebt om gegevens te verzenden naar de opname punten.
* Voor het gebruik van een secundaire opname-URL is extra band breedte vereist.
* De binnenkomende multi-bitrate stroom kan Maxi maal 10 niveaus voor video kwaliteit (lagen) en Maxi maal 5 audio tracks hebben.
* De hoogste gemiddelde bitsnelheid voor elk van de video kwaliteits niveaus moet lager zijn dan 10 Mbps.
* De som van de gemiddelde bitrates voor alle video-en audio-streams moet lager zijn dan 25 Mbps.
* U kunt het invoer protocol niet wijzigen terwijl het kanaal of de gekoppelde Program ma's worden uitgevoerd. Als u verschillende protocollen nodig hebt, maakt u afzonderlijke kanalen voor elk invoerprotocol.
* U kunt één bitrate opnemen in uw kanaal. Maar omdat de stroom niet door het kanaal wordt verwerkt, ontvangen de client toepassingen ook een enkele bitrate stroom. (Deze optie wordt niet aanbevolen.)

Hier volgen andere overwegingen met betrekking tot het werken met kanalen en verwante onderdelen:

* Telkens wanneer u het Live coderings programma opnieuw configureert, roept u de methode **Reset** op het kanaal aan. Voordat u het kanaal opnieuw instelt, moet u het programma stoppen. Nadat u het kanaal opnieuw hebt ingesteld, start u het programma opnieuw.

  > [!NOTE]
  > Wanneer u het programma opnieuw start, moet u dit koppelen aan een nieuwe Asset en een nieuwe Locator maken. 
  
* Een kanaal kan alleen worden gestopt wanneer het zich in de **uitvoerings** status bevindt en alle Program ma's op het kanaal zijn gestopt.
* Standaard kunt u slechts vijf kanalen toevoegen aan uw Media Services-account. Zie [quota's en beperkingen](media-services-quotas-and-limitations.md)voor meer informatie.
* U wordt alleen gefactureerd wanneer uw kanaal de status **actief** heeft. Zie de sectie [kanaal Staten en facturering](media-services-live-streaming-with-onprem-encoders.md#states) voor meer informatie.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="suggestions-and-feedback"></a>Suggesties en feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Verwante onderwerpen
[Aanbevolen voor on-premises encoders](media-services-recommended-encoders.md)

[Specificatie van opname van Azure Media Services gefragmenteerde MP4-levens duur](../media-services-fmp4-live-ingest-overview.md)

[Overzicht van Azure Media Services en algemene scenario's](media-services-overview.md)

[Media Services-concepts](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
