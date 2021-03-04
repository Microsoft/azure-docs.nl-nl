---
title: Gegevens visualiseren van Azure Monitor | Microsoft Docs
description: Hierin wordt een overzicht gegeven van de beschik bare methoden voor het visualiseren van metrische gegevens en logboeken die zijn opgeslagen in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2021
ms.openlocfilehash: b90d628f0d24e43d7b9f2e3fa87e74d426648c6e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048570"
---
# <a name="visualizing-data-from-azure-monitor"></a>Gegevens van Azure Monitor visualiseren
Dit artikel bevat een overzicht van de beschik bare methoden voor het visualiseren van logboek-en metrische gegevens die zijn opgeslagen in Azure Monitor.

Visualisaties zoals grafieken en grafieken kunnen u helpen bij het analyseren van uw bewakings gegevens om in te zoomen op problemen en patronen te identificeren. Afhankelijk van het hulp programma dat u gebruikt, hebt u mogelijk ook de mogelijkheid om visualisaties te delen met andere gebruikers binnen en buiten uw organisatie.

## <a name="workbooks"></a>Werkmappen
[Werkmappen](./visualize/workbooks-overview.md) zijn interactieve documenten die diep inzicht geven in uw gegevens, onderzoek en samen werking binnen het team. Specifieke voor beelden waarin werkmappen handig zijn, zijn probleemoplossings richtlijnen en incident postmortem.

