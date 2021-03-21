---
title: Overzicht van Azure Monitor | Microsoft Docs
description: Overzicht van Microsoft-services en -functionaliteiten die bijdragen aan een complete bewakingsstrategie voor uw Azure-services en -toepassingen.
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 11/17/2019
ms.openlocfilehash: 544d6937e412e3e1cfc2cf4e520c02f3f804fc8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047159"
---
# <a name="azure-monitor-overview"></a>Overzicht van Azure Monitor

Azure Monitor helpt u de beschikbaarheid en prestaties van uw toepassingen en services te optimaliseren. Het biedt een uitgebreide oplossing voor het verzamelen, analyseren en handelen aan de hand van telemetriegegevens in uw cloud- en on-premises omgevingen. Deze informatie helpt u begrijpen hoe uw toepassingen presteren en stelt proactief problemen vast die betrekking hebben op de toepassingen en de resources waarvan ze afhankelijk zijn.

Enkele voorbeelden van de mogelijkheden die Azure Monitor biedt:

- Detecteren en analyseren van problemen in toepassingen en van afhankelijkheden met [Application Insights](app/app-insights-overview.md).
- Correleer infrastructuur problemen met [VM Insights](vm/vminsights-overview.md) en [container Insights](containers/container-insights-overview.md).
- Inzoomen op uw controlegegevens met [Log Analytics](logs/log-query-overview.md) voor probleemoplossing en uitgebreide diagnose.
- Op grote schaal bewerkingen londersteunen met [slimme waarschuwingen](alerts/alerts-smartgroups-overview.md) en [geautomatiseerde acties](alerts/alerts-action-rules.md).
- Visualisaties maken met Azure-[dashboards](visualize/tutorial-logs-dashboards.md) en [werkmappen](visualize/workbooks-overview.md).
- Gegevens verzamelen van [bewaakte resources](./monitor-reference.md) met behulp van [metrische gegevens van Azure Monitor](./essentials/data-platform-metrics.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Overzicht

Het volgende diagram geeft u een algemeen beeld van Azure Monitor. In het midden van het diagram vindt u de gegevensopslag voor metrische gegevens en logboeken. Dit zijn de twee basistypen gegevens die door Azure Monitor worden gebruikt. Aan de linkerkant ziet u de [bronnen van de controlegegevens](agents/data-sources.md) waarmee deze [gegevensopslag](data-platform.md) wordt gevuld. Aan de rechterkant bevinden zich verschillende functies die door Azure Monitor worden uitgevoerd met deze verzamelde gegevens. Dit omvat acties als analyse, waarschuwingen en streaming naar externe systemen.

![Overzicht van Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Platform voor het controleren van gegevens

Alle gegevens die worden verzameld door Azure Monitor, kunnen worden onderverdeeld in twee fundamentele typen gegevens, [metrische gegevens en logboeken](data-platform.md). [Metrische gegevens](essentials/data-platform-metrics.md) zijn numeriek waarden waarmee een bepaald aspect van een systeem op een bepaald tijdstip wordt beschreven. Ze zijn licht van gewicht en kunnen in bijna realtime scenario's ondersteunen. [Logboeken](logs/data-platform-logs.md) bevatten verschillende soorten gegevens die zijn ingedeeld in records met verschillende sets eigenschappen voor elk type. Telemetrische gegevens zoals gebeurtenissen en traceringen worden niet alleen opgeslagen als prestatiegegevens maar ook als logboeken, zodat alles kan worden gecombineerd voor analysedoeleinden.

Voor veel Azure-resources worden de gegevens die door Azure Monitor zijn verzameld, rechts op de pagina Overzicht in Azure Portal weergegeven. Als u bijvoorbeeld naar een virtuele machine kijkt, ziet u diverse diagrammen met metrische gegevens over prestaties. Klik op een van de grafieken om de gegevens in [Metrics Explorer](essentials/metrics-charts.md) in Azure Portal weer te geven, zodat u de waarden van meerdere metrische gegevens in de loop van de tijd op de grafiek kunt plaatsen.  U kunt de grafieken interactief weergeven of deze vastmaken aan een dashboard om ze te bekijken met andere visualisaties.

![Diagram met metrische gegevens die naar Metrics Explorer stromen om in visualisaties te worden gebruikt.](media/overview/metrics.png)

De logboekgegevens die door Azure Monitor zijn verzameld, kunnen worden geanalyseerd met [query's](logs/log-query-overview.md) om snel verzamelde gegevens op te halen, samen te voegen en te analyseren.  U kunt query's maken en testen met behulp van [Log Analytics](./logs/log-query-overview.md) in de Azure Portal. U kunt de gegevens vervolgens meteen analyseren met verschillende hulpprogramma's, maar u kunt de query's ook opslaan om deze te gebruiken met [visualisaties](visualizations.md) of [waarschuwingsregels](alerts/alerts-overview.md).

Azure Monitor gebruikt een versie van de [Kusto-querytaal](/azure/kusto/query/) (KQL) die geschikt is voor eenvoudige logboekquery's maar die ook geavanceerde functies bevat, zoals aggregaties, joins en slimme analyse. Via [diverse lessen](logs/get-started-queries.md) kunt u de querytaal snel leren.  Er worden specifieke richtlijnen gegeven voor gebruikers die al bekend zijn met [SQL](/azure/data-explorer/kusto/query/sqlcheatsheet) en [Splunk](/azure/data-explorer/kusto/query/splunk-cheat-sheet).

![Diagram met logboekgegevens die naar Log Analytics stromen voor analyse.](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Welke gegevens worden door Azure Monitor verzameld?

Met Azure Monitor kunt u gegevens verzamelen van [verschillende bronnen](monitor-reference.md). Dit varieert van uw toepassing, alle besturingssystemen en services waarvan het afhankelijk is, tot het platform zelf. Azure Monitor verzamelt gegevens uit elk van de volgende lagen:

- Gegevens over de prestaties en functionaliteit van de code die u hebt geschreven, ongeacht het platform.
- **Controlegegevens van het gastbesturingssysteem**: Gegevens over het besturingssysteem waarop uw toepassing wordt uitgevoerd. Dit kan worden uitgevoerd in Azure, een andere cloud of on-premises. 
- **Bewakingsgegevens van Azure-resources**: Gegevens over de werking van een Azure-resource.
- Gegevens over de werking en het beheer van een Azure-abonnement en gegevens over de status en werking van Azure zelf. 
- **Bewakingsgegevens van Azure-tenants**: Gegevens over de werking van Azure-services op tenantniveau, zoals Azure Active Directory.

Zodra u een Azure-abonnement maakt en resources zoals virtuele machines en web-apps toevoegt, begint Azure Monitor met het verzamelen van gegevens.  [Activiteitenlogboeken](essentials/platform-logs-overview.md) registreren wanneer resources worden gemaakt of gewijzigd. [Metrische gegevens](data-platform.md) vertellen u hoe de resource presteert en welke resources worden gebruikt. 

[Diagnostische gegevens inschakelen](essentials/platform-logs-overview.md) om de gegevens die u verzamelt, uit te breiden naar de interne bewerking van de resources.  [Een agent toevoegen](agents/agents-overview.md) om resources te berekenen voor het verzamelen van telemetriegegevens van hun gastbesturingssystemen. 

Schakel bewaking in voor uw toepassing met [Application Insights](app/app-insights-overview.md) om gedetailleerde informatie te verzamelen, waaronder paginaweergaven, toepassingsaanvragen en uitzonderingen. Controleer de beschikbaarheid van uw toepassing door een [beschikbaarheidstest ](app/monitor-web-app-availability.md) te configureren om gebruikersverkeer te simuleren.

### <a name="custom-sources"></a>Aangepaste bronnen

Azure Monitor kan met behulp van de [Data Collector-API](logs/data-collector-api.md) logboekgegevens verzamelen van elke REST-client. Hierdoor kunt u aangepaste controlescenario's maken en de controle uitbreiden naar resources die geen telemetrie via andere bronnen beschikbaar stellen.

## <a name="insights"></a>Inzichten
Het controleren van gegevens is alleen nuttig als dit meer inzicht biedt in de werking van uw computeromgeving. [Inzichten](monitor-reference.md#insights-and-core-solutions) bieden een aangepaste bewakingservaring voor bepaalde Azure-services. Ze vereisen een minimale configuratie en vergroten uw zicht op de werking van kritieke resources.

### <a name="application-insights"></a>Application Insights
Met [Application Insights](app/app-insights-overview.md) kunt u de beschikbaarheid, prestaties en het gebruik van uw toepassingen controleren, ongeacht of deze worden gehost in de cloud of on-premises. De oplossing maakt gebruik van het krachtige platform voor gegevensanalyse in Azure Monitor om u uitgebreid inzicht te geven in de werking van uw toepassing. Het geeft u de mogelijkheid een diagnose te stellen voor fouten, zonder te wachten tot een gebruiker ze meldt. Application Insights bevat verbindingspunten naar verschillende ontwikkelprogramma's en integreert met Visual Studio om uw DevOps-processen te ondersteunen.

![App Insights](media/overview/app-insights.png)

### <a name="container-insights"></a>Container inzichten
[Container Insights](containers/container-insights-overview.md) bewaakt de prestaties van container werkbelastingen die zijn geïmplementeerd voor beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes service (AKS). Dit geeft u inzicht in de prestaties door via de Metrics API metrische gegevens te verzamelen van controllers, knooppunten en containers die beschikbaar zijn in Kubernetes. Er worden ook containerlogboeken verzameld.  Nadat u de controle van Kubernetes-clusters hebt ingeschakeld, worden deze metrische gegevens en logboeken automatisch voor u verzameld via een containerversie van de Log Analytics-agent voor Linux.

![Containerstatus](media/overview/container-insights.png)

### <a name="vm-insights"></a>VM Insights
Met [VM Insights](vm/vminsights-overview.md) worden uw Azure virtual machines (VM) op schaal gecontroleerd. Het analyseert de prestaties en status van uw Windows- en Linux-VM's en identificeert hun verschillende processen en onderlinge afhankelijkheden voor externe processen. De oplossing bevat ondersteuning voor het controleren van prestatie- en toepassingsafhankelijkheden voor VM's die on-premises of door een andere cloudprovider worden gehost.  


![VM Insights](media/overview/vm-insights.png)


## <a name="responding-to-critical-situations"></a>Reageren op kritieke situaties
Naast de mogelijkheid om controlegegevens interactief te analyseren, moet een efficiënte bewakingsoplossing proactief kunnen reageren op kritieke omstandigheden die in de verzamelde gegevens worden geïdentificeerd. Dit kan bijvoorbeeld door een tekst of e-mailbericht te versturen naar een beheerder die verantwoordelijk is voor het onderzoeken van een probleem. U kunt ook een geautomatiseerd proces starten waarmee wordt geprobeerd een fout te corrigeren.


### <a name="alerts"></a>Waarschuwingen
[Waarschuwingen in Azure Monitor](alerts/alerts-overview.md) stellen u proactief op de hoogte van kritieke omstandigheden en proberen corrigerende maatregelen te nemen. Waarschuwingsregels op basis van metrische gegevens bieden bijna realtime waarschuwingen op basis van numerieke waarden. Regels op basis van logboeken bieden complexe logica voor gegevens uit meerdere bronnen.

Waarschuwingsregels in Azure Monitor gebruiken [actiegroepen](alerts/action-groups.md) Deze actiegroepen bevatten unieke sets ontvangers en acties die kunnen worden gedeeld met meerdere regels. Op basis van uw vereisten kunnen actiegroepen bijvoorbeeld acties uitvoeren met behulp van webhooks, zodat waarschuwingen externe acties starten of worden geïntegreerd met uw ITSM-hulpprogramma's.

![Schermopname met waarschuwingen in Azure Monitor met ernst van de waarschuwing, totaal aantal waarschuwingen en overige informatie.](media/overview/alerts.png)

### <a name="autoscale"></a>Automatisch schalen
Met automatisch schalen kunt u de juiste hoeveelheid resources uitvoeren om de belasting van uw toepassing te verwerken. Maak regels die gebruikmaken van metrische gegevens die worden verzameld door Azure Monitor om te bepalen wanneer resources automatisch moeten worden toegevoegd wanneer de belasting toeneemt. Bespaar geld door resources te verwijderen die niet actief zijn. U geeft een minimum en maximum aantal instanties en de logica voor het vergroten of verkleinen van het aantal resources op.

![Diagram met automatische schaalaanpassing, waarbij verschillende servers op een regel gelabeld voor processortijd > 80% en twee servers die als minimum zijn gemarkeerd, drie servers als huidige capaciteit en vijf als maximum.](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Bewakingsgegevens visualiseren
[Visualisaties](visualizations.md) zoals grafieken en tabellen zijn effectief hulpmiddelen voor het samenvatten van bewakingsgegevens en om deze te presenteren aan verschillende doelgroepen. Azure Monitor beschikt over eigen functies voor het visualiseren van bewakingsgegevens en maakt gebruik van andere Azure-services om de gegevens naar verschillende doelgroepen te publiceren.

### <a name="dashboards"></a>Dashboards
U kunt [Azure-dashboards](../azure-portal/azure-portal-dashboards.md) gebruiken om verschillende soorten gegevens te combineren in één deelvenster in de [Azure Portal](https://portal.azure.com). U kunt het dashboard eventueel delen met andere Azure-gebruikers. Voeg de uitvoer van een logboekquery of metrische grafiek toe aan een Azure-dashboard. U kunt bijvoorbeeld een dashboard maken met een combinatie van verschillende tegels. Bijvoorbeeld tegels waarop een grafiek met metrische gegevens, een tabel met activiteitenlogboeken, een gebruiksdiagram van Application Insights en de uitvoer van een logboekquery worden weergegeven.

![Schermopname met een Azure-Dashboard, met daarop de tegels Toepassing en Beveiliging, en overige aanpasbare informatie.](media/overview/dashboard.png)

### <a name="workbooks"></a>Werkmappen
[Werkmappen](visualize/workbooks-overview.md) bieden een flexibel raamwerk voor gegevensanalyse en het maken van uitgebreide visuele rapporten in de Azure Portal. Ze stellen u in staat om meerdere gegevensbronnen uit Azure aan te boren en deze te combineren tot uniforme interactieve ervaringen. Gebruik werkmappen die zijn meegeleverd met Insights of maak uw eigen werkmappen op basis van vooraf gedefinieerde sjablonen.


![Voorbeeld van werkmappen](media/overview/workbooks.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) is een Business Analytics-service die interactieve visualisaties biedt in diverse gegevensbronnen. Het is een effectieve manier om gegevens beschikbaar te maken voor anderen binnen en buiten uw organisatie. U kunt Power BI zodanig configureren dat er [automatisch logboekgegevens vanuit Azure Monitor worden geïmporteerd](./visualize/powerbi.md) om te profiteren van deze extra visualisaties.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Gegevens integreren en exporteren
U moet Azure Monitor waarschijnlijk vaak integreren met andere systemen en aangepaste oplossingen bouwen die gebruikmaken van uw controlegegevens. Andere Azure-services werken met Azure Monitor om deze integratie te bieden.

### <a name="event-hub"></a>Event Hub
[Azure Event Hubs](../event-hubs/index.yml) is een streamingplatform en service voor gegevensopname van gebeurtenissen. Het kan gegevens omzetten en opslaan met een realtime analytics-provider of batchverwerking-/opslagadapters. Gebruik Event Hubs voor het [streamen van Azure Monitor-gegevens](essentials/stream-monitoring-data-event-hubs.md) naar de SIEM-voorziening van een partner en bewakingshulpprogramma's.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) is een service waarmee u taken en bedrijfsprocessen kunt automatiseren met werkstromen die kunnen worden geïntegreerd met verschillende systemen en services. Er zijn activiteiten beschikbaar die metrische gegevens en logboeken in Azure Monitor lezen en schrijven. Hierdoor kunt u werkstromen bouwen die zijn geïntegreerd met verschillende systemen.


### <a name="api"></a>API
Naast de toegang tot gegenereerde waarschuwingen zijn er meerdere API's beschikbaar voor het lezen en schrijven van metrische gegevens en logboeken van en naar Azure Monitor. U kunt waarschuwingen ook configureren en ophalen. Dit biedt u de onbeperkte mogelijkheden om aangepaste oplossingen te bouwen die met Azure Monitor kunnen worden geïntegreerd.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over:

* [Metrische gegevens en logboeken](https://docs.microsoft.com/azure/azure-monitor/data-platform#metrics) voor de gegevens die worden verzameld door Azure Monitor.
* [Gegevensbronnen](agents/data-sources.md) voor de manier waarop de verschillende onderdelen van uw toepassing telemetrie verzenden.
* [Logboekquery's](logs/log-query-overview.md) voor het analyseren van verzamelde gegevens.
* [Aanbevolen procedures](/azure/architecture/best-practices/monitoring) voor het bewaken van cloudtoepassingen en -services.
