---
title: Prestaties van Edge-knoop punten in Azure CDN analyseren | Microsoft Docs
description: Analyseer de prestaties van Edge-knoop punten in Microsoft Azure CDN. Edge Performance Analytics biedt gedetailleerd gegevens verkeer en bandbreedte gebruik voor het CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d8119c56c26ed32db136064d93775d21a71f238f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778658"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Prestaties van edge nod analyseren in Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Overzicht
Edge Performance Analytics biedt gedetailleerd gegevens verkeer en bandbreedte gebruik voor het CDN. Deze informatie kan vervolgens worden gebruikt voor het genereren van trend statistieken, waarmee u inzicht krijgt in hoe uw assets in de cache worden opgeslagen en worden geleverd aan uw klanten. Op zijn beurt kunt u een strategie vormen voor het optimaliseren van de levering van uw inhoud en om te bepalen welke problemen moeten worden aangepakt om de CDN beter te benutten. Hierdoor kunt u niet alleen de prestaties van de gegevens levering verbeteren, maar u kunt ook uw CDN-kosten verlagen.

> [!NOTE]
> Alle rapporten gebruiken UTC/GMT-notatie bij het opgeven van een datum/tijd.
> 
> 

## <a name="reports-and-log-collection"></a>Rapporten en logboek verzameling
De gegevens van de CDN-activiteit moeten worden verzameld door de Analytics-module voor de Edge-prestaties voordat er rapporten kunnen worden gegenereerd. Dit verzamelings proces vindt één keer per dag plaats en de activiteit die tijdens de vorige dag plaatsvond. Dit betekent dat de statistieken van een rapport een voor beeld van de statistieken van de dag vertegenwoordigen op het moment dat deze werd verwerkt, en niet noodzakelijkerwijs de volledige set gegevens voor de huidige dag bevatten. De primaire functie van deze rapporten is om de prestaties te beoordelen. Ze mogen niet worden gebruikt voor facturerings doeleinden of exacte numerieke statistieken.

> [!NOTE]
> De onbewerkte gegevens waarvan de prestatie analyse rapporten voor de rand worden gegenereerd, zijn ten minste 90 dagen beschikbaar.
> 
> 

## <a name="dashboard"></a>Dashboard
Het dash board voor de rand prestaties houdt het huidige en historisch CDN-verkeer via een diagram en statistieken bij. Gebruik dit dash board om recente en lange termijn trends te detecteren over de prestaties van CDN-verkeer voor uw account.

Dit dash board bestaat uit:

* Een interactieve grafiek die de visualisatie van belang rijke metrische gegevens en trends mogelijk maakt.
* Een tijd lijn die een beeld vormt van lange termijn patronen voor belang rijke metrische gegevens en trends.
* Belang rijke metrische gegevens en statistische informatie over hoe ons CDN-netwerk het site verkeer verbetert, zoals gemeten door de prestaties, het gebruik en de efficiëntie.

