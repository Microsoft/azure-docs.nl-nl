---
title: Overzicht van live streamen met behulp van Azure Media Services | Microsoft Docs
description: Dit artikel geeft een overzicht van live streamen met behulp van Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 195333f5f06145a1dfa84f0de2c5984190e3d7ca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014822"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Overzicht van live streamen met Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

## <a name="overview"></a>Overzicht

Bij het leveren van live streaming-gebeurtenissen met Azure Media Services worden de volgende onderdelen doorgaans betrokken:

* Een camera die wordt gebruikt voor het uitzenden van een gebeurtenis.
* Een coderingsprogramma voor live video dat de signalen van de camera converteert naar stromen die worden verzonden naar een service voor live streamen.

    Eventueel meerdere op tijd gesynchroniseerde coderingsprogramma’s. Voor bepaalde kritieke live gebeurtenissen die een zeer hoge beschikbaarheid en kwaliteit vereisen, kunt u het beste redundante coderingsprogramma’s (actief/actief) met tijdsynchronisatie gebruiken voor een naadloze failover zonder verlies van gegevens.
* Een service voor live streamen waarmee u het volgende kunt doen:

  * Live-inhoud met verschillende protocollen voor live streamen opnemen (bijvoorbeeld RTMP of Smooth Streaming).
  * (Optioneel) Uw stream coderen in een stream met een adaptieve bitsnelheid.
  * Een voorbeeld van uw livestream bekijken.
  * De opgenomen inhoud vastleggen en opnemen om deze later te streamen (Video-on-Demand).
  * De inhoud rechtstreeks aan uw klanten leveren via algemene protocollen voor streaming (bijvoorbeeld MPEG DASH, Smooth of HLS) of aan een Content Delivery Network (CDN) voor verdere distributie.

Met **Microsoft Azure Media Services** (AMS) kunt u de inhoud van uw live-streaming opnemen, coderen, een voorbeeld bekijken, opslaan en leveren.

Met Media Services kunt u gebruikmaken van [dynamische pakketten](media-services-dynamic-packaging-overview.md), waarmee u uw live streams kunt uitzenden in MPEG Dash-, HLS-en Smooth streaming-indelingen van de bijdrage-feed die naar de service wordt verzonden. Uw viewers kunnen de Live Stream afspelen met een HLS, DASH of Smooth Streaming compatibele spelers. U kunt Azure Media Player in uw web-of mobiele toepassingen gebruiken om uw stream in een van deze protocollen te leveren.

> [!NOTE]
> Vanaf 12 mei 2018 krijgen Live kanalen niet langer ondersteuning voor het RTP/MPEG-2 Trans Port stream opname protocol. Migreer van RTP/MPEG-2 naar RTMP-of gefragmenteerde MP4 (Smooth Streaming)-opname protocollen.

## <a name="streaming-endpoints-channels-programs"></a>Streaming-eind punten, kanalen, Program Ma's

In Azure Media Services wordt alle functionaliteiten voor live streamen afgehandeld door **kanalen**, **programma's** en **streaming-eindpunten**, waaronder opnemen, opmaken, DVR, beveiliging, schaalbaarheid en redundantie.

Een **kanaal** vertegenwoordigt een pijplijn voor de verwerking van inhoud voor live-streaming. Een kanaal kan op de volgende manieren live invoerstromen ontvangen:

