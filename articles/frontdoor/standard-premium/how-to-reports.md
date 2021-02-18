---
title: Azure front deur Standard/Premium-rapporten (preview-versie)
description: In dit artikel wordt uitgelegd hoe rapportage werkt in azure front-deur.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 9670d8204d04fc770bf3fe98a270a3f6ccbf234b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099287"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Azure front deur Standard/Premium-rapporten (preview-versie)

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Azure front deur Standard/Premium Analytics-rapporten bieden een ingebouwde en veelzijdige weer gave van de manier waarop u Azure front deur gedraagt, samen met gekoppelde firewall metrieken voor Web Application. U kunt ook gebruikmaken van Access-Logboeken om verdere problemen op te lossen en fouten op te sporen. Azure front-deur analyse rapporten bevatten verkeers rapporten en beveiligings rapporten.

| Rapporten | Details |
|---------|---------|
| Overzicht van belang rijke metrische gegevens | Toont de algemene gegevens die zijn verzonden vanuit Azure front deur-randen naar clients<br/>-Piek bandbreedte<br/>-Aanvragen <br/>-Verhouding van cache treffers<br/> -Totale latentie<br/>-5XX-fout frequentie |
| Verkeer per domein | -Biedt een overzicht van alle domeinen onder het profiel<br/>-Uitsplitsing van gegevens die vanuit een AFD-rand naar de client zijn verzonden<br/>-Totaal aantal aanvragen<br/>-3XX/4XX/5XX-respons code per domein |
| Verkeer per locatie | -Toont een kaart weergave van aanvraag en gebruik door de belangrijkste landen<br/>-Trend weergave van de belangrijkste landen |
| Gebruik | -Gegevens overdracht van de voor zijde van de Azure-deur naar clients weer geven<br/>-Gegevens overdracht van oorsprong naar AFD Edge<br/>-Band breedte van AFD Edge naar clients<br/>-Band breedte van oorsprong naar AFD Edge<br/>-Aanvragen<br/>-Totale latentie<br/>-Aantal aanvragen trend per HTTP-status code |
| Caching | -De verhouding van cache treffers op aanvraag aantal weer geven<br/>-Trend weergave van treffer-en Missing-aanvragen |
| Bovenste URL | -Aantal aanvragen weer geven <br/>-Overgedragen gegevens <br/>-Verhouding van cache treffers <br/>-Distributie van de status code voor de meest aangevraagde 50-assets. |
| Belangrijkste verwijzings bronnen | -Aantal aanvragen weer geven <br/>-Overgedragen gegevens <br/>-Verhouding van cache treffers <br/>-Distributie van de status code van de reactie voor de Top 50-verwijzende personen die verkeer genereren. |
| Beste gebruikers agent | -Aantal aanvragen weer geven <br/>-Overgedragen gegevens <br/>-Verhouding van cache treffers <br/>-Distributie van de status code van de gebruiker voor de meest 50 gebruikers agenten die zijn gebruikt om inhoud aan te vragen. |

| Beveiligingsrapporten | Details |
|---------|---------|
| Overzicht van belang rijke metrische gegevens | -Geeft overeenkomende WAF-regels weer<br/>-Overeenkomende OWASP-regels<br/>-Overeenkomende BOT-regels<br/>-Overeenkomende aangepaste regels |
| Metrische gegevens per dimensie | -Uitsplitsing van overeenkomende WAF-regels trend per actie<br/>-Ring grafiek van gebeurtenissen per regel instellen type en gebeurtenis op regel groep<br/>-Opsplitsen lijst met belangrijkste gebeurtenissen per regel-ID, land, IP-adres, URL en gebruikers agent  |

> [!NOTE]
> Beveiligings rapporten zijn alleen beschikbaar met de Azure front deur Premium-SKU.

De meeste rapporten zijn gebaseerd op Access-logboeken en worden gratis aangeboden aan klanten in de Azure front-deur. De klant hoeft geen toegangs logboeken in te scha kelen of een configuratie uit te voeren om deze rapporten weer te geven. Rapporten zijn toegankelijk via de portal en de API. Down load van CSV wordt ook ondersteund. 

