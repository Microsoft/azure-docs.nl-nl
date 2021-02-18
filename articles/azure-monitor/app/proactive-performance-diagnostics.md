---
title: Slimme detectie-prestatie afwijkingen | Microsoft Docs
description: Application Insights voert een slimme analyse uit van de telemetrie van uw app en waarschuwt u mogelijke problemen. Deze functie hoeft niet te worden ingesteld.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: a055a323001e0291d3cb5c1716e640b3c8b21dbf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573727"
---
# <a name="smart-detection---performance-anomalies"></a>Slimme detectie-prestatie afwijkingen

[Application Insights](./app-insights-overview.md) analyseert automatisch de prestaties van uw webtoepassing en kan u waarschuwen over mogelijke problemen. U kunt dit lezen omdat u een van onze slimme detectie meldingen hebt ontvangen.

Voor deze functie is geen speciale configuratie vereist, anders dan het configureren van uw app voor Application Insights voor uw [ondersteunde taal](./platforms.md). Het is actief wanneer uw app voldoende telemetrie genereert.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Wanneer krijg ik een melding over een slimme detectie?

Application Insights heeft gedetecteerd dat de prestaties van uw toepassing op een van de volgende manieren worden verslechterd:

* **Reactie tijd-degradatie** : uw app reageert op aanvragen langzamer dan deze is gebruikt voor. De wijziging is mogelijk snel geweest, bijvoorbeeld omdat er sprake was van een regressie in uw nieuwste implementatie. Het is ook mogelijk dat de computer geleidelijk kan worden veroorzaakt door een geheugenlek. 
* Afname van de **afhankelijkheids duur** : uw app maakt aanroepen van een rest API, data base of andere afhankelijkheid. De afhankelijkheid reageert trager dan wordt gebruikt.
* **Langzaam prestatie patroon** : uw app lijkt een prestatie probleem te hebben dat slechts enkele aanvragen beïnvloedt. Zo worden pagina's bijvoorbeeld langzamer geladen voor een type browser dan andere. of aanvragen worden langzamer van een bepaalde server bediend. Momenteel bekijken onze algoritmen de pagina laad tijden, reactie tijden van aanvragen en reactie tijden voor afhankelijkheden.  

Slimme detectie vereist ten minste 8 dagen telemetrie op een werkbaar volume om een basis lijn van de normale prestaties vast te leggen. Nadat uw toepassing voor die periode is uitgevoerd, resulteert dit in een belang rijk probleem in een melding.


## <a name="does-my-app-definitely-have-a-problem"></a>Heeft mijn app een probleem?

Nee, een melding betekent niet dat uw app een probleem heeft. Het is slechts een suggestie voor iets dat u misschien nauwkeuriger moet bekijken.

## <a name="how-do-i-fix-it"></a>Hoe kan ik dit probleem oplossen?

De meldingen bevatten diagnostische gegevens. Hier volgt een voorbeeld:


![Hier volgt een voor beeld van detectie van server reactietijd voor de degradatie](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Sorteren**. In de melding ziet u hoeveel gebruikers of hoeveel bewerkingen worden beïnvloed. Dit kan handig zijn bij het toewijzen van een prioriteit aan het probleem.
2. **Bereik**. Is het probleem van invloed op alle verkeer of alleen op sommige pagina's? Is het beperkt tot bepaalde browsers of locaties? Deze informatie kan worden opgehaald uit de melding.
3. **Diagnose stellen**. Vaak wordt de aard van het probleem voorgesteld door de diagnostische gegevens in de melding. Als de reactie tijd bijvoorbeeld langzaam verloopt wanneer het aanvraag aantal hoog is, wordt er een suggestie voor uw server of afhankelijkheden overbelast. 

    Als dat niet het geval is, opent u de Blade prestaties in Application Insights. Daar vindt u [profilerings](profiler.md) gegevens. Als er uitzonde ringen worden gegenereerd, kunt u ook de [fout opsporing voor de moment opname](./snapshot-debugger.md)proberen.



## <a name="configure-email-notifications"></a>E-mailmeldingen configureren

Slimme detectie meldingen zijn standaard ingeschakeld en worden verzonden naar personen die een [bewakings lezer](../../role-based-access-control/built-in-roles.md#monitoring-reader) hebben en de toegang tot het abonnement waartoe de Application Insights-bron zich bevindt, [bewaken.](../../role-based-access-control/built-in-roles.md#monitoring-contributor) Als u dit wilt wijzigen, klikt u in de e-mail melding op **configureren** of opent u instellingen voor slimme detectie in Application Insights. 
  
  ![Instellingen voor slimme detectie](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * U kunt de koppeling voor **Afmelden** in het e-mail bericht voor slimme detectie gebruiken om te stoppen met het ontvangen van e-mail meldingen.

E-mail berichten over fouten met betrekking tot Slimme detectie van prestaties zijn beperkt tot één e-mail per dag per Application Insights resource. Het e-mail bericht wordt alleen verzonden als er ten minste één nieuw probleem op die dag is gedetecteerd. U krijgt geen herhalingen van een bericht. 

## <a name="faq"></a>Veelgestelde vragen

* *Dus bekijken micro soft-mede werkers mijn gegevens?*
  * Nee. De service is volledig automatisch. Alleen de meldingen worden weer geven. Uw gegevens zijn [privé](./data-retention-privacy.md).
* *Analyseert u alle gegevens die worden verzameld door Application Insights?*
  * Momenteel niet. Op dit moment analyseren we de reactie tijd van de aanvraag, de reactie tijd van de afhankelijkheid en de laad tijd van de pagina. De analyse van extra metrische gegevens vindt u op onze achterstand.

* Voor welk type toepassing werkt dit?
  * Deze degradaties worden gedetecteerd in alle toepassingen die de juiste telemetrie genereren. Als u Application Insights in uw web-app hebt geïnstalleerd, worden aanvragen en afhankelijkheden automatisch bijgehouden. Maar in back-end-services of andere apps als u aanroepen hebt ingevoegd in [TrackRequest ()](./api-custom-events-metrics.md#trackrequest) of [TrackDependency](./api-custom-events-metrics.md#trackdependency), werkt Slimme detectie op dezelfde manier.

* *Kan ik mijn eigen anomalie detectie regels maken of bestaande regels aanpassen?*

  * Nog niet, maar u kunt het volgende doen:
    * [Stel waarschuwingen](../alerts/alerts-log.md) in waarmee u wordt gewaarschuwd wanneer een metriek een drempel waarde overschrijdt.
    * [Telemetrie exporteren](./export-telemetry.md) naar een [Data base](./code-sample-export-sql-stream-analytics.md) of [naar Power bi](./export-power-bi.md), waar u deze zelf kunt analyseren.
* *Hoe vaak wordt de analyse uitgevoerd?*

  * We voeren de analyse dagelijks uit op de telemetrie van de vorige dag (volle dag in UTC-tijd zone).
* *Daarom worden [metrische waarschuwingen](../alerts/alerts-log.md)vervangen?*
  * Nee.  Het detecteren van elk gedrag dat u mogelijk zou kunnen overwegen, wordt niet door ons vastgelegd.


* *Als ik niets doe in reactie op een melding, krijg ik dan een herinnering?*
  * Nee, er wordt een bericht weer gegeven over elk probleem. Als het probleem zich blijft voordoen, wordt het bijgewerkt op de Blade Slimme detectie-feed.
* *Ik ben de e-mail kwijt geraakt. Waar vind ik de meldingen in de portal?*
  * Klik in het Application Insights overzicht van uw app op de tegel **Slimme detectie** . U kunt alle meldingen tot 90 dagen terug vinden.

## <a name="how-can-i-improve-performance"></a>Hoe kan ik de prestaties verbeteren?
Trage en mislukte reacties zijn een van de grootste frustraties voor website gebruikers, zoals u van uw eigen ervaring kent. Het is dus belang rijk om de problemen op te lossen.

### <a name="triage"></a>Sorteren
Ten eerste is het belang rijk? Als een pagina altijd langzaam moet worden geladen, maar slechts 1% van de gebruikers van uw site ooit moet kijken, hebt u meer belang rijke dingen die u moet nadenken. Aan de andere kant, als er slechts 1% van de gebruikers deze opent, maar deze elke keer uitzonde ringen genereren, die mogelijk waard zijn.

Gebruik de impact-instructie (betrokken gebruikers of verkeer) als een algemene hand leiding, maar houd er rekening mee dat dit niet het hele verhaal is. Verzamel andere gegevens om te bevestigen.

Houd rekening met de para meters van het probleem. Als het geografie afhankelijke is, stelt u [beschikbaarheids tests](./monitor-web-app-availability.md) in, waaronder die regio: er kunnen zich gewoon netwerk problemen in dat gebied voordoen.

### <a name="diagnose-slow-page-loads"></a>Diagnose van langzame pagina laden
Waar is het probleem? Reageert de server traag, is de pagina erg lang, of heeft de browser veel werk om deze weer te geven?

Open de Blade metric voor browsers. De gesegmenteerde weer gave van de laad tijd van een browser pagina geeft aan waar de tijd naartoe gaat. 

* Als de **aanvraag tijd** hoog is, reageert de server langzaam of de aanvraag is een bericht dat veel gegevens bevat. Bekijk de [metrische gegevens voor prestaties](./performance-counters.md) om reactie tijden te onderzoeken.
* Stel het [bijhouden van afhankelijkheden](./asp-net-dependencies.md) in om te zien of de vertraging wordt veroorzaakt door externe services of uw data base.
* Als **antwoord wordt ontvangen** , zijn uw pagina en de afhankelijke onderdelen: Java script, CSS, afbeeldingen enzovoort (maar niet asynchroon geladen gegevens) lang. Stel een [beschikbaarheids test](./monitor-web-app-availability.md)in en zorg ervoor dat u de optie voor het laden van afhankelijke onderdelen hebt ingesteld. Wanneer u een aantal resultaten krijgt, opent u de details van een resultaat en vouwt u het uit om de laad tijden van verschillende bestanden weer te geven.
* De hoge **client verwerkings tijd** voor komt dat scripts langzaam worden uitgevoerd. Als dit niet het geval is, kunt u een timing code toevoegen en de tijden in trackMetric-aanroepen verzenden.

### <a name="improve-slow-pages"></a>Langzame pagina's verbeteren
Er is een webvol met advies over het verbeteren van uw server reacties en pagina laad tijden, dus we proberen deze niet hier te herhalen. Hier volgen enkele tips die u waarschijnlijk al kent, zodat u op de hoogte bent:

* Langzaam laden vanwege grote bestanden: de scripts en andere onderdelen asynchroon laden. Gebruik script bundeling. Splits de hoofd pagina in widgets die de gegevens afzonderlijk laden. Geen gewone oude HTML voor lange tabellen verzenden: gebruik een script om de gegevens als JSON of een andere compacte indeling aan te vragen en vul vervolgens de tabel in. Er zijn fantastische kaders om u hierbij te helpen. (Ze omvatten ook grote scripts, uiteraard.)
* Langzame server afhankelijkheden: Houd rekening met de geografische locaties van uw onderdelen. Als u bijvoorbeeld Azure gebruikt, zorg er dan voor dat de webserver en de data base zich in dezelfde regio bevinden. Halen query's meer informatie dan ze nodig hebben? Wordt de Help in de cache geplaatst of in batch verwerking?
* Capaciteits problemen: Bekijk de metrische gegevens van de server van de reactie tijden en het aantal aanvragen. Als de reactie tijden piek onevenredig zijn met pieken in het aantal aanvragen, is het waarschijnlijk dat uw servers worden uitgerekt.


## <a name="server-response-time-degradation"></a>Degradatie van server reactietijd

De melding over de degradatie van de reactie tijd vertelt u het volgende:

* De reactie tijd vergeleken met de normale reactie tijd voor deze bewerking.
* Hoeveel gebruikers worden beïnvloed.
* De gemiddelde reactie tijd en de reactie tijd van negen tigste percentielen voor deze bewerking op de dag van de detectie en 7 dagen vóór. 
* Aantal bewerkings aanvragen op de dag van de detectie en 7 dagen vóór.
* Correlatie tussen de degradatie van deze bewerking en degradatie in verwante afhankelijkheden. 
* Koppelingen waarmee u het probleem kunt vaststellen.
  * Profiler traceringen waarmee u kunt zien waar de bewerkings tijd wordt besteed (de koppeling is beschikbaar als Profiler-trace-voor beelden zijn verzameld voor deze bewerking tijdens de detectie periode). 
  * Prestatie rapporten in metrische Explorer, waar u tijd bereik/filters voor deze bewerking kunt segmenteren en analyseren.
  * Zoek naar deze aanroep om specifieke eigenschappen van het gesprek weer te geven.
  * Fouten rapporten-als Count > 1 Dit betekent dat er fouten zijn opgetreden in deze bewerking die mogelijk hebben bijgedragen aan de verslechtering van de prestaties.

## <a name="dependency-duration-degradation"></a>Degradatie van afhankelijkheids duur

Moderne toepassingen en meer een micro Services-ontwerp benadering, die in veel gevallen leidt tot een zware betrouw baarheid op externe services. Als uw toepassing bijvoorbeeld afhankelijk is van een bepaald gegevens platform of zelfs als u uw eigen bot-service bouwt, kunt u waarschijnlijk een enkele cognitieve Services-provider door sturen zodat uw bots kan communiceren op meer humane manieren en sommige Data Store-service voor bot om de antwoorden van te halen.  

Voor beeld van een melding over afhankelijkheids degradatie:

![Hier volgt een voor beeld van detectie van de duur van de afhankelijkheid van de periode](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

U ziet dat u:

* De duur vergeleken met de normale reactie tijd voor deze bewerking
* Hoeveel gebruikers worden beïnvloed
* De duur van de gemiddelde duur en de negen tigste percentiel voor deze afhankelijkheid op de dag van de detectie en 7 dagen vóór
* Aantal afhankelijkheids aanroepen op de dag van de detectie en 7 dagen vóór
* Koppelingen waarmee u het probleem kunt vaststellen
  * Prestatie rapporten in metrische Explorer voor deze afhankelijkheid
  * Zoeken naar deze afhankelijkheids aanroepen om eigenschappen van aanroepen weer te geven
  * Fouten rapporten-als Count > 1 Dit betekent dat er tijdens de detectie periode een afhankelijkheids aanroep is uitgevoerd die mogelijk heeft bijgedragen aan de duur van de degradatie. 
  * Open Analytics met query's die de duur en het aantal van deze afhankelijkheid berekenen  

## <a name="smart-detection-of-slow-performing-patterns"></a>Slimme detectie van langzaam uitgevoerde patronen 

Application Insights vindt u prestatie problemen die mogelijk alleen van invloed zijn op een deel van uw gebruikers, of alleen van invloed zijn op gebruikers in sommige gevallen. Bijvoorbeeld: melding over het laden van pagina's is trager voor een type browser dan voor andere typen browsers, of als aanvragen langzamer van een bepaalde server worden bediend. Het kan ook problemen detecteren die zijn gekoppeld aan combi Naties van eigenschappen, zoals het laden van langzame pagina's in één geografisch gebied voor clients die een bepaald besturings systeem gebruiken.  

Afwijkingen zoals deze zijn zeer moeilijk te detecteren door de gegevens te controleren, maar zijn veel gangbaar dan u mogelijk denkt. Vaak zijn ze alleen van het Opper vlak wanneer uw klanten klagen. Op dat moment is het te laat: de betrokken gebruikers wisselen al naar uw concurrenten.

Op dit moment zien onze algoritmen de pagina laad tijden, vraagt de reactie tijden van de server en de reactie tijden voor afhankelijkheden.  

U hoeft geen drempel waarden in te stellen of regels te configureren. Machine learning en algoritmen voor gegevens analyse worden gebruikt voor het detecteren van abnormale patronen.

![Klik in de e-mail waarschuwing op de koppeling om het diagnostische rapport in azure te openen](./media/proactive-performance-diagnostics/03.png)

* **Wanneer** de tijd wordt weer gegeven waarop het probleem is gedetecteerd.
* **Wat** beschrijft:

  * Het probleem dat is gedetecteerd;
  * De kenmerken van de set gebeurtenissen die worden gevonden, hebben het probleem gedrag weer gegeven.
* De tabel vergelijkt de slecht uitgevoerde set met het gemiddelde gedrag van alle andere gebeurtenissen.

Klik op de koppelingen om metrische Explorer te openen en te zoeken naar relevante rapporten, gefilterd op de tijd en eigenschappen van de langzaam uitgevoerde set.

Wijzig het tijds bereik en de filters om de telemetrie te verkennen.

## <a name="next-steps"></a>Volgende stappen
Met deze diagnostische hulpprogram ma's kunt u de telemetrie van uw app inspecteren:

* [Profiler](profiler.md) 
* [fout opsporing voor moment opnamen](./snapshot-debugger.md)
* [Analyse](../logs/log-analytics-tutorial.md)
* [Slimme diagnostische gegevens over analyse](../logs/log-query-overview.md)

Slimme detecties zijn volledig automatisch. Maar misschien wilt u nog meer waarschuwingen instellen?

* [Hand matig geconfigureerde metrische waarschuwingen](../alerts/alerts-log.md)
* [Webtests voor beschikbaarheid](./monitor-web-app-availability.md)