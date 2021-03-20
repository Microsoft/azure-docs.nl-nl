---
title: Optimalisatie van mediastreaming met Azure CDN
description: Meer informatie over opties voor het optimaliseren van streaming-media in azure Content Delivery Network, zoals delen van de cache en wacht tijd voor cache opvullen.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: c3ab722f182e32cf2f3aca6bb2f3d5a9598264af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88192601"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Optimalisatie van mediastreaming met Azure CDN 
 
Het gebruik van high-definition video neemt toe op internet, waardoor er problemen ontstaan voor een efficiënte levering van grote bestanden. Klanten verwachten een glad afspelen van video op aanvraag of live video-assets op diverse netwerken en clients over de hele wereld. Een snelle en efficiënte leverings methode voor mediastreaming van media is essentieel om te zorgen voor een soepele en plezierigere consumenten ervaring.  

Live streaming-media is vooral moeilijk te leveren vanwege de grote grootten en het aantal gelijktijdige viewers. Als er lange vertragingen zijn, worden gebruikers verlaten. Omdat live streams niet tijdig in de cache kunnen worden opgeslagen en grote latenties niet acceptabel zijn voor viewers, moeten video fragmenten tijdig worden geleverd. 

De aanvraag patronen van streaming bieden ook enkele nieuwe uitdagingen. Wanneer een populaire live stream of een nieuwe reeks wordt uitgebracht voor video op aanvraag, kunnen duizenden aan miljoenen viewers tegelijkertijd de stream aanvragen. In dit geval is slimme aanvraag consolidatie essentieel om de oorspronkelijke servers niet te overbelasten wanneer de activa nog niet in de cache zijn opgeslagen.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Optimalisatie van media-streaming voor Azure CDN van micro soft

**Azure CDN standaard van micro soft** -eind punten leveren streaming-media-assets rechtstreeks op met behulp van het optimalisatie type algemene weblevering. 

Optimalisatie van mediastreaming voor **Azure CDN standaard van micro soft** is van kracht voor Live-of video-on-demand streaming-media die afzonderlijke media fragmenten gebruiken voor levering. Dit proces wijkt af van één groot activum dat wordt overgedragen via progressief downloaden of door gebruik te maken van byte bereik aanvragen. Zie voor meer informatie over die manier van media levering [grote bestanden downloaden optimalisatie met Azure CDN](cdn-large-file-optimization.md).

De geavanceerde optimalisatie typen voor media levering of video-on-demand media gebruiken Azure Content Delivery Network (CDN) met back-end-optimalisaties om media-assets sneller te leveren. Ze gebruiken ook configuraties voor media-assets op basis van best practices die gedurende een bepaalde periode zijn geleerd.

### <a name="partial-cache-sharing"></a>Delen van cache gedeeltelijk
Met delen in cache kan het CDN gedeeltelijk in cache opgeslagen inhoud verzenden naar nieuwe aanvragen. Als de eerste aanvraag voor de CDN bijvoorbeeld resulteert in een cache-Missing, wordt de aanvraag verzonden naar de oorsprong. Hoewel deze onvolledige inhoud in de CDN-cache wordt geladen, kunnen andere aanvragen naar het CDN deze gegevens gaan ophalen. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Optimalisatie van media-streaming voor Azure CDN van Verizon

**Azure CDN standaard van Verizon** en **Azure CDN Premium van Verizon** -eind punten leveren media-assets rechtstreeks op met behulp van het optimalisatie type algemene weblevering. Een aantal functies van de CDN bieden rechtstreeks ondersteuning voor het leveren van media-assets.

### <a name="partial-cache-sharing"></a>Delen van cache gedeeltelijk

Met delen in cache kan het CDN gedeeltelijk in cache opgeslagen inhoud verzenden naar nieuwe aanvragen. Als de eerste aanvraag voor de CDN bijvoorbeeld resulteert in een cache-Missing, wordt de aanvraag verzonden naar de oorsprong. Hoewel deze onvolledige inhoud in de CDN-cache wordt geladen, kunnen andere aanvragen naar het CDN deze gegevens gaan ophalen. 

### <a name="cache-fill-wait-time"></a>Wacht tijd van cache vulling

 Met de functie wacht tijd voor cache opvullen wordt de rand server gedwongen om volgende aanvragen voor dezelfde resource op te slaan totdat HTTP-reactie headers van de oorspronkelijke server arriveren. Als de HTTP-antwoord headers van de oorsprong arriveren voordat de timer verloopt, worden alle aanvragen die in de wacht stand zijn geplaatst, vanuit de groeiende cache verzonden. Op hetzelfde moment wordt de cache gevuld met gegevens van de oorsprong. De wacht tijd voor de cache vulling wordt standaard ingesteld op 3.000 milliseconden. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Optimalisatie van media-streaming voor Azure CDN van Akamai
 
**Azure CDN Standard van Akamai** biedt een functie waarmee streaming-media-assets efficiënt kunnen worden verstrekt aan gebruikers in de hele wereld op schaal. De functie reduceert latentie omdat hiermee de belasting van de oorspronkelijke servers wordt gereduceerd. Deze functie is beschikbaar in de Standard Akamai prijs categorie. 