Rapporten bieden ondersteuning voor het geselecteerde datum bereik van de afgelopen 90 dagen. Met gegevens punten van elke 5 minuten, elk uur of elke dag op basis van het geselecteerde datum bereik. Normaal gesp roken kunt u gegevens weer geven met een vertraging van binnen een uur en af en toe met vertraging van Maxi maal enkele uren. 

## <a name="access-reports-using-the-azure-portal"></a>Toegang tot rapporten met behulp van de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en selecteer uw Azure front deur Standard/Premium-profiel.

1. Selecteer in het navigatie deel venster **rapporten of beveiliging** onder *analyse*.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Scherm afbeelding van landings pagina voor rapporten":::

1. Er zijn zeven tabbladen voor verschillende dimensies. Selecteer de gewenste dimensie.

   * Verkeer per domein
   * Gebruik 
   * Verkeer per locatie
   * Cache
   * Bovenste URL
   * Belangrijkste verwijzings bronnen
   * Beste gebruikers agent

1. Nadat u de dimensie hebt gekozen, kunt u verschillende filters selecteren.
  
    1. **Gegevens weer geven voor** : Selecteer het datum bereik waarvoor u het verkeer per domein wilt weer geven. Beschik bare bereiken zijn:
        
        * Afgelopen 24 uur
        * Afgelopen 7 dagen
        * Afgelopen 30 dagen
        * Afgelopen 90 dagen
        * Deze maand
        * Afgelopen maand
        * Aangepaste datum

         Standaard worden gegevens voor de afgelopen zeven dagen weer gegeven. Voor tabbladen met lijn diagrammen wordt de gegevens granulatie met de datumbereiken die u hebt geselecteerd als standaard gedrag. 
    
        * 5 minuten-één gegevens punt om de 5 minuten voor datumbereiken die kleiner dan of gelijk aan 24 uur zijn.
        * Per uur: één gegevens elk uur voor datumbereiken tussen 24 uur en 30 dagen
        * Per dag: één gegevens per dag voor datumbereiken die groter zijn dan 30 dagen.

        U kunt altijd aggregatie gebruiken om de standaard granulatie voor aggregatie te wijzigen. Opmerking: 5 minuten werkt niet langer dan 14 dagen voor gegevens bereik. 

    1. **Locatie** : Selecteer één of meerdere client locaties per land. Landen worden gegroepeerd in zes regio's: Noord-Amerika, Azië, Europa, Afrika, Oceania en Zuid-Amerika. Raadpleeg de [land/regio-koppeling](https://en.wikipedia.org/wiki/Subregion). Standaard zijn alle landen geselecteerd.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="Scherm opname van rapporten voor locatie dimensie.":::
   
    1. **Protocol** -Selecteer HTTP of HTTPS om de verkeers gegevens weer te geven.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="Scherm opname van rapporten voor de protocol dimensie.":::
    
    1. **Domeinen** : Selecteer één of meerdere eind punten of aangepaste domeinen. Standaard zijn alle eind punten en aangepaste domeinen geselecteerd. 
    
        * Als u een eind punt of een aangepast domein in één profiel verwijdert en vervolgens hetzelfde eind punt of domein opnieuw maakt in een ander profiel. Het eind punt wordt beschouwd als een tweede eind punt.  
        * Als u rapporten weergeeft op aangepaste domein: wanneer u een aangepast domein verwijdert en aan een ander eind punt koppelt. Ze worden behandeld als één aangepast domein. Als weer gave op eind punt: deze worden beschouwd als afzonderlijke items. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="Scherm opname van rapporten voor domein dimensie.":::

1. Als u de gegevens wilt exporteren naar een CSV-bestand, selecteert u de koppeling *CSV downloaden* op het geselecteerde tabblad.

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="Scherm afbeelding van down load CSV-bestand voor rapporten.":::

### <a name="key-metrics-for-all-reports"></a>Belangrijkste metrische gegevens voor alle rapporten

