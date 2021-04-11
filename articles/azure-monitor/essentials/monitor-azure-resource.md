---
title: Azure-resources bewaken met Azure Monitor | Microsoft Docs
description: Hierin wordt beschreven hoe u bewakings gegevens van resources in azure verzamelt en analyseert met behulp van Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 203af340a8bd48bdb6dee70f92c2ecc39708b8e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732326"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Azure-resources bewaken met Azure Monitor
Wanneer u belang rijke toepassingen en bedrijfs processen hebt die afhankelijk zijn van Azure-resources, wilt u deze resources controleren op hun Beschik baarheid, prestaties en werking. In dit artikel worden de bewakings gegevens beschreven die worden gegenereerd door Azure-resources en hoe u de functies van Azure Monitor kunt gebruiken om deze gegevens te analyseren en te waarschuwen.

> [!IMPORTANT]
> Dit artikel is van toepassing op alle services in azure die gebruikmaken van Azure Monitor. Reken bronnen, met inbegrip van Vm's en App Service, genereren dezelfde bewakings gegevens die hier worden beschreven, maar ook een gast besturingssysteem dat ook logboeken en metrieken kan genereren. Zie de documentatie over het controleren van deze services voor meer informatie over het verzamelen en analyseren van deze gegevens.

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?
Azure Monitor is een volledige stack monitoring-service in azure met een volledige set functies voor het bewaken van uw Azure-resources naast bronnen in andere Clouds en on-premises. Het [Azure monitor-gegevens platform](../data-platform.md) verzamelt gegevens in [Logboeken](../logs/data-platform-logs.md) en [meet waarden](../essentials/data-platform-metrics.md) waar ze kunnen worden geanalyseerd met behulp van een volledige set controle hulpprogramma's. Bekijk de volledige lijst met toepassingen en services die kunnen worden bewaakt door Azure Monitor op [wat door Azure monitor wordt bewaakt?](../monitor-reference.md).

