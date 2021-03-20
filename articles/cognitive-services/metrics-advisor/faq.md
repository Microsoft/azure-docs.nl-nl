---
title: Veelgestelde vragen over metrische gegevens adviseur
titleSuffix: Azure Cognitive Services
description: Veelgestelde vragen over de service Metrics Advisor.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0c4c296cb1454ed89eef102732533589b1c8ca0d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420955"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Veelgestelde vragen over metrische gegevens adviseur

### <a name="what-is-the-cost-of-my-instance"></a>Wat zijn de kosten van mijn instantie?

Er zijn momenteel geen kosten voor het gebruik van uw exemplaar tijdens de preview-versie.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Waarom kan ik de resource niet maken? De prijs categorie is niet beschikbaar en geeft aan dat u al 1 S0 hebt gemaakt voor dit abonnement?

:::image type="content" source="media/pricing.png" alt-text="Bericht wanneer een F0-resource al bestaat":::

Tijdens de open bare preview-periode kan slechts één exemplaar van de para meters Advisor per regio worden gemaakt onder een abonnement.

Als u al een exemplaar hebt gemaakt in dezelfde regio met hetzelfde abonnement, kunt u een andere regio of een ander abonnement proberen om een nieuw exemplaar te maken. U kunt ook een bestaande instantie verwijderen om een nieuwe te maken.

Als u het bestaande exemplaar al hebt verwijderd, maar nog steeds de fout ziet, wacht u ongeveer 20 minuten nadat de resource is verwijderd voordat u een nieuw exemplaar maakt.

## <a name="basic-concepts"></a>Basisbegrippen

### <a name="what-is-multi-dimensional-time-series-data"></a>Wat zijn multi-dimensionale time series-gegevens?

