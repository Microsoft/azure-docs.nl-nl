---
title: Dynamische site versnelling via Azure CDN
description: Azure CDN ondersteunt de optimalisatie van dynamische site versnelling (DSA) voor bestanden met dynamische inhoud.
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
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 5387fdc224cd77ee5273767df5033a51dc27608c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008358"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Dynamische site versnelling via Azure CDN

Met de explosie van sociale media, elektronische handel en het Hyper-gepersonaliseerde web wordt een snel stijgend percentage van de inhoud die aan eind gebruikers wordt aangeboden in realtime gegenereerd. Gebruikers verwachten een snelle, betrouw bare en gepersonaliseerde webervaring, onafhankelijk van hun browser, locatie, apparaat of netwerk. De meeste innovaties die deze ervaringen maken, zorgen er echter ook voor dat het downloaden van pagina's traag wordt en de kwaliteit van de consumenten ervaring op risico wordt gebracht. 

De standaard functionaliteit voor Content Delivery Network (CDN) omvat de mogelijkheid om bestanden dichter bij eind gebruikers op te slaan om het leveren van statische bestanden te versnellen. Met dynamische webtoepassingen is het in de cache plaatsen van inhoud in Edge-locaties echter niet mogelijk omdat de server de inhoud genereert als reactie op het gedrag van de gebruiker. Het versnellen van de levering van dergelijke inhoud is complexer dan de traditionele breedte van de rand, en vereist een end-to-end oplossing die elk element van het hele gegevenspad afstemt van het begin tot de levering. Met Azure CDN optimalisatie van dynamische site versnelling (DSA) kunnen de prestaties van webpagina's met dynamische inhoud meetbaar worden verbeterd.

**Azure CDN van Akamai** en **Azure CDN van Verizon** bieden DSA Optimization via het menu **Optimized for** tijdens het maken van een eind punt. Dynamische site versnelling van micro soft wordt aangeboden via de [Azure front-deur service](../frontdoor/front-door-overview.md).

> [!Important]
> Voor **Azure CDN van Akamai** -profielen, mag u de optimalisatie van een CDN-eind punt wijzigen nadat deze is gemaakt.
>   
> Voor profielen van **Azure CDN van Verizon** geldt dat u de optimalisatie van een CDN-eindpunt niet kunt wijzigen nadat deze is gemaakt.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Configuratie van het CDN-eind punt om de levering van dynamische bestanden te versnellen

Als u een CDN-eind punt wilt configureren om de levering van dynamische bestanden te optimaliseren, kunt u de Azure Portal, de REST-Api's of een van de client-Sdk's gebruiken om op een programmatische manier hetzelfde te doen. 

**Een CDN-eind punt voor DSA-optimalisatie configureren met behulp van de Azure Portal:**

