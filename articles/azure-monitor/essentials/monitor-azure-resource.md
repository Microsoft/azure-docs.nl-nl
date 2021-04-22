---
title: Azure-resources bewaken met Azure Monitor | Microsoft Docs
description: Beschrijft hoe u bewakingsgegevens van resources in Azure verzamelt en analyseert met behulp van Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: cb778d826ef094d71fd27f3c10bc1f2c292baa47
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862395"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Azure-resources bewaken met Azure Monitor
Wanneer u kritieke toepassingen en bedrijfsprocessen hebt die afhankelijk zijn van Azure-resources, wilt u deze resources controleren op beschikbaarheid, prestaties en werking. In dit artikel worden de bewakingsgegevens beschreven die worden gegenereerd door Azure-resources en wordt beschreven hoe u de functies van Azure Monitor kunt gebruiken om deze gegevens te analyseren en te waarschuwen.

> [!IMPORTANT]
> Dit artikel is van toepassing op alle services in Azure die gebruikmaken van Azure Monitor. Rekenbronnen, waaronder VM's en App Service, genereren dezelfde bewakingsgegevens die hier worden beschreven, maar hebben ook een gastbesturingssysteem dat ook logboeken en metrische gegevens kan genereren. Zie de bewakingsdocumentatie voor deze services voor meer informatie over het verzamelen en analyseren van deze gegevens.

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?
Azure Monitor is een full stack monitoring-service in Azure die een volledige set functies biedt voor het bewaken van uw Azure-resources naast resources in andere clouds en on-premises. Het [Azure Monitor verzamelt gegevens](../data-platform.md) in logboeken [](../essentials/data-platform-metrics.md) en metrische gegevens waar ze samen kunnen worden geanalyseerd met behulp van een volledige set bewakingshulpprogramma's. [](../logs/data-platform-logs.md) Bekijk de volledige lijst met toepassingen en services die kunnen worden bewaakt door Azure Monitor [in Wat wordt bewaakt door Azure Monitor?](../monitor-reference.md).