### <a name="accessing-the-edge-performance-dashboard"></a>Het dash board voor de Edge-prestaties openen
1. Klik op de Blade CDN-profiel op de knop **beheren** .
   
    ![De knop Blade van het CDN-profiel beheren](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    De CDN-beheer portal wordt geopend.
2. Beweeg de muis aanwijzer over het tabblad **Analytics** en Beweeg vervolgens de muis aanwijzer over de analyse-flyout voor de **rand prestaties** .  Klik op **dash board**.
   
    Het dash board met het Edge-knoop punt analyse wordt weer gegeven.

### <a name="chart"></a>Grafiek
Het dash board bevat een grafiek waarin een metrische waarde wordt bijgehouden voor de periode die in de tijd lijn wordt weer gegeven.  Een tijd lijn die tot de laatste twee jaren van CDN-activiteiten diagram, wordt direct onder de grafiek weer gegeven.

#### <a name="using-the-chart"></a>De grafiek gebruiken
* Standaard wordt het efficiëntie-tempo van de cache voor de afgelopen 30 dagen in een grafiek geplaatst.
* Deze grafiek wordt gegenereerd op basis van gegevens die dagelijks worden gesorteerd.
* Als u de muis aanwijzer over een dag in het lijn diagram houdt, wordt een datum en de waarde van de metriek op die datum aangegeven.
* Klik op weekends markeren om een overlay van lichtgrijs verticale balken te scha kelen die weekends vertegenwoordigen op de grafiek. Dit type overlay is handig voor het identificeren van verkeers patronen in het weekend.
* Klik op één jaar geleden weer geven om een overlay van de activiteit van het vorige jaar over te scha kelen naar de grafiek. Dit type vergelijking biedt inzicht in het gebruik van lange termijn CDN-gebruiks patronen. De rechter bovenhoek van de grafiek bevat een legenda waarmee de kleur code voor elke lijn grafiek wordt aangegeven.

#### <a name="updating-the-chart"></a>De grafiek bijwerken
* Tijds bereik: Voer een van de volgende handelingen uit:
  * Selecteer de gewenste regio in de tijd lijn. De grafiek wordt bijgewerkt met gegevens die overeenkomen met de geselecteerde tijds periode.
  * Dubbel klik op de grafiek om alle beschik bare historische gegevens weer te geven tot Maxi maal twee jaar.
* Metrisch: Klik op het grafiek pictogram dat naast de gewenste metrische waarde wordt weer gegeven. De grafiek en de tijd lijn worden vernieuwd met gegevens voor de bijbehorende metriek.

### <a name="key-metrics-and-statistics"></a>Belangrijkste metrische gegevens en statistieken
#### <a name="efficiency-metrics"></a>Efficiëntie-metrische gegevens
Het doel van deze metrische gegevens is om te zien of de efficiëntie van de cache kan worden verbeterd. De belangrijkste voor delen die zijn afgeleid van cache-efficiëntie zijn:

* Gereduceerde belasting op de oorspronkelijke server die kan leiden tot:
  * Betere prestaties van de webserver.
  * Lagere operationele kosten.
* Verbeterde verzorgings versnelling van gegevens omdat meer aanvragen rechtstreeks vanuit het CDN worden bediend.

| Veld | Description |
| --- | --- |
| Cache-efficiëntie |Hiermee wordt het percentage verzonden gegevens aangegeven dat uit de cache wordt uitgevoerd. Deze metrische gegevens worden gemeten wanneer een in cache opgeslagen versie van de aangevraagde inhoud rechtstreeks vanuit het CDN (Edge-servers) naar aanvragers (bijvoorbeeld webbrowser) wordt bediend |
| Frequentie van treffers |Hiermee wordt het percentage van aanvragen aangegeven dat vanuit de cache wordt verwerkt. Deze metrische gegevens worden gemeten wanneer een in cache opgeslagen versie van de aangevraagde inhoud rechtstreeks vanuit het CDN (Edge-servers) naar aanvragers (bijvoorbeeld webbrowser) werd geleverd. |
| % van externe bytes-geen cache configuratie |Hiermee wordt het percentage verkeer aangegeven dat vanaf de oorspronkelijke servers is uitgevoerd naar de CDN (Edge-servers) die niet in de cache worden opgeslagen als resultaat van de functie bypass cache (HTTP-regels-engine). |
| % van externe bytes-verlopen cache |Hiermee wordt het percentage verkeer aangegeven dat vanaf de oorspronkelijke servers aan het CDN (Edge-servers) is geleverd als gevolg van hervalidatie van verouderde inhoud. |

#### <a name="usage-metrics"></a>Metrische gegevens over gebruik
Het doel van deze metrische gegevens is om inzicht te krijgen in de volgende kostenbesparende metingen:

* Het minimaliseren van de operationele kosten via het CDN.
* Het verminderen van CDN-uitgaven door de efficiëntie en compressie van de cache.

> [!NOTE]
> Verkeers volume nummers vertegenwoordigen verkeer dat is gebruikt in berekeningen van verhoudingen en percentages, en mag alleen een deel van het totale verkeer voor klanten met grote volumes weer geven.
> 
> 

| Veld | Description |
| --- | --- |
| Ave bytes uit |Hiermee wordt het gemiddelde aantal overgedragen bytes aangegeven voor elke aanvraag die van het CDN (Edge-servers) naar de aanvrager (bijvoorbeeld webbrowser) wordt verzonden. |
| Geen frequentie van cache configuratie bytes |Hiermee wordt het percentage verkeer aangegeven dat vanuit het CDN (Edge-servers) wordt uitgevoerd naar de aanvrager (bijvoorbeeld webbrowser) die niet in de cache wordt geplaatst als gevolg van de bypass-cache functie. |
| Gecomprimeerde byte frequentie |Hiermee wordt het percentage verkeer aangegeven dat is verzonden van het CDN (Edge-servers) naar aanvragers (bijvoorbeeld webbrowser) in een gecomprimeerde indeling. |
| Uitgaande bytes |Hiermee wordt de hoeveelheid gegevens, in bytes, aangegeven die zijn geleverd vanuit het CDN (Edge-servers) naar de aanvrager (bijvoorbeeld webbrowser). |
| Bytes in |Hiermee wordt de hoeveelheid gegevens, in bytes, verzonden van aanvragers (bijvoorbeeld webbrowser) naar het CDN (Edge-servers). |
| Externe bytes |Hiermee wordt de hoeveelheid gegevens, in bytes, verzonden van CDN-en klant oorsprongs servers naar het CDN (Edge-servers). |

#### <a name="performance-metrics"></a>Metrische gegevens voor prestaties
Het doel van deze metrische gegevens is om de algemene CDN-prestaties voor uw verkeer bij te houden.

| Veld | Description |
| --- | --- |
| Overdrachts frequentie |Hiermee wordt het gemiddelde tempo aangegeven waarmee inhoud van het CDN naar een aanvrager is overgedragen. |
| Duur |Hiermee wordt de gemiddelde tijd in milliseconden aangegeven die nodig is om een Asset te leveren aan een aanvrager (bijvoorbeeld webbrowser). |
| Gecomprimeerde aanvraag frequentie |Hiermee wordt het percentage treffers aangegeven dat vanuit het CDN (Edge-servers) is geleverd aan de aanvrager (bijvoorbeeld webbrowser) in een gecomprimeerde indeling. |
| 4xx-fout frequentie |Hiermee wordt het percentage treffers aangegeven dat een 4xx-status code heeft gegenereerd. |
| 5xx-fout frequentie |Hiermee wordt het percentage treffers aangegeven dat een 5xx-status code heeft gegenereerd. |
| Treffers |Hiermee wordt het aantal aanvragen voor CDN-inhoud aangegeven. |

#### <a name="secure-traffic-metrics"></a>Metrische gegevens van beveiligd verkeer
Het doel van deze metrische gegevens is om de CDN-prestaties voor HTTPS-verkeer bij te houden.

| Veld | Description |
| --- | --- |
| Efficiëntie van de cache beveiligen |Hiermee wordt het percentage verzonden gegevens aangegeven voor HTTPS-aanvragen die vanuit de cache zijn bediend. Deze metrische gegevens worden gemeten wanneer een in cache opgeslagen versie van de aangevraagde inhoud rechtstreeks vanuit het CDN (Edge-servers) naar aanvragers (bijvoorbeeld webbrowser) via HTTPS wordt verzonden. |
| Beveiligde overdrachts frequentie |Hiermee wordt de gemiddelde snelheid aangegeven waarmee inhoud van het CDN (Edge-servers) naar aanvragers (bijvoorbeeld webservers) via HTTPS is overgedragen. |
| Gemiddelde beveiligde duur |Hiermee wordt de gemiddelde tijd in milliseconden aangegeven die nodig was om een Asset te leveren aan een aanvrager (bijvoorbeeld webbrowser) via HTTPS. |
| Beveiligde treffers |Hiermee wordt het aantal HTTPS-aanvragen voor CDN-inhoud aangegeven. |
| Beveiligde bytes uit |Hiermee wordt de hoeveelheid HTTPS-verkeer aangegeven, in bytes, die zijn geleverd vanuit het CDN (Edge-servers) aan de aanvrager (bijvoorbeeld webbrowser). |

## <a name="reports"></a>Rapporten
Elk rapport in deze module bevat een diagram en statistieken over band breedte en verkeer gebruik voor verschillende soorten metrische gegevens (bijvoorbeeld HTTP-status codes, cache status codes, aanvraag-URL, enzovoort). Deze informatie kan worden gebruikt om dieper in te zoomen op de manier waarop inhoud aan uw clients wordt geleverd en om het CDN-gedrag te verfijnen om de prestaties van de gegevens levering te verbeteren.

### <a name="accessing-the-edge-performance-reports"></a>Toegang tot de rand prestatie rapporten
1. Klik op de Blade CDN-profiel op de knop **beheren** .
   
    ![De knop Blade van het CDN-profiel beheren](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    De CDN-beheer portal wordt geopend.
2. Beweeg de muis aanwijzer over het tabblad **Analytics** en Beweeg vervolgens de muis aanwijzer over de analyse-flyout voor de **rand prestaties** .  Klik op **http-large object**.
   
    Het scherm voor analyse rapporten van het rand knooppunt wordt weer gegeven.

| Rapport | Beschrijving |
| --- | --- |
| Dagelijkse samen vatting |Hiermee kunt u dagelijks verkeer trends gedurende een opgegeven periode weer geven. Elke balk in deze grafiek vertegenwoordigt een bepaalde datum. De grootte van de balk geeft het totale aantal treffers dat op die datum plaatsvond. |
| Samen vatting per uur |Hiermee kunt u de trends van verkeer per uur weer geven gedurende een opgegeven periode. Elke balk in deze grafiek vertegenwoordigt een enkele uur op een bepaalde datum. De grootte van de balk geeft het totale aantal treffers aan dat is opgetreden tijdens dat uur. |
| Protocollen |Geeft de uitsplitsing van het verkeer tussen de HTTP-en HTTPS-protocollen. Een ring diagram geeft het percentage treffers aan dat is opgetreden voor elk type protocol. |
| HTTP-methoden |Hiermee krijgt u een snelle indruk van welke HTTP-methoden worden gebruikt om uw gegevens aan te vragen. Normaal gesp roken zijn de meest voorkomende methoden voor HTTP-aanvragen GET, HEAD en POST. Een ring diagram geeft het percentage treffers aan dat is opgetreden voor elk type HTTP-aanvraag methode. |
| URL's |Bevat een grafiek waarin de 10 aangevraagde Url's worden weer gegeven. Er wordt een balk voor elke URL weer gegeven. De hoogte van de balk geeft aan hoeveel treffers een bepaalde URL heeft gegenereerd gedurende de periode die in het rapport is opgenomen. Statistieken voor de Top 100 aangevraagde Url's worden direct onder deze grafiek weer gegeven. |
| CNAMEs |Bevat een grafiek met de Top 10 CNAME-waarden die worden gebruikt om activa te vragen over de tijds panne van een rapport. Statistieken voor de 100 meest aangevraagde CNAME-aanvragen worden direct onder deze grafiek weer gegeven. |
| Kiem |Bevat een grafiek met de Top 10 van de CDN-of oorsprongs servers van de klant van waaruit activa zijn aangevraagd gedurende een opgegeven periode. Statistieken voor de Top 100 aangevraagde CDN-of oorsprongs servers van de klant worden direct onder deze grafiek weer gegeven. Bron servers van klanten worden aangeduid met de naam die is gedefinieerd in de optie Directory naam. |
| Geo-Pop's |Hier wordt weer gegeven hoeveel van uw verkeer wordt gerouteerd via een bepaalde POP (Point-of-Presence). De afkorting van drie letters vertegenwoordigt een POP in het CDN-netwerk. |
| Clients |Bevat een grafiek waarin de Top 10-clients worden weer gegeven die activa hebben aangevraagd gedurende een opgegeven periode. Voor de doel einden van dit rapport worden alle aanvragen die afkomstig zijn van hetzelfde IP-adres, beschouwd als van dezelfde client. Statistieken voor de Top 100-clients worden direct onder deze grafiek weer gegeven. Dit rapport is handig voor het bepalen van de patronen voor de Download activiteit voor uw belangrijkste clients. |
| Cache statussen |Geeft een gedetailleerde uitsplitsing van het cache gedrag. Dit kan wijzen op manieren om de algehele ervaring van de eind gebruiker te verbeteren. Omdat de snelste prestaties afkomstig zijn uit cache treffers, kunt u de snelheid van de gegevens levering optimaliseren door cache missers en verlopen cache treffers te minimaliseren. |
| GEEN Details |Bevat een grafiek met de Top 10 van Url's voor assets waarvoor het vernieuwen van de cache-inhoud gedurende een opgegeven periode niet is gecontroleerd. Statistieken voor de Top 100 Url's voor deze typen assets worden direct onder deze grafiek weer gegeven. |
| CONFIG_NOCACHE Details |Bevat een grafiek met de Top 10 Url's voor activa die niet in de cache zijn opgeslagen vanwege de CDN-configuratie van de klant. Deze typen activa werden rechtstreeks vanaf de oorspronkelijke server bediend. Statistieken voor de Top 100 Url's voor deze typen assets worden direct onder deze grafiek weer gegeven. |
| Details niet in CACHE |Bevat een grafiek met de Top 10 Url's van assets die niet in de cache kunnen worden opgeslagen vanwege aanvraag header gegevens. Statistieken voor de Top 100 Url's voor deze typen assets worden direct onder deze grafiek weer gegeven. |
| TCP_HIT Details |Bevat een grafiek met de Top 10 Url's van assets die direct uit de cache worden bediend. Statistieken voor de Top 100 Url's voor deze typen assets worden direct onder deze grafiek weer gegeven. |
| TCP_MISS Details |Bevat een grafiek met de Top 10 Url's van assets met de cache status TCP_MISS. Statistieken voor de Top 100 Url's voor deze typen assets worden direct onder deze grafiek weer gegeven. |
| TCP_EXPIRED_HIT Details |Bevat een grafiek met de Top 10 Url's voor verouderde assets die rechtstreeks vanuit de POP werden bediend. Statistieken voor de Top 100 Url's voor deze typen assets worden direct onder deze grafiek weer gegeven. |
| TCP_EXPIRED_MISS Details |Bevat een grafiek met de Top 10 Url's voor verouderde assets waarvoor een nieuwe versie moet worden opgehaald van de oorspronkelijke server. Statistieken voor de Top 100 Url's voor deze typen assets worden direct onder deze grafiek weer gegeven. |
| TCP_CLIENT_REFRESH_MISS Details |Bevat een staaf diagram met de tien belangrijkste Url's voor assets die zijn opgehaald van een oorspronkelijke server vanwege een niet-cache aanvraag van de client. Statistieken voor de Top 100 Url's voor deze typen aanvragen worden direct onder deze grafiek weer gegeven. |
| Typen client aanvragen |Hiermee wordt het type aanvragen aangegeven dat door HTTP-clients (bijvoorbeeld browsers) is gemaakt. Dit rapport bevat een ring diagram, waarmee u kunt zien hoe aanvragen worden verwerkt. Band breedte en verkeers informatie voor elk aanvraag type wordt onder de grafiek weer gegeven. |
| Gebruikers agent |Bevat een staaf diagram met de tien belangrijkste gebruikers agenten om uw inhoud via ons CDN aan te vragen. Normaal gesp roken is een gebruikers agent een webbrowser, een media speler of een mobiele telefoon browser. Statistieken voor de meest 100 gebruikers agenten worden direct onder deze grafiek weer gegeven. |
| Verwijzende sites |Bevat een staaf grafiek met de Top 10 van verwijzende personen naar inhoud die toegankelijk is via onze CDN. Normaal gesp roken is een verwijzing naar de URL van de webpagina of de resource die aan uw inhoud is gekoppeld. Gedetailleerde informatie vindt u onder de grafiek voor de Top 100-verwijzende personen. |
| Compressie typen |Bevat een ring diagram waarmee aangevraagde assets worden opgesplitst door onze edge-servers te comprimeren. Het percentage van de gecomprimeerde activa wordt uitgesplitst op basis van het type compressie dat wordt gebruikt. Gedetailleerde informatie wordt weer gegeven onder de grafiek voor elk compressie type en-status. |
| Bestands typen |Bevat een staaf diagram waarin de tien belangrijkste bestands typen worden weer gegeven die via onze CDN voor uw account zijn aangevraagd. Voor de doel einden van dit rapport wordt een bestands type gedefinieerd door de bestands extensie van de Asset en het type Internet media (bijvoorbeeld. HTML \[ tekst/HTML \] ,. htm \[ text/HTML \] ,. aspx- \[ tekst/HTML, \] enzovoort). Gedetailleerde informatie vindt u onder de grafiek voor de meeste bestands typen van 100. |
| Unieke bestanden |Bevat een grafiek die het totale aantal unieke assets dat is aangevraagd op een bepaalde dag gedurende een opgegeven periode. |
| Samen vatting van token verificatie |Bevat een cirkel diagram met een kort overzicht van de vraag of de gevraagde assets zijn beveiligd door Token-Based-verificatie. Beveiligde assets worden weer gegeven in de grafiek volgens de resultaten van de verificatie. |
| Details van deny-token auth |Bevat een staaf diagram waarmee u de tien belangrijkste aanvragen kunt weer geven die zijn geweigerd vanwege Token-Based-verificatie. |
| HTTP-antwoord codes |Biedt een uitsplitsing van de HTTP-status codes (bijvoorbeeld 200 OK, 403 verboden, 404 niet gevonden, enzovoort) die aan uw HTTP-clients zijn geleverd door onze edge-servers. Met een cirkel diagram kunt u snel bepalen hoe uw assets zijn geleverd. Voor elke reactie code onder de grafiek worden gedetailleerde statistische gegevens gegeven. |
| 404 fouten |Bevat een staaf diagram waarmee u de tien belangrijkste aanvragen kunt weer geven die hebben geleid tot een niet-gevonden antwoord code van 404. |
| 403 fouten |Bevat een staaf diagram waarmee u de tien belangrijkste aanvragen kunt weer geven die hebben geleid tot een niet-toegestane antwoord code van 403. Er treedt een 403 niet-geautoriseerde respons code op wanneer een aanvraag wordt geweigerd door een bron server van de klant of een Edge-Server op de POP. |
| 4xx-fouten |Bevat een staaf diagram waarmee u de tien belangrijkste aanvragen kunt weer geven die hebben geresulteerd in een antwoord code in het 400-bereik. Uitgesloten van dit rapport zijn 403 niet gevonden en 404 verboden antwoord codes. Normaal gesp roken treedt er een 4xx-respons code op wanneer een aanvraag wordt geweigerd als gevolg van een client fout. |
| 504 fouten |Bevat een staaf diagram waarmee u de tien belangrijkste aanvragen kunt weer geven die hebben geleid tot een time-outantwoord code van 504-Gateway. Er treedt een time-outwaarde voor de 504-Gateway op wanneer er een time-out optreedt wanneer een HTTP-proxy probeert te communiceren met een andere server. In het geval van onze CDN treedt er een time-outantwoordcode van 504-Gateway op wanneer een Edge-server geen communicatie kan maken met een bron server van de klant. |
| 502 fouten |Bevat een staaf diagram waarmee u de tien belangrijkste aanvragen kunt weer geven die hebben geleid tot een ongeldige gateway-reactie code van 502. Een ongeldige gateway-reactie code van 502 treedt op wanneer een HTTP-protocol fout optreedt tussen een server en een HTTP-proxy. In het geval van ons CDN treedt 502 er meestal een ongeldige gateway-antwoord code op wanneer een klant van oorsprong een ongeldige reactie op een Edge-Server retourneert. Een antwoord is ongeldig als het niet kan worden geparseerd of niet is voltooid. |
| 5xx-fouten |Bevat een staaf diagram waarmee u de tien belangrijkste aanvragen kunt weer geven die hebben geresulteerd in een antwoord code in het 500-bereik.  Uitgesloten van dit rapport zijn 502 ongeldige gateway-en 504-time-outantwoord codes. |

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure CDN](cdn-overview.md)
* [Realtime-statistieken in Microsoft Azure CDN](cdn-real-time-stats.md)
* [Standaard HTTP-gedrag negeren met de regel engine](./cdn-verizon-premium-rules-engine.md)
* [Geavanceerde HTTP-rapporten](cdn-advanced-http-reports.md)