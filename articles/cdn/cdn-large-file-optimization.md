---
title: Optimalisatie van grote bestanden downloaden met Azure CDN
description: Meer informatie over hoe grote bestands downloads kunnen worden geoptimaliseerd in azure Content Delivery Network. Dit artikel bevat verschillende scenario's.
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
ms.openlocfilehash: ed5768e89482d32bb140e9ba7064de2d20809892
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020718"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optimalisatie van grote bestanden downloaden met Azure CDN

De bestands grootte van inhoud die via internet wordt geleverd, blijft groeien vanwege verbeterde functionaliteit, verbeterde grafische afbeeldingen en uitgebreide media-inhoud. Deze groei wordt aangestuurd door een groot aantal factoren: breedband penetratie, grotere goedkope opslag apparaten, uitgebreide toename van high-definition video en met internet verbonden apparaten (IoT). Een snelle en efficiënte leverings methode voor grote bestanden is essentieel om te zorgen voor een soepele en plezierigere consumenten ervaring.

Het leveren van grote bestanden heeft verschillende uitdagingen. Ten eerste kan de gemiddelde tijd voor het downloaden van een groot bestand aanzienlijk zijn, omdat toepassingen mogelijk niet alle gegevens opeenvolgend kunnen downloaden. In sommige gevallen kunnen toepassingen het laatste deel van een bestand vóór het eerste deel downloaden. Als slechts een kleine hoeveelheid van een bestand is aangevraagd of als een gebruiker een down load pauzeert, kan het downloaden mislukken. De down load kan ook worden uitgesteld totdat het volledige bestand van de oorspronkelijke server is opgehaald door het CDN (Content Delivery Network). 

Ten tweede bepaalt de latentie tussen de computer van een gebruiker en het bestand de snelheid waarmee ze inhoud kunnen weer geven. Daarnaast hebben netwerk congestie-en capaciteits problemen ook invloed op de door voer. Grotere afstanden tussen servers en gebruikers maken extra mogelijkheden om pakket verlies te voor komen, waardoor de kwaliteit wordt verminderd. De verminderde kwaliteit als gevolg van een beperkte door Voer en een verhoogd pakket verlies kan de wacht tijd voor het downloaden van een bestand verg Roten. 

Ten derde worden veel grote bestanden niet volledig bezorgd. Gebruikers kunnen een down load halverwege door lopen of alleen de eerste paar minuten van een lange MP4-video bekijken. Daarom willen bedrijven van software en media levering alleen het gedeelte van een bestand dat wordt aangevraagd, leveren. Een efficiënte distributie van de aangevraagde gedeelten vermindert het uitgaande verkeer van de oorspronkelijke server. Efficiënte distributie vermindert ook het geheugen en de I/O-druk op de oorspronkelijke server. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optimaliseer voor het leveren van grote bestanden met Azure CDN van micro soft

**Azure CDN standaard van micro soft** -eind punten leveren grote bestanden zonder een limiet voor de bestands grootte. Aanvullende functies zijn standaard ingeschakeld om het leveren van grote bestanden sneller te laten verlopen.

### <a name="object-chunking"></a>Object Chunking 

**Azure CDN standaard van micro soft** maakt gebruik van een techniek die object Chunking wordt genoemd. Wanneer een groot bestand wordt aangevraagd, haalt het CDN kleinere delen van het bestand op van de oorsprong. Nadat de CDN-POP-server een volledige of byte-bereik bestands aanvraag ontvangt, vraagt de CDN-rand server het bestand aan bij de oorsprong in segmenten van 8 MB. 

Nadat het segment bij de CDN-rand arriveert, wordt het in de cache opgeslagen en direct aan de gebruiker geleverd. Het CDN haalt vervolgens het volgende segment parallel op. Deze prefetch zorgt ervoor dat de inhoud één segment vóór de gebruiker blijft, waardoor de latentie wordt verminderd. Dit proces wordt voortgezet totdat het hele bestand is gedownload (indien aangevraagd), alle byte bereiken beschikbaar zijn (indien aangevraagd) of de client beëindigt de verbinding. 