| Metrisch | Beschrijving |
|---------|---------|
| Overgedragen gegevens | Toont gegevens die zijn overgebracht van AFD-rand Pop's naar de client voor de geselecteerde tijds periode, client locaties, domeinen en protocollen. |
| Piek bandbreedte | Piek gebruik van de band breedte in bits per seconde van de Azure front-deur Edge-Pop's naar de client voor de geselecteerde tijds periode, client locaties, domeinen en protocollen. | 
| Totaal aantal aanvragen | Het aantal aanvragen dat Pop's van de AFD-rand heeft gereageerd op de client voor de geselecteerde tijds periode, client locaties, domeinen en protocollen. |
| Percentage treffers in de cache | Het percentage van alle aanvragen in de cache waarvoor AFD de inhoud van de rand caches voor de geselecteerde tijds periode, client locaties, domeinen en protocollen heeft bediend. |
| 5XX-fout frequentie | Het percentage aanvragen waarvoor de HTTP-status code naar de client is 5XX voor de geselecteerde periode, client locaties, domeinen en protocollen. |
| Totale latentie | Gemiddelde latentie van alle aanvragen voor de geselecteerde tijds periode, client locaties, domeinen en protocollen. De latentie voor elke aanvraag wordt gemeten als de totale tijd van het moment waarop de client aanvraag wordt ontvangen door Azure front klep tot de laatste reactie byte die van de Azure front-deur naar de client is verzonden. |

## <a name="traffic-by-domain"></a>Verkeer per domein

Verkeer per domein biedt een raster weergave van alle domeinen onder dit Azure front deur-profiel. In dit rapport kunt u het volgende bekijken: 
* Aanvragen
* Gegevens die vanuit de front-deur van Azure naar de client zijn verzonden
* Aanvragen met de status code (3XX, 4Xx en 5XX) van elk domein

Domeinen omvatten endpoint-en aangepaste domeinen, zoals wordt uitgelegd in de toegang tot de rapport sessie.  

U kunt naar andere tabbladen gaan om verder te onderzoeken of Access-logboek weer te geven voor meer informatie als u de metrische gegevens onder uw verwachting vindt. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Scherm afbeelding van de landings pagina voor rapporten":::


## <a name="usage"></a>Gebruik

Dit rapport toont de trends van de status code van het verkeer en de reactie door verschillende dimensies, waaronder:

* Gegevens die worden overgedragen van Edge naar client en van oorsprong naar rand in lijn diagram. 

* Gegevens die worden overgedragen van Edge naar client per protocol in lijn diagram. 

* Het aantal aanvragen van de rand voor clients in een lijn diagram.  

* Aantal aanvragen van Edge naar clients per protocol, HTTP en HTTPS, in lijn diagram. 

* Band breedte van rand tot client in lijn diagram. 

* Totale latentie, waarmee de totale tijd wordt gemeten van de client aanvraag die wordt ontvangen door de voor deur tot de laatste reactie byte die van de voor deur naar de client is verzonden.

* Aantal aanvragen van Edge naar clients op basis van de HTTP-status code, in lijn diagram. Elke aanvraag genereert een HTTP-status code. HTTP-status code wordt weer gegeven in HTTP status code in onbewerkte Logboeken. De status code beschrijft hoe CDN Edge de aanvraag heeft afgehandeld. Een 2xx-status code geeft bijvoorbeeld aan dat de aanvraag is verzonden naar een client. Hoewel een 4xx-status code aangeeft dat er een fout is opgetreden. Zie de lijst met HTTP-status codes voor meer informatie over HTTP-status codes. 

* Het aantal aanvragen van de Edge naar clients op basis van de HTTP-status code. Het percentage aanvragen van de HTTP-status code voor alle aanvragen in het raster. 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="Scherm afbeelding van rapporten op basis van gebruik" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>Verkeer per locatie

In dit rapport worden de belangrijkste 50 locaties weer gegeven op het land van de bezoekers die de meeste toegang hebben tot uw asset. Het rapport biedt ook een uitsplitsing van de metrische gegevens per land en geeft u een algemeen overzicht van de landen waar het meeste verkeer wordt gegenereerd. Ten slotte kunt u zien welk land een hogere cache treffers of 4XX/5XX-fout codes heeft.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="Scherm afbeelding van rapporten per locatie" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

Het volgende is opgenomen in de rapporten:

* Een wereld kaart weergave van de Top 50-landen door gegevens die worden overgedragen of door hen gewenste verzoeken.
* Twee lijn diagrammen trend weergave van de vijf meest voorkomende landen door gegevens die worden overgedragen en aanvragen van uw keuze. 
* Een raster van de eerste landen met overeenkomende gegevens die vanuit AFD naar clients zijn overgedragen, gegevens die zijn overgedragen van% van alle landen, aanvragen, aanvraag% over alle landen, de cache treffers, de reactie code van de 4XX en de 5XX-respons code.