Zie de definitie voor [multi-dimensionale metrische gegevens](glossary.md#multi-dimensional-metric)  in de woorden lijst.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Hoeveel gegevens zijn er nodig om de anomalie detectie te starten?

Ten minste één gegevens punt kan anomalie detectie activeren. Dit brengt echter geen beste nauw keurigheid. De service gaat ervan uit dat een venster met eerdere gegevens punten wordt gebruikt door de waarde die u hebt opgegeven als de regel voor het door voeren van gegevens invoer tijdens het maken van de gegevensfeed.

We raden u aan enkele gegevens te hebben voordat u de tijds tempel wilt detecteren.
Op basis van de granulatie van uw gegevens varieert de aanbevolen gegevens hoeveelheid zoals hieronder.

| Granulariteit | Aanbevolen gegevens hoeveelheid voor detectie |
| ----------- | ------------------------------------- |
| Minder dan 5 minuten | 4 dagen aan gegevens |
| 5 minuten tot 1 dag | 28 dagen aan gegevens |
| Meer dan 1 dag, tot 31 dagen | 4 jaar aan gegevens |
| Meer dan 31 dagen | 48 jaar aan gegevens |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Waarom worden afwijkingen van historische gegevens niet gedetecteerd door Advisor.

Metrics Advisor is ontworpen voor het detecteren van live streaming-gegevens. Er is een beperking van de maximale lengte van historische gegevens die door de service wordt teruggestuurd en de afwijkings detectie uit te voeren. Dit betekent dat alleen gegevens punten na een bepaald vroeg tijds tempel afwijkende detectie resultaten hebben. De vroegste tijds tempel is afhankelijk van de granulatie van uw gegevens.

Op basis van de granulatie van uw gegevens worden de volgende gegevens weer gegeven: de lengte van de historisch bewaarde resultaten voor de detectie van afwijkingen.

| Granulariteit | Maximale lengte van historische gegevens voor anomalie detectie |
| ----------- | ------------------------------------- |
| Minder dan 5 minuten | Time-outtijd-13 uur |
| 5 minuten tot minder dan 1 uur | Time-out voor onboarding-4 dagen  |
| 1 uur tot minder dan 1 dag | Onboard-tijd-14 dagen  |
| 1 dag | Onboard tijd-28 dagen  |
| Meer dan 1 dag, minder dan 31 dagen | Tijd voor onboarding-2 jaar  |
| Meer dan 31 dagen | Onboard tijd-24 jaar   |

### <a name="more-concepts-and-technical-terms"></a>Meer concepten en technische voor waarden

Zie ook de [woorden lijst](glossary.md) voor meer informatie.

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>Hoe kan ik een geldige query schrijven voor het opnemen van mijn gegevens?  

Als u wilt dat de gegevens worden opgenomen in de data Advisor, moet u een query maken die de afmetingen van uw gegevens in één tijds tempel retourneert. Metrics Advisor voert deze query meermaals uit om de gegevens van elke tijds tempel op te halen. 

Houd er rekening mee dat de query ten hoogste één record moet retour neren voor elke dimensie combinatie, met een bepaalde tijds tempel. Alle records die worden geretourneerd, moeten dezelfde tijds tempel hebben. Er mogen geen dubbele records worden geretourneerd door de query.

Stel dat u de onderstaande query hebt gemaakt, voor een dagelijkse metrische waarde: 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

Zorg ervoor dat u de juiste granulatie voor uw tijd reeks gebruikt. Voor de metrische gegevens per uur gebruikt u: 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

Houd er rekening mee dat deze query's alleen gegevens retour neren met één tijds tempel en alle dimensie combinaties bevatten die moeten worden opgenomen door de para meters Advisor. 

:::image type="content" source="media/query-result.png" alt-text="Een query resultaat met één tijds tempel" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Hoe kan ik detecteert pieken & spannings dips als afwijkingen?

Als er vaste drempel waarden vooraf zijn gedefinieerd, kunt u de ' harde drempel waarde ' in werkelijkheid hand matig instellen in [anomalie detectie configuraties](how-tos/configure-metrics.md#anomaly-detection-methods).
Als er geen drempels zijn, kunt u Slimme detectie gebruiken. dit wordt mogelijk gemaakt door AI. Raadpleeg [de detectie configuratie aanpassen](how-tos/configure-metrics.md#tune-the-detecting-configuration) voor meer informatie.

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Hoe kan ik detecteert u niet-conformiteit met reguliere (seizoen) patronen als afwijkingen?

"Slimme detectie" kan het patroon van uw gegevens, met inbegrip van seizoensgebonden patronen, ontdekken. Vervolgens worden de gegevens punten gedetecteerd die niet voldoen aan de reguliere patronen als afwijkingen. Raadpleeg [de detectie configuratie aanpassen](how-tos/configure-metrics.md#tune-the-detecting-configuration) voor meer informatie.

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Hoe kan ik vaste lijnen detecteren als afwijkingen?

Als uw gegevens normaal gesp roken onstabiel zijn en een heleboel schommelingen hebben en u wilt worden gewaarschuwd wanneer het te stabiel of zelfs een vlakke lijn wordt, kan ' drempel waarde wijzigen ' worden geconfigureerd om dergelijke gegevens punten te detecteren wanneer de wijziging te klein is.
Raadpleeg de [configuraties voor anomalie detectie](how-tos/configure-metrics.md#anomaly-detection-methods) voor meer informatie.

### <a name="how-to-set-up-email-settings-and-enable-alerting-by-email"></a>E-mail instellingen instellen en waarschuwingen per e-mail inschakelen?

1.  Een gebruiker met de beheerders bevoegdheden van de abonnements beheerder of de resource groep moet naar de resource voor metrische gegevens Advisor navigeren die in de Azure Portal is gemaakt en het tabblad **toegangs beheer (IAM)** selecteren. 
2.  Selecteer **roltoewijzingen toevoegen**
3.  Kies een rol van **Cognitive Services metrics Advisor-beheerder**, selecteer uw account in de onderstaande afbeelding.
4.  Klik op de knop **Opslaan** . vervolgens is u toegevoegd als beheerder van de Advisor-resource van metrische gegevens. Houd er rekening mee dat alle bovenstaande acties moeten worden uitgevoerd door de abonnements beheerder of de beheerder van de resource groep. 

:::image type="content" source="media/access-control.png" alt-text="Menu pagina toegangs beheer (IAM) Hiermee kunt u een roltoewijzing toevoegen selecteren, gevolgd door een vak met toegang toewijzen aan de geselecteerde gebruiker die wordt weer gegeven met een toegangs rol van Cognitive Services metrics Advisor Administrator, gevolgd door de knop opslaan van de gebruikers interface die wordt geselecteerd om de stappen te illustreren voor het zoeken naar een gebruiker en het toevoegen van een bepaald niveau van toegangs machtigingen." lightbox="media/access-control.png":::


5.  Het kan een minuut duren voordat de machtigingen zijn door gegeven. Vervolgens selecteert u de werk ruimte voor metrische gegevens adviseur en selecteert u de optie **e-mail instelling** in het navigatie deel venster aan de linkerkant. Vul de vereiste items in, met name de SMTP-gerelateerde informatie. 
6.  Selecteer **Opslaan** en vervolgens bent u klaar met de e-mail configuratie. U kunt nieuwe hooks maken en u abonneren op metrische afwijkingen voor bijna realtime-waarschuwingen. 

## <a name="advanced-concepts"></a>Geavanceerde concepten

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>Hoe bouwt metrische Advisor een incident structuur voor multi-dimensionale metrieken?

Een metriek kan worden opgesplitst in meerdere tijd reeksen per dimensie. De metriek `Response latency` wordt bijvoorbeeld bewaakt voor alle services die eigendom zijn van het team. De `Service` categorie kan worden gebruikt als een dimensie om de metrische gegevens te verrijken, zodat we `Response latency` splitsen op `Service1` , enzovoort `Service2` . Elke service kan worden geïmplementeerd op verschillende computers in meerdere data centers, zodat de metriek kan worden gesplitst door `Machine` en `Data center` .

|Service| Data centrum| Machine  | 
|----|------|----------------   |
| S1 |  DC1 |   Categorie |
| S1 |  DC1 |   M2 |
| S1 |  DC2 |   B3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   Categorie |
| S2 |  DC1 |   M2 |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

Vanaf het totaal `Response latency` kunnen we inzoomen op de metrische waarde door `Service` `Data center` en `Machine` . Het is echter mogelijk dat het handiger is voor service-eigen aars om het pad te gebruiken `Service`  ->  `Data center`  ->  `Machine` , of dat het beter is voor infrastructuur technici om het pad te gebruiken `Data Center`  ->  `Machine`  ->  `Service` . Alles is afhankelijk van de afzonderlijke zakelijke vereisten van uw gebruikers. 

In metrische Advisor kunnen gebruikers een wille keurig pad opgeven dat ze willen inzoomen of samen vouwen vanaf één knoop punt van de hiërarchische topologie. Nauw keuriger is de hiërarchische topologie een Directed Acyclic Graph in plaats van een boom structuur. Er is een volledige hiërarchische topologie die bestaat uit alle mogelijke dimensie combinaties, zoals: 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="een hiërarchisch topologie diagram dat bestaat uit meerdere onderling verbonden hoek punten en randen met meerdere dimensies met het label S, DC en M met bijbehorende getallen van 1 tot en met 6" lightbox="media/dimension-combinations-view.png":::

In theorie geldt dat als de `Service` dimensie `Ls` DISTINCT-waarden heeft, de dimensie `Data center` `Ldc` DISTINCT-waarden heeft en de dimensie `Machine` `Lm` DISTINCT-waarden bevat, en dat er `(Ls + 1) * (Ldc + 1) * (Lm + 1)` dimensie combinaties kunnen zijn in de hiërarchische topologie. 

Maar gewoonlijk zijn niet alle dimensie combinaties geldig, waardoor de complexiteit aanzienlijk kan worden verminderd. Als gebruikers de metriek op dit moment samen voegen, beperken we het aantal dimensies niet. Als u de totaliserings functionaliteit van metrische gegevens Advisor moet gebruiken, mag het aantal dimensies niet meer zijn dan 6. We beperken echter het aantal tijd reeksen dat is uitgevouwen door dimensies voor een metrieke waarde van minder dan 10.000.

Het hulp programma voor **incident structuur** op de pagina Diagnostische gegevens bevat alleen knoop punten waar een afwijkend probleem is gedetecteerd, in plaats van de hele topologie. Zo kunt u zich richten op het huidige probleem. Het kan ook zijn dat niet alle afwijkingen binnen de metriek worden weer gegeven. in plaats daarvan worden de belangrijkste afwijkingen weer gegeven op basis van de bijdrage. Op deze manier kunnen we snel de impact, het bereik en het pad van de abnormale gegevens achterhalen. Waardoor het aantal afwijkingen dat we moeten concentreren, aanzienlijk vermindert en gebruikers helpen hun belangrijkste problemen te begrijpen en te vinden. 
 
Als er bijvoorbeeld sprake is van een afwijkend `Service = S2 | Data Center = DC2 | Machine = M5` , heeft de afwijking van de afwijkende invloed op het bovenliggende knoop punt `Service= S2` , dat ook de anomalie heeft gedetecteerd, maar de anomalie heeft geen invloed op het hele Data Center op `DC2` en alle services op `M5` . De incident structuur wordt opgebouwd zoals in de onderstaande scherm afbeelding, de meest afwijkende fouten worden vastgelegd `Service = S2` en de hoofd oorzaak kan worden geanalyseerd in twee paden die beide leiden tot `Service = S2 | Data Center = DC2 | Machine = M5` .

 :::image type="content" source="media/root-cause-paths.png" alt-text="5 gelabelde hoek punten met twee verschillende paden die zijn verbonden door randen met een gemeen schappelijk knoop punt met de naam s2. De belangrijkste afwijkingen worden vastgelegd op service = S2 en de hoofd oorzaak kan worden geanalyseerd door de twee paden die beide leiden tot service = S2 | Data Center = DC2 | Machine = M5" lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>Volgende stappen
- [Overzicht van Metrics Advisor](overview.md)
- [De webportal gebruiken](quickstarts/web-portal.md)