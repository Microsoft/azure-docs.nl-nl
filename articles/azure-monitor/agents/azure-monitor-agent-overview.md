---
title: Overzicht van Azure Monitor-agent
description: Overzicht van de Azure Monitor-agent (AMA), waarmee bewakings gegevens worden verzameld van het gast besturingssysteem van virtuele machines.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: e4837de70e9f00308b440933e0cd433ad5b27cf9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711532"
---
# <a name="azure-monitor-agent-overview-preview"></a>Overzicht van Azure Monitor-agent (preview)
De Azure Monitor-agent (AMA) verzamelt bewakings gegevens van het gast besturingssysteem van virtuele machines en levert deze aan Azure Monitor. In deze artikelen vindt u een overzicht van de Azure Monitor-agent, inclusief hoe u deze kunt installeren en hoe u gegevens verzameling kunt configureren.

## <a name="relationship-to-other-agents"></a>Relatie met andere agents
De Azure Monitor-agent vervangt de volgende agents die momenteel worden gebruikt door Azure Monitor om gast gegevens van virtuele machines te verzamelen:

- [Log Analytics-agent](./log-analytics-agent.md) : verzendt gegevens naar log Analytics-werk ruimte en biedt ondersteuning voor de mogelijkheden van de VM Insights-en bewakings oplossing.
- [Diagnostische extensie](./diagnostics-extension-overview.md) : verzendt gegevens naar Azure monitor meet waarden (alleen Windows), Azure Event Hubs en Azure Storage.
- [Telegrafa-agent](../essentials/collect-custom-metrics-linux-telegraf.md) : Hiermee worden gegevens verzonden naar Azure monitor metrieken (alleen voor Linux).

Naast het consolideren van deze functionaliteit in één agent biedt de Azure Monitor-agent de volgende voor delen ten opzichte van de bestaande agents:

- Bewakings bereik. Configureer verzameling centraal voor verschillende gegevens sets van verschillende sets virtuele machines.  
- Linux multi-multihoming: gegevens van Linux-Vm's naar meerdere werk ruimten verzenden.
- Windows-gebeurtenis filtering: XPATH-query's gebruiken om te filteren welke Windows-gebeurtenissen worden verzameld.
- Verbeterd extensie beheer: Azure Monitor-agent gebruikt een nieuwe methode voor het verwerken van uitbreid baarheid die transparanter en beter kan worden beheerd dan Management Packs en Linux-invoeg toepassingen in de huidige Log Analytics agents.

### <a name="changes-in-data-collection"></a>Wijzigingen in gegevens verzameling
De methoden voor het definiëren van het verzamelen van gegevens voor de bestaande agents zijn verschillend van elkaar en elk een probleem met Azure Monitor agent.

- De configuratie van Log Analytics-agent wordt opgehaald uit een Log Analytics-werk ruimte. Dit is eenvoudig te configureren, maar moeilijk om onafhankelijke definities te definiëren voor verschillende virtuele machines. Er kunnen alleen gegevens worden verzonden naar een Log Analytics-werk ruimte.

- Diagnostische uitbrei ding heeft een configuratie voor elke virtuele machine. Zo kunt u eenvoudig onafhankelijke definities definiëren voor verschillende virtuele machines, maar moeilijk te beheren. Er kunnen alleen gegevens worden verzonden naar Azure Monitor metrieken, Azure Event Hubs of Azure Storage. Voor Linux-agents is de open source-telegrafie agent vereist om gegevens te verzenden naar Azure Monitor meet waarden.

Azure Monitor agent gebruikt [gegevens verzamelings regels (DCR)](data-collection-rule-overview.md) om gegevens te configureren die van elke agent moeten worden verzameld. Regels voor het verzamelen van gegevens zorgen ervoor dat de verzamelings instellingen op schaal kunnen worden beheerd, terwijl er nog steeds unieke, scoped configuraties voor subsets van machines worden ingeschakeld. Ze zijn onafhankelijk van de werk ruimte en onafhankelijk van de virtuele machine, waardoor ze eenmaal kunnen worden gedefinieerd en opnieuw worden gebruikt tussen computers en omgevingen. Zie [gegevens verzameling configureren voor de Azure monitor-agent (preview)](data-collection-rule-azure-monitor-agent.md).

