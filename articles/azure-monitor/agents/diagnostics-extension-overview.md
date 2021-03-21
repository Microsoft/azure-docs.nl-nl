---
title: Overzicht van Azure Diagnostics-extensie
description: Diagnostische gegevens van Azure gebruiken voor het opsporen van fouten, het meten van prestaties, bewaking, verkeers analyse in Cloud Services, virtuele machines en service Fabric
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 90addb74811819dec563ba072e9a6aea2cf72ca4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036534"
---
# <a name="azure-diagnostics-extension-overview"></a>Overzicht van Azure Diagnostics-extensie
Azure Diagnostics extensie is een [agent in azure monitor](../agents/agents-overview.md) die bewakings gegevens verzamelt van het gast besturingssysteem van Azure Compute-resources, inclusief virtuele machines. Dit artikel bevat een overzicht van Azure Diagnostics extensie, inclusief specifieke functionaliteit die wordt ondersteund en opties voor installatie en configuratie. 

> [!NOTE]
> Azure Diagnostics-extensie is een van de agents die beschikbaar zijn voor het verzamelen van bewakings gegevens uit het gast besturingssysteem van reken resources. Zie [overzicht van de Azure monitor-agents ](../agents/agents-overview.md) voor een beschrijving van de verschillende agents en richt lijnen voor het selecteren van de juiste agents voor uw vereisten.

## <a name="primary-scenarios"></a>Primaire scenario's
De belangrijkste scenario's die worden behandeld door de uitbrei ding van diagnostische gegevens zijn:

- Verzamelen van metrische gegevens over de gast in Azure Monitor metrische gegevens.
- Gasten logboeken en metrische gegevens naar Azure Storage verzenden voor archivering.
- U kunt gast logboeken en-metrische gegevens verzenden naar Azure Event hubs om buiten Azure te verzenden.


## <a name="comparison-to-log-analytics-agent"></a>Vergelijking met Log Analytics agent
De Log Analytics-agent in Azure Monitor kan ook worden gebruikt voor het verzamelen van bewakings gegevens van het gast besturingssysteem van virtuele machines. U kunt ervoor kiezen om ofwel of beide afhankelijk van uw vereisten te gebruiken. Zie [overzicht van de Azure monitor agents](../agents/agents-overview.md) voor een gedetailleerde vergelijking van de Azure monitor agents. 

De belangrijkste verschillen die u moet overwegen:

- Azure Diagnostics extensie kan alleen worden gebruikt met virtuele machines van Azure. De Log Analytics-agent kan worden gebruikt met virtuele machines in azure, andere Clouds en on-premises.
- Met Azure Diagnostics extensie worden gegevens naar Azure Storage verzonden, worden [Azure monitor metriek](../essentials/data-platform-metrics.md) (alleen Windows) en Event hubs. De Log Analytics-agent verzamelt gegevens naar [Azure monitor-logboeken](../logs/data-platform-logs.md).
- De Log Analytics-agent is vereist voor [oplossingen](../monitor-reference.md#insights-and-core-solutions), [VM Insights](../vm/vminsights-overview.md)en andere services, zoals [Azure Security Center](../../security-center/index.yml).

## <a name="costs"></a>Kosten
Er zijn geen kosten verbonden aan de diagnostische Azure-extensie, maar mogelijk worden er kosten in rekening gebracht voor de gegevens die zijn opgenomen. Controleer [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor de bestemming waar u gegevens verzamelt.

## <a name="data-collected"></a>Verzamelde gegevens
De volgende tabellen geven een lijst van de gegevens die kunnen worden verzameld door de uitbrei ding voor Windows-en Linux-diagnoses.

### <a name="windows-diagnostics-extension-wad"></a>Windows diagnostische gegevens extensie (WAD)

| Gegevensbron | Beschrijving |
| --- | --- |
| Windows-gebeurtenis logboeken   | Gebeurtenissen van het Windows-gebeurtenis logboek. |
| Prestatiemeteritems | Numerieke waarden meten de prestaties van verschillende aspecten van het besturings systeem en de werk belastingen. |
| IIS-logboeken             | Gebruiks gegevens voor IIS-websites die worden uitgevoerd op het gast besturingssysteem. |
| Toepassings logboeken     | Berichten traceren die zijn geschreven door uw toepassing. |
| .NET Event source-logboeken |Code schrijven van gebeurtenissen met de .NET [Event source](/dotnet/api/system.diagnostics.tracing.eventsource) -klasse |
| [Op Manifest gebaseerde ETW-logboeken](/windows/desktop/etw/about-event-tracing) |Event Tracing for Windows gebeurtenissen die door een proces worden gegenereerd. |
| Crash dumps (Logboeken)   | Informatie over de status van het proces als een toepassing vastloopt. |
| Logboeken op basis van bestanden    | Logboeken die zijn gemaakt door uw toepassing of service. |
| Diagnostische logboeken voor agent | Informatie over Azure Diagnostics zichzelf. |


### <a name="linux-diagnostics-extension-lad"></a>Linux-extensie voor diagnostische gegevens (LAD)

| Gegevensbron | Beschrijving |
| --- | --- |
| Syslog | Gebeurtenissen die worden verzonden naar het systeem van de Linux-gebeurtenis registratie.   |
| Prestatiemeteritems  | Numerieke waarden meten de prestaties van verschillende aspecten van het besturings systeem en de werk belastingen. |
| Logboekbestanden | Vermeldingen die naar een logboek op basis van een bestand worden verzonden.  |

## <a name="data-destinations"></a>Gegevens bestemmingen
De diagnostische Azure-extensie voor Windows en Linux verzamelt altijd gegevens in een Azure Storage-account. Zie [Windows Azure Diagnostics extension (WAD) installeren en configureren](diagnostics-extension-windows-install.md) en de [Diagnostische Linux-extensie gebruiken om metrische gegevens en logboeken te controleren](../../virtual-machines/extensions/diagnostics-linux.md) voor een lijst met specifieke tabellen en blobs waarin deze gegevens worden verzameld.

Configureer een of meer *gegevens-sinks* voor het verzenden van gegevens naar andere extra bestemmingen. De volgende secties bevatten een lijst met de beschik bare sinks voor de Windows-en Linux-extensie voor diagnostische gegevens.

### <a name="windows-diagnostics-extension-wad"></a>Windows diagnostische gegevens extensie (WAD)

| Doel | Beschrijving |
|:---|:---|
| Metrische gegevens van Azure Monitor | Verzamelen van prestatie gegevens naar Azure Monitor meet waarden. Zie [metrische gegevens van het gast besturingssysteem verzenden naar de data base van de Azure monitor metriek](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Event Hubs | Azure Event Hubs gebruiken om gegevens buiten Azure te verzenden. Zie [Azure Diagnostics gegevens streamen naar Event hubs](diagnostics-extension-stream-event-hubs.md) |
| Azure Storage blobs | Schrijf naast tabellen ook gegevens naar blobs in Azure Storage. |
| Application Insights | Gegevens verzamelen van toepassingen die worden uitgevoerd in uw virtuele machine naar Application Insights om te integreren met andere toepassings bewaking. Zie [Diagnostische gegevens naar Application Insights verzenden](diagnostics-extension-to-application-insights.md). |

U kunt ook WAD-gegevens uit opslag verzamelen in een Log Analytics-werk ruimte om deze te analyseren met Azure Monitor logboeken, hoewel de Log Analytics-agent doorgaans wordt gebruikt voor deze functionaliteit. Het kan gegevens rechtstreeks naar een Log Analytics-werk ruimte verzenden en ondersteunt oplossingen en inzichten die extra functionaliteit bieden.  Zie [Azure Diagnostische logboeken verzamelen van Azure Storage](../agents/diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Linux-extensie voor diagnostische gegevens (LAD)
LAD schrijft gegevens naar tabellen in Azure Storage. Het ondersteunt de sinks in de volgende tabel.

| Doel | Beschrijving |
|:---|:---|
| Event Hubs | Azure Event Hubs gebruiken om gegevens buiten Azure te verzenden. |
| Azure Storage blobs | Schrijf naast tabellen ook gegevens naar blobs in Azure Storage. |
| Metrische gegevens van Azure Monitor | Installeer naast LAD de telegrafa-agent. Zie [aangepaste metrische gegevens verzamelen voor een virtuele Linux-machine met de InfluxData-Telegraf-agent](../essentials/collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Installatie en configuratie
De diagnostische uitbrei ding wordt geïmplementeerd als een [extensie voor virtuele machines](../../virtual-machines/extensions/overview.md) in azure, zodat deze dezelfde installatie opties ondersteunt met Resource Manager-sjablonen, Power shell en cli. Zie [extensies en functies van virtuele machines voor Windows](../../virtual-machines/extensions/features-windows.md) -en [virtuele-machine extensies en-functies voor Linux](../../virtual-machines/extensions/features-linux.md) voor algemene informatie over het installeren en onderhouden van extensies van virtuele machines.

U kunt ook de diagnostische Windows-en Linux-extensie installeren en configureren in het Azure Portal onder **Diagnostische instellingen** in de sectie **bewaking** van het menu van de virtuele machine.

Raadpleeg de volgende artikelen voor meer informatie over het installeren en configureren van de diagnostische uitbrei ding voor Windows en Linux.

- [De Windows Azure Diagnostics-extensie (WAD) installeren en configureren](diagnostics-extension-windows-install.md)
- [De diagnostische Linux-extensie gebruiken voor het bewaken van metrische gegevens en logboeken](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Andere documentatie

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Web-en werk rollen van de Azure-Cloud service (klassiek)
- [Inleiding tot Cloud service monitoring](../../cloud-services/cloud-services-how-to-monitor.md)
- [Azure Diagnostics in azure inschakelen Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Application Insights voor Azure Cloud Services](../app/cloudservices.md)<br>[De stroom van een Cloud Services toepassing traceren met Azure Diagnostics](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Services controleren en diagnosticeren in een ontwikkelomgeving met lokale machines](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Volgende stappen


* Meer informatie over het [gebruik van prestatie meter items in azure Diagnostics](../../cloud-services/diagnostics-performance-counters.md).
* Zie [problemen oplossen Azure Diagnostics](diagnostics-extension-troubleshooting.md) als u problemen ondervindt met het starten of vinden van uw gegevens in azure Storage-tabellen