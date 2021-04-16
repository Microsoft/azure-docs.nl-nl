---
title: Wat wordt bewaakt door Azure Monitor
description: Naslag voor alle services en andere resources die worden bewaakt door Azure Monitor.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 513f262f5d09cf56c4506a4f20c9aa41507c2abd
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515274"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Wat wordt er bewaakt door Azure Monitor?:
In dit artikel worden de verschillende toepassingen en services beschreven die worden bewaakt door Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Inzichten en kernoplossingen
Kerninzichten en oplossingen worden beschouwd als onderdeel van Azure Monitor en volgen de ondersteunings- en serviceovereenkomsten voor Azure. Ze worden ondersteund in alle Azure-regio's waar Azure Monitor beschikbaar is.

### <a name="insights"></a>Inzichten

Inzichten bieden een aangepaste bewakingservaring voor bepaalde toepassingen en services. Ze verzamelen en analyseren zowel logboeken als metrische gegevens.

| Inzicht | Beschrijving |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Een APM-service (Extensible Application Performance Management) voor het bewaken van uw live webtoepassing op elk platform. |
| [Containerinzichten](containers/container-insights-overview.md) | Bewaakt de prestaties van containerworkloads die zijn geïmplementeerd op Azure Container Instances kubernetes-clusters die worden gehost op Azure Kubernetes Service (AKS). |
| [Azure Monitor voor Cosmos DB](insights/cosmosdb-insights-overview.md) | Biedt een overzicht van de algehele prestaties, fouten, capaciteit en operationele status van al uw Azure Cosmos DB-resources in een uniforme interactieve ervaring. |
| [Azure Monitor voor netwerken (preview)](insights/network-insights-overview.md) | Biedt een uitgebreid overzicht van de status en metrische gegevens voor al uw netwerkresources. De geavanceerde zoekfunctie helpt u bij het identificeren van resourceafhankelijkheden, waardoor scenario's zoals het identificeren van resources die als host voor uw website worden gebruikt, mogelijk worden gemaakt door gewoon te zoeken naar de naam van uw website. |
[Azure Monitor voor resourcegroepen (preview)](insights/resource-group-insights.md) |  Triage and diagnose any problems your individual resources encounter, while offering context as to the health and performance of the resource group as a whole. |
| [Azure Monitor voor Storage](insights/storage-insights-overview.md) | Biedt uitgebreide bewaking van uw Azure Storage accounts door een uniforme weergave te bieden van uw Azure Storage services, prestaties, capaciteit en beschikbaarheid. |
| [VM-inzichten](vm/vminsights-overview.md) | Bewaakt uw virtuele Azure-machines (VM) en virtuele-machineschaalsets op schaal. De service analyseert de prestaties en status van uw Windows- en Linux-VM's en bewaakt hun processen en afhankelijkheden van andere resources en externe processen. |
| [Azure Monitor voor Key Vault (preview)](./insights/key-vault-insights-overview.md) | Biedt uitgebreide bewaking van uw sleutelkluizen door een uniforme weergave te bieden van uw Key Vault, prestaties, fouten en latentie. |
| [Azure Monitor voor Azure Cache voor Redis (preview)](insights/redis-cache-insights-overview.md) |  Biedt een uniforme, interactieve weergave van de algehele prestaties, fouten, capaciteit en operationele status. |


### <a name="core-solutions"></a>Kernoplossingen

Oplossingen zijn gebaseerd op logboekquery's en weergaven die zijn aangepast voor een bepaalde toepassing of service. Ze verzamelen en analyseren alleen logboeken en worden na een periode afgeschaft ten opzichte van inzichten.

| Oplossing | Description |
|:---|:---|
| [Status van agent](insights/solution-agenthealth.md) | Analyseer de status en configuratie van Log Analytics-agents. |
| [Waarschuwingsbeheer](insights/alert-management-solution.md) | Analyseer waarschuwingen die zijn verzameld System Center Operations Manager, Nagios of Zabbix. |
| [Servicetoewijzing](vm/service-map.md) | Detecteert automatisch toepassingsonderdelen op Windows- en Linux-systemen en wijs de communicatie tussen services toe. |



## <a name="azure-services"></a>Azure-services
De volgende tabel bevat Azure-services en de gegevens die ze verzamelen in Azure Monitor. 