* Een on-premises live codering verzendt multi-bitrate **RTMP** of **Smooth Streaming** (gefragmenteerd MP4) naar het kanaal dat is geconfigureerd voor **passthrough**-levering. **Passthrough**-levering vindt plaats wanneer de opgenomen streams het **kanaal** passeren zonder verdere verwerking. U kunt de volgende Live coderings Programma's gebruiken die multi-bitrate Smooth Streaming uitvoeren: MediaExcel, Ateme, denk aan communicatie, Envivio, Cisco en elementair. De volgende Live coderings Programma's uitvoeren RTMP: Telestream Wirecast, Haivision, Teradek-transcodeers.  Een live coderingsprogramma kan ook een stream met één bitsnelheid verzenden naar een kanaal dat niet is ingeschakeld voor Live Encoding, maar dit wordt niet aanbevolen. Desgevraagd levert Media Services de stream aan klanten.

  > [!NOTE]
  > Het gebruik van de passthrough-methode is de meest voordelige manier om live te streamen wanneer u meerdere gebeurtenissen gedurende een langere periode streamt en u al hebt geïnvesteerd in on-premises coderingsprogramma’s. Zie de details over de [prijzen](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Een on-premises Live Encoder verzendt een stream met één bitsnelheid naar het kanaal dat is ingeschakeld voor het uitvoeren van Live code ring met Media Services in een van de volgende indelingen: RTMP of Smooth Streaming (gefragmenteerde MP4). De volgende Live coderings Programma's met RTMP-uitvoer zijn bekend om te werken met kanalen van dit type: Telestream-Wirecast. Het kanaal codeert de inkomende single-bitrate stream vervolgens live naar een (adaptieve) multi-bitrate videostream. Desgevraagd levert Media Services de stream aan klanten.

Als u begint met de release van Media Services 2,10, kunt u, wanneer u een kanaal maakt, opgeven op welke manier u wilt dat uw kanaal de invoer stroom ontvangt en of u wilt dat het kanaal Live code ring van uw stream uitvoert. U hebt hiervoor twee opties:

* **Geen** (Pass-Through) – Geef deze waarde op als u van plan bent een on-premises Live Encoder te gebruiken waarmee een multi-bitrate stroom (een Pass-Through-stroom) wordt uitgevoerd. In dit geval wordt de inkomende stroom door gegeven aan de uitvoer zonder code ring. Dit is het gedrag van een kanaal vóór 2,10 release.  
* **Standaard** : Kies deze waarde als u Media Services wilt gebruiken om uw live stream met één bitsnelheid te coderen naar een multi-bitrate stream. Deze methode is rendabeler voor het snel omhoog schalen van onregelmatige gebeurtenissen. Houd er rekening mee dat er sprake is van een facturerings effect voor Live code ring en u moet er rekening mee houden dat het gebruik van een live coderings kanaal in de status ' running ' in rekening wordt gebracht.  Het is raadzaam om uw actieve kanalen direct te stoppen nadat uw live streaming-gebeurtenis is voltooid om extra kosten per uur te voor komen.

## <a name="comparison-of-channel-types"></a>Vergelijking van kanaal typen

De volgende tabel bevat een hand leiding voor het vergelijken van de twee typen kanalen die worden ondersteund in Media Services

| Functie | Pass-Through-kanaal | Standaard kanaal |
| --- | --- | --- |
| Invoer met één bitsnelheid wordt gecodeerd in meerdere bitrates in de Cloud |Nee |Ja |
| Maximum resolutie, aantal lagen |1080p, 8 lagen, 60 + fps |720p, 6 lagen, 30 fps |
| Invoer protocollen |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Prijs |Bekijk de [pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/) en klik op het tabblad live video |De [pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/) bekijken |
| Maximale uitvoerings tijd |24x7 |8 uur |
| Ondersteuning voor het invoegen van pastels |Nee |Ja |
| Ondersteuning voor AD-Signa lering |Nee |Ja |
| Pass-Through CEA 608/708-bijschriften |Ja |Ja |
| Ondersteuning voor niet-uniforme invoer GOPs |Ja |Nee – invoer moet worden vastgesteld 2sec GOPs |
| Ondersteuning voor variabele frame-rate invoer |Ja |Nee – invoer moet een vaste frame frequentie zijn.<br/>Kleine variaties zijn toegestaan, bijvoorbeeld tijdens hoge bewegende scènes. Code ring kan echter niet worden verwijderd uit 10 frames per seconde. |
| Automatische shutoff van kanalen wanneer invoer toevoer verloren gaat |Nee |Na 12 uur, als er geen programma wordt uitgevoerd |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Werken met kanalen die een multi-bitrate livestream van on-premises encoders ontvangen (pass-through)

Het volgende diagram toont de belangrijkste onderdelen van het AMS-platform die betrokken zijn bij de **passthrough**-werkstroom.

![Diagram waarin de belangrijkste onderdelen van het A M S-platform voor de werk stroom ' Pass-through ' worden weer gegeven.](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Zie [Werken met kanalen die een multi-bitrate livestream van on-premises coderingsprogramma’s ontvangen](media-services-live-streaming-with-onprem-encoders.md) voor meer informatie.

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services

Het volgende diagram toont de belangrijke onderdelen van het AMS-platform die betrokken zijn bij de Live Streaming-werkstroom waarbij een kanaal live codering kan uitvoeren met Media Services.

![Live-werkstroom](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Zie [Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.

## <a name="description-of-a-channel-and-its-related-components"></a>Beschrijving van een kanaal en de bijbehorende onderdelen

### <a name="channel"></a>Kanaal

In Media Services zijn [Channel](/rest/api/media/operations/channel)s verantwoordelijk voor het verwerken van live streaming-inhoud. Een kanaal biedt een invoer eindpunt (opname-URL) dat u vervolgens verstrekt aan een live-transcodeer. Het kanaal ontvangt Live-invoer stromen van de Live-transcodeer en maakt deze beschikbaar voor streaming via een of meer StreamingEndpoints. Kanalen bieden ook een preview-eind punt (Preview-URL) die u gebruikt om uw stroom te bekijken en te valideren vóór verdere verwerking en levering.

U kunt de opname-URL en de voor beeld-URL ophalen wanneer u het kanaal maakt. Om deze Url's op te halen, hoeft het kanaal niet de status gestart te hebben. Wanneer u klaar bent om gegevens van een live-transcodeer naar het kanaal te pushen, moet het kanaal worden gestart. Zodra de Live-code ring begint met het opnemen van gegevens, kunt u een voor beeld van de stroom bekijken.

Elk Media Services-account kan meerdere kanalen, meerdere Program Ma's en meerdere StreamingEndpoints bevatten. Afhankelijk van de behoefte aan band breedte en beveiliging, kunnen StreamingEndpoint-services worden toegewezen aan een of meer kanalen. Elk StreamingEndpoint kan uit elk kanaal halen.

Wanneer u een kanaal maakt, kunt u toegestane IP-adressen in een van de volgende indelingen opgeven: IpV4-adres met 4 getallen, CIDR-adres bereik.

### <a name="program"></a>Programma
Met een [programma](/rest/api/media/operations/program) kunt u het publiceren en opslaan van segmenten in een live stream beheren. Kanalen beheren programma's. De kanaal-/programmarelatie lijkt erg op traditionele media waarbij een kanaal een constante stream met inhoud heeft en een programma is afgestemd op een bepaalde getimede gebeurtenis op dat kanaal.
U kunt het aantal uren opgeven dat u de opgenomen inhoud voor het programma wilt behouden door de eigenschap **ArchiveWindowLength** in te stellen. Deze waarde kan worden ingesteld van minimaal 5 minuten tot maximaal 25 uur.

ArchiveWindowLength bepaalt ook de maximale hoeveelheid tijd die clients vanaf de huidige Live positie op tijd kunnen zoeken. Programma's kunnen in de opgegeven tijdsduur worden uitgevoerd, maar de inhoud die achter de lengte van het venster valt, wordt altijd verwijderd. De waarde van deze eigenschap bepaalt ook hoe lang de client manifesten kunnen groeien.

Elk programma is gekoppeld aan een asset. Als u het programma wilt publiceren, moet u een Locator voor de gekoppelde Asset maken. Met deze locator kunt u een streaming-URL maken die u aan uw clients kunt leveren.

Een kanaal ondersteunt maximaal drie gelijktijdig actieve programma's, zodat u meerdere archieven van dezelfde binnenkomende stream kunt maken. Hierdoor kunt u verschillende onderdelen van een gebeurtenis naar behoefte publiceren en archiveren. Zo kan het voor uw bedrijf nodig zijn zes uur van een programma te archiveren, maar alleen de laatste tien minuten uit te zenden. Hiervoor moet u twee gelijktijdig actieve programma's maken. Het ene programma wordt ingesteld om zes uur van de gebeurtenis te archiveren, maar het programma wordt niet gepubliceerd. Het andere programma wordt ingesteld om tien minuten te archiveren en dit programma wordt wel gepubliceerd.

## <a name="billing-implications"></a>Facturerings implicaties
Een kanaal begint met het factureren zodra de status overgangen via de API wordt uitgevoerd.  

In de volgende tabel ziet u hoe kanaal statussen worden toegewezen aan facturerings statussen in de API en Azure Portal. Houd er rekening mee dat de statussen enigszins verschillen tussen de API en de UX van de portal. Zodra een kanaal zich in de status ' running ' bevindt via de API of in de status ' gereed ' of ' streaming ' in de Azure Portal, wordt de facturering actief.

Als u het kanaal niet meer wilt factureren, moet u het kanaal stoppen via de API of in de Azure Portal.
U bent zelf verantwoordelijk voor het stoppen van uw kanalen wanneer u klaar bent met het kanaal. Als het kanaal niet kan worden gestopt, wordt de facturering voortgezet.

> [!NOTE]
> Wanneer u werkt met standaard kanalen, wordt AMS automatisch shutoff elk kanaal dat nog steeds de status actief heeft, 12 uur nadat de invoer is verwijderd en er geen Program Ma's worden uitgevoerd. Er worden echter nog steeds kosten in rekening gebracht voor de tijd dat het kanaal de status actief heeft.
>
>

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

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Verwante onderwerpen
[Azure Media Services gefragmenteerde MP4 Live opname-specificatie](../media-services-fmp4-live-ingest-overview.md)

[Werken met kanalen die zijn ingeschakeld om Live Encoding uit te voeren met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Werken met kanalen die multi-bitrate Live Stream van on-premises encoders ontvangen](media-services-live-streaming-with-onprem-encoders.md)

[Quota en beperkingen](media-services-quotas-and-limitations.md).  

[Media Services concepten](media-services-concepts.md)
