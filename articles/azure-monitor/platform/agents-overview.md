---
title: Overzicht van de Azure-bewakings agents | Microsoft Docs
description: Dit artikel bevat een gedetailleerd overzicht van de Azure-agents die beschikbaar zijn voor het bewaken van virtuele machines die worden gehost in azure of een hybride omgeving.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2020
ms.openlocfilehash: c6db5a856b127d4549de9597f8157f524a38af79
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186657"
---
# <a name="overview-of-azure-monitor-agents"></a>Overzicht van Azure Monitor agents

Voor virtuele machines en andere reken bronnen is een agent vereist voor het verzamelen van bewakings gegevens die nodig zijn om de prestaties en beschik baarheid van hun gast besturingssysteem en werk belastingen te meten. In dit artikel worden de agents beschreven die door Azure Monitor worden gebruikt en kunt u bepalen wat u moet doen aan de vereisten voor uw specifieke omgeving.

> [!NOTE]
> Azure Monitor heeft momenteel meerdere agents vanwege een recente consolidatie van Azure Monitor en Log Analytics. Hoewel er mogelijk overlap pingen zijn in de functies, heeft elk een unieke capaciteit. Afhankelijk van uw vereisten hebt u mogelijk een of meer van de agents op uw virtuele machines nodig. 

Mogelijk hebt u een specifieke set vereisten waaraan niet volledig kan worden voldaan met één agent voor een bepaalde virtuele machine. U kunt bijvoorbeeld metrische waarschuwingen gebruiken waarvoor Azure Diagnostics-extensie vereist is, maar u ook wilt gebruikmaken van de functionaliteit van Azure Monitor voor VM's waarvoor de Log Analytics agent en de afhankelijkheids agent vereist zijn. In dergelijke gevallen kunt u meerdere agents gebruiken. Dit is een veelvoorkomend scenario voor klanten die van elke functionaliteit moeten zijn.

## <a name="summary-of-agents"></a>Samen vatting van agents

De volgende tabellen bieden een snelle vergelijking van de Azure Monitor agents voor Windows en Linux. Meer details hierover vindt u in de volgende sectie.

> [!NOTE]
> De Azure Monitor-agent is momenteel in Preview met beperkte mogelijkheden. Deze tabel wordt bijgewerkt 

### <a name="windows-agents"></a>Windows-agents

| | Azure Monitor-agent (preview-versie) | Diagnostiek<br>extensie (WAD) | Log Analytics<br>agent | Afhankelijkheid<br>agent |
|:---|:---|:---|:---|:---|
| **Omgevingen worden ondersteund** | Azure<br>Andere Cloud (Azure-boog)<br>On-premises (Azure-boog)  | Azure | Azure<br>Andere Cloud<br>On-premises | Azure<br>Andere Cloud<br>On-premises | 
| **Agent vereisten**  | Geen | Geen | Geen | Vereist Log Analytics-agent |
| **Verzamelde gegevens** | Gebeurtenislogboeken<br>Prestaties | Gebeurtenislogboeken<br>ETW-gebeurtenissen<br>Prestaties<br>Logboeken op basis van bestanden<br>IIS-logboeken<br>.NET-app-logboeken<br>Crashdumps<br>Logboeken met diagnostische gegevens van agent | Gebeurtenislogboeken<br>Prestaties<br>Logboeken op basis van bestanden<br>IIS-logboeken<br>Inzicht en oplossingen<br>Overige services | Proces afhankelijkheden<br>Metrische netwerk verbindings gegevens |
| **Gegevens die worden verzonden naar** | Azure Monitor-logboeken<br>Metrische gegevens van Azure Monitor | Azure Storage<br>Metrische gegevens van Azure Monitor<br>Event Hub | Azure Monitor-logboeken | Azure Monitor-logboeken<br>(via Log Analytics-agent) |
| **Services en**<br>**functies**<br>**geboden** | Log Analytics<br>Metrics-explorer | Metrics-explorer | Azure Monitor voor virtuele machines<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | Azure Monitor voor virtuele machines<br>Servicetoewijzing |

### <a name="linux-agents"></a>Linux-agents

