---
title: Afhankelijkheids analyse in Azure Migrate detectie en evaluatie
description: Hierin wordt beschreven hoe u afhankelijkheids analyse gebruikt voor evaluatie met behulp van Azure Migrate detectie en evaluatie.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: 184c8099c0e86d8f8744948137b344c732bbf7b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778369"
---
# <a name="dependency-analysis"></a>Afhankelijkheidsanalyse

In dit artikel wordt de afhankelijkheids analyse in Azure Migrate: detectie en evaluatie beschreven.

Afhankelijkheids analyse identificeert afhankelijkheden tussen gedetecteerde on-premises servers. Dit biedt de volgende voor delen:

- U kunt servers in groepen verzamelen voor evaluatie, nauw keuriger, met een grotere betrouw baarheid.
- U kunt servers identificeren die samen moeten worden gemigreerd. Dit is vooral handig als u niet zeker weet welke servers deel uitmaken van een app-implementatie die u naar Azure wilt migreren.
- U kunt vaststellen of de servers in gebruik zijn en welke servers in plaats van worden gemigreerd.
- Het analyseren van afhankelijkheden helpt ervoor te zorgen dat er niets achterblijft, waardoor onverwachte storingen worden voor komen na de migratie.
- [Bekijk](common-questions-discovery-assessment.md#what-is-dependency-visualization) Veelgestelde vragen over afhankelijkheids analyse.

## <a name="analysis-types"></a>Soorten analyse

Er zijn twee opties voor het implementeren van afhankelijkheids analyse

**Optie** | **Details** | **Openbare cloud** | **Azure Government**
----  |---- | ----
**Zonder agent** | Controleert gegevens van servers op VMware met behulp van vSphere-Api's.<br/><br/> U hoeft geen agents op servers te installeren.<br/><br/> Deze optie is momenteel beschikbaar als preview-versie, alleen voor servers in VMware. | Ondersteund. | Ondersteund.
**Analyse op basis van een agent** | Maakt gebruik van de [servicetoewijzing oplossing](../azure-monitor/vm/service-map.md) in azure monitor om de visualisatie en analyse van afhankelijkheden in te scha kelen.<br/><br/> U moet agents installeren op elke on-premises server die u wilt analyseren. | Ondersteund | Wordt niet ondersteund.

## <a name="agentless-analysis"></a>Analyse zonder agent

Analyse van de afhankelijkheid van agents werkt door het vastleggen van TCP-verbindings gegevens van servers waarvoor deze functie is ingeschakeld. Er zijn geen agents geïnstalleerd op servers. Verbindingen met dezelfde bron server en hetzelfde proces en de doel server, het proces en de poort zijn logisch gegroepeerd in een afhankelijkheid. U kunt vastgelegde afhankelijkheids gegevens visualiseren in een kaart weergave of exporteren als CSV-bestand. Er zijn geen agents geïnstalleerd op servers die u wilt analyseren.

### <a name="dependency-data"></a>Afhankelijkheids gegevens

Nadat de detectie van de afhankelijkheids gegevens is gestart, begint polling:

- Het Azure Migrate apparaat vraagt om de vijf minuten TCP-verbindings gegevens van servers om gegevens te verzamelen.
- Gegevens worden verzameld van gast servers via vCenter Server, met behulp van vSphere-Api's.
- Met polling worden deze gegevens verzameld:

    - De naam van processen die actieve verbindingen hebben.
    - Naam van de toepassing die processen uitvoert die actieve verbindingen hebben.
    - Doel poort van de actieve verbindingen.

- De verzamelde gegevens worden verwerkt op het Azure Migrate apparaat, om identiteits gegevens af te leiden en worden elke zes uur naar Azure Migrate verzonden


## <a name="agent-based-analysis"></a>Analyse op basis van een agent

Voor analyse op basis van agents Azure Migrate: detectie en evaluatie maakt gebruik van de [servicetoewijzing](../azure-monitor/vm/service-map.md) oplossing in azure monitor. U installeert [micro soft Monitoring Agent/log Analytics agent](../azure-monitor/agents/agents-overview.md#log-analytics-agent) en de [dependency agent](../azure-monitor/agents/agents-overview.md#dependency-agent), op elke server die u wilt analyseren.

### <a name="dependency-data"></a>Afhankelijkheids gegevens

Analyse op basis van een agent biedt de volgende gegevens:

- Naam van bron server, proces, toepassings naam.
- Naam, proces, toepassings naam en poort van de doel server.
- Het aantal gegevens over verbindingen, latentie en gegevens overdracht wordt verzameld en beschikbaar gesteld voor Log Analytics query's.

## <a name="compare-agentless-and-agent-based"></a>Vergelijkt agentloos en op basis van een agent

De verschillen tussen visualisatie zonder agents en visualisaties op basis van agents worden in de tabel samenvatten.

**Vereiste** | **Zonder agent** | **Op basis van een agent**
--- | --- | ---
**Ondersteuning** | In de preview-versie voor servers alleen op VMware. [Bekijk](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) ondersteunde besturings systemen. | In algemene Beschik baarheid (GA).
**Tussen** | Er zijn geen agents nodig op servers die u wilt analyseren. | Agents die zijn vereist op elke on-premises server die u wilt analyseren.
**Log Analytics** | Niet vereist. | Azure Migrate gebruikt de [servicetoewijzing](../azure-monitor/vm/service-map.md) oplossing in [Azure monitor logboeken](../azure-monitor/logs/log-query-overview.md) voor afhankelijkheids analyse.<br/><br/> U koppelt een Log Analytics-werk ruimte aan een project. De werk ruimte moet zich bevinden in de regio's VS-Oost, Zuidoost-Azië of Europa-west. De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
**Proces** | Hiermee worden de TCP-verbindings gegevens vastgelegd. Na detectie verzamelt het gegevens met intervallen van vijf minuten. | Servicetoewijzing agents die op een server zijn geïnstalleerd, verzamelen gegevens over TCP-processen en inkomende/uitgaande verbindingen voor elk proces.
**Gegevens** | Naam van bron server, proces, toepassings naam.<br/><br/> Naam, proces, toepassings naam en poort van de doel server. | Naam van bron server, proces, toepassings naam.<br/><br/> Naam, proces, toepassings naam en poort van de doel server.<br/><br/> Het aantal gegevens over verbindingen, latentie en gegevens overdracht wordt verzameld en beschikbaar gesteld voor Log Analytics query's. 
**Visualisatie** | Afhankelijkheids toewijzing van één server kan worden weer gegeven gedurende een periode van één uur tot 30 dagen. | Afhankelijkheids toewijzing van één server.<br/><br/> Afhankelijkheids toewijzing van een groep servers.<br/><br/>  De kaart kan alleen over een uur worden weer gegeven.<br/><br/> Servers in een groep toevoegen aan en verwijderen uit de kaart weergave.
Gegevensexport | Gegevens van de afgelopen 30 dagen kunnen worden gedownload in een CSV-indeling. | Gegevens kunnen worden opgevraagd met Log Analytics.



## <a name="next-steps"></a>Volgende stappen

- Visualisatie van afhankelijkheid op basis van [een agent instellen](how-to-create-group-machine-dependencies.md) .
- [Probeer](how-to-create-group-machine-dependencies-agentless.md) de visualisatie van de afhankelijkheid van agents uit voor servers in VMware.
- Bekijk [Veelgestelde vragen](common-questions-discovery-assessment.md#what-is-dependency-visualization) over de visualisatie van afhankelijkheden.