Zie [RFC 7233](https://tools.ietf.org/html/rfc7233)voor meer informatie over de aanvraag voor byte bereik.

De CDN slaat de segmenten op wanneer ze worden ontvangen. Het hele bestand hoeft niet in de cache te worden opgeslagen in de CDN-cache. Volgende aanvragen voor het bestand of de byte bereik worden aangeboden vanuit de CDN-cache. Als niet alle segmenten in de cache zijn opgeslagen in het CDN, wordt prefetch gebruikt om segmenten van de oorsprong aan te vragen. Deze optimalisatie is afhankelijk van de mogelijkheid van de oorspronkelijke server om aanvragen voor byte bereik te ondersteunen; Als de oorspronkelijke server geen aanvragen voor byte bereik ondersteunt, zullen aanvragen om gegevens te downloaden die groter zijn dan de grootte van 8mb, mislukken. 

### <a name="conditions-for-large-file-optimization"></a>Voor waarden voor de optimalisatie van grote bestanden
Er zijn geen limieten voor de maximale bestands grootte.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimaliseer voor het leveren van grote bestanden met Azure CDN van Verizon

**Azure CDN standaard van Verizon** en **Azure CDN Premium van Verizon** -eind punten leveren grote bestanden zonder een limiet voor de bestands grootte. Aanvullende functies zijn standaard ingeschakeld om het leveren van grote bestanden sneller te laten verlopen.

### <a name="complete-cache-fill"></a>Volledige cache opvulling

Met de standaard functie volledige cache opvulling kan het CDN een bestand in de cache ophalen wanneer een eerste aanvraag wordt afgebroken of verloren gaat. 

Volledige cache vulling is vooral nuttig voor grote activa. Normaal gesp roken downloaden gebruikers niet van het begin tot het einde. Ze gebruiken progressief downloaden. Met het standaard gedrag wordt de Edge-server gedwongen een achtergrond op te halen van de Asset van de oorspronkelijke server. Daarna bevindt de Asset zich in de lokale cache van de Edge-server. Nadat het volledige object zich in de cache bevindt, voldoet de Edge-server aanvragen voor byte bereik aan het CDN voor het object in de cache.

Het standaard gedrag kan worden uitgeschakeld via de regel engine van **Azure CDN Premium van Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Hot-Filing voor opvulling van peer-cache

De standaard functie voor het opvullen van de peer-cache maakt gebruik van een geavanceerd eigen algoritme. Er worden extra rand cache servers gebruikt op basis van de metrische gegevens over band breedte en statistische aanvragen om client aanvragen voor grote, uiterst populaire objecten te kunnen uitvoeren. Deze functie voor komt een situatie waarin grote aantallen extra aanvragen worden verzonden naar de oorspronkelijke server van een gebruiker. 

### <a name="conditions-for-large-file-optimization"></a>Voor waarden voor de optimalisatie van grote bestanden

De functies voor de optimalisatie van grote bestanden voor **Azure CDN standaard van Verizon** en **Azure CDN Premium van Verizon** zijn standaard ingeschakeld wanneer u het optimalisatie type algemene weblevering gebruikt. Er zijn geen limieten voor de maximale bestands grootte. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optimaliseren voor het leveren van grote bestanden met Azure CDN standaard van Akamai

**Azure CDN Standard van Akamai** profile-eind punten bieden een functie die grote bestanden efficiënt levert aan gebruikers op de hele wereld op schaal. De functie reduceert latentie omdat hiermee de belasting van de oorspronkelijke servers wordt gereduceerd.

Met de functie voor bestands optimalisatie van grote bestanden worden netwerk optimalisaties en configuraties ingeschakeld zodat deze sneller en veiliger kunnen worden geleverd. Met de algemene weblevering met **Azure CDN standaard van Akamai** -eind punten worden bestanden alleen in de cache van 1,8 GB opgeslagen en kunnen bestanden (niet in cache) worden getunneld tot 150 GB. Met de optimalisatie van grote bestanden worden bestanden Maxi maal 150 GB opgeslagen.

De optimalisatie van grote bestanden is effectief wanneer aan bepaalde voor waarden wordt voldaan. Voor waarden is te zien hoe de oorspronkelijke server werkt en wat de grootte en typen van de bestanden zijn die worden aangevraagd. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Een Akamai CDN-eind punt configureren om de levering van grote bestanden te optimaliseren

U kunt uw **Azure CDN-standaard configureren vanaf Akamai** -eind punt om de levering van grote bestanden via de Azure portal te optimaliseren. U kunt ook de REST-Api's of een van de client-Sdk's gebruiken om dit te doen. In de volgende stappen wordt het proces weer gegeven via de Azure Portal voor een **Azure CDN-standaard van** het profiel Akamai:

1. Als u een nieuw eind punt wilt toevoegen, selecteert u op de pagina Akamai **CDN-profiel** **eind punt**.

    ![Nieuw eind punt](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. Selecteer **grote bestanden downloaden** in de vervolg keuzelijst **geoptimaliseerd voor** .

    ![Optimalisatie van grote bestanden geselecteerd](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Nadat u het CDN-eind punt hebt gemaakt, worden de grote bestands optimalisaties toegepast voor alle bestanden die aan bepaalde criteria voldoen. Dit proces wordt beschreven in de volgende sectie.

### <a name="object-chunking"></a>Object Chunking 

Grote bestands optimalisatie met **Azure CDN standaard van Akamai** maakt gebruik van een techniek die object Chunking wordt genoemd. Wanneer een groot bestand wordt aangevraagd, haalt het CDN kleinere delen van het bestand op van de oorsprong. Nadat de CDN POP-server een volledige of byte-bereik bestands aanvraag ontvangt, wordt gecontroleerd of het bestands type voor deze optimalisatie wordt ondersteund. Ook wordt gecontroleerd of het bestands type voldoet aan de bestands grootte vereisten. Als de bestands grootte groter is dan 10 MB, vraagt de CDN-rand server het bestand aan bij de oorsprong in segmenten van 2 MB. 

Nadat het segment bij de CDN-rand arriveert, wordt het in de cache opgeslagen en direct aan de gebruiker geleverd. Het CDN haalt vervolgens het volgende segment parallel op. Deze prefetch zorgt ervoor dat de inhoud één segment vóór de gebruiker blijft, waardoor de latentie wordt verminderd. Dit proces wordt voortgezet totdat het hele bestand is gedownload (indien aangevraagd), alle byte bereiken beschikbaar zijn (indien aangevraagd) of de client beëindigt de verbinding. 

Zie [RFC 7233](https://tools.ietf.org/html/rfc7233)voor meer informatie over de aanvraag voor byte bereik.

De CDN slaat de segmenten op wanneer ze worden ontvangen. Het hele bestand hoeft niet in de cache te worden opgeslagen in de CDN-cache. Volgende aanvragen voor het bestand of de byte bereik worden aangeboden vanuit de CDN-cache. Als niet alle segmenten in de cache zijn opgeslagen in het CDN, wordt prefetch gebruikt om segmenten van de oorsprong aan te vragen. Deze optimalisatie is afhankelijk van de mogelijkheid van de oorspronkelijke server om aanvragen voor byte bereik te ondersteunen; Als de oorspronkelijke server geen aanvragen voor byte bereik ondersteunt, is deze optimalisatie niet effectief.

### <a name="caching"></a>Caching
Voor de optimalisatie van grote bestanden worden verschillende standaard waarden voor caching-verloop tijd gebruikt van de algemene Internet levering. Het onderscheidt zich van positieve caching en negatief opslaan in cache op basis van HTTP-antwoord codes. Als de oorspronkelijke server een verloop tijd opgeeft via een cache-control of Expires-header in het antwoord, wordt die waarde door het CDN gehonoreerd. Als de oorsprong niet opgeeft en het bestand overeenkomt met het type en de grootte voor dit optimalisatie type, gebruikt het CDN de standaard waarden voor de optimalisatie van grote bestanden. Als dat niet het geval is, gebruikt het CDN standaard instellingen voor de algemene weblevering.

| Caching  | Algemeen Web | Optimalisatie van grote bestanden 
--- | --- | --- 
Caching: positief <br> HTTP 200, 203, 300, <br> 301, 302 en 410 | 7 dagen |1 dag  
Caching: negatief <br> HTTP 204, 305, 404, <br> en 405 | Geen | 1 seconde 

### <a name="deal-with-origin-failure"></a>Omgaan met fout van oorsprong

De lengte van de Lees-time-out van de herkomst neemt toe van twee seconden voor een algemene Internet levering tot twee minuten voor het type van de grote bestands optimalisatie. Hiermee verg root u accounts voor grotere bestanden om een time-out voor tijdig gebruik te voor komen.

Wanneer er een time-out optreedt voor een verbinding, probeert het CDN een aantal keren opnieuw geprobeerd voordat de fout ' 504-Gateway-timeout ' naar de client wordt verzonden. 

### <a name="conditions-for-large-file-optimization"></a>Voor waarden voor de optimalisatie van grote bestanden

De volgende tabel geeft een lijst van criteria waaraan moet worden voldaan voor grote bestands optimalisatie:

Voorwaarde | Waarden 
--- | --- 
Ondersteunde bestands typen | 3g2, 3GP, ASF, AVI, bz2, dmg, exe, F4V, FLV, <br> gz, HDP, ISO, jxr, M4V, MKV, MOV, MP4, <br> MPEG, MPG, MTS, pak, Qt, RM, SWF, tar, <br> tgz, WDP, WEBM, webp, WMA, WMV, zip  
Minimale bestands grootte | 10 MB 
Maximale bestandsgrootte | 150 GB 
Kenmerken van de oorspronkelijke server | Moet aanvragen voor byte bereik ondersteunen 

## <a name="additional-considerations"></a>Aanvullende overwegingen

Houd rekening met de volgende aanvullende aspecten voor dit optimalisatie type:

- Het segment proces genereert extra aanvragen naar de oorspronkelijke server. Het totale volume van de gegevens die van de oorsprong worden geleverd, is echter veel kleiner. Het delen van resultaten zorgt voor betere cache kenmerken in het CDN.

- Het geheugen en de I/O-druk worden gereduceerd, omdat kleinere delen van het bestand worden geleverd.

- Voor segmenten die in de cache zijn opgeslagen in het CDN, zijn er geen extra aanvragen voor de oorsprong totdat de inhoud verloopt of uit de cache wordt verwijderd.

- Gebruikers kunnen bereik aanvragen naar het CDN maken, die worden behandeld als een normaal bestand. Optimalisatie is alleen van toepassing als het een geldig bestands type is en het byte bereik tussen 10 MB en 150 GB ligt. Als de aangevraagde gemiddelde bestands grootte kleiner is dan 10 MB, gebruikt u in plaats daarvan algemene Internet levering.