Zodra u een Azure-resource maakt, Azure Monitor ingeschakeld en begint met het verzamelen van metrische gegevens en activiteitenlogboeken die u [in](#monitoring-in-the-azure-portal)de Azure Portal. Met een bepaalde configuratie kunt u aanvullende bewakingsgegevens verzamelen en aanvullende functies inschakelen. Zie [Bewakingsgegevens](#monitoring-data) hieronder voor meer informatie over configuratievereisten.


## <a name="costs-associated-with-monitoring"></a>Kosten die zijn gekoppeld aan bewaking
Er zijn geen kosten voor het analyseren van bewakingsgegevens die standaard worden verzameld. Dit omvat het volgende:

- Metrische platformgegevens verzamelen en analyseren met Metrics Explorer.
- Activiteitenlogboek verzamelen en analyseren in de Azure Portal.
- Een waarschuwingsregel voor activiteitenlogboek maken.

Er zijn geen Azure Monitor voor het verzamelen en exporteren van logboeken en metrische gegevens, maar er zijn mogelijk gerelateerde kosten verbonden aan de bestemming:

- Kosten voor gegevensingestie en -retentie bij het verzamelen van logboeken en metrische gegevens in de Log Analytics-werkruimte. Zie [Azure Monitor prijzen voor Log Analytics.](https://azure.microsoft.com/pricing/details/monitor/)
- Kosten voor gegevensopslag bij het verzamelen van logboeken en metrische gegevens naar een Azure-opslagaccount. Zie [Azure Storage prijzen voor blob-opslag.](https://azure.microsoft.com/pricing/details/storage/blobs/)
- Kosten voor event hub-streaming bij het doorsturen van logboeken en metrische gegevens naar Azure Event Hubs. Zie [Azure Event Hubs prijzen.](https://azure.microsoft.com/pricing/details/event-hubs/)

Er kunnen Azure Monitor zijn gekoppeld aan het volgende. Zie [Azure Monitor prijzen:](https://azure.microsoft.com/pricing/details/monitor/)

- Een logboekquery uitvoeren.
- Een waarschuwingsregel voor metrische gegevens of logboekquery's maken.
- Een melding verzenden vanuit een waarschuwingsregel.
- Toegang tot metrische gegevens via API.

## <a name="monitoring-data"></a>Bewakingsgegevens
Resources in Azure genereren [logboeken en](../logs/data-platform-logs.md) [metrische gegevens die](../essentials/data-platform-metrics.md) worden weergegeven in het volgende diagram. Raadpleeg de documentatie voor elke Azure-services voor de specifieke gegevens die ze genereren en eventuele aanvullende oplossingen of inzichten die ze bieden.

![Overzicht](media/monitor-azure-resource/logs-metrics.png)



- [Metrische platformgegevens:](../essentials/data-platform-metrics.md) numerieke waarden die automatisch met regelmatige tussenpozen worden verzameld en die een bepaald aspect van een resource op een bepaald moment beschrijven. 
- [Resourcelogboeken:](./platform-logs-overview.md) geef inzicht in bewerkingen die zijn uitgevoerd binnen een Azure-resource (het gegevensvlak), bijvoorbeeld het verkrijgen van een geheim uit een Key Vault of het indienen van een aanvraag bij een database. De inhoud en structuur van resourcelogboeken variëren per Azure-service en resourcetype.
- [Activiteitenlogboek:](./platform-logs-overview.md) biedt inzicht in de bewerkingen op elke Azure-resource in het abonnement van buitenaf (het beheervlak), bijvoorbeeld het maken van een nieuwe resource of het starten van een virtuele machine. Dit is informatie over wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die worden uitgevoerd voor de resources in uw abonnement.


## <a name="configuration-requirements"></a>Configuratievereisten

### <a name="configure-monitoring"></a>Bewaking configureren
Sommige bewakingsgegevens worden automatisch verzameld, maar mogelijk moet u een bepaalde configuratie uitvoeren, afhankelijk van uw vereisten. Zie de onderstaande informatie voor specifieke informatie voor elk type bewakingsgegevens.

- [Metrische platformgegevens:](../essentials/data-platform-metrics.md) metrische platformgegevens worden automatisch verzameld in [Azure Monitor Metrische](../essentials/data-platform-metrics.md) gegevens zonder dat configuratie is vereist. Maak een diagnostische instelling voor het verzenden van vermeldingen naar Azure Monitor logboeken of om ze buiten Azure door te sturen.
- [Resourcelogboeken:](./platform-logs-overview.md) resourcelogboeken worden automatisch gegenereerd door Azure-resources, maar worden niet verzameld zonder een diagnostische instelling.  Maak een diagnostische instelling voor het verzenden van vermeldingen naar Azure Monitor logboeken of om ze buiten Azure door te sturen.
- [Activiteitenlogboek:](./platform-logs-overview.md) het activiteitenlogboek wordt automatisch verzameld zonder dat configuratie is vereist en kan worden bekeken in de Azure Portal. Maak een diagnostische instelling om ze te kopiëren naar Azure Monitor logboeken of om ze buiten Azure door te geven.

### <a name="log-analytics-workspace"></a>Log Analytics-werkruimte
Voor het verzamelen van gegevens Azure Monitor logboeken is een Log Analytics-werkruimte vereist. U kunt snel beginnen met het bewaken van uw service door een nieuwe werkruimte te maken, maar het kan waardevol zijn om een werkruimte te gebruiken die gegevens verzamelt van andere services. Zie Create a Log Analytics workspace in the Azure Portal (Een Log Analytics-werkruimte maken in de [Azure Portal)](../logs/quick-create-workspace.md) voor meer informatie over het maken van een werkruimte en De implementatie van [uw Azure Monitor-logboeken](../logs/design-logs-deployment.md) ontwerpen om het beste ontwerp voor uw vereisten te bepalen. Als u een bestaande werkruimte in uw organisatie gebruikt, hebt u de juiste machtigingen nodig, zoals beschreven in Toegang tot logboekgegevens en [werkruimten](../logs/manage-access.md)beheren in Azure Monitor . 





## <a name="diagnostic-settings"></a>Diagnostische instellingen
Diagnostische instellingen definiëren waar resourcelogboeken en metrische gegevens voor een bepaalde resource moeten worden verzonden. Mogelijke bestemmingen zijn:

- [Log Analytics-werkruimte](./resource-logs.md#send-to-log-analytics-workspace) waarmee u gegevens kunt analyseren met andere bewakingsgegevens die zijn verzameld door Azure Monitor met behulp van krachtige logboekquery's en ook om gebruik te maken van andere Azure Monitor-functies, zoals logboekwaarschuwingen en visualisaties. 
- [Event Hubs voor](./resource-logs.md#send-to-azure-event-hubs) het streamen van gegevens naar externe systemen, zoals SIEM's van derden en andere log analytics-oplossingen. 
- [Azure Storage-account](./resource-logs.md#send-to-azure-storage) dat handig is voor controle, statische analyse of back-up.

Volg de procedure in [Diagnostische instelling maken voor het verzamelen van platformlogboeken](../essentials/diagnostic-settings.md) en metrische gegevens in Azure om diagnostische instellingen te maken en te beheren via de Azure Portal. Zie [Diagnostische instelling maken in Azure met](./resource-manager-diagnostic-settings.md) behulp van een Resource Manager sjabloon om deze te definiëren in een sjabloon en volledige bewaking voor een resource in teschakelen wanneer deze wordt gemaakt.


## <a name="monitoring-in-the-azure-portal"></a>Bewaking in de Azure Portal
 U hebt toegang tot bewakingsgegevens voor de meeste Azure-resources via het menu van de resource in Azure Portal. Hiermee krijgt u toegang tot de gegevens van één resource met behulp van standard Azure Monitor hulpprogramma's. Sommige Azure-services bieden verschillende opties, dus u moet verwijzen naar de documentatie voor die service voor meer informatie. Gebruik het **Azure Monitor** om gegevens van alle bewaakte resources te analyseren. 

### <a name="overview"></a>Overzicht
Veel services bevatten bewakingsgegevens op de pagina **Overzicht** als een snelle weergave van hun werking. Dit is doorgaans gebaseerd op een subset van metrische gegevens van het platform die zijn opgeslagen in de metrische gegevens van Azure Monitor. Andere bewakingsopties zijn doorgaans beschikbaar in een **sectie Bewaking** van het menu van de service.

![Overzichtspagina](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Inzichten en oplossingen 
Sommige services bieden hulpprogramma's buiten de standaardfuncties van Azure Monitor. [Inzichten bieden](../monitor-reference.md) een aangepaste bewakingservaring die is gebouwd op Azure Monitor gegevensplatform en standaardfuncties. [Oplossingen](../insights/solutions.md) bieden vooraf gedefinieerde bewakingslogica die is gebaseerd op Azure Monitor logboeken. 

Als een service een Azure Monitor heeft, kunt u deze openen via **Bewaking** in het menu van elke resource. Krijg toegang tot alle inzichten en oplossingen via **Azure Monitor** menu.

![Inzichten in de Azure Portal](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Metrische gegevens
Analyseer metrische gegevens in de Azure Portal metrics [explorer](./metrics-getting-started.md) die beschikbaar is via het **menu-item Metrische** gegevens voor de meeste services. Met dit hulpprogramma kunt u werken met afzonderlijke metrische gegevens of meerdere metrische gegevens combineren om correlaties en trends te identificeren. 

- Zie [Aan de slag met Azure Metrics Explorer](./metrics-getting-started.md) basisbeginselen van het gebruik van Metrics Explorer.
- Zie [Geavanceerde functies van Azure Metrics Explorer](../essentials/metrics-charts.md) geavanceerde functies van Metrics Explorer, zoals het gebruik van meerdere metrische gegevens en het toepassen van filters en splitsen.

![Metrics Explorer in de Azure Portal](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Activiteitenlogboek 
Bekijk vermeldingen in het activiteitenlogboek in de Azure Portal met het eerste filter ingesteld op de huidige resource. Kopieer het activiteitenlogboek naar een Log Analytics-werkruimte om het te gebruiken in logboekquery's en werkmappen. 

- Zie [Gebeurtenissen in het Azure-activiteitenlogboek weergeven](../essentials/activity-log.md#view-the-activity-log) en ophalen voor meer informatie over het weergeven van het activiteitenlogboek en het ophalen van vermeldingen met behulp van verschillende methoden.
- Zie de documentatie voor uw Azure-service voor de specifieke gebeurtenissen die worden geregistreerd.

![Activiteitenlogboek](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken
Azure Monitor logboeken consolideert logboeken en metrische gegevens van meerdere services en andere gegevensbronnen voor analyse met een krachtig queryhulpprogramma. Zoals hierboven beschreven, maakt u een diagnostische instelling voor het verzamelen van platformgegevens, activiteitenlogboeken en resourcelogboeken in een Log Analytics-werkruimte in Azure Monitor.

[Met Log Analytics](../logs/log-analytics-tutorial.md) kunt [](../logs/log-query-overview.md)u werken met logboekquery's. Dit is een krachtige functie van Azure Monitor waarmee u geavanceerde analyse van logboekgegevens kunt uitvoeren met behulp van een volledig uitgeruste querytaal. Open Log Analytics vanuit **logboeken** in **het** menu Bewaking voor een Azure-resource om te werken met logboekquery's die gebruikmaken van de resource als [het querybereik.](../logs/scope.md#query-scope) Hiermee kunt u gegevens in meerdere tabellen analyseren voor alleen die resource. Gebruik **Logboeken** uit het Azure Monitor voor toegang tot logboeken voor alle resources. 

- Zie [Aan de slag met logboekquery's in Azure Monitor](../logs/get-started-queries.md) voor een zelfstudie over het gebruik van de querytaal die wordt gebruikt om logboekquery's te schrijven.
- Zie [Azure-resourcelogboeken verzamelen in de Log Analytics-werkruimte in Azure Monitor](./resource-logs.md#send-to-log-analytics-workspace) voor informatie over hoe resourcelogboeken worden verzameld in Azure Monitor-logboeken en informatie over hoe u ze kunt openen in een query.
- Zie [Verzamelingsmodus](./resource-logs.md#send-to-log-analytics-workspace) voor een uitleg over hoe resourcelogboekgegevens zijn gestructureerd in Azure Monitor logboeken.
- Zie de documentatie voor elke Azure-service voor meer informatie over de tabel in Azure Monitor Logboeken.

![Log Analytics in de Azure Portal](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Bewaking vanaf de opdrachtregel
U kunt toegang krijgen tot bewakingsgegevens die zijn verzameld van uw resource vanaf een opdrachtregel of opnemen in een script met [behulp van Azure PowerShell](/powershell/azure/) of Azure Command Line [Interface](/cli/azure/). 

- Zie [Referentie voor metrische GEGEVENS van CLI](/cli/azure/monitor/metrics) voor toegang tot metrische gegevens vanuit CLI.
- Zie [Naslaginformatie over CLI-logboekanalyse](/cli/azure/monitor/log-analytics) voor toegang Azure Monitor logboekgegevens met behulp van een logboekquery van CLI.
- Zie [Azure PowerShell metrische gegevens voor](/powershell/module/azurerm.insights/get-azurermmetric) toegang tot metrische gegevens van Azure PowerShell.
- Zie [Azure PowerShell logboekquery voor](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) toegang tot de logboekgegevens Azure Monitor logboeken met behulp van een logboekquery van Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Bewaking vanaf REST API
Neem bewakingsgegevens op die zijn verzameld van uw resource in een aangepaste toepassing met behulp van een REST API.

- Zie [Azure Monitoring REST API walkthrough](./rest-api-walkthrough.md) voor meer informatie over het openen van metrische gegevens uit Azure Monitor REST API.
- Zie [Azure Log Analytics REST API](https://dev.loganalytics.io/) voor meer informatie over het openen Azure Monitor logboekgegevens met behulp van een logboekquery van Azure PowerShell.

## <a name="alerts"></a>Waarschuwingen
[Waarschuwingen](../alerts/alerts-overview.md) stellen u proactief op de hoogte en ondernemen mogelijk actie wanneer er belangrijke voorwaarden in uw bewakingsgegevens worden gevonden. U maakt een waarschuwingsregel die een doel voor de waarschuwing definieert, de voorwaarden voor het maken van een waarschuwing en eventuele acties die als reactie moeten worden ondernomen.

Er worden verschillende soorten bewakingsgegevens gebruikt voor verschillende soorten waarschuwingsregels.

- [Waarschuwing voor](../alerts/alerts-activity-log.md) activiteitenlogboek: maak een waarschuwing wanneer er een vermelding wordt gemaakt in het activiteitenlogboek die aan bepaalde criteria voldoet. Hiermee kunt u bijvoorbeeld een melding ontvangen wanneer een bepaald type resource wordt gemaakt of als een configuratiewijziging mislukt.
- [Waarschuwing voor metrische](../alerts/alerts-metric.md) gegevens: maak een waarschuwing wanneer een metrische waarde een bepaalde drempelwaarde overschrijdt. Waarschuwingen voor metrische gegevens zijn responsiefer dan andere waarschuwingen en kunnen automatisch worden opgelost wanneer het probleem wordt opgelost.
- [Waarschuwing voor logboekquery:](../alerts/alerts-log.md) voer regelmatig een logboekquery uit en maak een waarschuwing als er een bepaalde voorwaarde wordt gevonden. Hiermee kunt u complexe analyses uitvoeren op meerdere gegevenssets en .

Gebruik **Waarschuwingen** in het menu van een resource om waarschuwingen weer te geven en waarschuwingsregels voor die resource te beheren. Alleen waarschuwingen voor activiteitenlogboek en waarschuwingen voor metrische gegevens gebruiken afzonderlijke Azure-resources als doel. Logboekquerywaarschuwingen gebruiken een Log Analytics-werkruimte als doel en zijn gebaseerd op een query die toegang heeft tot logboeken die zijn opgeslagen in die werkruimte. Gebruik het Azure Monitor voor het weergeven en beheren van waarschuwingen voor alle resources en de waarschuwingsregels voor het beheren van logboekquery's.

- Zie de artikelen voor de verschillende soorten waarschuwingen hierboven voor meer informatie over het maken van waarschuwingsregels.
- Zie [Actiegroepen maken en beheren in Azure Portal](../alerts/action-groups.md) voor meer informatie over het maken van een actiegroep waarmee u reacties op waarschuwingen kunt beheren.



## <a name="next-steps"></a>Volgende stappen

* Zie [Ondersteunde services, schema's en categorieën voor Azure-resourcelogboeken](./resource-logs-schema.md) voor meer informatie over resourcelogboeken voor verschillende Azure-services.