![Diagram toont scherm opnamen van verschillende pagina's van een werkmap, inclusief analyse van pagina weergaven, het gebruik en de tijd die aan de pagina zijn besteed.](media/visualizations/workbook.png)

### <a name="advantages"></a>Voordelen
- Ondersteunt metrische gegevens en Logboeken.
- Biedt ondersteuning voor para meters die interactieve rapporten inschakelen waarbij een element in een tabel wordt geselecteerd, worden gekoppelde grafieken en visualisaties dynamisch bijgewerkt.
- Document-achtige stroom.
- Optie voor persoonlijke of gedeelde werkmappen.
- Eenvoudige, gebruiks vriendelijke ontwerp ervaring.
- Sjablonen ondersteunen galerie met open bare GitHub-sjablonen.

### <a name="limitations"></a>Beperkingen
- Geen automatische vernieuwing.
- Geen compacte indeling zoals Dash boards, waardoor werkmappen minder nuttig zijn als één glas venster. Meer bedoeld voor meer inzicht.


## <a name="azure-dashboards"></a>Azure Dashboards
[Azure-Dash boards](../azure-portal/azure-portal-dashboards.md) zijn de primaire Dashboard technologie voor Azure. Ze zijn vooral handig voor het leveren van een enkel glas venster over uw Azure-infra structuur en-services, zodat u snel belang rijke problemen kunt identificeren.

![Scherm afbeelding toont een voor beeld van een Azure-dash board met aanpas bare informatie.](media/visualizations/dashboard.png)

Hier volgt een video-overzicht van het maken van Dash boards.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

### <a name="advantages"></a>Voordelen
- Diep gaande integratie in Azure. Visualisaties kunnen worden vastgemaakt aan dash boards van meerdere Azure-pagina's, waaronder [Metrics Explorer](essentials/metrics-charts.md), [log Analytics](logs/log-analytics-overview.md)en [Application Insights](app/app-insights-overview.md).
- Ondersteunt metrische gegevens en Logboeken.
- Combi neer gegevens uit meerdere bronnen, inclusief uitvoer van [Metrics Explorer](essentials/metrics-charts.md), [logboek query's](logs/log-query-overview.md)en [kaarten](app/app-map.md) en beschik baarheid in [Application Insights](app/app-insights-overview.md).
- Optie voor persoonlijke of gedeelde Dash boards. Geïntegreerd met [op rollen gebaseerd toegangs beheer van Azure (Azure RBAC)](../role-based-access-control/overview.md).
- Automatisch vernieuwen. Het vernieuwen van metrische gegevens is afhankelijk van het tijds bereik met mini maal vijf minuten. Logboeken worden elk uur vernieuwd, met een optie voor hand matige vernieuwing op aanvraag door te klikken op het pictogram Vernieuwen op een bepaalde visualisatie of door het volledige dash board te vernieuwen.
- Parametrized metrische Dash boards met tijds tempel en aangepaste para meters.
- Flexibele indelings opties.
- Modus volledig scherm.


### <a name="limitations"></a>Beperkingen
- Beperkte controle over logboek visualisaties zonder ondersteuning voor gegevens tabellen. Het totale aantal gegevens reeksen is beperkt tot 50 met verdere gegevens reeksen die zijn gegroepeerd onder een _andere_ Bucket.
- Er zijn geen aangepaste parameter ondersteuning voor logboek grafieken.
- Logboek grafieken zijn beperkt tot de afgelopen 30 dagen.
- Logboek grafieken kunnen alleen worden vastgemaakt aan gedeelde Dash boards.
- Geen interactiviteit met Dashboard gegevens.
- Beperkt context-inzoomen.


## <a name="power-bi"></a>Power BI
[Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) is vooral handig voor het maken van zakelijke Dash boards en rapporten, evenals rapporten waarmee kpi's trends op lange termijn worden geanalyseerd. U kunt [de resultaten van een logboek query importeren](visualize/powerbi.md) in een Power bi-gegevensset, zodat u kunt profiteren van de functies, zoals het combi neren van gegevens uit verschillende bronnen en het delen van rapporten op de web-en mobiele apparaten.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Voordelen
- Uitgebreide visualisaties.
- Uitgebreide interactiviteit, inclusief inzoomen en kruislings filteren.
- Eenvoudig te delen binnen uw organisatie.
- Integratie met andere gegevens uit meerdere gegevens bronnen.
- Betere prestaties met de resultaten in de cache van een kubus.


### <a name="limitations"></a>Beperkingen
- Ondersteunt logboeken maar geen metrische gegevens.
- Geen Azure-integratie. Kan geen Dash boards en modellen beheren via Azure Resource Manager.
- Query resultaten moeten worden geïmporteerd in Power BI model om te configureren. Beperking van de resultaat grootte en het vernieuwen.
- Beperkte gegevens vernieuwing van acht keer per dag.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) is een open platform dat Excel in operationele Dash boards bevat. Dit is met name handig voor het detecteren en isoleren en uitsorteren van operationele incidenten. U kunt [Grafana Azure monitor gegevens bron-invoeg toepassing](visualize/grafana-plugin.md) toevoegen aan uw Azure-abonnement om uw Azure-metrische gegevens te visualiseren.

![Scherm opname toont Grafana-visualisaties.](media/visualizations/grafana.png)

### <a name="advantages"></a>Voordelen
- Uitgebreide visualisaties.
- Uitgebreid ecosysteem van gegevens bronnen.
- Gegevens interactiviteit, inclusief inzoomen.
- Ondersteunt para meters.

### <a name="limitations"></a>Beperkingen
- Geen Azure-integratie. Kan geen Dash boards en modellen beheren via Azure Resource Manager.
- Kosten ter ondersteuning van extra Grafana-infra structuur of extra kosten voor Grafana-Cloud.


## <a name="build-your-own-custom-application"></a>Uw eigen aangepaste toepassing bouwen
U kunt toegang krijgen tot gegevens in logboek-en metrische gegevens in Azure Monitor via hun API met behulp van elke REST-client, waarmee u uw eigen aangepaste websites en toepassingen kunt bouwen.

### <a name="advantages"></a>Voordelen
- Volledige flexibiliteit in de gebruikers interface, visualisatie, interactiviteit en functies.
- Metrische gegevens combi neren en gegevens in een logboek vastleggen met andere bronnen.

### <a name="disadvantages"></a>Nadelen
- Aanzienlijke technische inspanning vereist.


## <a name="azure-monitor-views"></a>Azure Monitor weergaven

> [!IMPORTANT]
> Weer gaven worden afgeschaft. Zie de [overgangs gids voor de werk bladen van Azure monitor weer geven](visualize/view-designer-conversion-overview.md) voor meer informatie over het converteren van weer gaven naar werkmappen.

Met [weer gaven in azure monitor](visualize/view-designer.md) kunt u aangepaste visualisaties met logboek gegevens maken. Deze worden gebruikt door [bewakings oplossingen](insights/solutions.md) om de verzamelde gegevens te presen teren.


![In de scherm afbeelding ziet u een tegel van de container bewakings oplossing en de gedetailleerde Azure Monitor weer gave die wordt geopend wanneer u deze selecteert.](media/visualizations/view.png)

### <a name="advantages"></a>Voordelen
- Uitgebreide visualisaties voor logboek gegevens.
- Weer gaven exporteren en importeren om ze over te dragen naar andere resource groepen en-abonnementen.
- Kan worden geïntegreerd in Azure Monitor-beheer model met werk ruimten en bewakings oplossingen.
- [Filters](visualize/view-designer-filters.md) voor aangepaste para meters.
- Interactief, ondersteunt inzoomen op meerdere niveaus (weer gave van inzoomen in een andere weer gave)

### <a name="limitations"></a>Beperkingen
- Ondersteunt logboeken maar geen metrische gegevens.
- Geen persoonlijke weer gaven. Beschikbaar voor alle gebruikers met toegang tot de werk ruimte.
- Geen automatische vernieuwing.
- Beperkte indelings opties.
- Geen ondersteuning voor het uitvoeren van query's in meerdere werk ruimten of Application Insights toepassingen.
- Query's zijn beperkt tot de antwoord grootte tot 8 MB en de uitvoer tijd van de query van 110 seconden.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [gegevens die worden verzameld door Azure monitor](data-platform.md).
- Meer informatie over [Azure-Dash boards](../azure-portal/azure-portal-dashboards.md).
- Meer informatie over [Metrics Explorer](essentials/metrics-getting-started.md)
- Meer informatie over [werkmappen](./visualize/workbooks-overview.md).
- Meer informatie over het [importeren van logboek gegevens in Power bi](./visualize/powerbi.md).
- Meer informatie over de [Grafana Azure monitor-invoeg toepassing voor gegevens bronnen](./visualize/grafana-plugin.md).
- Meer informatie over [weer gaven in azure monitor](visualize/view-designer.md).

