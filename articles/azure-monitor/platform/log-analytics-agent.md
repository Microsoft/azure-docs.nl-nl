---
title: Overzicht van Log Analytics-agent
description: In dit onderwerp leert u hoe u gegevens kunt verzamelen en computers kunt bewaken die worden gehost in azure, on-premises of andere Cloud omgevingen met Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 8563f734db8524d6e90171bb2272723f14533055
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185926"
---
# <a name="log-analytics-agent-overview"></a>Overzicht van Log Analytics-agent
De Azure Log Analytics-agent verzamelt telemetrie van virtuele Windows-en Linux-machines in elke Cloud, on-premises machines en die worden bewaakt door [System Center Operations Manager](/system-center/scom/) en verzendt de verzamelde gegevens naar uw log Analytics-werk ruimte in azure monitor. De Log Analytics-agent biedt ook ondersteuning voor inzichten en andere services in Azure Monitor zoals [Azure monitor voor VM's](../insights/vminsights-enable-overview.md), [Azure Security Center](../../security-center/index.yml)en [Azure Automation](../../automation/automation-intro.md). Dit artikel bevat een gedetailleerd overzicht van de agent-, systeem-en netwerk vereisten en implementatie methoden.

> [!NOTE]
> Mogelijk ziet u ook de Log Analytics agent waarnaar wordt verwezen als de micro soft Monitoring Agent (MMA) of OMS Linux-agent.

## <a name="comparison-to-azure-diagnostics-extension"></a>Vergelijking met de Azure Diagnostics-extensie
De [Azure Diagnostics-extensie](diagnostics-extension-overview.md) in azure monitor kan ook worden gebruikt voor het verzamelen van bewakings gegevens van het gast besturingssysteem van virtuele machines van Azure. U kunt ervoor kiezen om ofwel of beide afhankelijk van uw vereisten te gebruiken. Zie [overzicht van de Azure monitor agents](agents-overview.md) voor een gedetailleerde vergelijking van de Azure monitor agents. 

De belangrijkste verschillen die u moet overwegen:

- Azure Diagnostics extensie kan alleen worden gebruikt met virtuele machines van Azure. De Log Analytics-agent kan worden gebruikt met virtuele machines in azure, andere Clouds en on-premises.
- Met Azure Diagnostics extensie worden gegevens naar Azure Storage verzonden, worden [Azure monitor metriek](data-platform-metrics.md) (alleen Windows) en Event hubs. De Log Analytics agent verzendt gegevens naar [Azure monitor logboeken](data-platform-logs.md).
- De Log Analytics-agent is vereist voor [oplossingen](../monitor-reference.md#insights-and-core-solutions), [Azure monitor voor VM's](../insights/vminsights-overview.md)en andere services, zoals [Azure Security Center](../../security-center/index.yml).

## <a name="costs"></a>Kosten
Er zijn geen kosten verbonden aan Log Analytics-agent, maar mogelijk worden er kosten in rekening gebracht voor de gegevens die zijn opgenomen. Controleer het [gebruik en de kosten beheren met Azure monitor logboeken](manage-cost-storage.md) voor gedetailleerde informatie over de prijzen voor gegevens die zijn verzameld in een log Analytics-werk ruimte.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

 Zie [ondersteunde besturings systemen](agents-overview.md#supported-operating-systems) voor een lijst met versies van het Windows-en Linux-besturings systeem die worden ondersteund door de log Analytics-agent. 


## <a name="data-collected"></a>Verzamelde gegevens
De volgende tabel bevat de typen gegevens die u kunt configureren voor het verzamelen van een Log Analytics werkruimte van alle verbonden agents. Zie [wat wordt bewaakt door Azure monitor?](../monitor-reference.md) voor een lijst met inzichten, oplossingen en andere oplossingen die gebruikmaken van de log Analytics-agent voor het verzamelen van andere soorten gegevens.

| Gegevensbron | Description |
| --- | --- |
| [Windows-gebeurtenis logboeken](data-sources-windows-events.md) | Gegevens die worden verzonden naar het Windows-systeem voor gebeurtenis registratie. |
| [Syslog](data-sources-syslog.md)                     | Informatie die wordt verzonden naar het systeem voor het registreren van Linux-gebeurtenissen. |
| [Prestaties](data-sources-performance-counters.md)  | Numerieke waarden meten de prestaties van verschillende aspecten van het besturings systeem en de werk belastingen. |
| [IIS-logboeken](data-sources-iis-logs.md)                 | Gebruiks gegevens voor IIS-websites die worden uitgevoerd op het gast besturingssysteem. |
| [Aangepaste logboeken](data-sources-custom-logs.md)           | Gebeurtenissen uit tekst bestanden op zowel Windows-als Linux-computers. |

## <a name="data-destinations"></a>Gegevens bestemmingen
De Log Analytics agent verzendt gegevens naar een Log Analytics-werk ruimte in Azure Monitor. De Windows-agent kan multihomed zijn om gegevens te verzenden naar meerdere werk ruimten en System Center Operations Manager-beheer groepen. De Linux-agent kan slechts naar één bestemming verzenden, hetzij een werk ruimte of een beheer groep.

## <a name="other-services"></a>Overige services
De agent voor Linux en Windows is niet alleen voor het maken van verbinding met Azure Monitor. Andere services, zoals Azure Security Center en Azure Sentinel, zijn afhankelijk van de agent en de gekoppelde Log Analytics-werk ruimte. De agent biedt ook ondersteuning voor Azure Automation voor het hosten van de Hybrid Runbook worker-rol en andere services, zoals [Wijzigingen bijhouden](../../automation/change-tracking/overview.md), [updatebeheer](../../automation/update-management/overview.md)en [Azure Security Center](../../security-center/security-center-introduction.md). Zie [Azure Automation Hybrid Runbook worker](../../automation/automation-hybrid-runbook-worker.md)voor meer informatie over de functie Hybrid Runbook Worker.  

## <a name="workspace-and-management-group-limitations"></a>Beperkingen van de werk ruimte en de beheer groep

Zie [agent configureren om te rapporteren aan een Operations Manager-beheer groep](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) voor meer informatie over het verbinden van een agent met een Operations Manager-beheer groep.

* Windows-agents kunnen verbinding maken met Maxi maal vier werk ruimten, zelfs als ze zijn verbonden met een System Center Operations Manager-beheer groep.
* De Linux-agent biedt geen ondersteuning voor multi-multihoming en kan alleen verbinding maken met één werk ruimte of beheer groep.


## <a name="security-limitations"></a>Beveiligings beperkingen

* De Windows-en Linux-agents ondersteunen de [FIPS 140-standaard](/windows/security/threat-protection/fips-140-validation), maar [andere typen beveiliging kunnen niet worden ondersteund](agent-linux.md#supported-linux-hardening).


## <a name="installation-options"></a>Installatieopties

Er zijn meerdere methoden om de Log Analytics-agent te installeren en uw computer te verbinden met Azure Monitor afhankelijk van uw vereisten. In de volgende secties worden de mogelijke methoden voor verschillende typen virtuele machines vermeld.
> [!NOTE]
> Het is niet mogelijk om een machine te klonen waarvoor de Log Analytics-agent al is geconfigureerd. Als de agent al is gekoppeld aan een werk ruimte, werkt dit niet voor gouden installatie kopieën.

### <a name="azure-virtual-machine"></a>Azure virtuele machine

- [Azure monitor voor VM's](../insights/vminsights-enable-overview.md) biedt meerdere methoden om agents op schaal in te scha kelen. Dit omvat de installatie van de Log Analytics agent en de afhankelijkheids agent. 
- [Azure Security Center kunt de log Analytics agent inrichten](../../security-center/security-center-enable-data-collection.md) op alle ondersteunde Azure-vm's en eventuele nieuwe virtuele machines die worden gemaakt als u deze inschakelt om te controleren op beveiligings problemen en bedreigingen.
- Log Analytics VM-extensie voor [Windows](../../virtual-machines/extensions/oms-windows.md) of [Linux](../../virtual-machines/extensions/oms-linux.md) kan worden geïnstalleerd met de Azure Portal, Azure CLI, Azure PowerShell of een Azure Resource Manager sjabloon.
- Installeer voor afzonderlijke virtuele Azure-machines [hand matig vanuit het Azure Portal](../learn/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json).


### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>Virtuele Windows-machine on-premises of in een andere Cloud 

- [Installeer de agent hand matig](agent-windows.md) vanaf de opdracht regel.
- Automatiseer de installatie met [Azure Automation DSC](agent-windows.md#install-agent-using-dsc-in-azure-automation).
- Een [Resource Manager-sjabloon gebruiken met Azure stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>Virtuele Linux-machine on-premises of in een andere Cloud

- [Hand matig](../learn/quick-collect-linux-computer.md) de agent installeren die een wrapper-script aanroept dat wordt gehost op github.
- System Center Operations Manager | [Integreer Operations Manager met Azure monitor](./om-agents.md) voor het door sturen van verzamelde gegevens van Windows-computers die rapporteren aan een beheer groep.

## <a name="workspace-id-and-key"></a>Werk ruimte-ID en-sleutel
Ongeacht de gebruikte installatie methode hebt u de werk ruimte-ID en-sleutel nodig voor de Log Analytics werk ruimte waarmee de agent verbinding maakt. Selecteer de werk ruimte in het menu **log Analytics werk ruimten** in de Azure Portal. Selecteer vervolgens **agents beheren** in de sectie **instellingen** . 

[![Werkruimte Details](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>TLS 1,2-protocol

We raden u ten zeerste aan de agent te configureren om ten minste Transport Layer Security (TLS) 1,2 te gebruiken om de beveiliging van gegevens die onderweg zijn naar Azure Monitor logboeken te garanderen. Er zijn oudere versies van TLS/Secure Sockets Layer (SSL) gevonden die kwetsbaar zijn en terwijl ze nog steeds werken om achterwaartse compatibiliteit mogelijk te maken, worden ze **niet aanbevolen**.  Raadpleeg voor meer informatie [veilig verzenden van gegevens met behulp van TLS 1,2](data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-requirements"></a>Netwerkvereisten
De agent voor Linux en Windows communiceert uitgaande naar de Azure Monitor-service via TCP-poort 443. Als de computer verbinding maakt via een firewall of proxy server om via internet te communiceren, controleert u de vereisten hieronder om inzicht te krijgen in de vereiste netwerk configuratie. Als uw IT-beveiligings beleid niet toestaat dat computers in het netwerk verbinding maken met internet, kunt u een [log Analytics gateway](gateway.md) instellen en vervolgens de agent configureren om via de gateway verbinding te maken met Azure monitor. De agent kan vervolgens configuratie-informatie ontvangen en verzamelde gegevens verzenden.

![Communicatie diagram van Log Analytics agent](./media/log-analytics-agent/log-analytics-agent-01.png)

De volgende tabel geeft een lijst van de proxy-en firewall configuratie gegevens die zijn vereist voor de Linux-en Windows-agents om te communiceren met Azure Monitor-Logboeken.

### <a name="firewall-requirements"></a>Firewallvereisten

|Agentresource|Poorten |Richting |HTTPS-controle overslaan|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Poort 443 |Uitgaand|Yes |  
|*.oms.opinsights.azure.com |Poort 443 |Uitgaand|Yes |  
|*.blob.core.windows.net |Poort 443 |Uitgaand|Yes |
|*.azure-automation.net |Poort 443 |Uitgaand|Yes |

Zie [Azure Government Management](../../azure-government/compare-azure-government-global-azure.md#azure-monitor)voor informatie over de firewall die vereist is voor Azure Government. 

Als u van plan bent de Azure Automation Hybrid Runbook Worker te gebruiken om verbinding te maken met de Automation-Service voor het gebruik van runbooks of beheer oplossingen in uw omgeving, moet deze toegang hebben tot het poort nummer en de Url's die worden beschreven in [uw netwerk configureren voor de Hybrid Runbook worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

### <a name="proxy-configuration"></a>Proxyconfiguratie

De Windows-en Linux-agent ondersteunt communicatie via een proxy server of Log Analytics gateway naar Azure Monitor met behulp van het HTTPS-protocol.  Zowel anonieme als basis verificatie (gebruikers naam en wacht woord) worden ondersteund.  Voor de Windows-agent die rechtstreeks is verbonden met de service, wordt de proxy configuratie opgegeven tijdens de installatie of [na de implementatie](agent-manage.md#update-proxy-settings) vanuit het configuratie scherm of met Power shell.  

Voor de Linux-agent wordt de proxy server tijdens de installatie of [na de installatie](agent-manage.md#update-proxy-settings) opgegeven door het configuratie bestand proxy. conf te wijzigen.  De configuratie waarde van de Linux-agent proxy heeft de volgende syntaxis:

`[protocol://][user:password@]proxyhost[:port]`

|Eigenschap| Beschrijving |
|--------|-------------|
|Protocol | https |
|gebruiker | Optionele gebruikers naam voor proxy verificatie |
|wachtwoord | Optioneel wacht woord voor proxy verificatie |
|proxyhost | Adres of FQDN van de proxy server/Log Analytics gateway |
|poort | Optioneel poort nummer voor de proxy server/Log Analytics gateway |

Bijvoorbeeld: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Als u speciale tekens zoals ' \@ ' in uw wacht woord gebruikt, ontvangt u een proxy verbindings fout omdat de waarde onjuist is geparseerd.  U kunt dit probleem omzeilen door het wacht woord in de URL te coderen met een hulp programma zoals [UrlDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Volgende stappen

* Bekijk [gegevens bronnen](agent-data-sources.md) om inzicht te krijgen in de gegevens bronnen die beschikbaar zijn voor het verzamelen van gegevens uit uw Windows-of Linux-systeem. 
* Meer informatie over [logboek query's](../log-query/log-query-overview.md) voor het analyseren van de gegevens die zijn verzameld uit gegevens bronnen en oplossingen. 
* Meer informatie over het [controleren van oplossingen](../insights/solutions.md) voor het toevoegen van functionaliteit aan Azure monitor en het verzamelen van gegevens in de log Analytics-werk ruimte.