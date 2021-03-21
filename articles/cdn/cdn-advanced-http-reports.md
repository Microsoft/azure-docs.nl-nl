---
title: Gebruiks statistieken analyseren met Azure CDN geavanceerde HTTP-rapporten | Microsoft Docs
description: Meer informatie over het maken van geavanceerde HTTP-rapporten in Microsoft Azure CDN. Deze rapporten bevatten gedetailleerde informatie over CDN-activiteiten.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e2ef1367cbbb1fb71159c4520c3d1481904c1769
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92779319"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Gebruiksstatistieken analyseren met geavanceerde HTTP-rapporten in Azure CDN
## <a name="overview"></a>Overzicht
In dit document wordt uitgebreide HTTP-rapportage in Microsoft Azure CDN uitgelegd. Deze rapporten bevatten gedetailleerde informatie over CDN-activiteiten.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Geavanceerde HTTP-rapporten openen
1. Klik op de Blade CDN-profiel op de knop **beheren** .
   
    ![De knop Blade van het CDN-profiel beheren](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    De CDN-beheer portal wordt geopend.
2. Beweeg de muis aanwijzer over het tabblad **Analytics** en Beweeg vervolgens de muis aanwijzer over de **Geavanceerde http-rapporten** flyout.  Klik op een **groot http-platform**.
   
    ![CDN-beheer Portal-menu Geavanceerde rapporten](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Er worden rapport opties weer gegeven.

## <a name="geography-reports-map-based"></a>Geografische rapporten (op basis van een kaart)
Er zijn vijf rapporten die gebruikmaken van een kaart om de regio's aan te geven van waaruit de inhoud wordt aangevraagd. Deze rapporten zijn wereld kaart, Verenigde Staten kaart, Canada kaart, Europe-kaart en Azië en Stille Oceaan kaart.

Elk rapport op basis van een kaart rangschikt geografische entiteiten (bijvoorbeeld landen/regio's, een kaart die u helpt bij het visualiseren van de locaties van waaruit uw inhoud wordt aangevraagd. Het kan dit doen door elke regio in kleur te coderen op basis van de hoeveelheid vraag die in die regio is opgetreden. Lichtere gearceerde regio's geven een lagere vraag voor uw inhoud aan, terwijl donkere regio's een hoger niveau van de vraag voor uw inhoud aangeven.

Gedetailleerde verkeer-en bandbreedte gegevens voor elke regio worden direct onder de kaart verstrekt. Zo kunt u het totale aantal treffers, het percentage treffers, de totale hoeveelheid overgedragen gegevens (in gigabytes), en het percentage gegevens dat voor elke regio wordt overgedragen, weer geven. Een beschrijving voor elk van deze metrische gegevens weer geven. Ten slotte, wanneer u de muis aanwijzer boven een regio (land/regio, staat of provincie) houdt, worden de naam en het percentage treffers in de regio weer gegeven als knop info.

Hieronder vindt u een korte beschrijving voor elk type geografie rapport op basis van een kaart.

| Rapportnaam | Beschrijving |
| --- | --- |
| Wereld kaart |Met dit rapport kunt u de wereld wijde vraag naar uw CDN-inhoud weer geven. Elk land/elke regio heeft een kleur code op de wereld kaart om het percentage treffers op te geven dat afkomstig is van die regio. |
| Verenigde Staten kaart |Met dit rapport kunt u de vraag naar uw CDN-inhoud in het Verenigde Staten weer geven. Elke status wordt in kleur code op deze kaart vermeld om het percentage treffers aan te geven dat afkomstig is van die regio. |
| Canada-kaart |Met dit rapport kunt u de vraag naar uw CDN-inhoud in Canada weer geven. Elke provincie heeft een kleur code op deze kaart om het percentage treffers op te geven dat afkomstig is van deze regio. |
| Europa-kaart |Met dit rapport kunt u de vraag naar uw CDN-inhoud in Europa bekijken. Elk land/elke regio heeft een kleur code op deze kaart om het percentage treffers op te geven dat afkomstig is van deze regio. |
| Azië en Stille Oceaan kaart |Met dit rapport kunt u de vraag naar uw CDN-inhoud in Azië bekijken. Elk land/elke regio heeft een kleur code op deze kaart om het percentage treffers op te geven dat afkomstig is van deze regio. |

## <a name="geography-reports-bar-charts"></a>Geografische rapporten (staaf diagrammen)
Er zijn twee aanvullende rapporten waarmee statistische gegevens worden verstrekt op basis van geografie, de belangrijkste steden en de belangrijkste landen. Deze rapporteert respectievelijk steden en landen/regio's op basis van het aantal treffers dat afkomstig is uit deze landen/regio's. Bij het genereren van dit type rapport duidt een staaf diagram de tien steden of landen/regio's aan die inhoud hebben aangevraagd via een specifiek platform. Met deze staaf grafiek kunt u de regio's die het hoogste aantal aanvragen voor uw inhoud genereren, snel beoordelen.

De linkerkant van de grafiek (y-as) geeft aan hoeveel treffers er zijn opgetreden in de opgegeven regio. Direct onder de grafiek (x-as) ziet u een label voor elk van de Top 10 regio's.

### <a name="using-the-bar-charts"></a>De staaf diagrammen gebruiken
* Als u de muis aanwijzer over een staaf houdt, worden de naam en het totale aantal treffers in de regio weer gegeven als knop info.
* De knop Info voor het bovenste steden rapport identificeert een plaats op basis van de naam, de staat/provincie en de afkorting van het land/de regio.
* Als de plaats of regio (bijvoorbeeld staat/provincie) van waaruit een aanvraag afkomstig is, niet kan worden vastgesteld, wordt aangegeven dat ze onbekend zijn. Als het land/de regio onbekend is, worden er twee vraag tekens (bijvoorbeeld??) weer gegeven.
* Een rapport kan metrische gegevens bevatten voor "Europa" of de regio "Azië en Stille Oceaan". Deze items zijn niet bedoeld om statistische gegevens te verstrekken over alle IP-adressen in die regio's. In plaats daarvan zijn ze alleen van toepassing op aanvragen die afkomstig zijn van IP-adressen die zijn verdeeld over Europa of Azië/Pacific in plaats van naar een specifieke plaats of land/regio.

De gegevens die zijn gebruikt voor het genereren van het staaf diagram kunnen hieronder worden weer gegeven. Daar ziet u het totale aantal treffers, het percentage treffers, de hoeveelheid overgedragen gegevens (in gigabytes) en het percentage gegevens dat wordt overgedragen voor de meeste 250 regio's. Een beschrijving voor elk van deze metrische gegevens weer geven.

Hieronder vindt u een korte beschrijving van beide typen rapporten.

| Rapportnaam | Beschrijving |
| --- | --- |
| Populairste steden |In dit rapport worden steden gerangschikt op basis van het aantal treffers dat afkomstig is uit die regio. |
| Belangrijkste landen |In dit rapport worden landen/regio's gerangschikt op basis van het aantal treffers dat afkomstig is uit dat land/deze regio. |

## <a name="daily-summary"></a>Dagelijkse samen vatting
In het rapport dagelijkse samen vatting kunt u het totale aantal treffers en gegevens die dagelijks over een bepaald platform worden overgebracht, weer geven. Deze informatie kan worden gebruikt om snel CDN-activiteiten patronen te onderscheiden. Met dit rapport kunt u bijvoorbeeld vaststellen welke dagen hoger of lager zijn dan verwacht verkeer.

Bij het genereren van dit type rapport biedt een staaf diagram een visuele indicatie van de hoeveelheid platformspecifieke vraag die dagelijks is opgetreden gedurende de periode die door het rapport wordt gedekt. Dit wordt gedaan door een balk voor elke dag in het rapport weer te geven. Als u bijvoorbeeld de periode met de naam ' afgelopen week ' selecteert, wordt er een staaf diagram met zeven balken gegenereerd. Op elke balk wordt het totale aantal treffers aangegeven dat op die dag is opgetreden.

De linkerkant van de grafiek (y-as) geeft aan hoeveel treffers er zijn opgetreden op de opgegeven datum. Direct onder de grafiek (x-as) ziet u een label dat de datum (notatie: JJJJ-MM-DD) aangeeft voor elke dag die in het rapport is opgenomen.

> [!TIP]
> Als u de muis aanwijzer boven een balk houdt, wordt het totale aantal treffers dat op die datum plaatsvond, weer gegeven als knop info.
> 
> 

De gegevens die zijn gebruikt voor het genereren van het staaf diagram kunnen hieronder worden weer gegeven. Hier vindt u het totale aantal treffers en de hoeveelheid overgedragen gegevens (in gigabytes) voor elke dag waarop het rapport betrekking heeft.

## <a name="by-hour"></a>Per uur
In het rapport per uur kunt u het totale aantal treffers en gegevens die per uur over een bepaald platform worden overgebracht, bekijken. Deze informatie kan worden gebruikt om snel CDN-activiteiten patronen te onderscheiden. Dit rapport helpt u bijvoorbeeld bij het detecteren van de tijds perioden op de dag die hoger of lager zijn dan het verwachte verkeer.

Bij het genereren van dit type rapport biedt een staaf diagram een visuele indicatie van de hoeveelheid platformspecifieke vraag die per uur is onderhevig aan de periode die door het rapport wordt gedekt. Dit doet u door een balk weer te geven voor elk uur waarop het rapport betrekking heeft. Als u bijvoorbeeld een periode van 24 uur selecteert, wordt er een staaf diagram met twintig staven gegenereerd. Op elke balk wordt het totale aantal treffers aangegeven dat is opgetreden tijdens dat uur.

De linkerkant van de grafiek (y-as) geeft aan hoeveel treffers er zijn opgetreden op het opgegeven uur. Direct onder de grafiek (x-as) ziet u een label dat de datum/tijd aangeeft (notatie: JJJJ-MM-DD uu: mm) voor elk uur dat in het rapport is opgenomen. Tijd wordt gerapporteerd in 24-uurs notatie en wordt opgegeven met behulp van de UTC/GMT-tijd zone.

> [!TIP]
> Als u de muis aanwijzer over een staaf houdt, wordt het totale aantal treffers in dat uur weer gegeven als knop info.
> 
> 

De gegevens die zijn gebruikt voor het genereren van het staaf diagram kunnen hieronder worden weer gegeven. Hier vindt u het totale aantal treffers en de hoeveelheid overgedragen gegevens (in gigabytes) voor elk uur waarop het rapport betrekking heeft.

## <a name="by-file"></a>Op bestand
In het rapport per bestand kunt u de hoeveelheid vraag en het verkeer dat is gemaakt over een bepaald platform weer geven voor de meest aangevraagde assets. Bij het genereren van dit type rapport wordt een staaf diagram gegenereerd voor de Top 10 van de aangevraagde assets gedurende de opgegeven periode.

> [!NOTE]
> Voor de doel einden van dit rapport worden Edge CNAME-Url's geconverteerd naar hun equivalente CDN-Url's. Hiermee kan een nauw keurigheid worden berekend voor het totale aantal treffers dat is gekoppeld aan een Asset, ongeacht de CDN-of Edge CNAME-URL die wordt gebruikt om deze aan te vragen.
> 
> 

De linkerkant van de grafiek (y-as) geeft het aantal aanvragen voor elk activum over de opgegeven periode aan. Direct onder de grafiek (x-as) vindt u een label dat de bestands naam voor elk van de eerste 10 aangevraagde assets aangeeft.

De gegevens die zijn gebruikt voor het genereren van het staaf diagram kunnen hieronder worden weer gegeven. Hier vindt u de volgende informatie voor elk van de meest aangevraagde assets van 250: relatief pad, het totale aantal treffers, het percentage treffers, de hoeveelheid overgedragen gegevens (in gigabytes) en het percentage overgedragen gegevens.

## <a name="by-file-detail"></a>Op bestands Details
In het rapport per bestands gegevens kunt u de hoeveelheid vraag en het verkeer dat is gemaakt over een bepaald platform voor een specifiek activum weer geven. Bovenaan dit rapport worden de details van het bestand voor de optie. Met deze optie geeft u een lijst weer met de meest aangevraagde assets op het geselecteerde platform. Als u een rapport op basis van bestands gegevens wilt genereren, moet u het gewenste activum selecteren in de optie Details van bestand voor. Daarna geeft een staaf diagram de hoeveelheid dagelijkse vraag aan die gedurende de opgegeven periode is gegenereerd.

De linkerkant van de grafiek (y-as) geeft het totale aantal aanvragen aan dat een Asset op een bepaalde dag heeft ondervonden. Direct onder de grafiek (x-as) vindt u een label dat de datum aangeeft (notatie: JJJJ-MM-DD) waarvoor de CDN-vraag naar het activum is gerapporteerd.

De gegevens die zijn gebruikt voor het genereren van het staaf diagram kunnen hieronder worden weer gegeven. Hier vindt u het totale aantal treffers en de hoeveelheid overgedragen gegevens (in gigabytes) voor elke dag waarop het rapport betrekking heeft.

## <a name="by-file-type"></a>Op bestands type
In het rapport per bestands type kunt u de hoeveelheid vraag en het verkeer dat wordt gemaakt door het bestands type bekijken. Bij het genereren van dit type rapport geeft een ring diagram het percentage treffers aan dat wordt gegenereerd door de tien tien bestands typen.

> [!TIP]
> Als u de muis aanwijzer boven een segment in de ring grafiek houdt, wordt het type Internet Media van dat bestands type weer gegeven als knop info.
> 
> 

De gegevens die zijn gebruikt om de ring grafiek te genereren, kunnen hieronder worden weer gegeven. Hier vindt u de bestands extensie/het internet media type, het totale aantal treffers, het percentage treffers, de hoeveelheid overgedragen gegevens (in gigabytes) en het percentage gegevens dat wordt overgedragen voor elk van de belangrijkste bestands typen van 250.

## <a name="by-directory"></a>Op map
In het rapport per Directory kunt u de hoeveelheid vraag en het verkeer dat is gemaakt via een bepaald platform weer geven voor inhoud van een specifieke map. Bij het genereren van dit type rapport wordt in een staaf diagram het totale aantal treffers aangegeven dat is gegenereerd door inhoud in de 10 tien directory's.

### <a name="using-the-bar-chart"></a>Het staaf diagram gebruiken
* Beweeg de muis aanwijzer over een balk om het relatieve pad naar de bijbehorende map weer te geven.
* Inhoud die is opgeslagen in een submap van een directory, telt niet bij het berekenen van de vraag door de Directory. Deze berekening is alleen afhankelijk van het aantal aanvragen dat is gegenereerd voor inhoud die is opgeslagen in de map werkelijk.
* Voor de doel einden van dit rapport worden Edge CNAME-Url's geconverteerd naar hun equivalente CDN-Url's. Dit maakt een nauw keurig beeld van alle statistische gegevens die zijn gekoppeld aan een Asset, ongeacht de CDN-of Edge CNAME-URL die wordt gebruikt om deze aan te vragen.

De linkerkant van de grafiek (y-as) geeft het totale aantal aanvragen voor de inhoud die is opgeslagen in uw 10 tien directory's. Elke balk in de grafiek vertegenwoordigt een map. Gebruik het kleur coderings schema om een balk te vergelijken met een map die wordt vermeld in de sectie bovenste 250 volledige Directory's.

De gegevens die zijn gebruikt voor het genereren van het staaf diagram kunnen hieronder worden weer gegeven. Hier vindt u de volgende informatie voor elk van de populairste 250 directory's: relatief pad, het totale aantal treffers, het percentage treffers, de hoeveelheid overgedragen gegevens (in gigabytes) en het percentage overgedragen gegevens.

## <a name="by-browser"></a>Op browser
In het rapport per browser kunt u zien welke browsers zijn gebruikt om inhoud aan te vragen. Bij het genereren van dit type rapport geeft een cirkel diagram het percentage van aanvragen aan dat door de Top 10 van de browsers wordt verwerkt.

### <a name="using-the-pie-chart"></a>Het cirkel diagram gebruiken
* Beweeg de muis aanwijzer over een segment in het cirkel diagram om de naam en versie van een browser weer te geven.
* Voor het doel van dit rapport wordt elke combi natie van een unieke browser/versie beschouwd als een andere browser.
* Het segment met de naam ' Overig ' geeft het percentage aanvragen dat door alle andere browsers en versies wordt verwerkt.

De gegevens die zijn gebruikt voor het genereren van het cirkel diagram, kunnen hieronder worden weer gegeven. Hier vindt u het type/versie nummer van de browser, het totale aantal treffers en het percentage treffers voor elk van de Top 250-browsers.

## <a name="by-referrer"></a>Op verwijzende site
In het rapport per verwijzende site kunt u de belangrijkste verwijzingen naar inhoud op het geselecteerde platform weer geven. Een verwijzende site geeft de hostnaam aan waaruit een aanvraag is gegenereerd. Bij het genereren van dit type rapport geeft een staaf diagram de hoeveelheid vraag aan (dat wil zeggen treffers) die is gegenereerd door de Top 10 van verwijzende personen.

Aan de linkerkant van de grafiek (y-as) wordt het totale aantal aanvragen aangegeven dat voor elke verwijzing naar een activum is opgetreden. Elke balk in de grafiek vertegenwoordigt een verwijzings object. Gebruik het kleuren code schema om een balk te vergelijken met een verwijzing naar de lijst boven 250 van de verwijzende site.

De gegevens die zijn gebruikt voor het genereren van het staaf diagram kunnen hieronder worden weer gegeven. Daar vindt u de URL, het totale aantal treffers en het percentage treffers dat is gegenereerd op basis van elk van de Top 250-verwijzende personen.

## <a name="by-download"></a>Door te downloaden
In het rapport per download kunt u Download patronen analyseren voor de meest aangevraagde inhoud. De bovenkant van het rapport bevat een staaf diagram dat gedownloade down loads vergelijkt met voltooide down loads voor de Top 10 van aangevraagde assets. Elke balk is een kleur code op basis van het feit of het gaat om een down load (blauw) of een voltooide down load (groen).

> [!NOTE]
> Voor de doel einden van dit rapport worden Edge CNAME-Url's geconverteerd naar hun equivalente CDN-Url's. Dit maakt een nauw keurig beeld van alle statistische gegevens die zijn gekoppeld aan een Asset, ongeacht de CDN-of Edge CNAME-URL die wordt gebruikt om deze aan te vragen.
> 
> 

De linkerkant van de grafiek (y-as) geeft de bestands naam aan voor elk van de Top 10 aangevraagde assets. Direct onder de grafiek (x-as) worden labels gevonden die het totale aantal pogingen/voltooide down loads aangeven.

Direct onder het staaf diagram wordt de volgende informatie weer gegeven voor de Top 250 aangevraagde assets: relatief pad (inclusief bestands naam), het aantal keren dat het is gedownload voor voltooiing, het aantal keren dat het is aangevraagd en het percentage aanvragen dat heeft geresulteerd in een volledige down load.

> [!TIP]
> Ons CDN wordt niet op de hoogte gesteld door een HTTP-client (bijvoorbeeld browser) wanneer een activum volledig is gedownload. Daarom moeten we berekenen of een activum volledig is gedownload volgens status codes en aanvragen voor byte bereik. Het eerste wat we tijdens het maken van deze berekening zoeken, is of de aanvraag resulteert in een 200 OK-status code. Als dit het geval is, kijken we naar aanvragen voor byte bereik om ervoor te zorgen dat ze het hele activum beslaan. Ten slotte vergelijken we de hoeveelheid gegevens die worden overgedragen naar de grootte van de aangevraagde activa. Als de overgedragen gegevens gelijk zijn aan of groter zijn dan de bestands grootte en de byte bereik aanvragen geschikt zijn voor die Asset, wordt de treffer als een volledige down load beschouwd.
> 
> Als gevolg van de interpretatieve aard van dit rapport, moet u rekening houden met de volgende punten die de consistentie en nauw keurigheid van dit rapport kunnen wijzigen.
> 
> * Verkeers patronen kunnen niet nauw keurig worden voor speld wanneer gebruikers agents zich op verschillende manieren gedragen. Dit kan leiden tot voltooide download resultaten die groter zijn dan 100%.
> * Activa die gebruikmaken van HTTP progressief downloaden, worden mogelijk niet nauw keurig weer gegeven in dit rapport. Dit wordt veroorzaakt door gebruikers die naar verschillende posities in een video willen zoeken.
> 
> 

## <a name="by-404-errors"></a>Door 404 fouten
In het rapport per 404-fouten kunt u het type inhoud identificeren dat het grootste aantal van 404 niet-gevonden status codes genereert. De bovenkant van het rapport bevat een staaf diagram voor de bovenste 10 assets waarvoor een status code van 404 niet gevonden is geretourneerd. Dit staaf diagram vergelijkt het totaal aantal aanvragen met aanvragen die hebben geleid tot een status code van 404 niet gevonden voor deze activa. Elke balk heeft een kleur code. Er wordt een gele balk gebruikt om aan te geven dat de aanvraag heeft geleid tot een niet-gevonden status code van 404. Er wordt een rode balk gebruikt om het totale aantal aanvragen voor de Asset aan te geven.

> [!NOTE]
> Voor de doel einden van dit rapport moet u rekening houden met het volgende:
> 
> * Een treffer vertegenwoordigt een wille keurige aanvraag voor een Asset, ongeacht de status code.
> * Edge CNAME Url's worden geconverteerd naar hun equivalente CDN-Url's. Dit maakt een nauw keurig beeld van alle statistische gegevens die zijn gekoppeld aan een Asset, ongeacht de CDN-of Edge CNAME-URL die wordt gebruikt om deze aan te vragen.
> 
> 

De linkerkant van de grafiek (y-as) geeft de bestands naam aan voor elk van de Top 10 van aangevraagde activa die heeft geresulteerd in een status code van 404 niet gevonden. Direct onder de grafiek (x-as) vindt u labels die het totale aantal aanvragen aangeven en het aantal aanvragen dat heeft geleid tot een status code van 404 niet gevonden.

Direct onder het staaf diagram wordt de volgende informatie weer gegeven voor de belangrijkste aangevraagde assets van 250: relatief pad (inclusief bestands naam), het aantal aanvragen dat heeft geresulteerd in een 404-status code, het totale aantal keren dat de Asset is aangevraagd en het percentage aanvragen dat heeft geresulteerd in een 404 niet gevonden status code.

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure CDN](cdn-overview.md)
* [Realtime-statistieken in Microsoft Azure CDN](cdn-real-time-stats.md)
* [Standaard HTTP-gedrag negeren met de regel engine](./cdn-verizon-premium-rules-engine.md)
* [Prestaties van de rand analyseren](cdn-edge-performance.md)