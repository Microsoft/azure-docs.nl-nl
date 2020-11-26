---
title: Virtuele Azure-machines bewaken met Azure Monitor
description: Hierin wordt beschreven hoe u bewakings gegevens van virtuele machines in azure verzamelt en analyseert met behulp van Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: fc89790c7d268bcfa0c08bd26249bc91979d7fca
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186895"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Virtuele Azure-machines bewaken met Azure Monitor
In dit artikel wordt beschreven hoe u Azure Monitor kunt gebruiken om bewakings gegevens van virtuele Azure-machines te verzamelen en analyseren om hun status te behouden. Virtuele machines kunnen worden bewaakt voor Beschik baarheid en prestaties met Azure Monitor zoals elke [andere Azure-resource](monitor-azure-resource.md), maar ze zijn uniek van andere resources, aangezien u ook de gast besturingssystemen en het systeem en de werk belastingen die hierop worden uitgevoerd, moet bewaken. 

> [!NOTE]
> In dit artikel vindt u een volledig overzicht van de concepten en opties voor het bewaken van virtuele machines in Azure Monitor. Zie [Quick Start: een virtuele machine van Azure bewaken met Azure monitor](../learn/quick-monitor-azure-vm.md)als u snel uw virtuele machines wilt bewaken zonder de onderliggende concepten te benadrukt.


## <a name="differences-from-other-azure-resources"></a>Verschillen van andere Azure-resources
Het [bewaken van Azure-resources met Azure monitor](monitor-azure-resource.md) beschrijft de bewakings gegevens die zijn gegenereerd door Azure-resources en hoe u de functies van Azure monitor kunt gebruiken om deze gegevens te analyseren en te waarschuwen. U kunt dezelfde bewakings gegevens van virtuele Azure-machines verzamelen en gebruiken met de volgende verschillen:

-  [Platform metrieken](../platform/data-platform-metrics.md) worden automatisch verzameld voor virtuele machines, maar alleen voor de host van de [virtuele machine](#monitoring-data). U hebt een agent nodig voor het verzamelen van prestatie gegevens van het gast besturingssysteem. 
- Virtuele machines genereren geen [resource logboeken](../platform/platform-logs-overview.md) die inzicht bieden in bewerkingen die zijn uitgevoerd in een Azure-resource. U gebruikt een agent voor het verzamelen van logboek gegevens van het gast besturingssysteem.
- U kunt [Diagnostische instellingen](../platform/diagnostic-settings.md) voor een virtuele machine maken om platform metrieken te verzenden naar andere bestemmingen, zoals opslag en Event hubs, maar u kunt deze diagnostische instellingen niet configureren in de Azure Portal. 

## <a name="monitoring-data"></a>Bewakingsgegevens
Virtuele machines in azure genereren [Logboeken](../platform/data-platform-logs.md) en [metrische gegevens](../platform/data-platform-metrics.md) , zoals wordt weer gegeven in het volgende diagram.

![Overzicht](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Host van virtuele machine
Met virtuele machines in Azure worden de volgende gegevens voor de virtuele-machine host op dezelfde manier gegenereerd als andere Azure-resources, zoals beschreven in [gegevens bewaken](monitor-azure-resource.md#monitoring-data).

- [Platform metrieken](../platform/data-platform-metrics.md) : numerieke waarden die automatisch met regel matige tussen pozen worden verzameld en een zekere hoogte van een resource op een bepaald moment beschrijven. Er worden platform metrieken voor de virtuele-machinehost verzameld, maar u hebt de diagnostische extensie nodig voor het verzamelen van metrische gegevens voor het gast besturingssysteem.
- [Activiteiten logboek](../platform/platform-logs-overview.md) : biedt inzicht in de bewerkingen op elke Azure-resource in het abonnement van buiten (het beheer vlak). Voor een virtuele machine omvat dit informatie zoals wanneer het is gestart en eventuele configuratie wijzigingen.


### <a name="guest-operating-system"></a>Gastbesturingssysteem
Als u gegevens wilt verzamelen uit het gast besturingssysteem van een virtuele machine, hebt u een agent nodig die lokaal op elke virtuele machine wordt uitgevoerd en gegevens verzendt naar Azure Monitor. Er zijn meerdere agents beschikbaar voor Azure Monitor waarbij elk verschillende gegevens verzamelt en gegevens naar verschillende locaties schrijft. Bekijk een gedetailleerde vergelijking van de verschillende agents op [overzicht van de Azure monitor agents](../platform/agents-overview.md). 

- [Log Analytics-agent](../platform/agents-overview.md#log-analytics-agent) : beschikbaar voor virtuele machines in azure, andere Cloud omgevingen en on-premises. Hiermee worden gegevens verzameld voor Azure Monitor Logboeken. Ondersteunt Azure Monitor voor VM's-en bewakings oplossingen. Dit is dezelfde agent die wordt gebruikt voor System Center Operations Manager.
- [Afhankelijkheids agent](../platform/agents-overview.md#dependency-agent) : verzamelt gegevens over de processen die worden uitgevoerd op de virtuele machine en de bijbehorende afhankelijkheden. Is afhankelijk van de Log Analytics-agent om gegevens naar Azure te verzenden en biedt ondersteuning voor Azure Monitor voor VM's, Servicetoewijzing en Wire Data 2.0 oplossingen.
- [Diagnostische Azure-extensie](../platform/agents-overview.md#azure-diagnostics-extension) : alleen beschikbaar voor virtuele machines van Azure monitor. Kan gegevens verzamelen op meerdere locaties, maar hoofd zakelijk gebruikt voor het verzamelen van prestatie gegevens van de gast in Azure Monitor metrieken voor virtuele Windows-machines.
- [Telegrafie-agent](../platform/collect-custom-metrics-linux-telegraf.md) : Verzamel prestatie gegevens van virtuele Linux-machines in azure monitor meet waarden.


## <a name="configuration-requirements"></a>Configuratievereisten
Als u alle functies van Azure Monitor voor het bewaken van een virtuele machine wilt inschakelen, moet u bewakings gegevens van de virtuele-machinehost en het gast besturingssysteem verzamelen voor zowel [Azure monitor metrieken](../platform/data-platform-logs.md) als [Azure monitor logboeken](../platform/data-platform-logs.md). De volgende tabel geeft een lijst van de configuratie die moet worden uitgevoerd om deze verzameling in te scha kelen. U kunt ervoor kiezen om niet al deze stappen uit te voeren, afhankelijk van uw specifieke vereisten.

| Configuratie stap | Acties voltooid | Functies ingeschakeld |
|:---|:---|:---|
| Geen configuratie | -De metrische platform gegevens die worden verzameld voor metrische gegevens van de host.<br>-Verzamelde activiteiten Logboeken. | -Metrics Explorer voor de host.<br>-Metrische waarschuwingen voor de host.<br>-Waarschuwingen voor activiteiten Logboeken. |
| [Azure Monitor voor VM's inschakelen](#enable-azure-monitor-for-vms) | -Log Analytics-agent geïnstalleerd.<br>-De agent voor afhankelijkheden is geïnstalleerd.<br>-Gast prestatie gegevens die worden verzameld voor Logboeken.<br>-Proces en afhankelijkheids details die zijn verzameld voor Logboeken. | -Prestatie diagrammen en werkmappen voor prestatie gegevens van de gast.<br>-Query's vastleggen voor prestatie gegevens van de gast.<br>-Waarschuwingen in logboek registreren voor prestatie gegevens van de gast.<br>-Afhankelijkheids toewijzing. |
| [De diagnostische uitbrei ding en de telegrafie agent installeren](#enable-diagnostics-extension-and-telegraf-agent) | -De prestatie gegevens voor de gast die worden verzameld voor de meet waarden. | -Metrics Explorer voor gast.<br>-Metrische waarschuwingen voor gast.  |
| [Log Analytics-werkruimte configureren](#configure-log-analytics-workspace) | -Gebeurtenissen die worden verzameld van de gast. | -Query's vastleggen voor gast gebeurtenissen.<br>-Waarschuwingen vastleggen voor gast gebeurtenissen. |
| [Diagnostische instelling voor virtuele machine maken](#collect-platform-metrics-and-activity-log) | -Metrische platform gegevens die worden verzameld voor Logboeken.<br>-Het activiteiten logboek dat is verzameld voor Logboeken. | -Query's vastleggen voor metrische gegevens van de host.<br>-Waarschuwingen vastleggen voor metrische gegevens van de host.<br>-Query's vastleggen voor het activiteiten logboek.

Elk van deze configuratie stappen wordt beschreven in de volgende secties.

### <a name="enable-azure-monitor-for-vms"></a>Azure Monitor voor VM's inschakelen
[Azure monitor voor VM's](vminsights-overview.md) is een [inzicht](../monitor-reference.md) in azure monitor dat het primaire hulp programma is voor het bewaken van virtuele machines in azure monitor. Het biedt de volgende aanvullende waarde ten opzichte van standaard Azure Monitor-functies.

- Vereenvoudigd onboarding van Log Analytics agent en afhankelijkheids agent om de bewaking van een gast besturingssysteem en werk belastingen van een virtuele machine mogelijk te maken. 
- Vooraf gedefinieerde diagrammen voor trend prestaties en werkmappen waarmee u de metrische prestatie gegevens van het gast besturingssysteem van de virtuele machine kunt analyseren.
- Afhankelijkheids toewijzing met processen die worden uitgevoerd op elke virtuele machine en de onderling verbonden onderdelen met andere computers en externe bronnen.

![Weer gave Azure Monitor voor VM's prestaties](media/monitor-vm-azure/vminsights-01.png)

![Weer gave Azure Monitor voor VM's kaarten](media/monitor-vm-azure/vminsights-02.png)


Schakel Azure Monitor voor VM's in via de optie **inzichten** in het menu virtuele machine van de Azure Portal. Zie [Azure monitor voor VM's overzicht inschakelen](vminsights-enable-overview.md) voor meer informatie en andere configuratie methoden.

![Azure Monitor voor VM's inschakelen](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Log Analytics-werkruimte configureren
De Log Analytics agent die door Azure Monitor voor VM's wordt gebruikt, verzendt gegevens naar een [log Analytics-werk ruimte](../platform/data-platform-logs.md). U kunt het verzamelen van aanvullende prestatie gegevens, gebeurtenissen en andere bewakings gegevens van de agent inschakelen door de Log Analytics-werk ruimte te configureren. Het hoeft slechts eenmaal te worden geconfigureerd, omdat elke agent die verbinding maakt met de werk ruimte automatisch de configuratie downloadt en meteen begint met het verzamelen van de gedefinieerde gegevens. 

U kunt de configuratie voor de werk ruimte rechtstreeks vanuit Azure Monitor voor VM's openen door **werkruimte configuratie** te selecteren in de **aan de slag**. Klik op de naam van de werk ruimte om het menu te openen.

![Werkruimteconfiguratie](media/monitor-vm-azure/workspace-configuration.png)

Selecteer **Geavanceerde instellingen** in het menu van de werk ruimte en vervolgens **gegevens** voor het configureren van gegevens bronnen. Voor Windows-agents selecteert u **Windows-gebeurtenis logboeken** en voegt u algemene gebeurtenis Logboeken toe, zoals *systeem* en *toepassing*. Selecteer voor Linux-agents **syslog** en Voeg algemene faciliteiten toe, zoals *spatiëring* en *daemon*. Zie [gegevens bronnen van agents in azure monitor](../platform/agent-data-sources.md) voor een lijst met beschik bare gegevens bronnen en Details over het configureren van deze resources. 

![Gebeurtenissen configureren](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> U kunt prestatie meter items die moeten worden verzameld uit de werkruimte configuratie configureren, maar dit kan redundant zijn met de prestatie meter items die worden verzameld door Azure Monitor voor VM's. Azure Monitor voor VM's verzamelt de meest voorkomende set tellers met een frequentie van één keer per minuut. Configureer alleen prestatie meter items die moeten worden verzameld door de werk ruimte als u items wilt verzamelen die niet al zijn verzameld door Azure Monitor voor VM's of als u bestaande query's hebt die prestatie gegevens gebruiken.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>De uitbrei ding voor diagnostische gegevens en de telegrafie agent inschakelen
Azure Monitor voor VM's is gebaseerd op de Log Analytics-agent die gegevens naar een Log Analytics-werk ruimte verzendt. Dit ondersteunt meerdere functies van Azure Monitor, zoals [logboek query's](../log-query/log-query-overview.md), [logboek waarschuwingen](../platform/alerts-log.md)en [werkmappen](../platform/workbooks-overview.md). De [Diagnostische uitbrei ding](../platform/diagnostics-extension-overview.md) verzamelt prestatie gegevens van het gast besturingssysteem van virtuele Windows-machines tot Azure Storage en stuurt optioneel prestatie gegevens naar [Azure monitor metrieken](../platform/data-platform-metrics.md). Voor virtuele Linux-machines is de [telegrafa-agent](../platform/collect-custom-metrics-linux-telegraf.md) vereist voor het verzenden van gegevens naar Azure Metrics.  Hiermee kunnen andere functies van Azure Monitor zoals [metrische gegevens Verkenner](../platform/metrics-getting-started.md) en [metrische waarschuwingen worden gegenereerd](../platform/alerts-metric.md). U kunt de diagnostische extensie ook zo configureren dat gebeurtenissen en prestatie gegevens buiten Azure Monitor worden verzonden met behulp van Azure Event Hubs.

Installeer de diagnostische uitbrei ding voor één virtuele Windows-machine in de Azure Portal van de optie **Diagnostische gegevens** in het VM-menu. Selecteer de optie om **Azure monitor** in te scha kelen op het tabblad **sinks** . Zie [installatie en configuratie](../platform/diagnostics-extension-overview.md#installation-and-configuration)om de uitbrei ding van een sjabloon of opdracht regel voor meerdere virtuele machines in te scha kelen. In tegens telling tot de Log Analytics-agent, wordt de te verzamelen gegevens gedefinieerd in de configuratie voor de uitbrei ding op elke virtuele machine.

![Diagnostische instelling](media/monitor-vm-azure/diagnostic-setting.png)

Zie [Telegraf installeren en configureren](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) voor meer informatie over het configureren van de telegrafie agenten op virtuele Linux-machines. De menu optie voor het instellen van de **Diagnostische** gegevens is beschikbaar voor Linux, maar u kunt er ook voor zorgen dat u niet naar Azure Storage gaat verzenden.

### <a name="collect-platform-metrics-and-activity-log"></a>Metrische platform gegevens en activiteiten logboek verzamelen
U kunt de platform metrieken en het activiteiten logboek dat is verzameld voor elke virtuele-machinehost in het Azure Portal weer geven. Verzamel deze gegevens in dezelfde Log Analytics werk ruimte als Azure Monitor voor VM's om deze te analyseren met de andere bewakings gegevens die voor de virtuele machine zijn verzameld. Deze verzameling is geconfigureerd met een [Diagnostische instelling](../platform/diagnostic-settings.md). Verzamel het activiteiten logboek met een [Diagnostische instelling voor het abonnement](../platform/diagnostic-settings.md#create-in-azure-portal).

Verzamel platform metrieken met een diagnostische instelling voor de virtuele machine. In tegens telling tot andere Azure-resources kunt u geen diagnostische instelling maken voor een virtuele machine in de Azure Portal, maar moet u een [andere methode](../platform/diagnostic-settings.md#create-using-powershell)gebruiken. In de volgende voor beelden ziet u hoe u metrische gegevens voor een virtuele machine kunt verzamelen met behulp van Power shell en CLI.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace
```

## <a name="monitoring-in-the-azure-portal"></a>Bewaking in de Azure Portal 
Wanneer u de verzameling van bewakings gegevens voor een virtuele machine hebt geconfigureerd, hebt u meerdere opties om deze te openen in de Azure Portal:

- Gebruik het menu **Azure monitor** om toegang te krijgen tot gegevens van alle bewaakte bronnen. 
- Gebruik Azure Monitor voor VM's voor het bewaken van sets virtuele machines op schaal.
- Analyseer gegevens voor één virtuele machine in het menu van het Azure Portal. De volgende tabel bevat verschillende opties voor het bewaken van het menu met virtuele machines.

![Bewaking in de Azure Portal](media/monitor-vm-azure/monitor-menu.png)

| Menu optie | Beschrijving |
|:---|:---|
| Overzicht | Geeft de [platform metrische gegevens](../platform/data-platform-metrics.md) weer voor de host van de virtuele machine. Klik op een grafiek om met deze gegevens in [Metrics Explorer](../platform/metrics-getting-started.md)te werken. |
| Activiteitenlogboek | Vermeldingen in het [activiteiten logboek](../platform/activity-log.md#view-the-activity-log) gefilterd op de huidige virtuele machine. |
| Inzichten | Hiermee opent u [Azure monitor voor VM's](./vminsights-overview.md) met de kaart voor de geselecteerde virtuele machine. |
| Waarschuwingen | [Waarschuwingen](../platform/alerts-overview.md) weer geven voor de huidige virtuele machine.  |
| Metrische gegevens | Open [Metrics Explorer](../platform/metrics-getting-started.md) met de scope die is ingesteld op de huidige virtuele machine. |
| Diagnostische instellingen | De [uitbrei ding voor diagnostische gegevens](../platform/diagnostics-extension-overview.md) voor de huidige virtuele machine inschakelen en configureren. |
| Advisor-aanbevelingen | Aanbevelingen voor de huidige virtuele machine van [Azure Advisor](../../advisor/index.yml). |
| Logboeken | Open [log Analytics](../log-query/log-analytics-overview.md) met de [Scope](../log-query/scope.md) die is ingesteld op de huidige virtuele machine. |
| Verbindings monitor | Open [Network Watcher verbindings monitor](../../network-watcher/connection-monitor-overview.md) om de verbindingen tussen de huidige virtuele machine en andere virtuele machines te bewaken. |


## <a name="analyzing-metric-data"></a>Metrische gegevens analyseren
U kunt metrische gegevens voor virtuele machines analyseren met metrische gegevens Verkenner door **metrische gegevens** te openen in het menu van de virtuele machine. Zie [aan de slag met Azure Metrics Explorer](../platform/metrics-getting-started.md) voor meer informatie over het gebruik van dit hulp programma. 

Er zijn drie naam ruimten die door virtuele machines worden gebruikt voor metrische gegevens:

| Naamruimte | Description | Vereiste |
|:---|:---|:---|
| Host van virtuele machine | Metrische gegevens van de host worden automatisch verzameld voor alle virtuele machines van Azure. Gedetailleerde lijst met metrische gegevens bij [micro soft. Compute/informatie](../platform/metrics-supported.md#microsoftcomputevirtualmachines). | Automatisch verzameld zonder configuratie vereist. |
| Gast (klassiek) | Beperkte set gast besturingssysteem en prestatie gegevens van toepassingen. Beschikbaar in Metrics Explorer, maar niet op andere Azure Monitor functies, zoals metrische waarschuwingen.  | [Diagnostische uitbrei ding](../platform/diagnostics-extension-overview.md) geïnstalleerd. Gegevens worden uit Azure Storage gelezen.  |
| Gast voor virtuele machine | Prestatie gegevens van gast besturingssystemen en-toepassingen die beschikbaar zijn voor alle Azure Monitor-functies met behulp van gegevens. | Voor Windows is de [Diagnostische uitbrei ding](../platform/diagnostics-extension-overview.md) geïnstalleerd met Azure monitor Sink ingeschakeld. Voor Linux is de [telegrafa-agent geïnstalleerd](../platform/collect-custom-metrics-linux-telegraf.md). |

![Metrics Explorer in de Azure Portal](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Logboek gegevens analyseren
Met virtuele machines van Azure worden de volgende gegevens verzameld voor Azure Monitor Logboeken. 

Azure Monitor voor VM's kunt het verzamelen van een vooraf vastgestelde set prestatie meter items die naar de tabel *InsightsMetrics* zijn geschreven. Dit is dezelfde tabel die wordt gebruikt door [Azure monitor voor containers](container-insights-overview.md). 

| Gegevensbron | Vereisten | Tabellen |
|:---|:---|:---|
| Azure Monitor voor virtuele machines | Schakel deze optie in op elke virtuele machine. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>Zie [Logboeken van Azure monitor voor VM's opvragen](vminsights-log-search.md) voor meer informatie. |
| Activiteitenlogboek | Diagnostische instelling voor het abonnement. | AzureActivity |
| Metrische gegevens van de host | Diagnostische instelling voor de virtuele machine. | AzureMetrics |
| Gegevens bronnen van het gast besturingssysteem | Schakel Log Analytics agent in en configureer gegevens bronnen. | Zie de documentatie voor elke gegevens bron. |


> [!NOTE]
> Prestatie gegevens die door de Log Analytics agent worden verzameld, worden naar de *prestatie* tabel geschreven terwijl Azure monitor voor VM's deze verzamelt naar de tabel *InsightsMetrics* . Dit zijn dezelfde gegevens, maar de tabellen hebben een andere structuur. Als u bestaande query's op basis van *perf* hebt, moet de worden herschreven om *InsightsMetrics* te gebruiken.


## <a name="alerts"></a>Waarschuwingen
[Waarschuwingen](../platform/alerts-overview.md) in azure monitor proactief u op de hoogte te stellen wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens en mogelijk een actie starten, zoals het starten van een logische app of het aanroepen van een webhook. Waarschuwings regels definiëren de logica die wordt gebruikt om te bepalen wanneer een waarschuwing moet worden gemaakt. Azure Monitor verzamelt de gegevens die worden gebruikt door waarschuwings regels, maar u moet regels maken voor het definiëren van waarschuwings voorwaarden in uw Azure-abonnement.

In de volgende secties worden de typen waarschuwings regels en aanbevelingen beschreven voor wanneer u elk moet gebruiken. Deze aanbeveling is gebaseerd op de functionaliteit en kosten van het type waarschuwings regel. Zie [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/)voor meer informatie over de prijzen van waarschuwingen.


### <a name="activity-log-alert-rules"></a>Waarschuwingsregels voor activiteitenlogboek
[Waarschuwings regels voor activiteiten logboeken](../platform/alerts-activity-log.md) worden gestart wanneer een vermelding die overeenkomt met bepaalde criteria wordt gemaakt in het activiteiten logboek. Ze hebben geen kosten, dus ze moeten uw eerste keuze zijn als de logica die u nodig hebt, zich in het activiteiten logboek bevindt. 

De doel resource voor waarschuwingen voor activiteiten logboeken kan een specifieke virtuele machine zijn, alle virtuele machines in een resource groep of alle virtuele machines in een abonnement.

Maak bijvoorbeeld een waarschuwing als een kritieke virtuele machine wordt gestopt door de uitschakeling van de *virtuele machine* voor de signaal naam in te scha kelen.

![Waarschuwing voor activiteiten logboek](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Regels voor metrische waarschuwingen
[Metrische waarschuwings regels](../platform/alerts-metric.md) worden gestart wanneer een metrische waarde een drempelwaarde overschrijdt. U kunt een specifieke drempel waarde definiëren of toestaan dat Azure Monitor dynamisch een drempelwaarde bepaalt op basis van historische gegevens.  Gebruik waar mogelijk metrische waarschuwingen met metrische gegevens, omdat deze minder kosten en sneller reageren dan logboek waarschuwings regels. Ze zijn ook stateful, wat betekent dat ze zichzelf zullen oplossen wanneer de metriek onder de drempel waarde daalt.

De doel resource voor waarschuwingen voor activiteiten logboeken kan een specifieke virtuele machine of alle virtuele machines in een resource groep zijn.

Als u bijvoorbeeld een waarschuwing wilt maken wanneer de processor van een virtuele machine een bepaalde waarde overschrijdt, maakt u een waarschuwings regel voor metrische gegevens met behulp van *percentage CPU* als signaal type. Stel een specifieke drempel waarde in of laat Azure Monitor een dynamische drempel instellen. 

![Waarschuwing voor metrische gegevens](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Waarschuwingen voor logboeken
[Waarschuwings regels voor logboeken](../platform/alerts-log.md) worden gestart wanneer de resultaten van een geplande logboek query overeenkomen met bepaalde criteria. Waarschuwingen voor logboek query's zijn de duur en de minimale reactie van de waarschuwings regels, maar ze hebben toegang tot de meest uiteenlopende gegevens en kunnen complexe logica uitvoeren die niet kan worden uitgevoerd door de andere waarschuwings regels. 

De doel resource voor een logboek query is een Log Analytics-werk ruimte. Filter op specifieke computers in de query.

Als u bijvoorbeeld een waarschuwing wilt maken waarmee wordt gecontroleerd of virtuele machines in een bepaalde resource groep offline zijn, gebruikt u de volgende query waarmee een record wordt geretourneerd voor elke computer die in de afgelopen tien minuten een heartbeat heeft gemist. Gebruik een drempel van 1 die wordt geactiveerd als ten minste één computer een gemiste heartbeat heeft.

```kusto
Heartbeat
| where TimeGenerated > ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Logboek waarschuwing voor gemiste heartbeat](media/monitor-vm-azure/log-alert-01.png)

Als u een waarschuwing wilt maken als er een uitzonderlijk aantal mislukte aanmeldingen is opgetreden op virtuele Windows-machines in het abonnement, gebruikt u de volgende query waarmee een record voor elke mislukte aanmeldings gebeurtenis in het afgelopen uur wordt geretourneerd. Gebruik een drempel waarde die is ingesteld op het aantal mislukte aanmeldingen dat u wilt toestaan. 

```kusto
Event
| where TimeGenerated > ago(1hr)
| where EventID == 4625
```

![Logboek waarschuwing voor mislukte aanmeldingen](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager biedt gedetailleerde bewaking van workloads op virtuele machines. Raadpleeg de [Cloud monitoring-hand leiding](/azure/cloud-adoption-framework/manage/monitor/) voor een vergelijking van bewakings platforms en verschillende strategieën voor implementatie.

Als u een bestaande Operations Manager omgeving hebt die u wilt blijven gebruiken, kunt u deze integreren met Azure Monitor om extra functionaliteit te bieden. De Log Analytics-agent die door Azure Monitor wordt gebruikt, is hetzelfde als die voor Operations Manager, zodat u hebt gecontroleerd of de virtuele machines gegevens naar beide verzenden. U moet de agent nog steeds toevoegen aan Azure Monitor voor VM's en de werk ruimte zodanig configureren dat er aanvullende gegevens worden verzameld, zoals hierboven is beschreven, maar de virtuele machines kunnen de bestaande Management Packs in een Operations Manager omgeving blijven uitvoeren zonder aanpassing.

De functies van Azure Monitor die een bestaande Operations Manager functies uitbreiden, zijn onder andere:

- Gebruik Log Analytics om uw logboek-en prestatie gegevens interactief te analyseren.
- Gebruik logboek waarschuwingen voor het definiëren van waarschuwings voorwaarden op meerdere virtuele machines en het gebruik van lange termijn trends die niet mogelijk zijn met behulp van waarschuwingen in Operations Manager.   

Zie [Connect Operations Manager to Azure monitor](../platform/om-agents.md) voor meer informatie over het koppelen van uw bestaande Operations Manager-beheer groep aan uw log Analytics-werk ruimte.


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het analyseren van gegevens in Azure Monitor logboeken met behulp van logboek query's.](../log-query/get-started-queries.md)
* [Meer informatie over waarschuwingen met metrische gegevens en Logboeken in Azure Monitor.](../platform/alerts-overview.md)