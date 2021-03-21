---
title: Verklarende woorden lijst voor metrische gegevens adviseur
titleSuffix: Azure Cognitive Services
description: Belang rijke ideeën en concepten voor de biometrische Advisor-service
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 6c461983053a145dfda58b9e3d26b39db0c339e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92893417"
---
# <a name="metrics-advisor-glossary-of-common-vocabulary-and-concepts"></a>Verklarende woorden lijst voor metrische gegevens adviseur van common vocabulaire en concepten

In dit document worden de technische termen beschreven die worden gebruikt in de biometrische Advisor. In dit artikel vindt u meer informatie over algemene concepten en objecten die u kunt tegen komen wanneer u de service gebruikt.

## <a name="data-feed"></a>Gegevensfeed

> [!NOTE]
> Meerdere meet waarden kunnen dezelfde gegevens bron en zelfs dezelfde gegevensfeed delen.

Een gegevensfeed is de metrische gegevens die door Advisor worden opgenomen in de gegevens bron, zoals Cosmos DB of een SQL Server. Een gegevensfeed bevat rijen van:
* tijds tempels
* nul of meer dimensies
* een of meer metingen. 

## <a name="metric"></a>Metrisch

Een metriek is een meet bare meting die wordt gebruikt om de status van een specifiek bedrijfs proces te controleren en te beoordelen. Dit kan een combinatie zijn van meerdere tijdreekswaarden, onderverdeeld in dimensies. Een *status* van een webservice kan bijvoorbeeld dimensies bevatten voor het *aantal gebruikers* en de *-en-US-markt*.

## <a name="dimension"></a>Dimensie

Een dimensie bestaat uit een of meer categorische-waarden. De combi natie van deze waarden duidt een bepaalde univariate tijd reeks aan, bijvoorbeeld: land, taal, Tenant, enzovoort.

## <a name="multi-dimensional-metric"></a>Multi-dimensionale metriek

Wat is een metrische waarde voor meerdere dimensies? We gaan twee voor beelden gebruiken. 

**Omzet van een bedrijf**

Stel dat u gegevens hebt voor de omzet van uw bedrijf. De tijdreeks gegevens kunnen er ongeveer als volgt uitzien:

| Tijdstempel | Categorie | Markt | Opbrengst |
| ----------|----------|--------|----- |
| 2020-6-1 | Voedsel | VS | 1000 |
| 2020-6-1 | Kleding | VS | 2000 |
| 2020-6-2 | Voedsel | VK | 800 | 
| ...      | ...  |... | ... |

In dit voor beeld zijn *categorie* en *markt* dimensies. *Omzet* is de KPI (Key Performance indicator) die kan worden gesegmenteerd in verschillende categorieën en/of markten, en kan ook worden geaggregeerd. Bijvoorbeeld de opbrengst van *levens middelen* voor alle markten.
 
**Aantal fouten voor een complexe toepassing**

Stel dat u gegevens hebt voor het aantal fouten dat in een toepassing is geregistreerd. De tijdreeks gegevens kunnen er ongeveer als volgt uitzien:

| Tijdstempel | Toepassings onderdeel | Region | Aantal fouten |
| ----------|----------|--------|----- |
| 2020-6-1 | Werknemers database | WEST-EU | 9000 |
| 2020-6-1 | Berichtenwachtrij | VS-OOST | 1000 |
| 2020-6-2 | Berichtenwachtrij | VS-OOST | 8000| 
| ...      | ...           | ...     |  ...|

In dit voor beeld zijn het *toepassings onderdeel* en de *regio* dimensies. *Aantal fouten* is de KPI die kan worden gesegmenteerd in verschillende categorieën en/of markten, en kan ook worden geaggregeerd. Bijvoorbeeld het aantal fouten in de *berichten wachtrij* in alle regio's.

## <a name="measure"></a>Meting

Een meting is een basis-of eenheids gerichte term en een meet bare waarde van de metriek.

## <a name="time-series"></a>Tijdreeks

Een tijd reeks is een reeks gegevens punten die in chronologische volg orde worden geïndexeerd (of weer gegeven of gegrafiekd). Meestal is een tijd reeks een reeks die wordt uitgevoerd op opeenvolgende, gelijkmatig geplaatste punten in de tijd. Het is een opeenvolging van eenmalige gegevens.