## <a name="caching"></a>Caching

Cache rapporten bieden een grafiek weergave van treffers/missers in de cache en de verhouding van cache treffers op basis van aanvragen. Met deze belang rijke metrische gegevens wordt uitgelegd hoe CDN inhoud in de cache opslaat sinds de snelste prestaties van treffers in de cache. U kunt de snelheid van de gegevens levering optimaliseren door cache missers te minimaliseren. Dit rapport bevat:

* De trend van treffers in de cache en het aantal missers in de lijn diagram.

* Verhouding van cache treffers in lijn diagram.

Cache treffers/missers beschrijven het aantal treffers in de cache van aanvragen en cache missers voor client aanvragen.

* Hits: de client vraagt om rechtstreeks vanaf Azure CDN edge-servers. Verwijst naar de aanvragen waarvan de waarden voor CacheStatus in onbewerkte logboeken worden bereikt, PARTIAL_HIT of externe TREFFERs. 

* Mis: client aanvragen die worden bediend door Azure CDN edge-servers inhoud van oorsprong ophalen. Verwijst naar de aanvragen waarvan de waarden voor het veld CacheStatus in onbewerkte logboeken ontbreken. 

**Verhouding van cache treffers** beschrijft het percentage van de in de cache geplaatste aanvragen die rechtstreeks vanuit de rand worden bediend. De formule voor de verhouding van de cache treffer is: `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%` . 

In dit rapport worden cache scenario's in overweging genomen en aanvragen die aan de volgende vereisten voldoen, worden in rekening gebracht. 

* De aangevraagde inhoud is opgeslagen in de cache op de POP die het dichtst bij de aanvrager of het Afscherm van de oorsprong ligt. 

* Gedeeltelijke in cache opgeslagen inhoud voor object Chunking.

Alle volgende gevallen worden uitgesloten: 

* Aanvragen die worden geweigerd vanwege de ingestelde regels.

* Aanvragen die een set met overeenkomende regels bevatten die zijn ingesteld op uitgeschakelde cache. 

* Aanvragen die worden geblokkeerd door WAF. 

* Origin-antwoord headers geven aan dat ze niet in de cache moeten worden opgeslagen. Zo wordt voor komen dat een asset in de cache wordt opgeslagen in de cache-Control: private, caching-Control: no-cache of Pragma: no-cache. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="Scherm opname van rapporten voor opslaan in cache.":::

## <a name="top-urls"></a>Belangrijkste Url's

Met de meeste Url's kunt u de hoeveelheid verkeer bekijken die is gemaakt voor een bepaald eind punt of aangepast domein. In de afgelopen 90 dagen worden gegevens weer gegeven voor de meest aangevraagde 50-assets. Populaire Url's worden weer gegeven met de volgende waarden. Gebruiker kan Url's sorteren op aantal aanvragen, aanvraag percentage, verzonden gegevens en verzonden gegevens%. Alle metrische gegevens worden samengevoegd per uur en kunnen per tijds bestek variëren. De URL verwijst naar de waarde van RequestUri in het Access-logboek. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="Scherm opname van rapporten voor de bovenste URL.":::

* URL verwijst naar het volledige pad van het aangevraagde activum in de indeling van `http(s)://contoso.com/index.html/images/example.jpg` . 
* Aantal aanvragen. 
* Aanvraag percentage van het totaal aantal aanvragen dat wordt geleverd door de Azure front-deur. 
* Verzonden gegevens. 
* Verzonden gegevens%. 
* Percentage treffers in cache
* Aanvragen met een antwoord code als 4XX
* Aanvragen met een antwoord code als 5XX

> [!NOTE]
> De meeste Url's kunnen na verloop van tijd veranderen en om een nauw keurige lijst van de 50 populairste Url's te krijgen, telt Azure front deur al uw URL-aanvragen per uur en blijft het lopende totaal in de loop van de dag. De Url's aan de onderkant van de 500 Url's kunnen de lijst over de dag toenemen of neerzetten, zodat het totaal aantal van deze Url's wordt gebenaderingen.  
>
> De Url's van de populairste 50 kunnen in de lijst toenemen en vallen, maar ze worden zelden verwijderd uit de lijst. de cijfers voor de meeste Url's zijn dus doorgaans betrouwbaar. Wanneer een URL buiten de lijst komt en opnieuw oploopt, wordt het aantal aanvragen in de periode die in de lijst ontbreken, geschat op basis van het aanvraag nummer van de URL die in die periode wordt weer gegeven.  
>
> Dezelfde logica is van toepassing op de meest gebruikte gebruikers agent. 

