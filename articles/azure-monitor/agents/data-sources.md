---
title: Gegevens bronnen in Azure Monitor | Microsoft Docs
description: Beschrijft de gegevens die beschikbaar zijn voor het bewaken van de status en prestaties van uw Azure-resources en de toepassingen die erop worden uitgevoerd.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/06/2020
ms.openlocfilehash: 537163b4a78954443eeb7799dc624837ba03e2bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036619"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Bronnen van bewakings gegevens voor Azure Monitor
Azure Monitor is gebaseerd op een [Algemeen bewakings gegevensplatform](../data-platform.md) dat [Logboeken](../logs/data-platform-logs.md) en [metrische](../essentials/data-platform-metrics.md)gegevens bevat. Door gegevens te verzamelen in dit platform kunnen gegevens uit meerdere bronnen worden geanalyseerd met behulp van een gemeen schappelijke set hulpprogram ma's in Azure Monitor. Bewakings gegevens kunnen ook worden verzonden naar andere locaties ter ondersteuning van bepaalde scenario's en sommige resources kunnen naar andere locaties schrijven voordat ze kunnen worden verzameld in Logboeken of metrische gegevens.

In dit artikel worden de verschillende bronnen beschreven van bewakings gegevens die zijn verzameld door Azure Monitor naast de bewakings gegevens die zijn gemaakt door Azure-resources. Er zijn koppelingen naar gedetailleerde informatie over de configuratie die is vereist voor het verzamelen van deze gegevens op verschillende locaties.

## <a name="application-tiers"></a>Toepassingslagen

Bronnen van bewakings gegevens van Azure-toepassingen kunnen worden ingedeeld in lagen, de hoogste lagen van uw toepassing zelf en de lagere lagen die onderdeel zijn van het Azure-platform. De methode voor het openen van gegevens van elke laag varieert. De toepassings lagen worden in de onderstaande tabel samengebracht en de bronnen van bewakings gegevens in elke laag worden weer gegeven in de volgende secties. Zie [gegevens locaties bewaken in azure](../monitor-reference.md) voor een beschrijving van elke gegevens locatie en hoe u toegang kunt krijgen tot de gegevens.


![Bewakings lagen](../media/overview/overview.png)


### <a name="azure"></a>Azure
De volgende tabel bevat een korte beschrijving van de toepassings lagen die specifiek zijn voor Azure. Volg de onderstaande koppeling voor meer informatie over elk van de onderstaande secties.