In Metrics Advisor worden waarden van één metriek voor een specifieke dimensie combinatie één reeks genoemd.

## <a name="granularity"></a>Granulariteit

Granulariteit geeft aan hoe vaak gegevens punten worden gegenereerd bij de gegevens bron. Bijvoorbeeld dagelijks, per uur.

## <a name="start-time"></a>Begintijd

Begin tijd is de tijd dat u wilt dat metrische gegevens worden opgenomen in de gegevens bron. De gegevens bron moet gegevens bevatten op de opgegeven begin tijd.

## <a name="confidence-boundaries"></a>Betrouwbaarheids grenzen

> [!NOTE]
> Vertrouwens grenzen zijn niet de enige meting die wordt gebruikt om afwijkingen op te sporen. Het is mogelijk dat gegevens punten buiten deze grens als normaal worden gemarkeerd door het detectie model. 

In de metrische gegevens adviseur vertegenwoordigen betrouwbaarheids grenzen de gevoeligheid van het gebruikte algoritme en worden ze gebruikt om te filteren op gevoelige afwijkingen. In de webportal worden vertrouwens grenzen weer gegeven als een transparante blauwe band. Alle punten in de band worden beschouwd als normale punten.

Met metrische gegevens adviseur beschikt u over hulpprogram ma's waarmee u de gevoeligheid van de gebruikte algoritmen kunt aanpassen. Zie [How to: Configure metrics and fijnafstelling Configuration](how-tos/configure-metrics.md) voor meer informatie.

![Betrouw bare grenzen](media/confidence-bounds.png)


## <a name="hook"></a>Accolade

Met metrische gegevens adviseur kunt u real-time waarschuwingen maken en u hierop abonneren. Deze waarschuwingen worden via internet verzonden [met behulp van een hook](how-tos/alerts.md).

## <a name="anomaly-incident"></a>Afwijkend incident

Nadat een detectie configuratie is toegepast op metrische gegevens, worden incidenten gegenereerd wanneer een reeks erin een afwijkingen heeft. In grote gegevens sets kan dit erg groot zijn, waardoor metrische Advisor groepen een reeks afwijkingen binnen een metriek in een incident ondervindt. De service evalueert ook de ernst en voorziet in hulpprogram ma's voor [het vaststellen van het incident](how-tos/diagnose-incident.md).

### <a name="incident-tree"></a>Incident structuur

In Metrics Advisor kunt u afwijkings detectie op metrische gegevens Toep assen. vervolgens wordt door de Advisor voor metrische gegevens automatisch alle tijd reeksen van alle dimensie combinaties gecontroleerd. Wanneer er sprake is van een afwijkend gedetecteerd, worden afwijkingen door de metrische gegevens adviseur in incidenten geaggregeerd.
Nadat een incident is opgetreden, levert metrische gegevens adviseur een incident boom met een hiërarchie van afwijkende afwijkingen en identificeert deze met de grootste impact. Elk incident heeft een hoofd oorzaak van afwijkingen, het bovenste knoop punt van de structuur.

### <a name="anomaly-grouping"></a>Afwijkings groepering

Met metrische gegevens adviseur kunt u verwante tijd reeksen vinden met een vergelijkbaar patroon. Het kan ook dieper inzicht bieden in de gevolgen voor andere dimensies en de afwijkingen te correleren.

### <a name="time-series-comparison"></a>Vergelijking van de tijd reeks

U kunt meerdere tijd reeksen kiezen voor het vergelijken van trends in één visualisatie. Dit biedt een duidelijke en zicht bare manier om gerelateerde reeksen weer te geven en te vergelijken.

## <a name="detection-configuration"></a>Detectie configuratie

>[!Note]
>Detectie configuraties worden alleen toegepast binnen een afzonderlijke metriek.

Op de metrics Advisor-webportal wordt een detectie configuratie (zoals gevoeligheid, automatisch uitstellen en richting) vermeld in het linkerdeel venster bij het weer geven van een metriek. Para meters kunnen worden afgestemd en toegepast op alle reeksen binnen deze metrische gegevens.

Een detectie configuratie is vereist voor elke tijd reeks en bepaalt of een punt in de tijd reeks een afwijkend is. Met metrische gegevens adviseur wordt een standaard configuratie ingesteld voor de gehele metriek wanneer u de eerste keer inschakelt. 