Zodra u een Azure-resource maakt, wordt Azure Monitor ingeschakeld en worden er metrische gegevens en activiteiten logboeken verzameld die u [in de Azure Portal kunt bekijken en analyseren](#monitoring-in-the-azure-portal). Met een configuratie kunt u extra bewakings gegevens verzamelen en aanvullende functies inschakelen. Zie de onderstaande [bewakings gegevens](#monitoring-data) voor meer informatie over de configuratie vereisten.


## <a name="costs-associated-with-monitoring"></a>Kosten die zijn gekoppeld aan bewaking
Er zijn geen kosten voor het analyseren van bewakings gegevens die standaard worden verzameld. Dit omvat het volgende:

- De metrische gegevens van het platform verzamelen en analyseren met metrische gegevens Verkenner.
- Het activiteiten logboek verzamelen en analyseren in het Azure Portal.
- Een waarschuwings regel voor het activiteiten logboek maken.

Er zijn geen Azure Monitor kosten voor het verzamelen en exporteren van Logboeken en metrische gegevens, maar er zijn mogelijk gerelateerde kosten verbonden aan het doel:

- Kosten die zijn gekoppeld aan gegevens opname en-retentie bij het verzamelen van Logboeken en metrieken in Log Analytics werk ruimte. Zie [Azure monitor prijzen voor log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Kosten die zijn gekoppeld aan gegevens opslag bij het verzamelen van Logboeken en metrieken voor een Azure-opslag account. Zie [Azure Storage prijzen voor Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Kosten die zijn gekoppeld aan Event Hub streaming bij het door sturen van Logboeken en metrische gegevens naar Azure Event Hubs. Zie [prijzen voor Azure Event hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Er zijn mogelijk Azure Monitor kosten gekoppeld aan het volgende. Zie [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/):

- Een logboek query uitvoeren.
- Er wordt een waarschuwings regel voor metrische gegevens of logboek query's gemaakt.
- Er wordt een melding verzonden vanuit elke waarschuwings regel.
- Toegang tot metrische gegevens via API.

## <a name="monitoring-data"></a>Bewakingsgegevens
Resources in azure genereren [Logboeken](../logs/data-platform-logs.md) en [metrische gegevens](../essentials/data-platform-metrics.md) die in het volgende diagram worden weer gegeven. Raadpleeg de documentatie voor elke Azure-service voor de specifieke gegevens die ze genereren en eventuele aanvullende oplossingen of inzichten die ze bieden.

![Overzicht](media/monitor-azure-resource/logs-metrics.png)



- [Platform metrieken](../essentials/data-platform-metrics.md) : numerieke waarden die automatisch met regel matige tussen pozen worden verzameld en een zekere hoogte van een resource op een bepaald moment beschrijven. 
- [Bron logboeken](./platform-logs-overview.md) : bieden inzicht in bewerkingen die zijn uitgevoerd in een Azure-resource (het gegevens vlak), bijvoorbeeld het verkrijgen van een geheim van een Key Vault of het maken van een aanvraag voor een Data Base. De inhoud en structuur van bron logboeken zijn afhankelijk van de Azure-service en het resource type.
- [Activiteiten logboek](./platform-logs-overview.md) : biedt inzicht in de bewerkingen op elke Azure-resource in het abonnement van buiten (het beheer vlak), bijvoorbeeld het maken van een nieuwe resource of het starten van een virtuele machine. Hier vindt u informatie over de functies wat, wie en wanneer u schrijf bewerkingen (PUT, POST, DELETE) hebt uitgevoerd voor de resources in uw abonnement.


## <a name="configuration-requirements"></a>Configuratievereisten

### <a name="configure-monitoring"></a>Bewaking configureren
Sommige bewakings gegevens worden automatisch verzameld, maar u moet mogelijk een configuratie uitvoeren, afhankelijk van uw vereisten. Zie de onderstaande informatie voor specifieke informatie voor elk type bewakings gegevens.

- [Platform metrieken](../essentials/data-platform-metrics.md) : platform metrieken worden automatisch verzameld in [Azure monitor metrieken](../essentials/data-platform-metrics.md) zonder configuratie vereist. Maak een diagnostische instelling voor het verzenden van vermeldingen naar Azure Monitor Logboeken of om ze buiten Azure door te sturen.
- [Resource logboeken](./platform-logs-overview.md) : resource logboeken worden automatisch gegenereerd door Azure-resources, maar niet zonder een diagnostische instelling.  Maak een diagnostische instelling voor het verzenden van vermeldingen naar Azure Monitor Logboeken of om ze buiten Azure door te sturen.
- [Activiteiten logboek](./platform-logs-overview.md) : het activiteiten logboek wordt automatisch verzameld zonder vereiste configuratie en kan worden weer gegeven in de Azure Portal. Een diagnostische instelling maken om deze te kopiëren naar Azure Monitor Logboeken of om ze buiten Azure door te sturen.

### <a name="log-analytics-workspace"></a>Log Analytics-werkruimte
Voor het verzamelen van gegevens in Azure Monitor Logboeken is een Log Analytics-werk ruimte vereist. U kunt snel aan de slag met het bewaken van uw service door een nieuwe werk ruimte te maken, maar er kan ook een waarde zijn in het gebruik van een werk ruimte die gegevens uit andere services verzamelt. Zie [een log Analytics-werk ruimte maken in de Azure Portal](../logs/quick-create-workspace.md) voor meer informatie over het maken van een werk ruimte en [het ontwerpen van de implementatie van uw Azure monitor-logboeken](../logs/design-logs-deployment.md) om het beste ontwerp van de werk ruimte voor uw vereisten te bepalen. Als u een bestaande werk ruimte in uw organisatie gebruikt, hebt u de juiste machtigingen nodig, zoals wordt beschreven in [toegang beheren tot logboek gegevens en werk ruimten in azure monitor](../logs/manage-access.md). 





## <a name="diagnostic-settings"></a>Diagnostische instellingen
Diagnostische instellingen bepalen waar bron logboeken en metrische gegevens voor een bepaalde resource moeten worden verzonden. Mogelijke bestemmingen zijn:

- [Log Analytics werk ruimte](./resource-logs.md#send-to-log-analytics-workspace) waarmee u gegevens kunt analyseren met andere bewakings gegevens die worden verzameld door Azure monitor met behulp van krachtige logboek query's en ook voor het gebruik van andere Azure monitor functies, zoals logboek waarschuwingen en visualisaties. 
- [Event hubs](./resource-logs.md#send-to-azure-event-hubs) voor het streamen van gegevens naar externe systemen, zoals siem's van derden en andere log Analytics-oplossingen. 
- [Azure Storage-account](./resource-logs.md#send-to-azure-storage) dat nuttig is voor controle, statische analyses of back-ups.

Volg de procedure in [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../essentials/diagnostic-settings.md) voor het maken en beheren van diagnostische instellingen via de Azure Portal. Zie [Diagnostische instelling maken in azure met behulp van een resource manager-sjabloon](./resource-manager-diagnostic-settings.md) om deze in een sjabloon te definiëren en volledige bewaking in te scha kelen voor een resource wanneer deze wordt gemaakt.


## <a name="monitoring-in-the-azure-portal"></a>Bewaking in de Azure Portal
 U hebt toegang tot bewakings gegevens voor de meeste Azure-resources vanuit het menu van de resource in de Azure Portal. Hiermee krijgt u toegang tot de gegevens van één resource met behulp van standaard Azure Monitor-hulpprogram ma's. Sommige Azure-Services bieden andere opties, dus u moet de documentatie voor die service raadplegen voor aanvullende informatie. Gebruik het menu **Azure monitor** om gegevens van alle bewaakte resources te analyseren. 

### <a name="overview"></a>Overzicht
Veel services bevatten bewakingsgegevens op de pagina **Overzicht** als een snelle weergave van hun werking. Dit is doorgaans gebaseerd op een subset van metrische gegevens van het platform die zijn opgeslagen in de metrische gegevens van Azure Monitor. Andere bewakings opties zijn doorgaans beschikbaar in een **bewakings** sectie van het menu van de service.

![Overzichtspagina](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Inzichten en oplossingen 
Sommige services bieden nog meer hulp middelen dan de standaard functies van Azure Monitor. [Inzichten](../monitor-reference.md) bieden een aangepaste bewakings ervaring die is gebaseerd op het Azure monitor gegevens platform en de standaard functies. [Oplossingen](../insights/solutions.md) bieden vooraf gedefinieerde bewakings logica die is gebaseerd op Azure monitor Logboeken. 

Als een service een Azure Monitor inzicht heeft, kunt u deze openen vanuit **bewaking** in elk resource menu. Toegang tot alle inzichten en oplossingen vanuit het **Azure monitor** menu.

![Inzichten in de Azure Portal](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Metrische gegevens
Analyseer de metrische gegevens in de Azure Portal met behulp van [Metrics Explorer](./metrics-getting-started.md) , die beschikbaar is via de menu opdracht **metrische gegevens** voor de meeste services. Met dit hulp programma kunt u met afzonderlijke metrische gegevens werken of meerdere combi neren om correlaties en trends te identificeren. 

- Zie [aan de slag met Azure Metrics Explorer](./metrics-getting-started.md) voor de basis principes van het gebruik van metrische gegevens Verkenner.
- Zie [geavanceerde functies van Azure Metrics Explorer](../essentials/metrics-charts.md) voor geavanceerde functies van de Verkenner voor metrische gegevens, zoals het gebruik van meerdere metrische gegevens en het Toep assen van filters en splitsen.

![Metrics Explorer in de Azure Portal](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Activiteitenlogboek 
Vermeldingen in het activiteiten logboek weer geven in de Azure Portal met het eerste filter dat is ingesteld op de huidige resource. Kopieer het activiteiten logboek naar een Log Analytics werk ruimte om het te openen om het te gebruiken in logboek query's en-werkmappen. 

- Zie activiteiten [logboek gebeurtenissen van Azure weer geven en ophalen](../essentials/activity-log.md#view-the-activity-log) voor meer informatie over het weer geven van het activiteiten logboek en het ophalen van vermeldingen op basis van verschillende methoden.
- Raadpleeg de documentatie voor uw Azure-service voor de specifieke gebeurtenissen die worden vastgelegd in het logboek.

![Activiteitenlogboek](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken
Met Azure Monitor logboeken worden logboeken en metrische gegevens van meerdere services en andere data bronnen voor analyse geconsolideerd met een krachtig query programma. Zoals hierboven beschreven, maakt u een diagnostische instelling voor het verzamelen van metrische gegevens van het platform, het activiteiten logboek en bron Logboeken in een Log Analytics-werk ruimte in Azure Monitor.

Met [log Analytics](../logs/log-analytics-tutorial.md) kunt u werken met [logboek query's](../logs/log-query-overview.md). Dit is een krachtige functie van Azure monitor waarmee u een geavanceerde analyse van logboek gegevens kunt uitvoeren met behulp van een volledig aanbevolen query taal. Open Log Analytics van **Logboeken** in het **controle** menu voor een Azure-resource om te werken met logboek query's met behulp van de resource als [query bereik](../logs/scope.md#query-scope). Zo kunt u gegevens analyseren over meerdere tabellen voor deze resource. Gebruik **Logboeken** in het menu Azure monitor om toegang te krijgen tot logboeken voor alle resources. 

- Zie aan de [slag met logboek query's in azure monitor](../logs/get-started-queries.md) voor een zelf studie over het gebruik van de query taal die wordt gebruikt om logboek query's te schrijven.
- Zie [Azure-resource logboeken verzamelen in log Analytics werk ruimte in azure monitor](./resource-logs.md#send-to-log-analytics-workspace) voor informatie over hoe bron logboeken worden verzameld in azure monitor logboeken en Details over hoe u ze in een query kunt openen.
- Zie [Verzamel modus](./resource-logs.md#send-to-log-analytics-workspace) voor een uitleg van hoe bron logboek gegevens worden gestructureerd in azure monitor Logboeken.
- Raadpleeg de documentatie voor elke Azure-service voor meer informatie over de tabel in Azure Monitor logs.

![Log Analytics in de Azure Portal](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Bewaking vanaf opdracht regel
U hebt toegang tot bewakings gegevens die vanuit uw resource zijn verzameld vanaf een opdracht regel of worden opgenomen in een script met behulp van [Azure PowerShell](/powershell/azure/) of de [Azure-opdracht regel interface](/cli/azure/). 

- Zie [cli-metrische](/cli/azure/monitor/metrics) gegevens voor informatie over het openen van metrieken van CLI.
- Zie [cli log Analytics Reference](/cli/azure/ext/log-analytics/monitor/log-analytics) voor toegang tot gegevens in azure monitor logboeken met behulp van een logboek query van CLI.
- Zie de [Naslag informatie over Azure PowerShell metrieken](/powershell/module/azurerm.insights/get-azurermmetric) voor het openen van metrische gegevens uit Azure PowerShell.
- Zie [Azure PowerShell Naslag informatie over logboek query's](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) voor toegang tot gegevens in azure monitor logboeken met behulp van een logboek query van Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Bewaking vanaf REST API
Bewakings gegevens die zijn verzameld uit uw resource in een aangepaste toepassing toevoegen met behulp van een REST API.

- Zie [Azure Monitoring rest API-overzicht](./rest-api-walkthrough.md) voor meer informatie over het openen van metrische gegevens via de Azure monitor rest API.
- Zie [Azure Log Analytics rest API](https://dev.loganalytics.io/) voor informatie over het openen van Azure monitor logboek gegevens met behulp van een logboek query van Azure PowerShell.

## <a name="alerts"></a>Waarschuwingen
[Waarschuwingen](../alerts/alerts-overview.md) geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. U maakt een waarschuwings regel die een doel voor de waarschuwing definieert, de voor waarden om te bepalen of er een waarschuwing moet worden gemaakt en welke acties moeten worden uitgevoerd als reactie.

Verschillende soorten bewakings gegevens worden gebruikt voor verschillende soorten waarschuwings regels.

- [Waarschuwing voor activiteiten logboek](../alerts/alerts-activity-log.md) -een waarschuwing maken wanneer er een vermelding in het activiteiten logboek wordt gemaakt die overeenkomt met bepaalde criteria. Zo kunt u een melding ontvangen wanneer een bepaald type resource wordt gemaakt of een configuratie wijziging mislukt.
- [Metrische waarschuwing](../alerts/alerts-metric.md) : een waarschuwing maken wanneer een metrische waarde een bepaalde drempel overschrijdt. Metrische waarschuwingen zijn meer reageren dan andere waarschuwingen en kunnen automatisch worden omgezet wanneer het probleem is opgelost.
- [Logboek query waarschuwing](../alerts/alerts-log.md) : Voer regel matig een logboek query uit en maak een waarschuwing als een bepaalde voor waarde wordt gevonden. Zo kunt u complexe analyses uitvoeren op meerdere gegevens sets en.

**Waarschuwingen** gebruiken in het menu van een resource om waarschuwingen weer te geven en waarschuwings regels voor die resource te beheren. Alleen waarschuwingen voor activiteiten logboeken en metrische waarschuwingen gebruiken afzonderlijke Azure-resources als doel. Waarschuwingen voor logboek query's maken gebruik van een Log Analytics werkruimte als doel en zijn gebaseerd op een query die toegang heeft tot de logboeken die zijn opgeslagen in die werk ruimte. Gebruik het menu Azure Monitor om waarschuwingen voor alle resources en de waarschuwings regels voor het beheren van logboek query's weer te geven en te beheren.

- Zie de artikelen voor de verschillende soorten waarschuwingen hierboven voor meer informatie over het maken van waarschuwings regels.
- Zie [actie groepen maken en beheren in de Azure Portal](../alerts/action-groups.md) voor meer informatie over het maken van een actie groep waarmee u antwoorden op waarschuwingen kunt beheren.



## <a name="next-steps"></a>Volgende stappen

* Zie [ondersteunde services, schema's en categorieën voor Azure-resource logboeken](./resource-logs-schema.md) voor meer informatie over resource logboeken voor verschillende Azure-Services.