| | Azure Monitor-agent (preview-versie) | Diagnostiek<br>extensie (LAD) | Telegrafie<br>agent | Log Analytics<br>agent | Afhankelijkheid<br>agent |
|:---|:---|:---|:---|:---|:---|
| **Omgevingen worden ondersteund** | Azure<br>Andere Cloud (Azure-boog)<br>On-premises (Arc-boog) | Azure | Azure<br>Andere Cloud<br>On-premises | Azure<br>Andere Cloud<br>On-premises | Azure<br>Andere Cloud<br>On-premises |
| **Agent vereisten**  | Geen | Geen | Geen | Geen | Vereist Log Analytics-agent |
| **Verzamelde gegevens** | Syslog<br>Prestaties | Syslog<br>Prestaties | Prestaties | Syslog<br>Prestaties| Proces afhankelijkheden<br>Metrische netwerk verbindings gegevens |
| **Gegevens die worden verzonden naar** | Azure Monitor-logboeken<br>Metrische gegevens van Azure Monitor | Azure Storage<br>Event Hub | Metrische gegevens van Azure Monitor | Azure Monitor-logboeken | Azure Monitor-logboeken<br>(via Log Analytics-agent) |
| **Services en**<br>**functies**<br>**geboden** | Log Analytics<br>Metrics-explorer | | Metrics-explorer | Azure Monitor voor virtuele machines<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | Azure Monitor voor virtuele machines<br>Servicetoewijzing |


## <a name="azure-monitor-agent-preview"></a>Azure Monitor-agent (preview-versie)
De [Azure monitor-agent](azure-monitor-agent-overview.md) bevindt zich momenteel in de preview-versie en vervangt de log Analytics agent en de telegrafie agent voor virtuele Windows-en Linux-machines. Het kan gegevens verzenden naar Azure Monitor logboeken en Azure Monitor metrische gegevens en gebruikt [gegevensverzamelings regels (DCR)](data-collection-rule-overview.md) die een meer schaal bare methode bieden voor het configureren van gegevens verzameling en-bestemmingen voor elke agent.

Gebruik de Azure Monitor agent als u het volgende moet doen:

- Verzamel logboeken en metrische gegevens voor gasten van elke virtuele machine in azure, in andere Clouds of on-premises. (Azure-Arc vereist voor virtuele machines buiten Azure.) 
- Gegevens verzenden naar Azure Monitor logboeken en Azure Monitor metrieken voor analyse met Azure Monitor. 
- Gegevens verzenden naar Azure Storage voor archiveren.
- Gegevens verzenden naar hulpprogram ma's van derden met behulp van [Azure Event hubs](diagnostics-extension-stream-event-hubs.md).
- Beheer de beveiliging van uw virtuele machines met behulp van [Azure Security Center](../../security-center/security-center-introduction.md)  of [Azure Sentinel](../../sentinel/overview.md). (Niet beschikbaar in de preview-versie.)

De beperkingen van de Azure Monitor-agent zijn:

- Momenteel beschikbaar als open bare preview. Bekijk de [huidige beperkingen](azure-monitor-agent-overview.md#current-limitations) voor een lijst met beperkingen tijdens de open bare preview.

## <a name="log-analytics-agent"></a>Log Analytics-agent

De [log Analytics-agent](log-analytics-agent.md) verzamelt bewakings gegevens van het gast besturingssysteem en werk belastingen van virtuele machines in azure, andere cloud providers en on-premises. De gegevens worden verzonden naar een Log Analytics-werk ruimte. De Log Analytics-agent is dezelfde agent die wordt gebruikt door System Center Operations Manager, en u kunt computers met meerdere thuis agenten gebruiken om met uw beheer groep en Azure Monitor tegelijk te communiceren. Deze agent is ook vereist voor bepaalde inzichten in Azure Monitor en andere services in Azure.


> [!NOTE]
> De Log Analytics-agent voor Windows wordt vaak micro soft Monitoring Agent (MMA) genoemd. De Log Analytics-agent voor Linux wordt vaak OMS-agent genoemd.



Gebruik de Log Analytics agent als u het volgende moet doen:

* Logboeken en prestatie gegevens verzamelen van virtuele of fysieke machines binnen of buiten Azure. 
* Gegevens verzenden naar een Log Analytics-werk ruimte om te profiteren van de functies die worden ondersteund door [Azure monitor-logboeken](data-platform-logs.md) , zoals [logboek query's](../log-query/log-query-overview.md).
* Gebruik [Azure monitor voor VM's](../insights/vminsights-overview.md) waarmee u uw virtuele machines op schaal kunt bewaken en de processen en afhankelijkheden van andere bronnen en externe processen bewaakt.  
* Beheer de beveiliging van uw virtuele machines met behulp van [Azure Security Center](../../security-center/security-center-introduction.md)  of [Azure Sentinel](../../sentinel/overview.md).
* Gebruik [Azure Automation update beheer](../../automation/update-management/overview.md), [Azure Automation status configuratie](../../automation/automation-dsc-overview.md)of [Azure Automation wijzigingen bijhouden en inventaris](../../automation/change-tracking/overview.md) om een uitgebreid beheer van uw Azure-vm's te leveren
* Gebruik verschillende [oplossingen](../monitor-reference.md#insights-and-core-solutions) voor het bewaken van een bepaalde service of toepassing.

De beperkingen van de Log Analytics-agent zijn:

- Kan geen gegevens verzenden naar Azure Monitor meet waarden, Azure Storage of Azure Event Hubs.
- Het is lastig om unieke bewakings definities voor afzonderlijke agents te configureren.
- Moeilijk te beheren op schaal omdat elke virtuele machine een unieke configuratie heeft.

## <a name="azure-diagnostics-extension"></a>Azure-extensie voor diagnostische gegevens

Met de [extensie Azure Diagnostics](diagnostics-extension-overview.md) worden bewakings gegevens verzameld van het gast besturingssysteem en de workloads van Azure virtual machines en andere reken bronnen. Er worden voornamelijk gegevens in Azure Storage verzameld, maar u kunt ook gegevenssinks definiëren om gegevens te verzenden naar andere bestemmingen, zoals Azure Monitor metrieken en Azure Event Hubs.

Gebruik de diagnostische extensie van Azure als u het volgende moet doen:

- Gegevens verzenden naar Azure Storage voor archivering of het analyseren met hulpprogram ma's zoals [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Gegevens verzenden naar [Azure monitor metrieken](data-platform-metrics.md) om deze te analyseren met [metrische gegevens Verkenner](metrics-getting-started.md) en te profiteren van functies zoals bijna realtime [waarschuwingen](./alerts-metric-overview.md) en [automatisch schalen](autoscale-overview.md) (alleen Windows).
- Gegevens verzenden naar hulpprogram ma's van derden met behulp van [Azure Event hubs](diagnostics-extension-stream-event-hubs.md).
- Verzamel de [Diagnostische gegevens over opstarten](../../virtual-machines/troubleshooting/boot-diagnostics.md) om opstart problemen met de virtuele machine te onderzoeken.

De beperkingen van de Azure Diagnostics-extensie zijn onder andere:

- Kan alleen worden gebruikt met Azure-resources.
- Beperkte mogelijkheid om gegevens te verzenden naar Azure Monitor-Logboeken.

## <a name="telegraf-agent"></a>Telegraf-agent

De [InfluxData-telegrafe-agent](collect-custom-metrics-linux-telegraf.md) wordt gebruikt voor het verzamelen van prestatie gegevens van Linux-computers tot Azure monitor meet waarden.

Gebruik telegrafie agent als u het volgende moet doen:

* Verzend gegevens naar [Azure monitor metrieken](data-platform-metrics.md) om deze te analyseren met [metrische gegevens Verkenner](metrics-getting-started.md) en gebruik te maken van functies zoals bijna realtime [waarschuwingen](./alerts-metric-overview.md) en [automatisch schalen](autoscale-overview.md) (alleen Linux). 



## <a name="dependency-agent"></a>Agent voor afhankelijkheden

De afhankelijkheids agent verzamelt gedetecteerde gegevens over processen die worden uitgevoerd op de virtuele machine en de afhankelijkheden van het externe proces. 

Gebruik de afhankelijkheids agent als u het volgende moet doen:

* Gebruik de kaart functie [Azure monitor voor VM's](../insights/vminsights-overview.md) of de [servicetoewijzing](../insights/service-map.md) oplossing.

Houd rekening met het volgende wanneer u de afhankelijkheids agent gebruikt:

- Voor de afhankelijkheids agent moet de Log Analytics agent op dezelfde virtuele machine zijn geïnstalleerd.
- Op virtuele Linux-machines moet de Log Analytics-agent zijn geïnstalleerd vóór de diagnostische Azure-extensie.

## <a name="virtual-machine-extensions"></a>Extensies voor virtuele machines

De Log Analytics-extensie voor [Windows](../../virtual-machines/extensions/oms-windows.md) en [Linux](../../virtual-machines/extensions/oms-linux.md) Installeer de log Analytics agent op virtuele machines van Azure. Met de uitbrei ding voor de Azure Monitor afhankelijkheid voor [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) en [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) wordt de afhankelijkheids agent op virtuele machines van Azure geïnstalleerd. Dit zijn dezelfde agents die hierboven worden beschreven, maar u kunt ze ook beheren via de extensies van de [virtuele machine](../../virtual-machines/extensions/overview.md). Gebruik waar mogelijk extensies om de agents te installeren en te beheren.


## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
De volgende tabellen geven een lijst van de besturings systemen die worden ondersteund door de Azure Monitor agents. Raadpleeg de documentatie voor elke agent voor unieke overwegingen en voor het installatie proces. Zie Telegraf-documentatie voor de ondersteunde besturings systemen. Er wordt ervan uitgegaan dat alle besturings systemen x64 zijn. x86 wordt niet ondersteund voor elk besturings systeem.

### <a name="windows"></a>Windows

| Operations-systeem | Azure Monitor-agent | Log Analytics-agent | Agent voor afhankelijkheden | Diagnostics-extensie | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 core                                 |   |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2                                   |   | X | X | X |
| Windows 10 Enterprise<br>(inclusief meerdere sessies) en Pro<br>(Alleen server scenario's)  | X | X | X | X |
| Windows 8 Enter prise en Pro<br>(Alleen server scenario's)  |   | X | X |   |
| Windows 7 SP1<br>(Alleen server scenario's)                 |   | X | X |   |


### <a name="linux"></a>Linux

| Operations-systeem | Azure Monitor-agent | Log Analytics-agent | Agent voor afhankelijkheden | Diagnostics-extensie | 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017,09                                     |   | X |   |   |
| CentOS Linux 8                                           |   | X |   |   |
| CentOS Linux 7                                           | X | X |   | X |
| CentOS Linux 7,8                                         | X | X | X | X |
| CentOS Linux 7,6                                         | X | X | X | X |
| CentOS Linux 6                                           |   | X |   |   |
| CentOS Linux 6.5 +                                        |   | X |   | X |
| Debian 10                                                | X |   |   |   |
| Debian 9                                                 | X | X | x | X |
| Debian 8                                                 |   | X | X | X |
| Debian 7                                                 |   |   |   | X |
| OpenSUSE 13.1 +                                           |   |   |   | X |
| Oracle Linux 7                                           | X | X |   | X |
| Oracle Linux 6                                           |   | X |   |   |
| Oracle Linux 6.4 +                                        |   | X |   | X |
| Red Hat Enterprise Linux Server 8                        |   | X |   |   |
| Red Hat Enterprise Linux Server 7                        | X | X | X | X |
| Red Hat Enterprise Linux Server 6                        |   | X | X |   |
| Red Hat Enterprise Linux Server 6,7 +                     |   | X | X | X |
| SUSE Linux Enterprise Server 15                          | X | X |   |   |
| SUSE Linux Enterprise Server 12                          | X | X | X | X |
| Ubuntu 20,04 LTS                                         |   | X |   |   |
| Ubuntu 18.04 LTS                                         | X | X | X | X |
| Ubuntu 16.04 LTS                                         | X | X | X | X |
| Ubuntu 14,04 LTS                                         |   | X |   | X |


#### <a name="dependency-agent-linux-kernel-support"></a>Ondersteuning voor Linux-kernel van afhankelijkheids agent
Omdat de afhankelijkheids agent werkt op het niveau van de kernel, is ondersteuning ook afhankelijk van de versie van de kernel. De volgende tabel geeft een overzicht van de belangrijkste en secundaire Linux-versie van het besturings systeem en ondersteunde kernel-versies voor de afhankelijkheids agent.

| Distributie | Besturingssysteemversie | Kernelversie |
|:---|:---|:---|
|  Red Hat Linux 7   | 7.6     | 3.10.0-957  |
|                    | 7,5     | 3.10.0-862  |
|                    | 7.4     | 3.10.0-693  |
| Red Hat Linux 6    | 6.10    | 2.6.32-754 |
|                    | 6.9     | 2.6.32-696  |
| CentOSPlus         | 6.10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6.9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Ubuntu Server      | 18,04   | 5.3.0-1020<br>5,0 (inclusief door Azure afgestemde kernel)<br>4,18 *<br> 4,15* |
|                    | 16.04.3 | 4,15. * |
|                    | 16,04   | 4,13.\*<br>4,11.\*<br>4,10.\*<br>4,8.\*<br>4,4.\* |
| SUSE Linux 12 Enter prise server | 12 SP4 | 4,12. * (inclusief door Azure afgestemde kernel) |
|                                 | 12 SP3 | 4,4. * |
|                                 | 12 SP2 | 4,4. * |
| Debian                          | 9      | 4.9  | 


## <a name="next-steps"></a>Volgende stappen

Meer informatie over elk van de agents vindt u op het volgende:

- [Overzicht van de Log Analytics-agent](log-analytics-agent.md)
- [Overzicht van Azure Diagnostics-extensie](diagnostics-extension-overview.md)
- [Aangepaste metrische gegevens verzamelen voor een virtuele Linux-machine met de InfluxData-Telegraf-agent](collect-custom-metrics-linux-telegraf.md)