U kunt de configuratie ook verfijnen door afstemmings parameters toe te passen op een groep reeksen of een specifieke. Er wordt slechts één configuratie toegepast op een tijd reeks:
* Configuraties voor een groep worden overschreven door configuraties die worden toegepast op een specifieke serie
* Configuraties voor een groep overschrijven configuraties die zijn toegepast op de gehele metriek.

De metrische gegevens adviseur biedt verschillende [detectie methoden](how-tos/configure-metrics.md#anomaly-detection-methods)en u kunt deze combi neren met logische Opera tors.

### <a name="smart-detection"></a>Slimme detectie

Afwijkings detectie met meerdere algoritmen van machine learning.

**Gevoeligheid**: een numerieke waarde om de tolerantie van de afwijkings detectie aan te passen. Visueel, hoe hoger de waarde, hoe smaller de bovenste en onderste grenzen rond de tijd reeks.

### <a name="hard-threshold"></a>Harde drempel waarde

Waarden buiten de bovenste of onderste grenzen zijn afwijkingen.

**Min**: de ondergrens

**Max**: de bovengrens

### <a name="change-threshold"></a>Drempel waarde wijzigen

Gebruik de waarde voor vorig punt om te bepalen of dit punt een afwijkend is.

**Percentage wijzigen**: vergeleken met het vorige punt is het huidige punt een afwijkend als het percentage van de wijziging meer is dan deze para meter.

**Punten wijzigen**: hoeveel punten moeten worden teruggestuurd.

### <a name="common-parameters"></a>Algemene para meters

**Richting**: een punt is alleen een afwijkend wanneer de afwijking in de richting *omhoog*, *omlaag* of *beide* wordt uitgevoerd.

**Ongeldige afwijkings waarde tot**: een gegevens punt is alleen een afwijkingen als een opgegeven percentage van vorige punten ook afwijkingen bevat.

## <a name="alert-settings"></a>Waarschuwings instellingen

Waarschuwings instellingen bepalen welke afwijkingen moeten een waarschuwing activeren. U kunt meerdere waarschuwingen instellen met andere instellingen. U kunt bijvoorbeeld een waarschuwing maken voor afwijkingen met lagere impact op het bedrijf en een andere voor meer belang rijke waarschuwingen.

U kunt ook een waarschuwing over metrische gegevens maken. Bijvoorbeeld een waarschuwing die alleen wordt geactiveerd als twee opgegeven metrische gegevens afwijkingen hebben.

### <a name="alert-scope"></a>Waarschuwings bereik

Het waarschuwings bereik verwijst naar het bereik waarop de waarschuwing van toepassing is. Er zijn vier opties:

**Afwijkingen van alle reeksen**: waarschuwingen worden geactiveerd voor afwijkingen in alle reeksen binnen de metrische gegevens.

**Afwijkingen in reeks groep**: waarschuwingen worden alleen geactiveerd voor afwijkingen in specifieke dimensies van de reeks groep. Het aantal opgegeven dimensies moet kleiner zijn dan het totale aantal dimensies.

**Afwijkingen in de favorieten reeks**: waarschuwingen worden alleen geactiveerd voor afwijkingen die als favorieten worden toegevoegd. U kunt een groep reeksen kiezen als favoriet voor elke detectie configuratie.

**Afwijkingen in de bovenste n van alle reeksen**: waarschuwingen worden alleen geactiveerd voor afwijkingen in de eerste n-serie. U kunt para meters instellen om het aantal tijds tempels op te geven waarmee rekening moet worden gehouden en hoeveel afwijkingen er moeten zijn om de waarschuwing te verzenden.

### <a name="severity"></a>Severity

Ernst is een cijfer dat door de metrieke Advisor wordt gebruikt om de ernst van het incident te beschrijven, met inbegrip van *hoog*, *gemiddeld* en *laag*.

Op dit moment gebruikt metrische Advisor de volgende factoren om de ernst van de waarschuwing te meten:
1. De waarde van de verhouding en het aantal afwijkingen in de metrische gegevens.
1. Vertrouwen van afwijkingen.
1. Uw favoriete instellingen dragen ook bij aan de ernst.

### <a name="auto-snooze"></a>Automatisch uitstellen

Enkele afwijkingen zijn tijdelijke problemen, met name voor de metrische gegevens voor kleine granulatie. U kunt een waarschuwing *uitstellen* voor een bepaald aantal tijd punten. Als er afwijkingen worden gevonden binnen het opgegeven aantal punten, wordt er geen waarschuwing geactiveerd. Het gedrag van automatisch uitstellen kan worden ingesteld op het niveau van metrische gegevens of reeksen.

Het gedrag van uitstellen kan worden ingesteld op het niveau van metrische gegevens of reeksen.

## <a name="data-feed-settings"></a>Instellingen voor gegevensfeed

### <a name="ingestion-time-offset"></a>Offset van opname tijd

Standaard worden gegevens opgenomen op basis van de granulatie (bijvoorbeeld *dagelijks*). Door een positief geheel getal te gebruiken, kunt u de opname van de gegevens vertragen met de opgegeven waarde. Met een negatief getal kunt u de opname door gegeven aan de opgegeven waarde.

### <a name="max-ingestion-per-minute"></a>Max. opname per minuut

Stel deze para meter in als uw gegevens bron beperkte gelijktijdigheid ondersteunt. Wijzig anders de standaard instellingen.

### <a name="stop-retry-after"></a>Nieuwe poging stoppen na

Als er een fout is opgetreden bij het opnemen van gegevens, zal Advisor automatisch opnieuw proberen na een bepaalde tijd. Het begin van de periode is het tijdstip waarop de eerste gegevens opname is uitgevoerd. De lengte van de periode voor opnieuw proberen wordt bepaald op basis van de granulariteit. Als u de standaard waarde ( `-1` ) gebruikt, wordt de periode voor opnieuw proberen bepaald aan de hand van de granulatie:

| Granulariteit       | Nieuwe poging stoppen na           |
| :------------ | :--------------- |
| Dagelijks, aangepast (>= 1 dag), wekelijks, maandelijks, jaarlijks     | 7 dagen          |
| Elk uur, aangepast (< 1 dag)       | 72 uur |

### <a name="min-retry-interval"></a>Min. interval voor opnieuw proberen

U kunt het minimale interval opgeven wanneer u opnieuw probeert gegevens uit de bron te halen. Als u de standaard waarde ( `-1` ) gebruikt, wordt het interval voor nieuwe pogingen bepaald aan de hand van de granulatie:

| Granulariteit       | Mini maal interval voor opnieuw proberen           |
| :------------ | :--------------- |
| Dagelijks, aangepast (>= 1 dag), wekelijks, maandelijks     | 30 minuten          |
| Elk uur, aangepast (< 1 dag)      | 10 minuten |
| Jaar | 1 dag          |

### <a name="grace-period"></a>Respijtperiode

> [!Note]
> De respijt periode begint op de normale opname tijd, plus de opgegeven opname tijd verschuiving.
    
Een respijt periode is een periode waarin metrische gegevens van de gegevens bron door Advisor worden voortgezet, maar geen waarschuwingen worden geactiveerd. Als er na de respijt periode geen gegevens zijn opgenomen, wordt een waarschuwing voor een *niet-beschik bare gegevensfeed* geactiveerd.

### <a name="snooze-alerts-in"></a>Waarschuwingen uitstellen in

Als deze optie is ingesteld op nul, wordt er met elke tijds tempel en *niet beschikbaar* een waarschuwing geactiveerd. Als u een andere waarde dan nul instelt, wordt het opgegeven aantal *niet-beschik bare* waarschuwingen uitgesteld als er geen gegevens zijn opgehaald.

## <a name="data-feed-permissions"></a>Machtigingen voor gegevens invoer

Er zijn twee rollen voor het beheren van machtigingen voor gegevens invoer: *beheerder* en *Viewer*. 

* Een *beheerder* heeft volledige controle over de gegevensfeed en de metrische gegevens. Ze kunnen de gegevensfeed activeren, onderbreken, verwijderen en updates maken voor feeds en configuraties. Een *beheerder* is doorgaans de eigenaar van de metrische gegevens.

* Een *Viewer* kan de gegevensfeed of metrische gegevens weer geven, maar kan geen wijzigingen aanbrengen. 

## <a name="next-steps"></a>Volgende stappen
- [Overzicht van Metrics Advisor](overview.md)
- [De webportal gebruiken](quickstarts/web-portal.md)