1. Selecteer op de pagina **CDN-profiel** de optie **eind punt**.

   ![Een nieuw CDN-eind punt toevoegen](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Het deelvenster **Een eindpunt toevoegen** wordt weergegeven.

2. Selecteer onder **geoptimaliseerd voor** **dynamische site versnelling**.

    ![Een nieuw CDN-eind punt maken met DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Voer voor **probe Path** een geldig pad naar een bestand in.

    Probe is een functie die specifiek is voor DSA en een geldig pad is vereist voor het maken van. DSA gebruikt een klein *probe Path* -bestand dat op de oorspronkelijke server is geplaatst om de configuratie van netwerk routering voor het CDN te optimaliseren. Voor het pad naar het probe-bestand kunt u het voorbeeld bestand downloaden en uploaden naar uw site, of een bestaand activum op uw oorsprong gebruiken dat ongeveer 10 KB groot is.

4. Voer de andere vereiste eindpunt opties in (Zie [een nieuw CDN-eind punt maken](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)voor meer informatie) en selecteer vervolgens **toevoegen**.

   Nadat het CDN-eind punt is gemaakt, worden de DSA-optimalisaties toegepast voor alle bestanden die aan bepaalde criteria voldoen. 


**Een bestaand eind punt voor DSA configureren (alleen Azure CDN van Akamai-profielen):**

1. Selecteer op de pagina **CDN-profiel** het eind punt dat u wilt wijzigen.

2. Selecteer **optimalisatie** in het linkerdeel venster. 

   De pagina **optimalisatie** wordt weer gegeven.

3. Selecteer onder **geoptimaliseerd voor** **dynamische site versnelling** en selecteer vervolgens **Opslaan**.

> [!Note]
> DSA brengt extra kosten met zich mee. Zie [Content Delivery Network prijzen](https://azure.microsoft.com/pricing/details/cdn/)voor meer informatie.

## <a name="dsa-optimization-using-azure-cdn"></a>DSA-optimalisatie met Azure CDN

Dynamische site versnelling op Azure CDN versnelt de levering van dynamische activa door gebruik te maken van de volgende technieken:

-   [Route optimalisatie](#route-optimization)
-   [TCP-optimalisaties](#tcp-optimizations)
-   [Prefetch van object (alleen Azure CDN van Akamai)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Compressie van adaptieve afbeeldingen (alleen Azure CDN van Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Route optimalisatie

Route optimalisatie is belang rijk omdat Internet een dynamische locatie is, waarbij verkeer en tijdelijke storingen de netwerk topologie voortdurend wijzigen. De Border Gateway Protocol (BGP) is het routerings Protocol van het Internet, maar er zijn mogelijk snellere routes via de beschik bare PoP-servers (Point of Presence). 

Met route optimalisatie wordt het meest optimale pad naar de oorsprong gekozen, zodat een site voortdurend toegankelijk is en dynamische inhoud aan eind gebruikers wordt geleverd via de snelste en meest betrouw bare route die mogelijk is. 

Het Akamai-netwerk gebruikt technieken voor het verzamelen van realtimegegevens en het vergelijken van verschillende paden via verschillende knoop punten in de Akamai-server, evenals de standaard BGP-route over het open Internet om de snelste route tussen de oorsprong en de CDN-rand te bepalen. Deze technieken vermijden internet congestie punten en lange routes. 

Op dezelfde manier gebruikt het Verizon-netwerk een combi natie van geauthenticeerde DNS-ondersteuning met hoge capaciteit en status controles om de beste gateways te bepalen om gegevens van de client naar de oorsprong te routeren.
 
Als gevolg hiervan worden volledige dynamische en transactionele inhoud sneller en betrouwbaarder geleverd aan eind gebruikers, zelfs als deze niet in de cache zijn opgeslagen. 

### <a name="tcp-optimizations"></a>TCP-optimalisaties

Transmission Control Protocol (TCP) is de standaard van de Internet protocol suite die wordt gebruikt voor het leveren van informatie tussen toepassingen op een IP-netwerk.  Standaard zijn diverse back-en-out-aanvragen vereist voor het instellen van een TCP-verbinding, evenals beperkingen om netwerk congestie te voor komen. Dit leidt tot inefficiëntie op schaal. **Azure CDN van Akamai** dit probleem te verhelpen door de optimalisatie op drie gebieden: 

 - [TCP langzaam starten elimineren](#eliminating-tcp-slow-start)
 - [Permanente verbindingen gebruiken](#leveraging-persistent-connections)
 - [TCP-pakket parameters afstemmen](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>TCP langzaam starten elimineren

*Langzaam starten* van TCP is een algoritme van het TCP-protocol dat overbelasting van het netwerk voor komt door de hoeveelheid gegevens die via het netwerk worden verzonden, te beperken. De functie wordt gestart met kleine congestie venster grootten tussen de afzender en de ontvanger totdat het maximum wordt bereikt of er pakket verlies wordt gedetecteerd.

 Beide **Azure CDN van Akamai** en **Azure CDN van Verizon** -profielen elimineren TCP langzaam starten met de volgende drie stappen:

1. Status-en bandbreedte bewaking wordt gebruikt om de band breedte van verbindingen tussen PoP-servers met Edge te meten.
    
2. Metrische PoP-servers worden gedeeld, zodat elke server op de hoogte is van de netwerk omstandigheden en de server status van de andere Pop's rond ze.  
    
3. De CDN-rand servers maken veronderstellingen over bepaalde overdrachts parameters, zoals wat de optimale venster grootte moet zijn wanneer u communiceert met andere CDN edge-servers in de buurt. Deze stap betekent dat de initiële insluitings venster grootte kan worden verhoogd als de status van de verbinding tussen de CDN-rand servers geschikt is voor hogere pakket gegevens overdracht.  

#### <a name="leveraging-persistent-connections"></a>Permanente verbindingen gebruiken

Met behulp van een CDN hoeven minder unieke computers rechtstreeks verbinding te maken met uw bron server, vergeleken met gebruikers die rechtstreeks verbinding maken met uw oorsprong. Azure CDN ook gebruikers aanvragen groeperen om minder verbindingen met de oorsprong tot stand te brengen.

Zoals eerder vermeld, zijn er verschillende Handshake-aanvragen vereist om een TCP-verbinding tot stand te brengen. Permanente verbindingen, die worden geïmplementeerd door de `Keep-Alive` http-header, waarbij bestaande TCP-verbindingen voor meerdere HTTP-aanvragen opnieuw worden gebruikt voor het opslaan van retour tijden en het versnellen van de levering. 

**Azure CDN van Verizon** verzendt ook periodieke Keep-Alive-pakketten via de TCP-verbinding om te voor komen dat een open verbinding wordt gesloten.

#### <a name="tuning-tcp-packet-parameters"></a>TCP-pakket parameters afstemmen

**Azure CDN van Akamai** stemt de para meters voor server-naar-server-verbindingen af en vermindert de hoeveelheid van de lange termijn rondingen die nodig zijn om inhoud op de site op te halen met behulp van de volgende technieken:

- Het eerste congestie venster verhogen zodat er meer pakketten kunnen worden verzonden zonder dat er wordt gewacht op een bevestiging.
- Het verminderen van de eerste time-out voor opnieuw verzenden, zodat er een verlies wordt gedetecteerd en de hertransmissie wordt sneller uitgevoerd.
- De minimale en maximale time-out voor opnieuw verzenden verlagen om de wacht tijd te verminderen voordat er wordt aangenomen dat de pakketten verloren zijn gegaan.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Prefetch van object (alleen Azure CDN van Akamai)

De meeste websites bestaan uit een HTML-pagina, die verwijst naar verschillende andere resources, zoals afbeeldingen en scripts. Normaal gesp roken, wanneer een client een webpagina aanvraagt, downloadt en parseert de browser eerst het HTML-object en worden er vervolgens extra aanvragen voor gekoppelde assets gemaakt die nodig zijn om de pagina volledig te laden. 

*Prefetch* is een techniek voor het ophalen van afbeeldingen en scripts die zijn Inge sloten in de HTML-pagina terwijl de HTML-code naar de browser wordt verzonden, en voordat de browser deze object aanvragen maakt. 

Als de optie prefetch is ingeschakeld op het moment dat de CDN de HTML-basis pagina naar de browser van de client verzendt, parseert het CDN het HTML-bestand en worden er aanvullende aanvragen voor gekoppelde resources gemaakt en opgeslagen in de cache. Wanneer de client de aanvragen voor de gekoppelde assets maakt, heeft de CDN Edge-Server al de aangevraagde objecten en kan deze direct worden gebruikt zonder een retour verzending naar de oorsprong. Deze optimalisatie voordelen zowel cachebaar als niet-cachebaar inhoud.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Compressie van adaptieve afbeeldingen (alleen Azure CDN van Akamai)

Sommige apparaten, met name mobiel, hebben een tragere netwerk snelheid van tijd tot tijd. In deze scenario's is het gunstiger voor de gebruiker om sneller kleinere afbeeldingen op hun webpagina te ontvangen in plaats van een lange tijd te wachten op installatie kopieën met volledige resolutie.

Met deze functie wordt automatisch de netwerk kwaliteit gecontroleerd en worden standaard JPEG-compressie methoden gebruikt wanneer de netwerk snelheden langzamer zijn om de leverings tijd te verbeteren.

Adaptieve afbeeldings compressie | Bestands extensies  
--- | ---  
JPEG-compressie | . jpg,. JPEG,. JPE,. jig,. jgig,. JGI

## <a name="caching"></a>Caching

Met DSA is caching standaard uitgeschakeld in het CDN, zelfs wanneer de oorsprong `Cache-Control` of `Expires` headers in de reactie worden opgenomen. DSA wordt doorgaans gebruikt voor dynamische activa die niet in de cache moeten worden opgeslagen omdat deze uniek zijn voor elke client. Dit gedrag kan worden verbroken met caching.

Als u een website hebt met een combi natie van statische en dynamische assets, kunt u het beste een hybride benadering maken om de beste prestaties te verkrijgen. 

Voor **Azure CDN Standard van Verizon** en **Azure CDN Standard van Akamai** -profielen, kunt u caching inschakelen voor specifieke DSA-eind punten met behulp van de [cache regels](cdn-caching-rules.md).

Voor toegang tot de regels voor opslaan in cache:

1. Selecteer op de pagina **CDN-profiel** onder instellingen de optie **cache regels**.  
    
    ![Knop Regels voor CDN-caching](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    De pagina **regels voor opslaan in cache** wordt geopend.

2. Maak een algemene of aangepaste regel voor opslaan in cache om caching in te scha kelen voor uw DSA-eind punt. 

Voor **Azure CDN Premium van Verizon** -profielen, kunt u caching voor specifieke DSA-eind punten inschakelen met behulp van de [regel engine](./cdn-verizon-premium-rules-engine.md). Alle regels die worden gemaakt, zijn alleen van invloed op de eind punten van uw profiel die zijn geoptimaliseerd voor DSA. 

Voor toegang tot de regel Engine:
    
1. Selecteer op de pagina **CDN-profiel** de optie **beheren**.  
    
    ![Beheer knop voor CDN-profiel](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    De CDN-beheer portal wordt geopend.

2. Selecteer **ADN** in de CDN-beheer Portal en selecteer vervolgens **regel engine**. 

    ![Regel engine voor DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



U kunt ook twee CDN-eind punten gebruiken: één eind punt dat is geoptimaliseerd met DSA voor het leveren van dynamische assets en een ander eind punt dat is geoptimaliseerd voor een statisch optimalisatie type, zoals algemene weblevering, voor leverings cachebare assets. Wijzig de Url's van uw webpagina's zodat u deze rechtstreeks kunt koppelen aan de Asset op het CDN-eind punt dat u wilt gebruiken. 

Bijvoorbeeld: `mydynamic.azureedge.net/index.html` is een dynamische pagina en wordt geladen vanuit het DSA-eind punt.  De HTML-pagina verwijst naar meerdere statische assets, zoals Java script-bibliotheken of installatie kopieën die worden geladen vanuit het statische CDN-eind punt, zoals `mystatic.azureedge.net/banner.jpg` en `mystatic.azureedge.net/scripts.js` .