## <a name="top-referrers"></a>Belangrijkste verwijzende sites

Met de belangrijkste verwijzende sites kunnen klanten de Top 50 van de bron-en de meeste aanvragen van een bepaald eind punt of aangepast domein weer geven. U kunt gegevens weer geven voor elke periode in de afgelopen 90 dagen. Een verwijzende site geeft de URL op van waaruit een aanvraag is gegenereerd. Verwijzings bronnen kunnen afkomstig zijn van een zoek machine of andere websites. Als een gebruiker een URL (bijvoorbeeld http (s)://contoso.com/index.html) rechtstreeks in de adres regel van een browser typt, is de verwijzende site voor de aangevraagde ' empty '. Het rapport belangrijkste verwijzingen bevat de volgende waarden. U kunt sorteren op aantal aanvragen, aanvraag percentage, verzonden gegevens en verzonden gegevens%. Alle metrische gegevens worden samengevoegd per uur en kunnen per tijds bestek variëren. 

* Verwijzings, de waarde van de verwijzing naar de onbewerkte logboeken 
* Aantal aanvragen 
* Aanvraag percentage van totaal aantal aanvragen geleverd door Azure CDN in de geselecteerde tijds periode. 
* Overgedragen gegevens 
* Verzonden gegevens% 
* Percentage treffers in cache
* Aanvragen met een antwoord code als 4XX
* Aanvragen met een antwoord code als 5XX

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="Scherm opname van rapporten voor belangrijkste verwijzings bronnen.":::

## <a name="top-user-agent"></a>Beste gebruikers agent

Met dit rapport kunt u grafische en statistische weer gave hebben van de belangrijkste 50-gebruikers agenten die zijn gebruikt om inhoud aan te vragen. Bijvoorbeeld:
* Mozilla/5.0 (Windows NT 10,0; WOW64 
* AppleWebKit/537.36 (KHTML, zoals gecko) 
* Chrome/86.0.4240.75 
* Safari/537.36.  

In een raster worden het aantal aanvragen weer gegeven, de aanvraag%, de overgedragen gegevens en de overgedragen gegevens, het percentage van de cache treffers%, aanvragen met een antwoord code als 4XX en aanvragen met een antwoord code als 5XX. Gebruikers agent verwijst naar de waarde van agent in Access-Logboeken.

## <a name="security-report"></a>Beveiligings rapport

Met dit rapport kunt u grafische en statistische weer gave van WAF-patronen per verschillende dimensies hebben.

| Dimensies | Beschrijving |
|---------|---------|
| Overzicht van metrische gegevens-overeenkomende WAF-regels | Aanvragen die overeenkomen met aangepaste WAF-regels, beheerde WAF-regels en bot Manager. |
| Overzicht van metrische gegevens-geblokkeerde aanvragen | Het percentage aanvragen dat is geblokkeerd door WAF-regels voor alle aanvragen die overeenkomen met WAF-regels. |
| Overzicht van metrische gegevens-overeenkomende beheerde regels | Vier lijn diagrammen trend voor aanvragen die blok keren, registreren, toestaan en omleiden. |
| Overzicht van metrische gegevens-overeenkomende aangepaste regel | Aanvragen die overeenkomen met aangepaste WAF-regels. |
| Overzicht metrische gegevens-overeenkomende bot-regel | Aanvragen die overeenkomen met bot Manager. |
| Trend van WAF-aanvraag per actie | Vier lijn diagrammen trend voor aanvragen die blok keren, registreren, toestaan en omleiden. |
| Gebeurtenissen op regel type | Ring diagram van de WAF-aanvragen distributie per regel type, bijvoorbeeld bot, aangepaste regels en beheerde regels. |
| Gebeurtenissen per regel groep | Ring diagram van de WAF-aanvragen distributie per regel groep. |
| Aanvragen op acties | Een lijst met aanvragen voor acties, in aflopende volg orde. |
| Aanvragen op de bovenste regel-Id's | Een tabel met aanvragen van de Top 50-regel-Id's, in aflopende volg orde. |
| Aanvragen op de belangrijkste landen |  Een lijst met aanvragen van de belangrijkste 50 landen, in aflopende volg orde. |
| Aanvragen van top client Ip's |  Een tabel met aanvragen van Top 50 Ip's, in aflopende volg orde. |
| Aanvragen op de URL van de bovenste aanvraag |  Een tabel met aanvragen van Top 50 Url's, in aflopende volg orde. |
| Aanvragen op tophostnamen | Een tabel met aanvragen van de Top 50 hostnaam, in aflopende volg orde. |
| Aanvragen van de belangrijkste gebruikers agenten | Een tabel met aanvragen van Top 50-gebruikers agenten, in aflopende volg orde. |

## <a name="cvs-format"></a>CVS-indeling

U kunt CSV-bestanden voor verschillende tabbladen downloaden in rapporten. In deze sectie worden de waarden in elk CSV-bestand beschreven.

### <a name="general-information-about-the-cvs-report"></a>Algemene informatie over het CVS-rapport

Elk CSV-rapport bevat algemene informatie en de informatie is beschikbaar in alle CSV-bestanden. met variabelen op basis van het rapport dat u downloadt. 


| Waarde | Beschrijving |
|---------|---------|
| Rapport | De naam van het rapport. | 
| Domains | De lijst met de eind punten of aangepaste domeinen voor het rapport. |
| StartDateUTC | Het begin van het datum bereik waarvoor u het rapport hebt gegenereerd, in Coordinated Universal Time (UTC) |
| EndDateUTC | Het einde van het datum bereik waarvoor u het rapport hebt gegenereerd, in Coordinated Universal Time (UTC) |
| GeneratedTimeUTC | De datum en tijd waarop u het rapport hebt gegenereerd, in Coordinated Universal Time (UTC) |
| Locatie | De lijst met de landen waar de aanvragen van de client afkomstig zijn. De waarde is standaard alle. Niet van toepassing op het beveiligings rapport. |
| Protocol | Het Protocol van de aanvraag, HTTP of HTTPs. Niet van toepassing op de meest bezochte URL en het verkeer van de gebruikers agent in rapporten en het beveiligings rapport. |
| Aggregatie | De granulatie van het samen voegen van gegevens in elke rij, elke 5 minuten, elk uur en elke dag. Niet van toepassing op verkeer per domein, URL en verkeer door de gebruikers agent in rapporten en beveiligings rapport. |

### <a name="data-in-traffic-by-domain"></a>Gegevens in verkeer per domein

* Domain 
* Totaal aantal aanvragen 
* Percentage treffers in de cache 
* 3XX aanvragen 
* 4XX aanvragen 
* 5XX aanvragen 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>Gegevens in verkeer per locatie

* Locatie
* TotalRequests
* Schot
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>Gegevens in gebruik

Dit CSV-bestand bevat drie rapporten. Een voor het HTTP-protocol, een voor het HTTPS-protocol en een voor de HTTP-status code. 

Rapporten voor HTTP en HTTPs delen dezelfde gegevensset. 

* Tijd 
* Protocol 
* DataTransferred (bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

Rapport voor HTTP-status code. 

* Tijd 
* DataTransferred (bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>Gegevens in cache opslaan 

* Tijd
* CacheHitRatio 
* HitRequests 
* MissRequests 

### <a name="data-in-top-url"></a>Gegevens in de bovenste URL 

* URL 
* TotalRequests 
* Schot 
* DataTransferred (bytes) 
* DataTransferred% 

### <a name="data-in-user-agent"></a>Gegevens in gebruikers agent 

* User agent 
* TotalRequests 
* Schot 
* DataTransferred (bytes) 
* DataTransferred% 

### <a name="security-report"></a>Beveiligings rapport 

Er zijn zeven tabellen met dezelfde velden hieronder.  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* RedirectedRequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

De zeven tabellen zijn voor tijd, regel-ID, land, IP-adres, URL, hostnaam, gebruikers agent. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [metrische gegevens voor de controle van de voor-en Premium-standaard voor Azure front-deur](how-to-monitor-metrics.md).