- Metrische gegevens: de service verzamelt automatisch metrische gegevens in Azure Monitor metrische gegevens. 
- Logboeken: de service ondersteunt diagnostische instellingen waarmee platformlogboeken en metrische gegevens kunnen worden verzameld Azure Monitor logboeken.
- Inzicht: er is een inzicht beschikbaar voor de service dat een aangepaste bewakingservaring voor de service biedt.

| Service | Metrische gegevens | Logboeken | Inzicht | Notities |
|:---|:---|:---|:---|:---|
|Active Directory | Nee | Ja | [Ja](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Nee | Nee | Nee |  |
|Active Directory Domain Services | Nee | Ja | Nee |  |
|Activiteitenlogboek | Nee | Ja | Nee | |
|Advanced Threat Protection | Nee | Nee | Nee |  |
|Advisor | Nee | Nee | Nee |  |
|AI Builder | Nee | Nee | Nee |  |
|Analysis Services | Ja | Ja | Nee |  |
|API for FHIR | Nee | Nee | Nee |  |
|API Management | Ja | Ja | Nee |  |
|App Service | Ja | Ja | Nee |  |
|AppConfig | Nee | Nee | Nee |  |
|Application Gateway | Ja | Ja | Nee |  |
|Attestation-service | Nee | Nee | Nee |  |
|Automation | Ja | Ja | Nee |  |
|Azure Service Manager (RDFE) | Nee | Nee | Nee |  |
|Backup | Nee | Ja | Nee |  |
|Bastion | Nee | Nee | Nee |  |
|Batch | Ja | Ja | Nee |  |
|Batch AI | Nee | Nee | Nee |  |
|Blockchain-service | Nee | Ja | Nee |  |
|Blueprints | Nee | Nee | Nee |  |
|Bot-service | Nee | Nee | Nee |  |
|Cloud Services | Ja | Ja | Nee | Agent vereist voor het bewaken van het gastbesturingssysteem en werkstromen.  |
|Cloud Shell | Nee | Nee | Nee |  |
|Cognitive Services | Ja | Ja | Nee |  |
|Container Instances | Ja | Nee | Nee |  |
|Container Registry | Ja | Ja | Nee |  |
|Content Delivery Network (CDN) | Nee | Ja | Nee |  |
|Cosmos DB | Ja | Ja | [Ja](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Nee | Nee | Nee |  |
|Data Box | Nee | Nee | Nee |  |
|Data Catalog Gen2 | Nee | Nee | Nee |  |
|Data Explorer | Ja | Ja | Nee |  |
|Data Factory | Ja | Ja | Nee |  |
|Data Factory v2 | Nee | Ja | Nee |  |
|Data Share | Nee | Nee | Nee |  |
|Database for MariaDB | Ja | Ja | Nee |  |
|Database for MySQL | Ja | Ja | Nee |  |
|Database for PostgreSQL | Ja | Ja | Nee |  |
|Database Migration Service | Nee | Nee | Nee |  |
|Databricks | Nee | Ja | Nee |  |
|DDoS-beveiliging | Ja | Ja | Nee |  |
|DevOps | Nee | Nee | Nee |  |
|DNS | Ja | Nee | Nee |  |
|Domeinnamen | Nee | Nee | Nee |  |
|DPS | Nee | Nee | Nee |  |
|Dynamics 365 Customer Engagement | Nee | Nee | Nee |  |
|Dynamics 365 Finance and Operations | Nee | Nee | Nee |  |
|Event Grid | Ja | Nee | Nee |  |
|Event Hubs | Ja | Ja | Nee |  |
|ExpressRoute | Ja | Ja | Nee |  |
|Firewall | Ja | Ja | Nee |  |
|Front Door | Ja | Ja | Nee |  |
|Functions | Ja | Ja | Nee |  |
|HDInsight | Nee | Ja | Nee |  |
|HPC Cache | Nee | Nee | Nee |  |
|Information Protection | Nee | Ja | Nee |  |
|Intune | Nee | Ja | Nee |  |
|IoT Central | Nee | Nee | Nee |  |
|IoT Hub | Ja | Ja | Nee |  |
|Key Vault | Ja | Ja | [Ja](./insights/key-vault-insights-overview.md) |  |
|Kubernetes Service (AKS) | Nee | Nee | [Ja](containers/container-insights-overview.md)  |  |
|Load Balancer | Ja | Nee | Nee |  |
|Logic Apps | Ja | Ja | Nee |  |
|Machine Learning Service | Nee | Nee | Nee |  |
|Managed Applications  | Nee | Nee | Nee |  |
|Maps  | Nee | Nee | Nee |  |
|Media Services | Ja | Ja | Nee |  |
|Microsoft Managed Desktop | Nee | Nee | Nee |  |
|Microsoft PowerApps | Nee | Nee | Nee |  |
|Microsoft Social Engagement | Nee | Nee | Nee |  |
|Microsoft Stream | Ja | Ja | Nee |  |
|Migrate | Nee | Nee | Nee |  |
|Multi-Factor Authentication | Nee | Ja | Nee |  |
|Network Watcher | Ja | Ja | Nee |  |
|Notification Hubs | Ja | Nee | Nee |  |
|Open Datasets | Nee | Nee | Nee |  |
|Beleid | Nee | Nee | Nee |  |
|Power Automate | Nee | Nee | Nee |  |
|Power BI Embedded | Ja | Ja | Nee |  |
|Private Link | Nee | Nee | Nee |  |
|Project Spool Communication Platform | Nee | Nee | Nee |  |
|Red Hat OpenShift | Nee | Nee | Nee |  |
|Redis Cache | Ja | Ja | [Ja](insights/redis-cache-insights-overview.md) | |
|Resource Graph | Nee | Nee | Nee |  |
|Resource Manager | Nee | Nee | Nee |  |
|Retail Search - door Bing | Nee | Nee | Nee |  |
|Zoeken | Ja | Ja | Nee |  |
|Service Bus | Ja | Ja | Nee |  |
|Service Fabric | Nee | Ja | Nee | Agent vereist voor het bewaken van gastbesturingssystemen en werkstromen.  |
|Aanmeldingsportal | Nee | Nee | Nee |  |
|Siteherstel | Nee | Ja | Nee |  |
|Spring Cloud Service | Nee | Nee | Nee |  |
|Azure Synapse Analytics | Ja | Ja | Nee |  |
|SQL Database | Ja | Ja | Nee |  |
|SQL Server Stretch Database | Ja | Ja | Nee |  |
|Stack | Nee | Nee | Nee |  |
|Storage | Ja | Nee | [Ja](insights/storage-insights-overview.md) |  |
|Opslagcache | Nee | Nee | Nee |  |
|Opslagsynchronisatieservices | Nee | Nee | Nee |  |
|Stream Analytics | Ja | Ja | Nee |  |
|Time Series Insights | Ja | Ja | Nee |  |
|Tina | Nee | Nee | Nee |  |
|Traffic Manager | Ja | Ja | Nee |  |
|Universeel afdrukken | Nee | Nee | Nee |  |
|Virtuele-machineschaalsets | Nee | Ja | [Ja](vm/vminsights-overview.md) | Agent vereist voor het bewaken van gastbesturingssystemen en werkstromen. |
|Virtual Machines | Ja | Ja | [Ja](vm/vminsights-overview.md) | Agent vereist voor het bewaken van gastbesturingssystemen en werkstromen. |
|Virtual Network | Ja | Ja | [Ja](insights/network-insights-overview.md) |  |
|Virtual Network- NSG-stroomlogboeken | Nee | Ja | Nee |  |
|VPN Gateway | Ja | Ja | Nee |  |
|Windows Virtual Desktop | Nee | Ja | Nee |  |

## <a name="virtual-machine-agents"></a>Virtuele machine-agents
De volgende tabel bevat de agents die gegevens kunnen verzamelen van het gastbesturingssysteem van virtuele machines en gegevens kunnen verzenden naar Monitor. Elke agent kan verschillende gegevens verzamelen en verzenden naar metrische gegevens of logboeken in Azure Monitor. 

Zie [Overzicht van Azure Monitor agents voor](agents/agents-overview.md) meer informatie over de gegevens die elke agent kan verzamelen.

| Agent |  Metrische gegevens | Logboeken |
|:---|:---|:---|:---|
| [Azure Monitor-agent (preview)](agents/azure-monitor-agent-overview.md) | Ja | Ja |
| [Log Analytics-agent](agents/log-analytics-agent.md) | Nee | Ja|
| [Diagnostische extensie](agents/diagnostics-extension-overview.md) | Ja | Nee |
| [Telegraf-agent](essentials/collect-custom-metrics-linux-telegraf.md) | Ja | Nee |
| [Agent voor afhankelijkheden](vm/vminsights-enable-overview.md) | Nee | Ja |


## <a name="product-integrations"></a>Productintegraties
De services en oplossingen in de volgende tabel slaan hun gegevens op in een Log Analytics-werkruimte, zodat deze kunnen worden geanalyseerd met andere logboekgegevens die zijn verzameld door Azure Monitor.

| Product/service | Description |
|:---|:---|
| [Azure Automation](../automation/index.yml) | Besturingssysteemupdates beheren en wijzigingen bijhouden op Windows- en Linux-computers. Zie [Wijzigingen bijhouden](../automation/change-tracking/overview.md) en [Updatebeheer](../automation/update-management/overview.md). |
| [Azure Information Protection ](/azure/information-protection/) | Documenten en e-mailberichten classificeren en optioneel beveiligen. Zie [Centrale rapportage voor Azure Information Protection.](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports) |
| [Azure Security Center](../security-center/index.yml) | Beveiligingsgebeurtenissen verzamelen en analyseren en bedreigingsanalyse uitvoeren. Zie [Gegevensverzameling in Azure Security Center](../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../sentinel/index.yml) | Maakt verbinding met verschillende bronnen, waaronder Office 365 en Amazon Web Services Cloud Trail. Zie [Verbinding maken met gegevensbronnen.](../sentinel/connect-data-sources.md) |
| [Microsoft Intune](/intune/) | Maak een diagnostische instelling voor het verzenden van logboeken naar Azure Monitor. Zie [Logboekgegevens verzenden naar opslag, Event Hubs of Log Analytics in Intune (preview)](/intune/fundamentals/review-logs-using-azure-monitor).  |
| Netwerk  | [Netwerkprestatiemeter:](insights/network-performance-monitor.md) be monitor de netwerkverbinding en prestaties met service- en toepassings-eindpunten.<br>[Azure Application Gateway:](insights/azure-networking-analytics.md#azure-application-gateway-analytics) analyseer logboeken en metrische gegevens van Azure Application Gateway.<br>[Traffic Analytics:](../network-watcher/traffic-analytics.md) analyseert Network Watcher stroomlogboeken van netwerkbeveiligingsgroep (NSG) om inzicht te krijgen in de verkeersstroom in uw Azure-cloud. |
| [Office 365](insights/solution-office-365.md) | Uw Office 365-omgeving bewaken. Bijgewerkte versie met verbeterde onboarding beschikbaar via Azure Sentinel. |
| [SQL Analytics](insights/azure-sql.md) | De prestaties van Azure SQL databases en SQL Managed Instances op schaal en in meerdere abonnementen bewaken. |
| [Surface Hub](insights/surface-hubs.md) | De status en het gebruik van Surface Hub apparaten bijhouden. |
| [System Center Operations Manager](/system-center/scom) | Verzamel gegevens van Operations Manager agents door hun beheergroep te verbinden met Azure Monitor. Zie [Verbinding Operations Manager met Azure Monitor](agents/om-agents.md)<br> Evalueer het risico en de status van uw System Center Operations Manager beheergroep met [Operations Manager Evaluatieoplossing.](insights/scom-assessment.md) |
| [Microsoft Teams-ruimten](/microsoftteams/room-systems/azure-monitor-deploy) | Geïntegreerd, end-to-end beheer van Microsoft Teams Rooms-apparaten. |
| [Visual Studio App Center](/appcenter/) | Toepassingen bouwen, testen en distribueren en vervolgens de status en het gebruik ervan controleren. Zie [Beginnen met het analyseren van uw mobiele app met App Center en Application Insights](app/mobile-center-quickstart.md). |
| Windows | [Windows Update Naleving:](/windows/deployment/update/update-compliance-get-started) evalueer uw Windows-bureaubladupgrades.<br>[Desktop Analytics:](/configmgr/desktop-analytics/overview) integreert met Configuration Manager om inzicht en intelligentie te bieden om beter geïnformeerde beslissingen te nemen over de gereedheid voor updates van uw Windows-clients. |



## <a name="other-solutions"></a>Andere oplossingen
Er zijn andere oplossingen beschikbaar voor het bewaken van verschillende toepassingen en services, maar actieve ontwikkeling is gestopt en ze zijn mogelijk niet in alle regio's beschikbaar. Ze vallen onder de Service Level Agreement voor gegevensingestie van Azure Log Analytics.

| Oplossing | Description |
|:---|:---|
| [Active Directory-statuscontrole](insights/ad-assessment.md) | Beoordeel het risico en de status van uw Active Directory-omgevingen. |
| [Active Directory-replicatiestatus](insights/ad-replication-status.md) | Controleert regelmatig uw Active Directory-omgeving op replicatiefouten. |
| [Analyse van activiteitenlogboek](essentials/activity-log.md#activity-log-analytics-monitoring-solution) | Bekijk vermeldingen in het activiteitenlogboek. |
| [DNS-analyse (preview)](insights/dns-analytics.md) | Verzamelt, analyseert en correleert analytische en auditlogboeken van Windows DNS en andere gerelateerde gegevens van uw DNS-servers. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Verzamel, bekijk en analyseer uw Cloud Foundry metrische gegevens over de systeemtoestand en prestaties, voor meerdere implementaties. |
| [Containers](containers/containers.md) | Docker- en Windows-containerhosts weergeven en beheren. |
| [Evaluaties op aanvraag](/services-hub/health/getting_started_with_on_demand_assessments) | Evalueer en optimaliseer de beschikbaarheid, beveiliging en prestaties van uw on-premises, hybride en cloudtechnologieomgevingen van Microsoft. |
| [SQL-statuscontrole](insights/sql-assessment.md) | Evalueer het risico en de status van uw SQL Server omgevingen.  |
| [Bedradingsgegevens](insights/wire-data.md) | Geconsolideerde netwerk- en prestatiegegevens die zijn verzameld van computers met Windows en Linux die zijn verbonden met de Log Analytics-agent. |

## <a name="third-party-integration"></a>Integratie van derden

| Oplossing | Description |
|:---|:---|
| [ITSM](alerts/itsmc-overview.md) | Met de IT Service Management-connector (ITSMC) kunt u Azure verbinden met een ondersteund ITSM-product/service (IT-servicebeheer).  |


## <a name="resources-outside-of-azure"></a>Resources buiten Azure
Azure Monitor kunt gegevens verzamelen van resources buiten Azure met behulp van de methoden die in de volgende tabel worden vermeld.

| Resource | Methode |
|:---|:---|
| Toepassingen | Webtoepassingen buiten Azure bewaken met behulp van Application Insights. Zie [Wat is Application Insights?](./app/app-insights-overview.md). |
| Virtuele machines | Gebruik agents om gegevens te verzamelen van het gastbesturingssysteem van virtuele machines in andere cloudomgevingen of on-premises. Zie [Overzicht van Azure Monitor agents.](agents/agents-overview.md) |
| REST API Client | Er zijn afzonderlijke API's beschikbaar voor het schrijven van gegevens Azure Monitor logboeken en metrische gegevens van elke REST API client. Zie Logboekgegevens verzenden naar Azure Monitor met de [HTTP Data Collector-API](logs/data-collector-api.md) voor logboeken en Aangepaste metrische gegevens voor een [Azure-resource](essentials/metrics-store-custom-rest-api.md) verzenden naar het metrische Azure Monitor-opslag met behulp van een REST API voor metrische gegevens. |



## <a name="next-steps"></a>Volgende stappen

- Lees meer over het Azure Monitor gegevensplatform waarin de logboeken en metrische gegevens worden op slaat die [zijn verzameld door inzichten en oplossingen.](data-platform.md)
- Voltooi een [zelfstudie over het bewaken van een Azure-resource.](essentials/tutorial-resource-logs.md)
- Voltooi een [zelfstudie over het schrijven van een logboekquery voor het analyseren van gegevens in Azure Monitor logboeken.](essentials/tutorial-resource-logs.md)
- Voltooi een [zelfstudie over het maken van een grafiek met metrische gegevens voor het analyseren van gegevens in Azure Monitor Metrics](essentials/tutorial-metrics-explorer.md).

