---
title: Wat is er nieuw de documentatie van Azure Monitor?
description: Belangrijke updates voor Azure Monitor, documentatie wordt elke maand bijgewerkt.
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/10/2021
ms.openlocfilehash: dd6c44587ce3f4e2b5de940ef831a20a4079c4ef
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051919"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Wat is er nieuw de documentatie van Azure Monitor?

In dit artikel vindt u een lijst met Azure Monitor-artikelen die nieuw of aanzienlijk bijgewerkt zijn. Elke eerste week van de maand wordt de lijst bijgewerkt met artikelupdates van de vorige maand.

## <a name="january-2021"></a>Januari 2021 

### <a name="general"></a>Algemeen 
- [Azure monitor Veelgestelde vragen](faq.md) : er is informatie toegevoegd over de apparaatgegevens voor Application Insights.
### <a name="agents"></a>Agents  
- Het [verzamelen van Event Tracing for Windows gebeurtenissen (etw) voor analyse Azure monitor-logboeken](./agents/data-sources-event-tracing-windows.md) -nieuw artikel.
- [Regels voor gegevens verzameling in azure monitor (preview)](./agents/data-collection-rule-overview.md) : toegevoegde koppelingen naar Power shell en CLI-voor beelden.

### <a name="alerts"></a>Waarschuwingen  
- [Configureer Azure om verbinding te maken met ITSM-hulpprogram ma's met behulp van veilig exporteren](./alerts/itsm-connector-secure-webhook-connections-azure-configuration.md) -nieuw artikel.
- [Connector status fouten in het nieuwe artikel ITSMC dash board](./alerts/itsmc-dashboard-errors.md) .
- [Onderzoek fouten met behulp van het artikel ITSMC dash board](./alerts/itsmc-dashboard.md) -nieuw.
- [Problemen met Azure metrische waarschuwingen oplossen](./alerts/alerts-troubleshoot-metric.md) -secties toegevoegd voor dynamische drempel waarden.
- [Problemen oplossen in IT Service Management-connector](./alerts/itsmc-troubleshoot-overview.md) -nieuw artikel.

### <a name="application-insights"></a>Application Insights
- [Azure-toepassing Insights-correlatie van de telemetrie](app/correlation.md) heeft een tracering correlatie toegevoegd wanneer de ene module een andere aanroept in opentellingen python.
- [Application Insights voor](app/javascript.md) webpagina's: nieuw artikel.
- [Klik op Analytics-invoeg toepassing voor automatische verzamelingen voor Application Insights java script SDK](app/javascript-click-analytics-plugin.md) -nieuw artikel.
- [Bewaak uw apps zonder code wijzigingen-automatische instrumentatie voor Azure monitor](app/codeless-overview.md) python-kolom toegevoegd Application Insights.
- [React-invoegtoepassing voor Application Insights JavaScript SDK](app/javascript-react-plugin.md): nieuw artikel.
- [Telemetrie-processors (preview)-Azure Monitor Application Insights voor het schrijven van Java](app/java-standalone-telemetry-processors.md) .
- [Gebruiks analyse met Azure-toepassing Insights](app/usage-overview.md) -nieuw artikel.
- [Toepassings wijzigings analyse in azure monitor gebruiken om problemen met web-apps te vinden](app/change-analysis.md) -fout messges toegevoegd.


### <a name="insights"></a>Inzichten    
- [Azure monitor voor Azure Data Explorer (preview)](insights/data-explorer.md) -nieuw artikel.

### <a name="logs"></a>Logboeken    
- [Azure monitor door de klant beheerde sleutel](./logs/customer-managed-keys.md) : een door de gebruiker toegewezen beheerde identiteit introduceren.
- [Azure monitor logboeken toegewezen clusters](./logs/logs-dedicated-clusters.md) -bijgewerkte respons code.
- [Query-Azure monitor van meerdere services en Azure Data Explorer (preview)](/azure/azure-monitor/platform/azure-data-explorer-monitor-cross-service-query) -nieuw artikel.

### <a name="metrics"></a>Metrische gegevens
- [Azure monitor metrische gegevens aggregatie en weer gave](./essentials/metrics-aggregation-explained.md) van statistieken: nieuw artikel.

### <a name="platform-logs"></a>Platform logboeken
- [Azure monitor resource logboeken ondersteunde services en categorieën](./essentials/resource-logs-categories.md) : nieuw artikel.

### <a name="visualizations"></a>Visualisaties
- [Azure monitor werkmappen gegevens bronnen](./visualize/workbooks-data-sources.md) : toegevoegde samenvoeg-en wijzigings analyse.


## <a name="december-2020"></a>December 2020

### <a name="general"></a>Algemeen
- [Door klant beheerde sleutels in Azure Monitor](logs/customer-managed-keys.md): foutberichten toegevoegd.
- [Partners die integreren met Azure Monitor door](partners.md): sectie over Event Hub-integratie toegevoegd.

### <a name="agents"></a>Agents
- [Query's uitvoeren op meerdere bronnen in Azure Data Explorer met behulp van Azure Monitor](logs/azure-monitor-data-explorer-proxy.md): nieuw artikel.
- [Overzicht van de Azure-bewakingsagents](agents/agents-overview.md): ondersteuning toegevoegd voor Oracle 8.

### <a name="alerts"></a>Waarschuwingen
- [Problemen met metrische Azure-waarschuwingen oplossen](alerts/alerts-troubleshoot-metric.md): probleemoplossing voor dynamische drempelwaarden toegevoegd.
- [IT Service Management-connector in Log Analytics](alerts/itsmc-definition.md): nieuw artikel.
- [Overzicht van IT Service Management-connector](alerts/itsmc-overview.md): geherstructureerde informatie over probleemoplossing.
- [Cherwell verbinden met IT Service Management-connector](alerts/itsmc-connections-cherwell.md): nieuw artikel.
- [Provenance verbinden met IT Service Management-connector](alerts/itsmc-connections-provance.md): nieuw artikel.
- [SCSM verbinden met IT Service Management-connector](alerts/itsmc-connections-scsm.md): nieuw artikel.
- [ServiceNow verbinden met IT Service Management-connector](alerts/itsmc-connections-servicenow.md): nieuw artikel.
- [Synchronisatieproblemen met ServiceNow handmatig oplossen](alerts/itsmc-resync-servicenow.md): geherstructureerde informatie over probleemoplossing.




### <a name="application-insights"></a>Application Insights
- [Azure Application Insights voor JavaScript-web-apps](app/javascript.md): installatie van verbindingsreeks toegevoegd.
- [Metrische standaardgegevens van Azure Application Insights](app/standard-metrics.md): nieuw artikel.
- [Azure Monitor Application Insights Java](app/java-in-process-agent.md): aanvullende informatie over het verzenden van aangepaste telemetrie vanuit uw toepassing.
- [Continue export van telemetrie vanuit Application Insights](app/export-telemetry.md): op diagnostische instellingen gebaseerde export toegevoegd.
- [Snapshot Debugger inschakelen voor .NET- en .NET Core-apps in Azure Functions](app/snapshot-debugger-function-app.md): nieuw artikel.
- [Door Application Insights en Log Analytics gebruikte IP-adressen](app/ip-addresses.md): IP-adressen toegevoegd voor Azure Government.
- [Problemen met Azure Application Insights Profiler oplossen](app/profiler-troubleshooting.md): informatie over de statuspagina van de site-uitbreiding van Diagnostic Services toegevoegd.
- [Problemen met de beschikbaarheidstests van Azure Application Insights oplossen](app/troubleshoot-availability.md): wordt bijgewerkt naar probleemoplossing voor pingtests.
- [Problemen met Azure Monitor Application Insights voor Java oplossen](app/java-standalone-troubleshoot.md): nieuw artikel.

### <a name="containers"></a>Containers
- [Rapporten in container Insights](insights/container-insights-reports.md) -nieuw artikel.

### <a name="logs"></a>Logboeken
- [Toegewezen Azure Monitor Logs-clusters](logs/logs-dedicated-clusters.md): geautomatiseerde opdrachten, methoden voor ontkoppelen en verwijderen en probleemoplossing toegevoegd.
- [Query's uitvoeren voor meerdere services tussen Azure Monitor en Azure Data Explorer](logs/azure-data-explorer-monitor-cross-service-query.md): nieuw artikel.
- [Gegevens uit Log Analytics-werkruimte exporteren naar Azure Monitor (preview)](logs/logs-data-export.md): ARM-sjablonen toegevoegd.

### <a name="metrics"></a>Metrische gegevens
- [Geavanceerde functies van Azure Metrics Explorer](essentials/metrics-charts.md): informatie over resourcebereikkiezer toegevoegd.
- [Meerdere resources weergeven in Metrics Explorer](essentials/metrics-dynamic-scope.md): nieuw artikel.

### <a name="networks"></a>Netwerken
- [Azure Networking Analytics-oplossing in Azure Monitor](insights/azure-networking-analytics.md): informatie over Network Insights-workbook toegevoegd.

### <a name="virtual-machines"></a>Virtual Machines
- [Azure Monitor inschakelen voor een hybride omgeving](vm/vminsights-enable-hybrid.md): nieuwe versie van afhankelijkheidsagent.


### <a name="visualizations"></a>Visualisaties
- [Kaartvisualisaties in Azure Monitor-workbook](visualize/workbooks-map-visualizations.md): nieuw artikel.
- [Bring Your Own Storage van Azure Monitor Workbooks](visualize/workbooks-bring-your-own-storage.md): nieuw artikel.


## <a name="november-2020"></a>November 2020

### <a name="general"></a>Algemeen
- [Azure Monitor-servicelimieten](service-limits.md): bijgewerkt voor Azure Arc-ondersteuning.

### <a name="agents"></a>Agents
- [Overzicht van de Azure-bewakingsagents](agents/agents-overview.md): bijgewerkt voor Azure Arc-ondersteuning.
- [De Azure Monitor-agent installeren](agents/azure-monitor-agent-install.md): nieuw artikel.
- [Overzicht Azure Monitor-agent](agents/azure-monitor-agent-overview.md): bijgewerkt voor Azure Arc-ondersteuning.
- [Resource Manager-voorbeeldsjablonen voor agents](agents/resource-manager-agent.md): bijgewerkt voor Azure Arc-ondersteuning.

### <a name="alerts"></a>Waarschuwingen
- [Actiegroepen maken en beheren in Azure Portal](alerts/action-groups.md): IP-bronadressen voor webhooks toegevoegd.

### <a name="application-insights"></a>Application Insights
- [Bewaken van Java-toepassingen zonder code - Azure Monitor Application Insights](app/java-in-process-agent.md): configuratievoorbeeld toegevoegd.
- [React-invoegtoepassing voor Application Insights JavaScript SDK](app/javascript-react-plugin.md): sectie over het gebruik van React-hooks toegevoegd.
- [Upgraden van Application Insights Java 2.x SDK](app/java-standalone-upgrade-from-2x.md): nieuw artikel.
- [Opmerkingen bij de release voor Microsoft.ApplicationInsights.SnapshotCollector](app/snapshot-collector-release-notes.md): nieuw artikel.

### <a name="autoscale"></a>Automatisch schalen
- [Aan de slag met automatische schaalaanpassing in Azure](autoscale/autoscale-get-started.md): sectie over het verplaatsen van Automatisch schaalaanpassing naar een andere regio toegevoegd.

### <a name="data-collection"></a>Gegevens verzamelen
- [Gegevensverzameling configureren voor de Azure Monitor-agent (preview)](agents/data-collection-rule-azure-monitor-agent.md): bijgewerkt voor Azure Arc-ondersteuning.
- [Gegevensverzamelingsregels in Azure Monitor (preview)](agents/data-collection-rule-overview.md): bijgewerkt voor Azure Arc-ondersteuning.
- [Resource Manager-voorbeeldsjablonen voor gegevensverzamelingsregels](agents/resource-manager-data-collection-rules.md): nieuw artikel.

### <a name="insights-and-solutions"></a>Inzicht en oplossingen
- [Azure verbinden met ITSM-hulpprogramma's met behulp van beveiligde export](alerts/it-service-management-connector-secure-webhook-connections.md): sectie over verbinding maken met ServiceNow toegevoegd.

### <a name="logs"></a>Logboeken
- [Log Analytics en Excel integreren](logs/log-excel.md): nieuw artikel.
- [Log Analytics-gegevensbeveiliging](logs/data-security.md): sectie over aanvullende beveiligingsfuncties toegevoegd.
- [Integratie van Log Analytics met Power BI](logs/log-powerbi.md): nieuw artikel.
- [Standaardkolommen in Azure Monitor-logboekrecords](logs/log-standard-columns.md): kolom _SubscriptionId toegevoegd.

Nieuwe en bijgewerkte artikelen na opnieuw indelen van de inhoud van logboekquery's.

- [Log Analytics-zelfstudie](logs/log-analytics-tutorial.md)
- [Logboekquery's in Azure Monitor](logs/log-query-overview.md)
- [Overzicht van Log Analytics in Azure Monitor](logs/log-analytics-overview.md)
- [Voorbeelden voor query's voor Azure Data Explorer en Azure Monitor](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [Zelfstudie: Kusto-query's gebruiken in Azure Data Explorer en Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>Virtuele machines

- [Overzicht van VM Insights inschakelen](vm/vminsights-enable-overview.md) -ondersteunde regio's toegevoegd.

Nieuwe artikelen voor VM Insights-gast status (preview-versie)

- [VM Insights-gast status (preview-versie)](vm/vminsights-health-overview.md)
- [VM Insights-status waarschuwingen voor gast (preview-versie)](vm/vminsights-health-alerts.md)
- [Bewaking configureren in VM Insights-gast status (preview-versie)](vm/vminsights-health-configure.md)
- [Bewaking configureren in VM Insights-gast status met behulp van regels voor gegevens verzameling (preview)](vm/vminsights-health-configure-dcr.md)
- [VM Insights-gast status inschakelen (preview)](vm/vminsights-health-enable.md)
- [Problemen met VM Insights-gast status oplossen (preview-versie)](vm/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>Oktober 2020

### <a name="general"></a>Algemeen
- [Buitengebruikstelling van Azure Monitor-API](logs/operationalinsights-api-retirement.md): nieuw artikel.

### <a name="agents"></a>Agents
- [Wat wordt bewaakt door Azure Monitor](monitor-reference.md): sectie over agents toegevoegd.

### <a name="alerts"></a>Waarschuwingen
- [Actiegroepen maken en beheren in Azure Portal](alerts/action-groups.md): sectie over servicetag toegevoegd.
- [Voorbeelden van Resource Manager-sjablonen voor metrische waarschuwingen](alerts/resource-manager-alerts-metric.md): parameter voor inhoudsovereenkomst en testlocaties toegevoegd.
- [Problemen met metrische waarschuwingen in Azure oplossen](alerts/alerts-troubleshoot-metric.md): best practice voor het configureren van regels toegevoegd.

### <a name="application-insights"></a>Application Insights
- [Angular-invoegtoepassing voor Application Insights JavaScript SDK](app/javascript-angular-plugin.md): nieuw artikel.
- [Azure Application Insights voor ASP.NET Core-toepassingen](app/asp-net-core.md): veelgestelde vragen over ILogger-logboeken toegevoegd.
- [Bewaking voor ASP.NET configureren met Azure Application Insights](app/asp-net.md): artikel herschreven.
- [Op logboek gebaseerde en vooraf geaggregeerde metrische gegevens in Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md): tabellen met vooraf samengevoegde metrische gegevens toegevoegd.
- [De beschikbaarheid en reactiesnelheid van een website bewaken](app/monitor-web-app-availability.md): sectie over locatietags voor een populatie toegevoegd.
- [Java-toepassingen overal bewaken - Azure Monitor Application Insights](app/java-standalone-config.md): configuratievoorbeeld toegevoegd.
- [Java-toepassingen overal bewaken - Azure Monitor Application Insights](app/java-standalone-telemetry-processors.md): nieuw artikel.
- [Wijzigingsanalyse voor toepassingen gebruiken in Azure Monitor om problemen met web-apps te vinden](app/change-analysis.md): secties over virtuele machine en activiteitenlogboek toegevoegd.
  
### <a name="autoscale"></a>Automatisch schalen
- [Aan de slag met automatisch schaalaanpassing in Azure](autoscale/autoscale-get-started.md): sectie over het verplaatsen van Automatisch schaalaanpassing naar een andere regio toegevoegd.

### <a name="containers"></a>Containers
- [PowerVault-bewaking configureren met container Insights](containers/container-insights-persistent-volumes.md) -nieuw artikel.
- [De container Insights-agent beheren](containers/container-insights-manage-agent.md) : er is ondersteuning toegevoegd voor Azure Arc enabled Kubernetes-cluster.
- [Metrische waarschuwingen van container Insights](containers/container-insights-metric-alerts.md) : er is ondersteuning toegevoegd voor Azure Arc enabled Kubernetes-cluster.

### <a name="insights-and-solutions"></a>Inzicht en oplossingen
- [IT Service Management-connector: veilig exporteren in Azure Monitor](alerts/it-service-management-connector-secure-webhook-connections.md): sectie over ServiceNow toegevoegd.

### <a name="logs"></a>Logboeken
- [Gegevens uit Log Analytics-werkruimte archiveren naar Azure-opslag met behulp van logische app](logs/logs-export-logic-app.md): nieuw artikel.
- [Gegevens uit Log Analytics-werkruimte exporteren naar Azure Monitor (preview)](logs/logs-data-export.md): voorbeeldhoofdtekst voor REST-aanvraag voor Event Hub toegevoegd.
- [Gebruik en kosten voor Azure Monitor logboeken beheren](logs/manage-cost-storage.md) informatie over de relatie tussen Azure Monitor-logboeken en Azure Security Center-facturering toegevoegd. Query toegevoegd voor knooppuntaantallen als de prijscategorie per knooppunt wordt gebruikt. 
- [Status van Log Analytics-werkruimte in Azure Monitor](logs/monitor-workspace.md): nieuw artikel.
- [Query uitvoeren op gegevens in Azure Monitor met behulp van Azure Data Explorer (preview)](logs/azure-data-explorer-monitor-proxy.md): nieuw artikel.
- Query uitvoeren op [geëxporteerde gegevens in Azure Monitor met behulp van Azure Data Explorer (preview)](logs/azure-data-explorer-query-storage.md): nieuw artikel.

### <a name="networks"></a>Netwerken
- [Azure Monitor voor netwerken (preview)](insights/network-insights-overview.md): sectie over probleemoplossing toegevoegd. Sectie over connectiviteit toegevoegd.

### <a name="platform-logs"></a>Platformlogboeken
- [Gebeurtenisschema voor Azure-activiteitenlogboek](essentials/activity-log-schema.md): beschrijvingen van ernstniveaus toegevoegd.

### <a name="virtual-machines"></a>Virtuele machines
- [Wijzigingsanalyse in Azure Monitor voor VM's](vm/vminsights-change-analysis.md): nieuw artikel.
- [Overzicht Azure Monitor voor VM's inschakelen](vm/vminsights-enable-overview.md): ondersteunde regio's toegevoegd.
- [Container Insights bijwerken voor metrische gegevens](containers/container-insights-update-metrics.md) : extra ondersteuning voor Azure Arc enabled Kubernetes-cluster.



## <a name="september-2020"></a>September 2020

### <a name="general"></a>Algemeen
- [Veelgestelde vragen over Azure Monitor](faq.md): sectie toegevoegd over OpenTelemetry.

### <a name="agents"></a>Agents
- [Overzicht van Azure Monitor-agent](agents/azure-monitor-agent-overview.md): besluitfactoren toegevoegd over de overstap naar een nieuwe agent.
- [Overzicht van de Azure-bewakingsagents](agents/agents-overview.md): ondersteuning toegevoegd voor Windows 10.

### <a name="alerts"></a>Waarschuwingen
- [Een logboekwaarschuwing maken met een Azure Resource Manager-sjabloon](alerts/alerts-log-create-templates.md): nieuw artikel.
- [Problemen met metrische Azure-gegevens oplossen](alerts/alerts-troubleshoot-metric.md): sectie toegevoegd over het exporteren van een ARM-sjabloon voor een metrische waarschuwingsregel.

### <a name="application-insights"></a>Application Insights
- [Een nieuwe werkruimteresource voor Azure Monitor Application Insights maken](app/create-workspace-resource.md): preview-functie verwijderd.
- [Gegevensretentie en -opslag in Azure Application Insights](app/data-retention-privacy.md): details toegevoegd voor nieuwe ondersteuning voor bescherming tegen gegevensverlies in Mac en Linux.
- [Gebeurtenistellers in Application Insights](app/eventcounters.md): opmerking toegevoegd over tellers die standaard worden verzameld.
- [Op logboek gebaseerde en vooraf geaggregeerde metrische gegevens in Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md): preview-functie verwijderd.
- [Een klassieke Azure Monitor Application Insights-resource migreren naar een werkruimteresource](app/convert-classic-resource.md): nieuw artikel.
- [Java-toepassingen bewaken in een willekeurige omgeving - Azure Monitor Application Insights](app/java-in-process-agent.md): bijgewerkt voor een nieuwe preview-versie van de agent.
- [Web-app-analyse voor ASP.NET instellen met Azure Application Insights](app/asp-net.md): artikel herschreven.
- [Telemetriekanalen in Azure Application Insights](app/telemetry-channels.md): details toegevoegd voor nieuwe ondersteuning voor bescherming tegen gegevensverlies in Mac en Linux.
- [Problemen met Azure Application Insights Snapshot Debugger oplossen](app/snapshot-debugger-troubleshoot.md): SSL-sectie toegevoegd over probleemoplossing voor Snapshot Debugger.
- [Wijzigingsanalyse voor toepassingen gebruiken in Azure Monitor om problemen met web-apps te vinden](app/change-analysis.md): virtuele machine en activiteitenlogboek toegevoegd.


### <a name="containers"></a>Containers
- Het [Kubernetes-cluster met Azure-Arc configureren met](containers/container-insights-enable-arc-enabled-clusters.md) behulp van container Insights-Toegevoegde richt lijnen voor het inschakelen van bewaking met Service-Principal.
- [Implementatie & hpa-metrische gegevens met container Insights](containers/container-insights-deployment-hpa-metrics.md) -nieuw artikel.

### <a name="insights-and-solutions"></a>Inzicht en oplossingen
- [Azure Monitor voor Azure Cache voor Redis](insights/redis-cache-insights-overview.md): preview-functie verwijderd.
- [Azure Monitor voor netwerken (preview)](insights/network-insights-overview.md): secties toegevoegd over connectiviteit en verkeer.
- [IT Service Management-connector: veilig exporteren in Azure Monitor](alerts/it-service-management-connector-secure-webhook-connections.md): nieuw artikel.
- [IT Service Management-connector in Azure Monitor](alerts/itsmc-connections.md): opmerking over Cherwell- en Provance ITSM-integraties.
- [Key Vault bewaken met Azure Monitor voor Key Vault](insights/key-vault-insights-overview.md): preview-functie verwijderd.

### <a name="logs"></a>Logboeken
- [Query's controleren in Azure Monitor-logboekquery’s](logs/query-audit.md): nieuw artikel.
- [Door klant beheerde sleutels in Azure Monitor](logs/customer-managed-keys.md): toegevoegde klanten-lockbox.
- [Toegewezen clusters voor Azure Monitor-logboeken](logs/logs-dedicated-clusters.md): nieuw artikel.
- [Uw implementatie van Azure Monitor-logboeken ontwerpen](logs/design-logs-deployment.md): sectie over frequentielimieten voor schaal- en opnamevolumes bijgewerkt.
- [Logboekquerybereik in Azure Monitor Log Analytics](logs/scope.md): updates voor de opname van werkruimtetoepassingen.
- [Logboeken in Azure Monitor](logs/data-platform-logs.md): updates voor de opname van werkruimtetoepassingen.
- [Standaardkolommen in Azure Monitor-logboekrecords](logs/log-standard-columns.md): updates voor de opname van werkruimtetoepassingen.
- [Servicelimieten van Azure Monitor](service-limits.md): beperkingslimieten voor gebruikersquery's bijgewerkt.
- [Door de klant beheerde opslagaccounts gebruiken in Azure Monitor Log Analytics](logs/private-storage.md): artikel herschreven.
- [Gegevens weergeven en analyseren in Azure Log Analytics](./logs/data-platform-logs.md): updates voor de opname van werkruimtetoepassingen.


### <a name="platform-logs"></a>Platformlogboeken
- [Gebeurtenisschema voor Azure-activiteitenlogboek](essentials/activity-log-schema.md): ernstniveau toegevoegd.
- [Resource Manager-voorbeeldsjablonen voor diagnostische instellingen](essentials/resource-manager-diagnostic-settings.md): voorbeeld toegevoegd voor Azure-opslagaccount.

### <a name="visualizations"></a>Visualisaties
- [Grafiekvisualisaties in Azure Monitor-werkmap](visualize/workbooks-chart-visualizations.md): nieuw artikel.
- [Weergave van samengestelde balk in Azure Monitor-werkmap](visualize/workbooks-composite-bar.md): nieuw artikel.
- [Graafvisualisaties in Azure Monitor-werkmap](visualize/workbooks-graph-visualizations.md): nieuw artikel.
- [Rastervisualisaties in Azure Monitor-werkmap](visualize/workbooks-grid-visualizations.md): nieuw artikel.
- [Honingraatvisualisaties in Azure Monitor-werkmap](visualize/workbooks-honey-comb.md): nieuw artikel.
- [Tekstvisualisaties in Azure Monitor-werkmap](visualize/workbooks-text-visualizations.md): nieuw artikel.
- [Tegelvisualisaties in Azure Monitor-werkmap](visualize/workbooks-tile-visualizations.md): nieuw artikel.
- [Structuurvisualisaties in Azure Monitor-werkmap](visualize/workbooks-tree-visualizations.md): nieuw artikel.




## <a name="august-2020"></a>Augustus 2020

### <a name="general"></a>Algemeen

- [Wat wordt bewaakt door Azure Monitor](monitor-reference.md): bijgewerkt om Azure Monitor-agent toe te voegen.


### <a name="agents"></a>Agents
- [Overzicht van Azure Monitor-agent](agents/azure-monitor-agent-overview.md): nieuw artikel.
- [Azure Monitor inschakelen voor een hybride omgeving](vm/vminsights-enable-hybrid.md): versie van de afhankelijkheidsagent bijgewerkt.
- [Overzicht van de Azure Monitor-agents](agents/agents-overview.md): Azure Monitor-agent toegevoegd en ondersteuningstabel voor besturingssystemen samengevoegd.


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Nieuwe en bijgewerkte artikelen van het opnieuw indelen van de inhoud over agents
- [Overzicht van VM Insights inschakelen](vm/vminsights-enable-overview.md)
- [Log Analytics-agent installeren op Linux-computers](agents/agent-linux.md)
- [Log Analytics-agent installeren op Windows-computers](agents/agent-windows.md)
- [Overzicht van Log Analytics-agent](agents/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights voor JavaScript-web-apps](app/javascript.md): sectie toegevoegd met een beschrijving van de correlatie tussen client en server en configuratie voor CORS-correlatie.
- [Een nieuwe op werkruimte gebaseerde resource voor Azure Monitor Application Insights maken](app/create-workspace-resource.md): mogelijkheden toegevoegd die worden geboden door op werkruimte gebaseerde toepassingen.
- [IP-adressen gebruikt door Application Insights en logboekanalyse](app/ip-addresses.md): IP-adressen bijgewerkt voor Live Metrics Stream.
- [Java-toepassingen bewaken in een willekeurige omgeving - Azure Monitor Application Insights](app/java-in-process-agent.md): tabel toegevoegd voor ondersteunde aangepaste telemetrie.
- [Native React-invoegtoepassing voor Application Insights JavaScript SDK](app/javascript-react-native-plugin.md): nieuw artikel.
- [React-invoegtoepassing voor Application Insights JavaScript SDK](app/javascript-react-plugin.md): nieuw artikel.
- [Voorbeelden van Resource Manager-sjablonen voor het maken van Azure Function-apps en Application Insights-controles](app/resource-manager-function-app.md): nieuw artikel.
- [Voorbeelden van Resource Manager-sjablonen voor het maken van Azure App Service-web-apps en Application Insights-bewaking](app/resource-manager-web-app.md): nieuw artikel.
- [Gebruiksanalyse met Azure Application Insights](app/usage-overview.md): video toegevoegd.

### <a name="autoscale"></a>Automatisch schalen
- [Aan de slag met automatische schaalaanpassing in Azure](autoscale/autoscale-get-started.md): sectie toegevoegd over routering naar goede instanties voor App Service.

### <a name="data-collection"></a>Gegevens verzamelen
- [Gegevensverzameling configureren voor de Azure Monitor-agent (preview)](agents/data-collection-rule-azure-monitor-agent.md): nieuw artikel.
- [Regels voor gegevensverzameling in Azure Monitor (preview)](agents/data-collection-rule-overview.md): nieuw artikel.


### <a name="containers"></a>Containers
- [Implementatie & hpa-metrische gegevens met container Insights](containers/container-insights-deployment-hpa-metrics.md) -nieuw artikel.

### <a name="insights"></a>Inzichten
- [Bewakingsoplossing in Azure Monitor](insights/solutions.md): bijgewerkt voor nieuwe UI.
- [Netwerkprestatiemeter-oplossing in Azure](insights/network-performance-monitor.md): ondersteunde werkruimteregio's bijgewerkt.


### <a name="logs"></a>Logboeken
- [Veelgestelde vragen voor Azure Monitor](faq.md): vermelding toegevoegd over het verwijderen van gegevens uit een werkruimte. Vermelding toegevoegd over 502- en 503-reacties.
  - [Uw implementatie van Azure Monitor-logboeken ontwerpen](logs/design-logs-deployment.md): sectie over limiet op opnamevolume bijgewerkt.
- [Gebruik en kosten voor Azure Monitor-logboeken beheren](logs/manage-cost-storage.md): gebruiksquery's bijgewerkt voor een efficiëntere query-indeling.
- [Logboekquery's in Azure Monitor optimaliseren](logs/query-optimization.md): specifieke waarden toegevoegd aan prestatie-indicatoren.
- [Resource Manager-voorbeeldsjablonen voor diagnostische instellingen](essentials/resource-manager-diagnostic-settings.md): voorbeeld toegevoegd voor auditlogboeken voor logboekquery.


### <a name="platform-logs"></a>Platformlogboeken
- [Diagnostische instellingen maken om logboeken en metrische gegevens van het platform te verzenden naar verschillende bestemmingen](essentials/diagnostic-settings.md): regionale vereiste toegevoegd voor diagnostische instellingen.

### <a name="visualizations"></a>Visualisaties
- [Overzicht van Azure Monitor Workbooks](visualize/workbooks-overview.md): video toegevoegd.
- [Een Azure Workbook-sjabloon verplaatsen naar een andere regio](visualize/workbook-templates-move-region.md): nieuw artikel.
- [Een Azure Workbook verplaatsen naar een andere regio](visualize/workbooks-move-region.md): nieuw artikel.



## <a name="july-2020"></a>Juli 2020

### <a name="general"></a>Algemeen
- [Implementeer de Azure monitor](deploy-scale.md) herstructureren van de voor bereiding van VM Insights-onboarding.
- [Azure Private Link gebruiken om netwerken veilig te verbinden met Azure Monitor](logs/private-link-security.md) - Sectie toegevoegd over limieten.

### <a name="alerts"></a>Waarschuwingen
- [Actieregels voor Azure Monitor-waarschuwingen](alerts/alerts-action-rules.md) - CLI-processen toegevoegd.
- [Actiegroepen maken en beheren in Azure Portal](alerts/action-groups.md) - Bijgewerkt in overeenstemming met wijzigingen in UI.
- [Voorbeeldquery's in Azure Monitor-logboekanalyse](logs/example-queries.md): nieuw artikel.
- [Problemen met logboekwaarschuwingen oplossen in Azure Monitor](alerts/alerts-troubleshoot-log.md) - Sectie toegevoegd over het quotum voor waarschuwingsregels.
- [Het oplossen van problemen met Azure waarschuwingen voor metrische gegevens](alerts/alerts-troubleshoot-metric.md) - Sectie toegevoegd over een waarschuwingsregel voor een aangepaste metrische waarde die nog niet is verzonden.
- [Begrijpen hoe waarschuwingen voor metrische gegevens werken in Azure Monitor](alerts/alerts-metric-overview.md): Aanbeveling toegevoegd voor het selecteren van de granulariteit van aggregatie.

### <a name="application-insights"></a>Application Insights
- [Opmerkingen bij de release voor de Azure web-appextensie - Application Insights](app/web-app-extension-release-notes.md) - Nieuw artikel.
- [Voorbeelden van Resource Manager-sjablonen voor Application Insights-resources](app/resource-manager-app-resource.md) - Nieuw artikel.
- [Problemen oplossen met de Azure Application Insights Profiler](app/profiler-troubleshooting.md) - Opmerking toegevoegd over een fout bij het uitvoeren van de profiler voor ASP.NET Core Apps op Azure App Service. 

### <a name="containers"></a>Containers
- [Logboek waarschuwingen vanuit container Insights](containers/container-insights-log-alerts.md) -nieuw artikel.
- [Metrische waarschuwingen van container Insights](containers/container-insights-metric-alerts.md) -nieuw artikel.

### <a name="logs"></a>Logboeken
- [Door klant beheerde sleutel van Azure Monitor](logs/customer-managed-keys.md) - Er is een foutbericht en sectie over CMK-configuratie voor query's toegevoegd.
- [Azure Monitor HTTP Data Collector API](logs/data-collector-api.md) - Python 3-voorbeeld toegevoegd.
- [Het bijhouden van query's optimaliseren in Azure Monitor](logs/query-optimization.md) - Sectie toegevoegd over het voorkomen van meerdere datascans bij het gebruik van subquery's.
- [Zelfstudie: Aan de slag met de Log Analytics-query's](./logs/log-analytics-tutorial.md) - Video toegevoegd.

### <a name="platform-logs"></a>Platformlogboeken
- [Diagnostische instellingen maken om logboeken en metrische gegevens van het platform te verzenden naar verschillende bestemmingen](essentials/diagnostic-settings.md) - Video toegevoegd.
- [Voorbeelden van Resource Manager-sjablonen voor Azure Monitor](/resource-manager-samples.md) - ARM-voorbeeld met gebruik van het doeltype van het logboek toegevoegd. 

### <a name="solutions"></a>Oplossingen
- [Bewakingsoplossingen in Azure Monitor](insights/solutions.md) - CLI-processen toegevoegd.
- [Office 365-beheeroplossing in Azure](insights/solution-office-365.md) - Datum buiten gebruik stellen gewijzigd.

### <a name="virtual-machines"></a>Virtuele machines

Nieuwe en bijgewerkte artikelen van het herstructureren van de inhoud van de VM Insights

- [Wat is VM Insights?](vm/vminsights-overview.md)
- [Log Analytics werkruimte configureren voor VM Insights](vm/vminsights-configure-workspace.md)
- [Linux-computers verbinden met Azure Monitor](agents/agent-linux.md)
- [Azure Monitor inschakelen voor een hybride omgeving](vm/vminsights-enable-hybrid.md)
- [Azure Monitor inschakelen voor een enkele virtuele-machine of een virtuele-machineschaalset in Azure Portal](vm/vminsights-enable-portal.md)
- [VM Insights inschakelen met behulp van Azure Policy](./vm/vminsights-enable-policy.md)
- [Overzicht van VM Insights inschakelen](vm/vminsights-enable-overview.md)
- [VM Insights inschakelen met Power shell](vm/vminsights-enable-powershell.md)
- [VM Insights inschakelen met behulp van Resource Manager-sjablonen](vm/vminsights-enable-resource-manager.md)
- [VM Insights inschakelen met Power shell of sjablonen](./vm/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Visualisaties
- [Visualisaties in uw Log Analytics-dashboard bijwerken](logs/dashboard-upgrade.md) - Bijgewerkte vernieuwingssnelheid.
- [Gegevens visualiseren van Azure Monitor](visualizations.md) - Video toegevoegd.


## <a name="june-2020"></a>Juni 2020

### <a name="general"></a>Algemeen
- [Azure Monitor implementeren ](deploy-scale.md) - nieuw artikel.
- [Door de klant beheerde sleutel in Azure Monitor](logs/customer-managed-keys.md) - bijgewerkte eigenschap billingtype. PowerShell-opdrachten toegevoegd.

### <a name="agents"></a>Agents
- [Overzicht van Log Analytics-agent](agents/log-analytics-agent.md) - Python 2-vereisten toegevoegd.

### <a name="alerts"></a>Waarschuwingen
- [Waarschuwingsregels of actieregels bijwerken wanneer de doelresource wordt verplaatst naar een andere Azure-regio](alerts/alerts-resource-move.md) - Nieuw artikel.
- [Problemen met metrische waarschuwingen in Azure oplossen](alerts/alerts-troubleshoot-metric.md) - Nieuw artikel.
- [Problemen met logboekwaarschuwingen in Azure Monitor oplossen](alerts/alerts-troubleshoot-metric.md) - Nieuw artikel.
  
### <a name="application-insights"></a>Application Insights
- [Azure Application Insights voor JavaScript-webapps](app/javascript.md) - Update van de sectie JavaScript SDK. Fragment om fouten bij het laden te rapporteren bijgewerkt.
- [BYOS configureren (Bring Your Own Storage) voor Profiler en Snapshot Debugger](app/profiler-bring-your-own-storage.md) - Nieuw artikel.
- [Inkomende verzoeken traceren in Azure Application Insights met OpenCensus Python](app/opencensus-python-request.md) - Bijgewerkte logboekregistratie en configuratie voor OpenCensus.
- [Een live ASP.NET-web-app bewaken met Azure-Application Insights](app/monitor-performance-live-website-now.md) - Bijgewerkte afschaffingsdatum voor Status Monitor v1.
- [Node.js-services bewaken met Azure Application Insights](app/nodejs.md) - Meerdere updates, waaronder migratie van eerdere versies en SDK-configuratie
- [Python-toepassingen bewaken met Azure Monitor (preview)](app/opencensus-python.md) - Sectie toegevoegd over de configuratie van Azure Monitor Exporters.
- [Uw apps bewaken zonder codewijzigingen - Automatische instrumentatie voor Azure Monitor Application Insights](app/codeless-overview.md) - Nieuw artikel.
- [Problemen oplossen met het laden van de SDK voor Javascript-webtoepassingen](app/javascript-sdk-load-failure.md) - Nieuw artikel.

### <a name="containers"></a>Containers
- [De bewaking van uw hybride Kubernetes-cluster stopzetten](containers/container-insights-optout-hybrid.md) - Sectie toegevoegd voor Kubernetes met ingeschakelde Arc.
- [Configureer Azure Arc enabled Kubernetes-cluster met container Insights](containers/container-insights-enable-arc-enabled-clusters.md) -nieuw artikel.
- [Azure Red Hat open Shift v4. x configureren met container Insights](containers/container-insights-azure-redhat4-setup.md) -bijgewerkte vereisten.
- [Container Insights live data (preview) instellen](containers/container-insights-livedata-setup.md) -verwijderde opmerking over de functie die niet beschikbaar is in de Amerikaanse overheid van Azure.

### <a name="insights"></a>Inzichten
- [Veelgestelde vragen - Netwerkprestatiemeter-oplossing in Azure](insights/network-performance-monitor-faq.md) - Veelgestelde vragen toegevoegd voor ExpressRoute Monitor.

### <a name="logs"></a>Logboeken
- [Azure Log Analytics-werkruimte verwijderen en herstellen](logs/delete-workspace.md): PowerShell-opdracht toegevoegd. Probleemoplossing bijgewerkt.
- [Log Analytics-werkruimten beheren in Azure Monitor](logs/manage-access.md) - Voorbeeld toegevoegd voor niet-toegelaten tabellen in Azure RBAC-sectie.
- [Het gebruik en de kosten voor Azure Monitor-logboeken beheren](logs/manage-cost-storage.md) - Aanvullende details over de berekening van de gegevensgrootte. Configuratie van waarschuwingen voor gegevensvolumes bijgewerkt. Details over de beveiligingsgegevens die door Azure Sentinel worden verzameld. Toelichting bij gegevenslimiet.
- [Azure Monitor-logboeken gebruiken met Azure Logic Apps en Power Automate](logs/logicapp-flow-connector.md) - Connectorlimieten toegevoegd.

### <a name="metrics"></a>Metrische gegevens
- [Door Azure Monitor ondersteunde metrische gegevens per resourcetype](essentials/metrics-supported.md) - Metrische gegevens van SQL Server bijgewerkt.


### <a name="platform-logs"></a>Platformlogboeken

- [Voorbeeldsjablonen van Resource Manager voor diagnostische instellingen](essentials/resource-manager-diagnostic-settings.md) - Oplossing voor diagnostische instelling voor activiteitenlogboek.
- [Azure-activiteitenlogboek verzenden naar Log Analytics-werkruimte met behulp van een Azure-portal](essentials/quick-collect-activity-log-portal.md) - Nieuw artikel.
- [Azure-activiteitenlogboek verzenden naar Log Analytics-werkruimte met behulp van Azure Resource Manager-sjabloon](essentials/quick-collect-activity-log-arm.md) - Nieuw artikel.

Nieuwe en bijgewerkte artikelen over de herstructurering en consolidatie van de logboekinhoud van het platform

- [Azure-resourcelogboeken archiveren naar opslagaccount](./essentials/resource-logs.md#send-to-azure-storage)
- [Azure-gebeurtenisschema in het activiteitenlogboek](essentials/activity-log-schema.md)
- [Azure-activiteitenlogboek](essentials/activity-log.md)
- [CLI-voorbeelden van Azure Monitor](/cli-samples.md)
- [Azure Monitor PowerShell-voorbeelden](/powershell-samples.md)
- [Azure Monitoring REST API-overzicht](essentials/rest-api-walkthrough.md)
- [Ondersteunde services en schema's voor Azure-resourcelogboeken](./essentials/resource-logs-schema.md)
- [Azure-resourcelogboeken](essentials/resource-logs.md)
- [Azure-activiteitenlogboek in Azure Monitor verzamelen en analyseren](./essentials/activity-log.md)
- [Azure-resourcelogboeken verzamelen in Log Analytics-werkruimte](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Diagnostische instellingen maken om logboeken en metrische gegevens van het platform te verzenden naar verschillende bestemmingen](essentials/diagnostic-settings.md)
- [Azure-activiteitenlogboek exporteren](./essentials/activity-log.md#legacy-collection-methods)
- [Overzicht van Azure-platformlogboeken](essentials/platform-logs-overview.md)
- [Azure-platformlogboeken naar een event hub streamen](./essentials/resource-logs.md#send-to-azure-event-hubs)
- [Azure Activity-logboekgebeurtenissen weergeven in Azure Monitor](./essentials/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Virtuele machines
- [Schakel VM Insights in azure Portal](./vm/vminsights-enable-portal.md) -bijgewerkt om Azure-Arc op te stellen.
- [Overzicht van VM Insights inschakelen](vm/vminsights-enable-overview.md) -bijgewerkt zodat Azure-Arc wordt vermeld.
- [Wat is VM Insights?](vm/vminsights-overview.md) - Bijgewerkt met Azure-Arc.


### <a name="visualizations"></a>Visualisaties
- [Gegevensbronnen voor Azure Monitor-werkmappen](visualize/workbooks-data-sources.md) - Waarschuwingen en de sectie Aangepaste eindpunten toegevoegd.
- [Problemen oplossen met op werkmappen gebaseerde inzichten in Azure Monitor](insights/troubleshoot-workbooks.md) - Nieuw artikel.
- [Visualisaties in uw Log Analytics-dashboard bijwerken](logs/dashboard-upgrade.md) - Nieuw artikel.



## <a name="may-2020"></a>Mei 2020

### <a name="general"></a>Algemeen

- [Veelgestelde vragen over Azure Monitor](faq.md): toegevoegde sectie voor metrische gegevens.
- [Door de klant beheerde Azure Monitor-sleutel](logs/customer-managed-keys.md): verschillende wijzigingen in voorbereiding van algemene beschikbaarheid.
- [Ingebouwde beleidsdefinities voor Azure Monitor](./policy-reference.md): nieuw artikel.
- [Opslagaccounts van klanten voor logboekopname](logs/private-storage.md): nieuw artikel.
- [Gebruik en kosten beheren voor Azure Monitor-logboeken](logs/manage-cost-storage.md): proportionele clusterfacturering toegevoegd.
- [Azure Private Link gebruiken om netwerken veilig te verbinden met Azure Monitor](logs/private-link-security.md): nieuw artikel.


#### <a name="new-resource-manager-template-samples"></a>Nieuwe Azure Resource Manager-voorbeeldsjablonen 
- [Resource Manager-voorbeeldsjablonen voor Azure Monitor](/resource-manager-samples.md)
- [Resource Manager-voorbeeldsjablonen voor actiegroepen](alerts/resource-manager-action-groups.md)
- [Resource Manager-voorbeeldsjablonen voor agents](agents/resource-manager-agent.md)
- [Voor beelden van Resource Manager-sjablonen voor container Insights](containers/resource-manager-container-insights.md)
- [Voor beelden van Resource Manager-sjablonen voor VM Insights](vm/resource-manager-vminsights.md)
- [Resource Manager-voorbeeldsjablonen voor diagnostische instellingen](essentials/resource-manager-diagnostic-settings.md)
- [Resource Manager-voorbeeldsjablonen voor Log Analytics-werkruimten](logs/resource-manager-workspace.md)
- [Resource Manager-voorbeeldsjablonen voor logboekquery’s](logs/resource-manager-log-queries.md)
- [Resource Manager-voorbeeldsjablonen voor logboekquerywaarschuwingen](alerts/resource-manager-alerts-log.md)
- [Resource Manager-voorbeeldsjablonen voor metrische waarschuwingen](alerts/resource-manager-alerts-metric.md)
- [Resource Manager-voorbeeldsjablonen voor werkmappen](visualize/resource-manager-workbooks.md)

### <a name="agents"></a>Agents
- [WAD-extensie (Windows Azure Diagnostics) installeren en configureren](agents/diagnostics-extension-windows-install.md): meer details toegevoegd voor het configureren van diagnostische gegevens.
- [Overzicht Logboekanalyse-agent](agents/log-analytics-agent.md): ondersteunde Linux-versies toegevoegd.

### <a name="application-insights"></a>Application Insights

- [Toepassingen bewaken die worden uitgevoerd op Azure Functions met Application Insights, Azure Monitor](app/monitor-functions.md): nieuw artikel.
- [Node.js-services bewaken met Azure Application Insights](app/nodejs.md): algemene updates met inbegrip van nieuwe sectie over migratie van eerdere versies.
- [IP-adressen gebruikt door Application Insights en logboekanalyse](app/ip-addresses.md): IP-adressen toegevoegd voor webhooks en de Amerikaanse overheid.
- [Toepassingen bewaken op AKS (Azure Kubernetes Service) met Application Insights, Azure Monitor](app/kubernetes-codeless.md): nieuw artikel.
- [Problemen met ontbrekende gegevens oplossen, Application Insights voor .NET](app/asp-net-troubleshoot-no-data.md): toegevoegde sectie over het verzamelen van logboeken met dotnet-trace.
- [Wijzigingsanalyse voor toepassingen gebruiken in Azure Monitor om problemen met web-apps te vinden](app/change-analysis.md): meerdere updates in de functie Wijzigingsanalyse.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Nieuwe en bijgewerkte artikelen voor preview van op werkruimte gebaseerde toepassingen
- [Op werkruimte gebaseerd resourceschema van Azure Monitor Application Insights](app/apm-tables.md)
- [Een nieuwe op werkruimte gebaseerde resource voor Azure Monitor Application Insights maken](app/create-workspace-resource.md)
- [Expressie app() in Azure Monitor-logboekquery's](logs/app-expression.md)
- [Query-bereik vastleggen in Azure Monitor-logboekanalyse](logs/scope.md)
- [Query's uitvoeren op resources met Azure Monitor](logs/cross-workspace-query.md)
- [Standaardeigenschappen in Azure Monitor-logboekrecords](./logs/log-standard-columns.md)
- [Structuur van Azure Monitor-logboeken](./logs/data-platform-logs.md)





### <a name="containers"></a>Containers
- [Container Insights inschakelen](containers/container-insights-onboard.md) -bijgewerkte firewall configuratie tabel.
- [Container Insights bijwerken voor metrische gegevens](containers/container-insights-update-metrics.md) -bijwerken voor het gebruik van beheerde identiteiten voor het verzamelen van metrische gegevens.
- Bewaak de [kosten voor container Insights](containers/container-insights-cost.md) -nieuw artikel.
- [Container Insights live data (preview) instellen](containers/container-insights-livedata-setup.md) : ondersteuning voor nieuwe cluster functie binding.

### <a name="insights"></a>Inzichten
- [Azure Monitor voor Azure Cache voor Redis (preview)](insights/redis-cache-insights-overview.md): nieuw artikel.
- [Sleutelkluis bewaken met Azure Monitor voor Key Vault (preview)](./insights/key-vault-insights-overview.md): nieuw artikel.

### <a name="logs"></a>Logboeken
- [Logboekanalyse maken en configureren met PowerShell](logs/powershell-workspace-configuration.md): sectie probleemoplossing toegevoegd.
- [Een Log Analytics-werkruimte maken in de Azure-portal](logs/quick-create-workspace.md): sectie probleemoplossing toegevoegd.
- [Een Log Analytics-werkruimte maken met Azure CLI](logs/quick-create-workspace-cli.md): sectie probleemoplossing toegevoegd.
- [Azure Log Analytics-werkruimte verwijderen en herstellen](logs/delete-workspace.md): bijgewerkte informatie over het herstellen van een verwijderde werkruimte.
- [Functies in Azure Monitor-logboekquery's](logs/functions.md): notitie verwijderd over functies die geen andere functies bevatten.
- [Structuur va Azure Monitor-logboeken](./logs/data-platform-logs.md): eigenschapsbeschrijvingen verduidelijkt voor Application Insights-tabel.
- [Azure Monitor-logboeken gebruiken met Azure Logic Apps en Power Automate](logs/logicapp-flow-connector.md): sectie met limieten toegevoegd.
- [PowerShell gebruiken om een Log Analytics-werkruimte te maken en te configureren](logs/powershell-workspace-configuration.md): sectie probleemoplossing toegevoegd.


### <a name="metrics"></a>Metrische gegevens
- [Door Azure Monitor ondersteunde metrische gegevens per resourcetype](essentials/metrics-supported.md): metrische gegevens van gasten en routering van metrische gegevens verduidelijkt. 

### <a name="solutions"></a>Oplossingen
- [Uw Active Directory-omgeving optimaliseren met Azure Monitor](insights/ad-assessment.md): Windows Server 2019 toegevoegd aan ondersteunde versies.
- [Uw SQL Server-omgeving optimaliseren met Azure Monitor](insights/sql-assessment.md): ondersteunde versies van SQL Server toegevoegd.


### <a name="virtual-machines"></a>Virtuele machines
- [Overzicht van VM Insights inschakelen](vm/vminsights-enable-overview.md) : wordt toegevoegd aan ondersteunde versies van de Ubuntu-Server. Ondersteunde regio's voor Log Analytics-werkruimte toegevoegd.
- [Grafieken van prestaties met](vm/vminsights-performance.md) de sectie beperkingen van de toegevoegde virtuele machine voor niet-beschik bare metrische gegevens.

### <a name="visualizations"></a>Visualisaties
- [Azure Monitor-werkmappen en Azure Resource Manager-sjablonen](visualize/workbooks-automate.md): Resource Manager-update toegevoegd voor implementeren van een workbooksjabloon.
- [Azure Monitor-workbookgroepen](./visualize/workbooks-groups.md): nieuw artikel.
- [Azure Monitor-workbooks, JSON-gegevens transformeren met JSONPath](visualize/workbooks-jsonpath.md): nieuw artikel.


## <a name="april-2020"></a>April 2020

### <a name="general"></a>Algemeen

- [Door de klant beheerde sleutel in Azure Monitor](logs/customer-managed-keys.md): sectie over asynchrone bewerkingen toegevoegd.
- [Log Analytics-werkruimten beheren in Azure Monitor](logs/manage-access.md): aangepaste logboeksecties bijgewerkt.

### <a name="alerts"></a>Waarschuwingen

- [Actieregels voor Azure Monitor-waarschuwingen](alerts/alerts-action-rules.md): video toegevoegd.
- [Overzicht van bewaken van waarschuwingen en meldingen in Azure](alerts/alerts-overview.md): video toegevoegd.

### <a name="application-insights"></a>Application Insights

- [Toepassingsoverzicht in Azure Application Insights](app/app-map.md): configuratie van cloudrolnamen toegevoegd voor java-agent.
- [Azure Application Insights .NET Agent API-verwijzing](app/status-monitor-v2-api-reference.md): API-verwijzingen gecombineerd.
- [IP-adressen die worden gebruikt door Application Insights en Logboekanalyse](app/ip-addresses.md): IP-adressen bijgewerkt voor AppInsights- en Logboekanalyse-API's, actiegroepwebhooks, Azure Amerikaanse overheid.
- [Java-toepassingen overal bewaken](app/java-standalone-config.md): nieuw artikel.
- [Java-toepassingen op elke omgeving bewaken](app/java-in-process-agent.md): nieuw artikel.
- [Java-toepassingen die worden uitgevoerd in elke omgeving bewaken](app/java-standalone-arguments.md): nieuw artikel.
- [Java-toepassingen bewaken die on-premises worden uitgevoerd](app/java-on-premises.md): nieuw artikel.
- [Application Insights in Visual Studio verwijderen](app/remove-application-insights.md): nieuw artikel.
- [Steekproef nemen van telemetrie in Azure Application Insights](app/sampling.md): correctie in steekproef met vaste frequentie in Python.

### <a name="containers"></a>Containers

- [Azure Red Hat open Shift v4. x configureren met container Insights](containers/container-insights-azure-redhat4-setup.md) -nieuw artikel.
- [Handmatig ServiceNow-synchronisatieproblemen oplossen](alerts/itsmc-resync-servicenow.md): nieuw artikel.
- [Bewaking van Azure- en Red Hat OpenShift v4-cluster beëindigen](containers/container-insights-optout-openshift-v4.md): nieuw artikel.
- [Bewaking van Azure Red Hat OpenShift v3-cluster beëindigen](containers/container-insights-optout-openshift-v3.md): nieuw artikel.
- [Bewaking van hybride Kubernetes-cluster beëindigen](containers/container-insights-optout-hybrid.md): nieuw artikel.

### <a name="insights"></a>Inzichten

- [Azure-sleutelkluizen bewaken met Azure Monitor voor sleutelkluizen (preview)](insights/key-vault-insights-overview.md): nieuw artikel.

### <a name="logs"></a>Logboeken

- [Servicelimieten van Azure Monitor](service-limits.md): bandbreedtebeperking voor gebruikersquery's toegevoegd.
- [Gebruik en kosten voor Azure Monitor-logboeken beheren](logs/manage-cost-storage.md): facturering voor logboekclusters toegevoegd. Kusto-query toegevoegd zodat klanten met verouderde per-node-prijscategorie kunnen bepalen of ze moeten overgaan naar een per-GB- of capaciteitsreserveringscategorie.

### <a name="metrics"></a>Metrische gegevens

- [Geavanceerde functies van Azure Metrics Explorer](essentials/metrics-charts.md): sectie aggregatie toegevoegd.

### <a name="workbooks"></a>Werkmappen

- [Azure Monitor-workbooks en Azure Resource Manager-sjablonen](visualize/workbooks-automate.md): Resource Manager-sjabloon toegevoegd voor implementeren van workbooksjabloon.

## <a name="march-2020"></a>Maart 2020

### <a name="general"></a>Algemeen

- [Overzicht Azure Monitor](overview.md): overzichtsvideo van Azure Monitor toegevoegd.
- [Door klant beheerde sleutelconfiguratie van Azure Monitor](logs/customer-managed-keys.md): algemene updates.
- [Azure Monitor-gegevensreferenties](/azure/azure-monitor/reference/): nieuwe site.

### <a name="alerts"></a>Waarschuwingen

- [Meldingen voor activiteitenlogboek maken, bekijken en beheren in Azure Monitor](alerts/alerts-activity-log.md): aanvullende uitleg van Resource Manager-sjabloon.
- [Waarschuwingen voor metrische gegevens in Azure Monitor.](alerts/alerts-metric-overview.md): bijgewerkt voor overheidsondersteuning.
- [Problemen oplossen met Azure Monitor-waarschuwingen en -meldingen](alerts/alerts-troubleshoot.md): nieuw artikel.

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights automatiseren met PowerShell](app/powershell.md): ARMClient-voorbeelden toegevoegd.
- [Continue export van telemetrie van Application Insights](app/export-telemetry.md): tabel met informatie over exportstructuur toegevoegd.
- [Snapshot Debugger voor .NET-apps inschakelen in Azure App Service](app/snapshot-debugger-appservice.md): voorbeeld van Resource Manager-sjabloon toegevoegd.
- [Gebruik en kosten voor Azure Application Insights beheren](app/pricing.md): gegevens over waarschuwingen voor gegevenslimieten toegevoegd.
- [Python-toepassingen bewaken met Azure Monitor (preview)](app/opencensus-python.md): metrische standaardgegevens toegevoegd.
- [Ondersteuning voor brontoewijzing voor JavaScript-toepassinge, Azure Monitor Application Insights](app/source-map-support.md): nieuw artikel.

### <a name="containers"></a>Containers

- [Veelgestelde vragen over Azure monitor](faq.md) -update voor container Insights.
- [CONFIGUREER GPU-bewaking met container Insights](containers/container-insights-gpu-monitoring.md) -nieuw artikel.

### <a name="insights"></a>Inzichten

- [Beheeroplossing voor Office 365 in Azure](insights/solution-office-365.md): bijgewerkte datum van afschaffing.

### <a name="logs"></a>Logboeken

- [Logboekquery's optimaliseren in Azure Monitor](logs/query-optimization.md): CPU-voorwaarde voor XML- en JSON-parsering toegevoegd.
- [Azure Log Analytics-werkruimte verwijderen en herstellen](logs/delete-workspace.md): probleemoplossing toegevoegd.
- [Azure Monitor-logboeken gebruiken met Azure Logic Apps en Power Automate](logs/logicapp-flow-connector.md): bijgewerkt voor nieuwe Azure Monitor-connector.

### <a name="metrics"></a>Metrische gegevens

- [Afschaffing van metrische schijfgegevens in de Azure-portal](essentials/portal-disk-metrics-deprecation.md): nieuw artikel.
- [Zelfstudie: Een grafiek met metrische gegevens maken in Azure Monitor](essentials/tutorial-metrics-explorer.md): video toegevoegd.

### <a name="platform-logs"></a>Platformlogboeken

- [Azure-activiteitenlogboek verzamelen en analyseren in Azure Monitor](./essentials/activity-log.md): herschreven voor betere uitleg van verzamelen van activiteitenlogboek met diagnostische instellingen.

### <a name="virtual-machines"></a>Virtuele machines

- [Virtuele Azure-machines bewaken met Azure Monitor](vm/monitor-vm-azure.md): nieuw artikel.
- [Snelstartgids: virtuele machines van Azure bewaken met Azure monitor](vm/quick-monitor-azure-vm.md) -bijgewerkt om VM Insights toe te voegen.
- [Waarschuwingen van](vm/vminsights-alerts.md) het nieuwe artikel over VM Insights.
- [Overzicht van VM Insights inschakelen](vm/vminsights-enable-overview.md) -bijgewerkte koppelingen naar de agent downloaden.

Algemene updates voor algemene Beschik baarheid van VM Insights

- [Wat is VM Insights?](vm/vminsights-overview.md)
- [Veelgestelde vragen over VM Insights (GA)](vm/vminsights-ga-release-faq.md) 
- [VM Insights inschakelen met behulp van Azure Policy](./vm/vminsights-enable-policy.md) 
- [Prestaties presen teren met VM Insights](vm/vminsights-performance.md)
- [Logboeken vanuit VM Insights doorzoeken](vm/vminsights-log-search.md)
- [App-afhankelijkheden weer geven met VM Insights](vm/vminsights-maps.md) 

### <a name="visualizations"></a>Visualisaties

- [Gegevens van Azure Monitor visualiseren](visualizations.md): bijgewerkt om geplande afschaffing van View Designer aan te geven.

## <a name="february-2020"></a>Februari 2020

### <a name="agents"></a>Agents

Meerdere updates als onderdeel van het herschrijven van inhoud van extensie voor diagnostische gegevens.

- [Overzicht van de Azure-bewakingsagents](agents/agents-overview.md): tabellen geherstructureerd voor verheldering van unieke eigenschappen van elke agent.
- [Overzicht Azure-extensie voor diagnostische gegevens](agents/diagnostics-extension-overview.md): volledig herschreven.
- [Blobopslag gebruiken voor IIS en tabelopslag voor gebeurtenissen in Azure Monitor](agents/diagnostics-extension-logs.md): algemeen aangepast voor update en helderheid.
- [Windows Azure-extensie voor diagnostische gegevens (WAD) installeren en configureren](agents/diagnostics-extension-windows-install.md): nieuw artikel. 
- [Schema van Windows diagnostische extensie](agents/diagnostics-extension-schema-windows.md): gereorganiseerd.
- [Gegevens verzenden van Windows Azure-extensie voor diagnostische gegevens naar Azure Event Hubs](agents/diagnostics-extension-stream-event-hubs.md): volledig herschreven en bijgewerkt.
- [Diagnostische gegevens opslaan en weergeven in Azure Storage](../cloud-services/diagnostics-extension-to-storage.md): volledig herschreven en bijgewerkt.
- [Logboekanalyse van extensie van virtuele machine voor Windows](../virtual-machines/extensions/oms-windows.md): verduidelijking relatie met Logboekanalyse-agent.
- [Azure Monitor-extensie van virtuele machine voor Linux](../virtual-machines/extensions/oms-linux.md): verduidelijking relatie met Logboekanalyse-agent.

### <a name="application-insights"></a>Application Insights

- [Verbindingsreeksen in Azure Application Insights](app/sdk-connection-string.md): nieuw artikel.

### <a name="insights-and-solutions"></a>Inzicht en oplossingen

#### <a name="container-insights"></a>Container inzichten

- [Integreer Azure Active Directory met de door Azure Kubernetes service](../aks/azure-ad-integration-cli.md) toegevoegde opmerking voor het maken van een client toepassing ter ondersteuning van Kubernetes RBAC-enabled-cluster ter ondersteuning van container Insights.

#### <a name="vm-insights"></a>VM Insights

- [Veelgestelde vragen over VM Insights (ga)](vm/vminsights-ga-release-faq.md) : Wijzig hoe prestatie gegevens worden opgeslagen.

#### <a name="office-365"></a>Office 365

- [Beheeroplossing voor Office 365 in Azure](insights/solution-office-365.md): bijgewerkte datum van afschaffing.


### <a name="logs"></a>Logboeken

- [Logboekquery's optimaliseren in Azure Monitor](logs/query-optimization.md): nieuw artikel.
- [Gebruik en kosten beheren voor Azure Monitor-logboeken](logs/manage-cost-storage.md): verbeterde voorbeeldquery's voor beter begrip van uw gebruik.

### <a name="metrics"></a>Metrische gegevens

- [Metrische gegevens van Azure Monitor-platform exporteren via diagnostische instellingen](essentials/metrics-supported-export-diagnostic-settings.md): sectie toegevoegd over wijziging van gedrag voor null en nulwaarden.

### <a name="visualizations"></a>Visualisaties

Meerdere nieuwe artikelen voor conversiegids View Designer naar werkmappen.

- [Overgangsgids voor Azure Monitor View Designer naar werkmappen](visualize/view-designer-conversion-overview.md): nieuw artikel.
- [Opties voor conversie van Azure Monitor View Designer naar werkmappen](visualize/view-designer-conversion-options.md): nieuw artikel.
- [Tegelconversies van Azure Monitor View Designer naar werkmappen](visualize/view-designer-conversion-tiles.md): nieuw artikel.
- [Overzicht van en toegang tot conversie van Azure Monitor View Designer naar werkmappen](visualize/view-designer-conversion-access.md): nieuw artikel.
- [Algemene taken voor conversie van Azure Monitor View Designer naar werkmappen](visualize/view-designer-conversion-tasks.md): nieuw artikel.
- [Voorbeelden van conversie van Azure Monitor View Designer naar werkmappen](visualize/view-designer-conversion-examples.md): nieuw artikel.

## <a name="january-2020"></a>Januari 2020

### <a name="general"></a>Algemeen

- [Wat wordt er bewaakt door Azure Monitor?](monitor-reference.md): nieuw artikel.

### <a name="agents"></a>Agents

- [Logboekgegevens verzamelen met Azure Log Analytics-agent](agents/log-analytics-agent.md): tabel met vereisten voor netwerkfirewall bijgewerkt.

### <a name="alerts"></a>Waarschuwingen

- [Actiegroepen maken en beheren in de Azure-portal](alerts/action-groups.md): instelling verwijderd voor v2-functies die niet langer nodig is.
- [Een waarschuwing voor metrische gegevens maken met een Resource Manager-sjabloon](alerts/alerts-metric-create-templates.md): voorbeeld voor de parameter *ignoreDataBefore* toegevoegd.  Beperkingen toegevoegd over regels met meerdere criteria.
- [Logboekanalysewaarschuwing REST API gebruiken](alerts/api-alerts.md): JSON-voorbeeld gecorrigeerd.

### <a name="application-insights"></a>Application Insights

- [IP-adressen die worden gebruikt door Application Insights en logboekanalyse](app/ip-addresses.md): sectie beschikbaarheidstest bijgewerkt met hoe een regel moet worden toegevoegd voor binnenkomende poort, om verkeer toe te staan dat Azure Netwerkbeveiligingsgroepen gebruikt.
- [Problemen oplossen met Azure Application Insights Profiler](app/profiler-troubleshooting.md): algemene probleemoplossing bijgewerkt.
- [Steekproef nemen van telemetrie in Azure Application Insights](app/sampling.md): bijgewerkt en opnieuw gestructureerd om leesbaarheid te verbeteren op basis van klantenfeedback.

### <a name="data-security"></a>Gegevensbeveiliging

- [Door klant beheerde sleutelconfiguratie van Azure Monitor](logs/customer-managed-keys.md): nieuw artikel.

### <a name="insights-and-solutions"></a>Inzicht en oplossingen

#### <a name="container-insights"></a>Container inzichten

- [Container Insights-agent gegevens verzameling configureren](containers/container-insights-agent-config.md) : Details toegevoegd voor het bijwerken van de agent op Azure Red Hat open Shift en aanvullende informatie is toegevoegd om de methoden voor het bijwerken van de agent te onderscheiden.
- [Maak prestatie waarschuwingen voor container Insights](./containers/container-insights-log-alerts.md) : herziene informatie en bijgewerkte stappen voor het maken van een waarschuwing voor prestatie gegevens die in de werk ruimte zijn opgeslagen met behulp van de werk ruimte-context waarschuwingen.
- [Kubernetes bewaking met container Insights](containers/container-insights-analyze.md) : het artikel overzicht en het artikel analyseren met betrekking tot ondersteuning van Windows Kubernetes-clusters bijgewerkt.
- [Azure Red Hat open Shift-clusters configureren met container Insights](containers/container-insights-azure-redhat-setup.md) -Details toegevoegd voor het bijwerken van de agent op Azure Red Hat open Shift en extra informatie toevoegen om de methoden voor het bijwerken van de agent te onderscheiden.
- [Configureer hybride Kubernetes-clusters met container Insights](containers/container-insights-hybrid-setup.md) -bijgewerkt om toegevoegde ondersteuning voor beveiligde poort: 10250 met de Kubelet-cAdvisor te weer spie gelen.
- [Instructies voor het beheren van de container Insights-agent](containers/container-insights-manage-agent.md) -bijgewerkte details met betrekking tot gedrag en configuratie van metrische uitval met Azure Red Hat open Shift vergeleken met andere typen Kubernetes-clusters.
- [Prometheus-integratie met container Insights configureren](containers/container-insights-prometheus-integration.md) -bijgewerkte details met betrekking tot gedrag en configuratie van metrische uitval tijd met Azure Red Hat open Shift vergeleken met andere typen Kubernetes-clusters.
- [Container Insights bijwerken voor metrische](containers/container-insights-update-metrics.md) gegevens-bijgewerkte details met betrekking tot gedrag en configuratie van metrische uitval tijd met Azure Red Hat open Shift vergeleken met andere typen Kubernetes-clusters.

#### <a name="vm-insights"></a>VM Insights

- [Veelgestelde vragen over VM Insights (ga)](vm/vminsights-ga-release-faq.md) : er is informatie toegevoegd over de upgrade van de werk ruimte en agents naar de nieuwe versie.

#### <a name="office-365"></a>Office 365

- [Office 365-beheeroplossing in Azure](insights/solution-office-365.md): details en veelgestelde vragen toegevoegd over migratie naar Office 365-oplossing in Azure Sentinel. Sectie onboarding verwijderd.

### <a name="logs"></a>Logboeken

- [Log Analytics-werkruimten beheren in Azure Monitor](logs/manage-access.md): updates voor Niet-acties.
- [Gebruik en kosten voor Azure Monitor-logboeken beheren](logs/manage-cost-storage.md): verduidelijking toegevoegd van berekening van gegevensvolume in de sectie Prijsmodel.
- [Azure Resource Manager-sjablonen gebruiken om een logboekanalysewerkruimte te maken en te configureren](./logs/resource-manager-workspace.md): sjabloon bijgewerkt met nieuwe prijscategorieën.

### <a name="platform-logs"></a>Platformlogboeken

- [Azure-activiteitenlogboek verzamelen met diagnostische instellingen, Azure Monitor](./essentials/activity-log.md): extra informatie over gewijzigde eigenschappen.
- [Azure-activiteitenlogboek exporteren](./essentials/activity-log.md#legacy-collection-methods): bijgewerkt voor gebruikersinterface. 

## <a name="december-2019"></a>December 2019

### <a name="agents"></a>Agents

- [Linux-computers verbinden met Azure Monitor](agents/agent-linux.md): nieuw artikel.

### <a name="alerts"></a>Waarschuwingen

- [Een waarschuwing voor metrische gegevens maken met een Resource Manager-sjabloon](alerts/alerts-metric-create-templates.md): voorbeeld toegevoegd voor aangepaste metrische gegevens.
- [Waarschuwingen met dynamische drempelwaarden maken in Azure Monitor](alerts/alerts-dynamic-thresholds.md): sectie toegevoegd over interpretatie van grafieken van dynamische drempels.
- [Overzicht van bewaken van waarschuwingen en meldingen in Azure](alerts/alerts-overview.md): query bijgewerkt voor Resource Graph.
- [Ondersteunde resources voor metrische waarschuwingen in Azure Monitor](alerts/alerts-metric-near-real-time.md): update voor ondersteunde metrische gegevens en dimensies.
- [Schakelen van verouderde Log Analytics-waarschuwings-API naar nieuwe Azure-waarschuwings-API](alerts/alerts-log-api-switch.md): opmerking toegevoegd over aangepaste waarschuwingsnaam.
- [Begrijpen hoe waarschuwingen voor metrische gegevens werken in Azure Monitor](alerts/alerts-metric-overview.md): ondersteunde resourcetypen voor bewaking op schaal toegevoegd.

### <a name="application-insights"></a>Application Insights

- [Application Insights voor Worker Service-apps (niet-HTTP-apps)](app/worker-service.md): standaardlogboekniveau toegevoegd aan C#-code. Versie van verwijzing van pakket bijgewerkt.
- [ApplicationInsights.config-referentie, Azure](app/configuration-with-applicationinsights-config.md): voorbeeldcode bijgewerkt.
- [Azure Application Insights bijwerken met PowerShell](app/powershell.md): update van Resource Manager-sjabloon.
- [Nieuwe Azure Application Insights-resource maken](app/create-new-resource.md): opmerking toegevoegd aan wereldwijde unieke naam.
- [Diagnose met Live Metrics Stream, Azure Application Insights](app/live-stream.md): versievereisten bijgewerkt voor ASP.NET Core SDK.
- [Gebeurtenisteller in Application Insights](app/eventcounters.md): categorie en tabel bijgewerkt voor customMetrics.
- [Java-traceringlogboeken verkennen in Azure Application Insights](app/java-trace-logs.md): configuratie toegevoegd voor logboekdrempel Java-agent.
- [IP-adressen gebruikt door Application Insights en logboekanalyse](app/ip-addresses.md): IP-adressen bijgewerkt voor Live Metrics Stream.
- [Prestaties van Azure-appservices bewaken](app/azure-web-apps.md): ondersteuning toegevoegd voor ASP.NET Core 3.0. 
- [Python-toepassingen met Azure Monitor bewaken (preview)](app/opencensus-python.md): verduidelijking toegevoegd voor OpenCensus Python schematoewijzing aan Azure .Monitor-schema.
- [Releaseopmerkingen voor Azure Application Insights](app/release-notes.md): opmerkingen toegevoegd voor oudere releases.
- [Telemetriekanalen in Azure Application Insights](app/telemetry-channels.md): duur bijgewerkt van genegeerde gegevens tijdens verlengde periode van verloren verbinding.
- [Telemetriesteekproeven in Azure Application Insights](app/sampling.md): codefragment gecorrigeerd voor aangepaste TelemetryInitializer.
- [Problemen oplossen met Application Insights in een Java-webproject](app/java-troubleshoot.md): instructie verwijderd over niet ondersteunen van afhankelijkheidsverzameling in JDK 9.

### <a name="insights-and-solutions"></a>Inzicht en oplossingen

- [Veelgestelde vragen over container Insights](./faq.md) : er is een vraag toegevoegd over de velden afbeelding en naam.
- [Azure SQL-analyseoplossing in Azure Monitor](insights/azure-sql.md): ondersteuning bijgewerkt voor database wacht op beheerd exemplaar.
- [Container Insights-agent voor het verzamelen van gegevens configureren](containers/container-insights-agent-config.md) : toegevoegde instelling voor enrich_container_logs.
- [Configureer hybride Kubernetes-clusters met container Insights](containers/container-insights-hybrid-setup.md) -toegevoegde probleemoplossings sectie.
- [Active Directory-replicatiestatus bewaken met Azure Monitor](insights/ad-replication-status.md): vereisten .NET Framework bijgewerkt.
- [Netwerkprestatiemeter-oplossing in Azure](insights/network-performance-monitor.md): ondersteunde regio's bijgewerkt.
- [Active Directory-omgeving optimaliseren met Azure Monitor](insights/ad-assessment.md): vereisten .NET Framework bijgewerkt.
- [SQL Server-omgeving optimaliseren met Azure Monitor](insights/sql-assessment.md): vereisten .NET Framework bijgewerkt.
- [System Center Operations Manager-omgeving optimaliseren met Azure Monitor](insights/scom-assessment.md): vereisten .NET Framework bijgewerkt.
- [Ondersteunde verbindingen met IT Service Management-connector in Azure-logboekanalyse](alerts/itsmc-connections.md): New York toegevoegd aan vereist client-ID en clientgeheim.

### <a name="logs"></a>Logboeken

- [Azure Log Analytics-werkruimte verwijderen en herstellen](logs/delete-workspace.md): PowerShell-methode toegevoegd.
- [Azure Monitor-logboekimplementatie ontwerpen](logs/design-logs-deployment.md): opnamefrequentie van een werkruimte verhoogd.

### <a name="metrics"></a>Metrische gegevens

- [Metrische gegevens Azure Monitor-platform exporteren via diagnostische instellingen](essentials/metrics-supported-export-diagnostic-settings.md): nieuw artikel.

### <a name="platform-logs"></a>Platformlogboeken

Meerdere artikelen bijgewerkt als onderdeel van herstructurering van platformlogboeken op basis van nieuwe functie voor configuratie van activiteitenlogboek met diagnostische instellingen.

- [Azure-resourcelogboeken archiveren naar opslagaccount](./essentials/resource-logs.md#send-to-azure-storage)
- [Azure-gebeurtenisschema in het activiteitenlogboek](essentials/activity-log-schema.md)
- [Servicebeperkingen van Azure Monitor](service-limits.md)
- [Azure-activiteitenlogboeken verzamelen en analyseren in Log Analytics-werkruimte](./essentials/activity-log.md)
- [Azure-activiteitenlogboek verzamelen met diagnostische instellingen (preview), Azure Monitor](./essentials/activity-log.md)
- [Azure-activiteitenlogboeken verzamelen in een Log Analytics-werkruimte over Azure-tenants](./essentials/activity-log.md)
- [Azure-resourcelogboeken verzamelen in Log Analytics-werkruimte](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Diagnostische instellingen in Azure maken met Resource Manager-sjabloon](./essentials/resource-manager-diagnostic-settings.md)
- [Diagnostische instelling maken die logboeken metrische gegevens verzamelt in Azure](essentials/diagnostic-settings.md)
- [Azure-activiteitenlogboek exporteren](./essentials/activity-log.md#legacy-collection-methods)
- [Overzicht van Azure-platformlogboeken](essentials/platform-logs-overview.md)
- [Bewakingsgegevens van Azure naar een event hub streamen](essentials/stream-monitoring-data-event-hubs.md)
- [Azure-platformlogboeken naar een event hub streamen](./essentials/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Snelstarts en zelfstudies

- [Een grafiek met metrische gegevens maken in Azure Monitor](essentials/tutorial-metrics-explorer.md): nieuw artikel.
- [Resourcelogboeken verzamelen van een Azure-resource en analyseren met Azure Monitor](essentials/tutorial-resource-logs.md): nieuw artikel.
- [Een Azure-resource bewaken met Azure Monitor](essentials/quick-monitor-azure-resource.md): nieuw artikel.
   
## <a name="next-steps"></a>Volgende stappen

- Zie [Gids voor inzenders van documentatie](/contribute/) als u wilt bijdragen aan Azure Monitor-documentatie.