## <a name="should-i-switch-to-azure-monitor-agent"></a>Moet ik overschakelen naar Azure Monitor-agent?
Azure Monitor agent naast de [Algemeen beschik bare agents voor Azure monitor](agents-overview.md), maar u kunt overwegen uw vm's tijdens de open bare preview-periode van Azure monitor agent over te zetten naar de huidige agents. Houd rekening met de volgende factoren wanneer u deze bepaling maakt.

- **Omgevings vereisten.** Azure Monitor-agent heeft een beperktere set ondersteunde besturings systemen, omgevingen en netwerk vereisten dan de huidige agents. Toekomstige omgevings ondersteuning, zoals nieuwe besturingssysteem versies en typen netwerk vereisten, wordt waarschijnlijk alleen in Azure Monitor-agent geboden. U moet nagaan of uw omgeving door Azure Monitor agent wordt ondersteund. Als dat niet het geval is, moet u blijven beschikken over de huidige agent. Als Azure Monitor agent uw huidige omgeving ondersteunt, kunt u overwegen om over te stappen.
- **Risico tolerantie voor open bare preview.** Hoewel Azure Monitor agent uitvoerig is getest voor de momenteel ondersteunde scenario's, is de agent nog steeds beschikbaar in de open bare preview. Versie-updates en functionaliteits verbeteringen worden regel matig uitgevoerd en kunnen fouten veroorzaken. U moet het risico van een bug in de agent op uw Vm's beoordelen, waardoor het verzamelen van gegevens kan worden gestopt. Als een onderbreking in het verzamelen van gegevens geen aanzienlijke gevolgen heeft voor uw services, kunt u door gaan met Azure Monitor agent. Als u een lage tolerantie voor instabiliteit hebt, moet u de algemeen beschik bare agents blijven totdat Azure Monitor agent deze status bereikt.
- **Huidige en nieuwe functie vereisten.** Azure Monitor-agent introduceert een aantal nieuwe mogelijkheden, zoals filteren, bereik en multi-multihoming, maar het is nog geen pariteit met de huidige agents voor andere functionaliteit, zoals aangepaste logboek verzameling en integratie met oplossingen. De meeste nieuwe mogelijkheden in Azure Monitor worden alleen beschikbaar gesteld met Azure Monitor agent, waardoor de tijd meer functionaliteit alleen beschikbaar is in de nieuwe agent. U moet nagaan of Azure Monitor agent beschikt over de functies die u nodig hebt, en als er sommige functies zijn die u tijdelijk kunt uitvoeren zonder dat u andere belang rijke functies in de nieuwe agent hebt. Als Azure Monitor agent beschikt over alle kern mogelijkheden die u nodig hebt, kunt u overwegen om over te stappen. Als er belang rijke functies zijn die u nodig hebt, gaat u verder met de huidige agent totdat Azure Monitor agent de pariteit bereikt.
- **Tolerantie voor rewerken.** Als u een nieuwe omgeving instelt met resources zoals implementatie scripts en onboarding-sjablonen, kunt u overwegen of u deze wilt herstellen wanneer Azure Monitor agent algemeen beschikbaar wordt. Als de inspanningen voor deze herwerkings taken mini maal zijn, kunt u de huidige agents nu blijven gebruiken. Als dit een aanzienlijke hoeveelheid werk duurt, kunt u overwegen om uw nieuwe omgeving in te stellen met de nieuwe agent. De Azure Monitor-agent wordt naar verwachting algemeen beschikbaar en een datum voor de afschaffing van de Log Analytics agents in 2021. De huidige agents worden gedurende enkele jaren ondersteund zodra de afschaffing wordt gestart.



## <a name="current-limitations"></a>Huidige beperkingen
De volgende beperkingen zijn van toepassing tijdens de open bare preview van de Azure Monitor-agent:

- De Azure Monitor-agent biedt geen ondersteuning voor oplossingen en inzichten zoals VM Insights en Azure Security Center. Het enige scenario dat momenteel wordt ondersteund, is het verzamelen van gegevens met behulp van de regels voor gegevens verzameling die u configureert. 
- Regels voor het verzamelen van gegevens moeten in dezelfde regio worden gemaakt als de Log Analytics werk ruimte die als bestemming wordt gebruikt.
- Virtuele machines van Azure, virtuele-machine schaal sets en Azure Arc-servers worden momenteel ondersteund. De Azure Kubernetes-service en andere compute-resource typen worden momenteel niet ondersteund.
- De virtuele machine moet toegang hebben tot de volgende HTTPS-eind punten:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Samen werking met andere agents
De Azure Monitor-agent kan naast de bestaande agents worden gebruikt, zodat u de bestaande functionaliteit ervan kunt blijven gebruiken tijdens de evaluatie of migratie. Dit is met name belang rijk vanwege de beperkingen in de open bare Preview bij de ondersteuning van bestaande oplossingen. Wees voorzichtig met het verzamelen van dubbele gegevens, omdat dit ertoe kan leiden dat query resultaten worden gescheefd en dat er extra kosten in rekening worden gebracht voor gegevens opname en-retentie.

Zo gebruikt VM Insights de Log Analytics-agent voor het verzenden van prestatie gegevens naar een Log Analytics-werk ruimte. Mogelijk hebt u de werk ruimte ook geconfigureerd voor het verzamelen van Windows-gebeurtenissen en syslog-gebeurtenissen van agents. Als u de Azure Monitor Agent installeert en een regel voor het verzamelen van gegevens voor dezelfde gebeurtenissen en prestatie gegevens maakt, worden er dubbele gegevens geretourneerd.


## <a name="costs"></a>Kosten
Er zijn geen kosten verbonden aan Azure Monitor-agent, maar mogelijk worden er kosten in rekening gebracht voor de gegevens die zijn opgenomen. Zie [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor meer informatie over log Analytics gegevens verzameling en-retentie en voor de metriek van de klant.

## <a name="data-sources-and-destinations"></a>Gegevens bronnen en doelen
De volgende tabel bevat de gegevens typen die u op dit moment kunt verzamelen met de Azure Monitor-agent met behulp van regels voor het verzamelen van gegevens en waar u deze gegevens kunt verzenden. Zie [wat wordt bewaakt door Azure monitor?](../monitor-reference.md) voor een lijst met inzichten, oplossingen en andere oplossingen die gebruikmaken van de Azure monitor-agent voor het verzamelen van andere soorten gegevens.


De Azure Monitor-agent verzendt gegevens naar Azure Monitor metrieken of een Log Analytics-werk ruimte die Azure Monitor logboeken ondersteunt.

| Gegevensbron | Bestemmingen | Beschrijving |
|:---|:---|:---|
| Prestaties        | Metrische gegevens van Azure Monitor<br>Log Analytics-werkruimte | Numerieke waarden meten de prestaties van verschillende aspecten van het besturings systeem en de werk belastingen. |
| Windows-gebeurtenis logboeken | Log Analytics-werkruimte | Gegevens die worden verzonden naar het Windows-systeem voor gebeurtenis registratie. |
| Syslog             | Log Analytics-werkruimte | Informatie die wordt verzonden naar het systeem voor het registreren van Linux-gebeurtenissen. |


## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
Zie [ondersteunde besturings systemen](agents-overview.md#supported-operating-systems) voor een lijst met versies van het Windows-en Linux-besturings systeem die momenteel worden ondersteund door de Azure monitor-agent.



## <a name="security"></a>Beveiliging
Voor de Azure Monitor-agent zijn geen sleutels vereist, maar hiervoor is een door het [systeem toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)vereist. U moet een door het systeem toegewezen beheerde identiteit hebben ingeschakeld op elke virtuele machine voordat u de agent implementeert.

## <a name="networking"></a>Netwerken
De Azure Monitor-agent ondersteunt Azure-service tags (zowel AzureMonitor-als AzureResourceManager-Tags zijn vereist), maar zijn nog niet geschikt voor Azure Monitor persoonlijke koppelings bereiken of directe proxy's.


## <a name="next-steps"></a>Volgende stappen

- [Installeer Azure monitor agent](azure-monitor-agent-install.md) op virtuele Windows-en Linux-machines.
- [Maak een gegevensverzamelings regel](data-collection-rule-azure-monitor-agent.md) voor het verzamelen van gegevens van de agent en verzend deze naar Azure monitor.