Optimalisatie van media streaming voor **Azure CDN standaard van Akamai is van** kracht voor Live-of video-on-demand streaming-media die afzonderlijke media fragmenten gebruiken voor levering. Dit proces wijkt af van één groot activum dat wordt overgedragen via progressief downloaden of door gebruik te maken van byte bereik aanvragen. Zie [grote bestands optimalisatie](cdn-large-file-optimization.md)voor informatie over die manier van media levering.

De geavanceerde optimalisatie typen voor media levering of video-on-demand media gebruiken een CDN met back-end-optimalisaties om media-assets sneller te leveren. Ze gebruiken ook configuraties voor media-assets op basis van best practices die gedurende een bepaalde periode zijn geleerd.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Een Akamai CDN-eind punt configureren om mediastreaming te optimaliseren
 
U kunt het CDN-eind punt (Content Delivery Network) configureren om de levering van grote bestanden via de Azure Portal te optimaliseren. U kunt ook de REST-Api's of een van de client-Sdk's gebruiken om dit te doen. In de volgende stappen wordt het proces weer gegeven via de Azure Portal voor een **Azure CDN-standaard van** het profiel Akamai:

1. Als u een nieuw eind punt wilt toevoegen, selecteert u op de pagina Akamai **CDN-profiel** **eind punt**.
  
    ![Nieuw eind punt](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. Selecteer in de vervolg keuzelijst **geoptimaliseerd voor** de optie **video op aanvraag** mediastreaming voor video-on-demand-assets. Als u een combi natie van Live en video-on-demand streaming hebt, selecteert u **algemene** mediastreaming.

    ![Streaming geselecteerd](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Nadat u het eind punt hebt gemaakt, wordt de optimalisatie toegepast voor alle bestanden die aan bepaalde criteria voldoen. Dit proces wordt beschreven in de volgende sectie. 

### <a name="caching"></a>Caching

Als **Azure CDN standaard van Akamai** detecteert dat het activum een streaming-manifest of-fragment is, gebruikt het een andere verloop tijd voor caching vanaf de algemene Internet levering. (Zie de volledige lijst in de volgende tabel.) Zoals altijd, worden de headers van het cache-control-of expires-bericht van de oorsprong gehonoreerd. Als de Asset geen Media-Asset is, wordt deze in de cache opgeslagen met de verloop tijden voor de algemene Internet levering.

De korte, negatieve cache tijd is handig voor offload van de oorsprong wanneer veel gebruikers een fragment aanvragen dat nog niet bestaat. Een voor beeld is een live stream waarbij de pakketten niet beschikbaar zijn vanaf de oorspronkelijke oorsprong. Met het interval voor langere caching kunt u ook aanvragen van de oorsprong offloaden, omdat de inhoud van de video doorgaans niet wordt gewijzigd.

| Caching  | Algemene Internet levering | Algemene mediastreaming | Video-on-demand mediastreaming  
|--- | --- | --- | ---
| Caching: positief <br> HTTP 200, 203, 300, <br> 301, 302 en 410 | 7 dagen |365 dagen | 365 dagen   
| Caching: negatief <br> HTTP 204, 305, 404, <br> en 405 | Geen | 1 seconde | 1 seconde
 
### <a name="deal-with-origin-failure"></a>Omgaan met fout van oorsprong  

De levering van algemene media en video-on-demand media hebben ook bron-time-outs en een nieuwe poging op basis van aanbevolen procedures voor typische aanvraag patronen. Omdat de levering van algemene media bijvoorbeeld is voor Live en video-on-demand media levering, wordt een kortere verbindingstime-out gebruikt vanwege de tijd gevoelige aard van live streamen.

Wanneer er een time-out optreedt voor een verbinding, probeert het CDN een aantal keren opnieuw geprobeerd voordat de fout ' 504-Gateway-timeout ' naar de client wordt verzonden. 

Wanneer een bestand overeenkomt met de lijst met bestands typen en de grootte van de voor waarden, gebruikt het CDN het gedrag voor mediastreaming. Anders wordt gebruikgemaakt van algemene Internet-levering.
   
### <a name="conditions-for-media-streaming-optimization"></a>Voor waarden voor optimalisatie van mediastreaming 

De volgende tabel bevat de set criteria waaraan moet worden voldaan voor optimalisatie van mediastreaming: 
 
Ondersteunde streaming-typen | Bestandsextensies  
--- | ---  
Apple HLS | M3U8, M3U, m3ub, Key, TS, AAC
Adobe HDS | F4M, f4x, drmmeta, Boots trap, F4F,<br>Seg-Frag-URL-structuur <br> (overeenkomende regex: ^ (/. *) SEQ (\d +)-FRAG (\d +)
LIGGENDE | mpd, streepje, DivX, ismv, M4S, M4V, MP4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Vloeiend streamen | /manifest/, /QualityLevels/Fragments/
  
 
