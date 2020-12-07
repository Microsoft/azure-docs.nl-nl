---
title: Afhankelijkheids analyse in Azure Migrate server-evaluatie
description: Hierin wordt beschreven hoe u afhankelijkheids analyse gebruikt voor evaluatie met behulp van Azure Migrate server-evaluatie.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 09/15/2020
ms.openlocfilehash: 1f198d47191e7893e74b072ae8fd10546e3a6ee7
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752206"
---
# <a name="dependency-analysis"></a>Afhankelijkheidsanalyse

In dit artikel wordt de analyse van afhankelijkheden in Azure Migrate: Server evaluatie beschreven.


Afhankelijkheids analyse identificeert afhankelijkheden tussen gedetecteerde on-premises machines. Dit biedt de volgende voor delen: 

- U kunt computers in groepen verzamelen voor evaluatie, nauw keuriger, met een grotere betrouw baarheid.
- U kunt computers identificeren die samen moeten worden gemigreerd. Dit is vooral handig als u niet zeker weet welke machines deel uitmaken van een app-implementatie die u naar Azure wilt migreren.
- U kunt nagaan of machines in gebruik zijn en welke machines in plaats van worden gemigreerd.
- Het analyseren van afhankelijkheden helpt ervoor te zorgen dat er niets achterblijft, waardoor onverwachte storingen worden voor komen na de migratie.
- [Bekijk](common-questions-discovery-assessment.md#what-is-dependency-visualization) Veelgestelde vragen over afhankelijkheids analyse.


## <a name="analysis-types"></a>Soorten analyse

Er zijn twee opties voor het implementeren van afhankelijkheids analyse

**Optie** | **Details** | **Openbare cloud** | **Azure Government**
----  |---- | ---- 
**Zonder agent** | Controleert gegevens van virtuele VMware-machines met behulp van vSphere-Api's.<br/><br/> U hoeft geen agents te installeren op Vm's.<br/><br/> Deze optie is momenteel beschikbaar als preview-versie voor virtuele VMware-machines. | Ondersteund. | Ondersteund.
**Analyse op basis van een agent** | Maakt gebruik van de [servicetoewijzing oplossing](../azure-monitor/insights/service-map.md) in azure monitor om de visualisatie en analyse van afhankelijkheden in te scha kelen.<br/><br/> U moet agents installeren op elke on-premises computer die u wilt analyseren. | Ondersteund | Niet ondersteund.


## <a name="agentless-analysis"></a>Analyse zonder agent

Analyse van de afhankelijkheid van agents werkt door TCP-verbindings gegevens vast te leggen van computers waarvoor deze is ingeschakeld. Er zijn geen agents op Vm's geïnstalleerd. Verbindingen met dezelfde bron server en hetzelfde proces en de doel server, het proces en de poort zijn logisch gegroepeerd in een afhankelijkheid. U kunt vastgelegde afhankelijkheids gegevens visualiseren in een kaart weergave of exporteren als CSV-bestand. Er zijn geen agents geïnstalleerd op computers die u wilt analyseren.

### <a name="dependency-data"></a>Afhankelijkheids gegevens

Nadat de detectie van de afhankelijkheids gegevens is gestart, begint polling:

- Het Azure Migrate apparaat vraagt om de vijf minuten TCP-verbindings gegevens van machines om gegevens te verzamelen.
- Gegevens worden verzameld van gast-Vm's via vCenter Server, met behulp van vSphere-Api's.
- Met polling worden deze gegevens verzameld:

    - De naam van processen die actieve verbindingen hebben.
    - Naam van de toepassing die processen uitvoert die actieve verbindingen hebben.
    - Doel poort van de actieve verbindingen.

- De verzamelde gegevens worden verwerkt op het Azure Migrate apparaat, om identiteits gegevens af te leiden en worden elke zes uur naar Azure Migrate verzonden


## <a name="agent-based-analysis"></a>Analyse op basis van een agent

Voor analyse op basis van een agent gebruikt server evaluatie de [servicetoewijzing](../azure-monitor/insights/service-map.md) oplossing in azure monitor. U installeert [micro soft Monitoring Agent/log Analytics agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent) en de [dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent), op elke computer die u wilt analyseren.

### <a name="dependency-data"></a>Afhankelijkheids gegevens

Analyse op basis van een agent biedt de volgende gegevens:

- Naam van de bron computer server, proces, toepassings naam.
- Naam van de doel computer server, proces, toepassings naam en poort.
- Het aantal gegevens over verbindingen, latentie en gegevens overdracht wordt verzameld en beschikbaar gesteld voor Log Analytics query's. 



## <a name="compare-agentless-and-agent-based"></a>Vergelijkt agentloos en op basis van een agent

De verschillen tussen visualisatie zonder agents en visualisaties op basis van agents worden in de tabel samenvatten.

**Vereiste** | **Zonder agent** | **Op basis van een agent**
--- | --- | ---
**Ondersteuning** | Alleen in Preview voor virtuele VMware-machines. [Bekijk](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) ondersteunde besturings systemen. | In algemene Beschik baarheid (GA).
**Agent** | Er zijn geen agents nodig op computers die u wilt analyseren. | De agents die zijn vereist op elke on-premises computer die u wilt analyseren.
**Log Analytics** | Niet vereist. | Azure Migrate gebruikt de [servicetoewijzing](../azure-monitor/insights/service-map.md) oplossing in [Azure monitor logboeken](../azure-monitor/log-query/log-query-overview.md) voor afhankelijkheids analyse.<br/><br/> U koppelt een Log Analytics-werk ruimte aan een Azure Migrate-project. De werk ruimte moet zich bevinden in de regio's VS-Oost, Zuidoost-Azië of Europa-west. De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).
**Proces** | Hiermee worden de TCP-verbindings gegevens vastgelegd. Na detectie verzamelt het gegevens met intervallen van vijf minuten. | Servicetoewijzing agents die op een computer zijn geïnstalleerd, verzamelen gegevens over TCP-processen en inkomende/uitgaande verbindingen voor elk proces.
**Gegevens** | Naam van de bron computer server, proces, toepassings naam.<br/><br/> Naam van de doel computer server, proces, toepassings naam en poort. | Naam van de bron computer server, proces, toepassings naam.<br/><br/> Naam van de doel computer server, proces, toepassings naam en poort.<br/><br/> Het aantal gegevens over verbindingen, latentie en gegevens overdracht wordt verzameld en beschikbaar gesteld voor Log Analytics query's. 
**Visualisatie** | Afhankelijkheids toewijzing van één server kan worden weer gegeven gedurende een periode van één uur tot 30 dagen. | Afhankelijkheids toewijzing van één server.<br/><br/> Afhankelijkheids toewijzing van een groep servers.<br/><br/>  De kaart kan alleen over een uur worden weer gegeven.<br/><br/> Servers in een groep toevoegen aan en verwijderen uit de kaart weergave.
Gegevensexport | Gegevens van de afgelopen 30 dagen kunnen worden gedownload in een CSV-indeling. | Gegevens kunnen worden opgevraagd met Log Analytics.



## <a name="next-steps"></a>Volgende stappen

- Visualisatie van afhankelijkheid op basis van [een agent instellen](how-to-create-group-machine-dependencies.md) .
- [Probeer](how-to-create-group-machine-dependencies-agentless.md) visualisatie van de afhankelijkheid van agents uit voor VMware-vm's.
- Bekijk [Veelgestelde vragen](common-questions-discovery-assessment.md#what-is-dependency-visualization) over de visualisatie van afhankelijkheden.