| Laag | Beschrijving | Verzamel methode |
|:---|:---|:---|
| [Azure-Tenant](#azure-tenant) | Gegevens over de werking van Azure-services op tenant-niveau, zoals Azure Active Directory. | Bekijk AAD-gegevens in de portal of configureer de verzameling op Azure Monitor met behulp van een diagnostische instelling voor tenants. |
| [Azure-abonnement](#azure-subscription) | Gegevens met betrekking tot de status en het beheer van cross-resource Services in uw Azure-abonnement, zoals Resource Manager en Service Health. | Weer geven in portal of verzameling configureren voor Azure Monitor met behulp van een logboek profiel. |
| [Azure-resources](#azure-resources) |  Gegevens over de werking en prestaties van elke Azure-resource. | Gegevens die automatisch worden verzameld, worden weer gegeven in Metrics Explorer.<br>Diagnostische instellingen configureren voor het verzamelen van Logboeken in Azure Monitor.<br>Bewakings oplossingen en inzichten die beschikbaar zijn voor gedetailleerde bewaking van specifieke resource typen. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, andere Cloud of on-premises 
De volgende tabel bevat een korte beschrijving van de toepassings lagen in azure, een andere Cloud of on-premises. Volg de onderstaande koppeling voor meer informatie over elk van de onderstaande secties.

| Laag | Beschrijving | Verzamel methode |
|:---|:---|:---|
| [Besturings systeem (gast)](#operating-system-guest) | Gegevens over het besturings systeem op reken resources. | Installeer Log Analytics agent voor het verzamelen van client gegevens bronnen in Azure Monitor en een afhankelijkheids agent voor het verzamelen van afhankelijkheden die VM Insights ondersteunen.<br>Voor virtuele machines van Azure installeert u de diagnostische Azure-extensie voor het verzamelen van Logboeken en metrische gegevens in Azure Monitor. |
| [Toepassings code](#application-code) | Gegevens over de prestaties en functionaliteit van de daad werkelijke toepassing en code, met inbegrip van prestatie traceringen, toepassings logboeken en telemetrie van de gebruiker. | Instrumenteer uw code voor het verzamelen van gegevens in Application Insights. |
| [Aangepaste bronnen](#custom-sources) | Gegevens van externe services of andere onderdelen of apparaten. | Verzamelen van logboek-of metrische gegevens in Azure Monitor van elke REST-client. |

## <a name="azure-tenant"></a>Azure-tenant
Telemetrie die betrekking heeft op uw Azure-Tenant wordt verzameld van services voor de hele Tenant, zoals Azure Active Directory.

![Azure-Tenant verzameling](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Controle logboeken Azure Active Directory
[Azure Active Directory rapportage](../../active-directory/reports-monitoring/overview-reports.md) bevat de geschiedenis van de aanmeldings activiteit en de audittrail van wijzigingen die zijn aangebracht in een bepaalde Tenant. 

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Azure Monitor-logboeken | Configureer de Azure AD-logboeken die moeten worden verzameld in Azure Monitor om ze te analyseren met andere bewakings gegevens. | [Azure AD-logboeken integreren met Azure Monitor-Logboeken (preview-versie)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Exporteer Azure AD-logboeken naar Azure Storage voor archivering. | [Zelfstudie: Azure AD-logboeken archiveren in een Azure-opslagaccount (preview)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Event Hub | Stream Azure AD-logboeken naar andere locaties met Event Hubs. | [Zelf studie: Stream Azure Active Directory logboeken naar een Azure Event hub (preview)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Azure-abonnement
Telemetrie met betrekking tot de status en de werking van uw Azure-abonnement.

![Azure-abonnement](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure-activiteitenlogboek 
Het [Azure-activiteiten logboek](../essentials/platform-logs-overview.md) bevat service status records samen met records in configuratie wijzigingen die zijn aangebracht in de resources in uw Azure-abonnement. Het activiteiten logboek is beschikbaar voor alle Azure-resources en vertegenwoordigt hun _externe_ weer gave.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|
| Activiteitenlogboek | Het activiteiten logboek wordt verzameld in het eigen gegevens archief dat u kunt weer geven in het menu Azure Monitor of gebruiken om waarschuwingen in het activiteiten logboek te maken. | [Query's uitvoeren op het activiteiten logboek in de Azure Portal](../essentials/activity-log.md#view-the-activity-log) |
| Azure Monitor-logboeken | Configureer Azure Monitor Logboeken om het activiteiten logboek te verzamelen om het te analyseren met andere bewakings gegevens. | [Azure-activiteiten Logboeken in Log Analytics werk ruimte in Azure Monitor verzamelen en analyseren](../essentials/activity-log.md) |
| Azure Storage | Exporteer het activiteiten logboek naar Azure Storage voor het archiveren. | [Activiteiten logboek archiveren](../essentials/resource-logs.md#send-to-azure-storage)  |
| Event Hubs | Het activiteiten logboek streamen naar andere locaties met Event Hubs | [Activiteiten logboek streamen naar Event hub](../essentials/resource-logs.md#send-to-azure-event-hubs). |

### <a name="azure-service-health"></a>Azure Service Health
[Azure service Health](../../service-health/service-health-overview.md) biedt informatie over de status van de Azure-Services in uw abonnement waarvan uw toepassing en resources afhankelijk zijn.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Activiteitenlogboek<br>Azure Monitor-logboeken | Service Health Records worden opgeslagen in het Azure-activiteiten logboek, zodat u ze kunt weer geven in de Azure Portal of andere activiteiten kunt uitvoeren die u met het activiteiten logboek uitvoert. | [Servicestatusmeldingen bekijken met de Azure-portal](../../service-health/service-notifications.md) |


## <a name="azure-resources"></a>Azure-resources
Metrische gegevens en bron logboeken bevatten informatie over de _interne_ werking van Azure-resources. Deze zijn beschikbaar voor de meeste Azure-Services en bewakings oplossingen en inzichten verzamelen extra gegevens voor bepaalde services.

![Azure-resource verzameling](media/data-sources/data-source-azure-resources.svg)


### <a name="platform-metrics"></a>Metrische platformgegevens 
De meeste Azure-Services verzenden [platform metrieken](../essentials/data-platform-metrics.md) die de prestaties en bewerking rechtstreeks aan de data base met metrische gegevens weer geven. De specifieke [metrische gegevens zijn afhankelijk van elk type resource](../essentials/metrics-supported.md). 

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Metrische gegevens van Azure Monitor | De metrische gegevens van het platform worden geschreven naar de data base van de Azure Monitor metriek zonder configuratie. Toegang krijgen tot platform gegevens van Metrics Explorer.  | [Aan de slag met Azure Metrics Explorer](../essentials/metrics-getting-started.md)<br>[Ondersteunde metrische gegevens met Azure Monitor](../essentials/metrics-supported.md) |
| Azure Monitor-logboeken | Platform metrieken kopiëren naar Logboeken voor trending en andere analyse met behulp van Log Analytics. | [Diagnostische gegevens van Azure rechtstreeks naar Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Meet gegevens streamen naar andere locaties met behulp van Event Hubs. |[Azure-bewakings gegevens streamen naar een Event Hub voor gebruik door een extern hulp programma](../essentials/stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Resourcelogboeken
[Bron logboeken](../essentials/platform-logs-overview.md) bieden inzicht in de _interne_ werking van een Azure-resource.  Bron logboeken worden automatisch gemaakt, maar u moet een diagnostische instelling maken om een bestemming op te geven die voor elke resource moet worden verzameld.

De configuratie vereisten en de inhoud van bron logboeken variëren per bron type en niet alle services maken ze nog. Zie [ondersteunde services, schema's en categorieën voor Azure-resource logboeken](../essentials/resource-logs-schema.md) voor meer informatie over elke service en koppelingen naar gedetailleerde configuratie procedures. Als de service niet wordt vermeld in dit artikel, maakt deze service momenteel geen resource Logboeken.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Azure Monitor-logboeken | Resource logboeken verzenden naar Azure Monitor-logboeken voor analyse met andere verzamelde logboek gegevens. | [Azure-resource logboeken verzamelen in Log Analytics werk ruimte in Azure Monitor](../essentials/resource-logs.md#send-to-azure-storage) |
| Storage | Resource logboeken verzenden naar Azure Storage voor archiveren. | [Azure-resource logboeken archiveren](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Bron logboeken streamen naar andere locaties met Event Hubs. |[Azure-resource logboeken streamen naar een Event Hub](../essentials/resource-logs.md#send-to-azure-event-hubs) |

## <a name="operating-system-guest"></a>Besturings systeem (gast)
Reken resources in azure, in andere Clouds en on-premises, hebben een gast besturingssysteem dat kan worden bewaakt. Met de installatie van een of meer agents kunt u telemetrie van de gast in Azure Monitor verzamelen om deze te analyseren met dezelfde controle hulpprogramma's als de Azure-Services zelf.

![Azure Compute-resource verzameling](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Diagnostische Azure-extensie
Door de Azure Diagnostics-extensie voor virtuele Azure-machines in te scha kelen, kunt u Logboeken en metrische gegevens verzamelen uit het gast besturingssysteem van Azure Compute-resources, inclusief de web-en werk rollen van Azure Cloud service (klassiek), Virtual Machines, schaal sets voor virtuele machines en Service Fabric.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Storage | De Azure Diagnostics-extensie schrijft altijd naar een Azure Storage-account. | [De Windows Azure Diagnostics-extensie (WAD) installeren en configureren](./diagnostics-extension-windows-install.md)<br>[De diagnostische Linux-extensie gebruiken voor het bewaken van metrische gegevens en logboeken](../../virtual-machines/extensions/diagnostics-linux.md) |
| Metrische gegevens van Azure Monitor | Wanneer u de uitbrei ding voor diagnostische gegevens configureert voor het verzamelen van prestatie meter items, worden deze geschreven naar de data base van de Azure Monitor metrische gegevens. | [Metrische gegevens van het gast besturingssysteem naar het Azure Monitor metrische archief verzenden met een resource manager-sjabloon voor een virtuele Windows-machine](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) |
| Event Hubs | Configureer de diagnostische extensie om de gegevens naar andere locaties te streamen met behulp van Event Hubs.  | [Azure Diagnostics gegevens streamen met behulp van Event Hubs](./diagnostics-extension-stream-event-hubs.md)<br>[De diagnostische Linux-extensie gebruiken voor het bewaken van metrische gegevens en logboeken](../../virtual-machines/extensions/diagnostics-linux.md) |
| Application Insights logboeken | Verzamelen van Logboeken en prestatie meter items van de reken resource die uw toepassing ondersteunen om te worden geanalyseerd met andere toepassings gegevens. | [De diagnostische gegevens voor de Cloud service, virtuele machine of Service Fabric verzenden naar Application Insights](./diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Log Analytics-agent 
Installeer de Log Analytics-agent voor uitgebreidere bewaking en beheer van uw virtuele Windows-of Linux-machines. De virtuele machine kan worden uitgevoerd in azure, een andere Cloud of on-premises.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Azure Monitor-logboeken | De Log Analytics agent maakt rechtstreeks of via System Center Operations Manager verbinding met Azure Monitor en biedt u de mogelijkheid om gegevens te verzamelen van gegevens bronnen die u configureert of van bewakings oplossingen die extra inzichten bieden in toepassingen die op de virtuele machine worden uitgevoerd. | [Gegevens bronnen van agents in Azure Monitor](../agents/agent-data-sources.md)<br>[Operations Manager verbinden met Azure Monitor](./om-agents.md) |
| VM-opslag | VM Insights maakt gebruik van de Log Analytics-agent om status informatie op een aangepaste locatie op te slaan. Zie de volgende sectie voor meer informatie.  |


### <a name="vm-insights"></a>VM Insights 
[VM Insights](../vm/vminsights-overview.md) biedt een aangepaste bewakings ervaring voor virtuele machines die meer functies bieden dan de kern functionaliteit van Azure monitor. Hiervoor is een Dependency Agent vereist op virtuele Windows-en Linux-machines die zijn geïntegreerd met de Log Analytics-agent om gedetecteerde gegevens te verzamelen over processen die worden uitgevoerd op de virtuele machine en externe proces afhankelijkheden.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Azure Monitor-logboeken | Hiermee worden gegevens over processen en afhankelijkheden van de agent opgeslagen. | [Overzicht van het gebruik van VM Insights (preview) om inzicht te krijgen in toepassings onderdelen](../vm/vminsights-maps.md) |



## <a name="application-code"></a>Toepassings code
Gedetailleerde toepassings bewaking in Azure Monitor wordt uitgevoerd met [Application Insights](/azure/application-insights/) waarmee gegevens worden verzameld van toepassingen die op verschillende platforms worden uitgevoerd. De toepassing kan worden uitgevoerd in azure, een andere Cloud of on-premises.

![Verzameling van toepassings gegevens](media/data-sources/applications.png)


### <a name="application-data"></a>Toepassingsgegevens
Wanneer u Application Insights voor een toepassing inschakelt door een instrumentatie pakket te installeren, worden metrische gegevens en logboeken verzameld die betrekking hebben op de prestaties en werking van de toepassing. Application Insights slaat de gegevens op die worden verzameld in hetzelfde Azure Monitor gegevens platform dat wordt gebruikt door andere gegevens bronnen. Het bevat uitgebreide hulpprogram ma's voor het analyseren van deze gegevens, maar u kunt deze ook analyseren met gegevens uit andere bronnen met behulp van hulpprogram ma's zoals Metrics Explorer en Log Analytics.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Azure Monitor-logboeken | Operationele gegevens over uw toepassing, waaronder pagina weergaven, toepassings aanvragen, uitzonde ringen en traceringen. | [Logboek gegevens in Azure Monitor analyseren](../logs/log-query-overview.md) |
|                    | Afhankelijkheids informatie tussen toepassings onderdelen ter ondersteuning van Application map en telemetrie-correlatie. | [Intermetrie-correlatie in Application Insights](../app/correlation.md) <br> [Toepassingskaart](../app/app-map.md) |
|            | Resultaten van beschikbaarheids tests waarmee de beschik baarheid en reactie tijd van uw toepassing vanaf verschillende locaties op het open bare Internet worden getest. | [De beschikbaarheid en reactiesnelheid van een website bewaken](../app/monitor-web-app-availability.md) |
| Metrische gegevens van Azure Monitor | Application Insights verzamelt metrische gegevens met een beschrijving van de prestaties en werking van de toepassing, naast aangepaste metrische gegevens die u in uw toepassing definieert in de data base Azure Monitor Metrics. | [Vooraf samengevoegde metrische gegevens op basis van logboeken in Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[Application Insights-API voor aangepaste gebeurtenissen en metrische gegevens](../app/api-custom-events-metrics.md) |
| Azure Storage | Toepassings gegevens verzenden naar Azure Storage voor archiveren. | [Telemetrie exporteren vanuit Application Insights](../app/export-telemetry.md) |
|            | Details van beschikbaarheids testen worden opgeslagen in Azure Storage. Gebruik Application Insights in het Azure Portal om te downloaden voor lokale analyse. Resultaten van beschikbaarheids tests worden opgeslagen in Azure Monitor Logboeken. | [De beschikbaarheid en reactiesnelheid van een website bewaken](../app/monitor-web-app-availability.md) |
|            | Tracerings gegevens van Profiler worden opgeslagen in Azure Storage. Gebruik Application Insights in het Azure Portal om te downloaden voor lokale analyse.  | [Profileer productie toepassingen in azure met Application Insights](../app/profiler-overview.md) 
|            | Fout opsporing voor moment opnamen van gegevens die zijn vastgelegd voor een subset van uitzonde ringen wordt opgeslagen in Azure Storage. Gebruik Application Insights in het Azure Portal om te downloaden voor lokale analyse.  | [Hoe moment opnamen werken](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Bewakings oplossingen en inzichten
[Bewakings oplossingen](../insights/solutions.md) en [inzichten](../monitor-reference.md) verzamelen gegevens om meer inzicht te krijgen in de werking van een bepaalde service of toepassing. Ze kunnen resources in verschillende toepassings lagen en zelfs op meerdere lagen.

### <a name="monitoring-solutions"></a>Bewakingsoplossingen

| Doel | Beschrijving | Naslaginformatie
|:---|:---|:---|
| Azure Monitor-logboeken | Bewakings oplossingen verzamelen gegevens in Azure Monitor logboeken waarin deze kunnen worden geanalyseerd met behulp van de query taal of- [weer gaven](../visualize/view-designer.md) die doorgaans in de oplossing zijn opgenomen. | [Details van gegevens verzameling voor het controleren van oplossingen in azure](../monitor-reference.md) |


### <a name="container-insights"></a>Container inzichten
[Container Insights](../containers/container-insights-overview.md) biedt een aangepaste bewakings ervaring voor [Azure KUBERNETES service (AKS)](../../aks/index.yml). Er worden aanvullende gegevens verzameld over deze resources die in de volgende tabel worden beschreven.

| Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|
| Azure Monitor-logboeken | Slaat bewakings gegevens voor AKS op, waaronder inventaris, logboeken en gebeurtenissen. Metrische gegevens worden ook opgeslagen in Logboeken om gebruik te kunnen maken van de analyse functionaliteit in de portal. | [Inzicht krijgen in AKS-cluster prestaties met container Insights](../containers/container-insights-analyze.md) |
| Metrische gegevens van Azure Monitor | Metrische gegevens worden opgeslagen in de metrische data base om visualisatie en waarschuwingen te testen. | [Metrische container gegevens weer geven in Metrics Explorer](../containers/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Biedt directe toegang tot de AKS-container Logboeken (Kubernetes) (stdout/stderr), gebeurtenissen en pod-metrische gegevens in de portal. | [Kubernetes-logboeken, gebeurtenissen en metrische gegevens over pod in realtime weer geven ](../containers/container-insights-livedata-overview.md) |

### <a name="vm-insights"></a>VM Insights
[VM Insights](../vm/vminsights-overview.md) biedt een aangepaste ervaring voor het bewaken van virtuele machines. Een beschrijving van de gegevens die worden verzameld door VM Insights is opgenomen in de sectie [Operating System (gast)](#operating-system-guest) .

## <a name="custom-sources"></a>Aangepaste bronnen
Naast de standaard lagen van een toepassing, moet u mogelijk andere resources bewaken die telemetrie hebben die niet met de andere gegevens bronnen kunnen worden verzameld. Voor deze resources moet u deze gegevens schrijven naar metrieken of Logboeken met behulp van een Azure Monitor-API.

![Aangepaste verzameling](media/data-sources/custom.png)

| Doel | Methode | Beschrijving | Naslaginformatie |
|:---|:---|:---|:---|
| Azure Monitor-logboeken | Gegevensverzamelaar-API | Verzamel logboek gegevens van elke REST-client en sla deze op in Log Analytics werk ruimte. | [Logboek gegevens naar Azure Monitor verzenden met de HTTP-gegevens verzamelaar-API (open bare preview)](../logs/data-collector-api.md) |
| Metrische gegevens van Azure Monitor | API voor aangepaste metrische gegevens | Gegevens verzamelen van een wille keurige REST-client en opslaan in de data base van Azure Monitor Metrics. | [Aangepaste metrische gegevens voor een Azure-resource verzenden naar het Azure Monitor metrische archief met behulp van een REST API](../essentials/metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Overige services
Andere services in azure schrijven gegevens naar het Azure Monitor-gegevens platform. Zo kunt u gegevens analyseren die door deze services worden verzameld met gegevens die zijn verzameld door Azure Monitor en gebruikmaken van dezelfde analyse-en visualisatie hulpprogramma's.

| Service | Doel | Beschrijving | Naslaginformatie |
|:---|:---|:---|:---|
| [Azure Security Center](../../security-center/index.yml) | Azure Monitor-logboeken | Azure Security Center slaat de beveiligings gegevens op die worden verzameld in een Log Analytics-werk ruimte, zodat deze kunnen worden geanalyseerd met andere door Azure Monitor verzamelde logboek gegevens.  | [Gegevensverzameling in Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../../sentinel/index.yml) | Azure Monitor-logboeken | Azure Sentinel slaat de gegevens op die worden verzameld uit verschillende gegevens bronnen in een Log Analytics-werk ruimte, zodat deze kunnen worden geanalyseerd met andere door Azure Monitor verzamelde logboek gegevens.  | [Verbinding maken met gegevensbronnen](../../sentinel/quickstart-onboard.md) |


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [typen bewakings gegevens die worden verzameld door Azure monitor](../data-platform.md) en hoe u deze gegevens kunt bekijken en analyseren.
- Geef een lijst weer met de [verschillende locaties waar Azure-resources gegevens opslaan](../monitor-reference.md) en hoe u deze kunt openen.