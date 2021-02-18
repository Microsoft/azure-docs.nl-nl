---
title: Log Analytics agent-gegevens bronnen in Azure Monitor
description: Gegevens bronnen definiëren de logboek gegevens die Azure Monitor verzameld van agents en andere verbonden bronnen.  In dit artikel wordt beschreven hoe Azure Monitor gegevens bronnen gebruikt, wordt uitgelegd hoe u deze kunt configureren en wordt een overzicht gegeven van de verschillende gegevens bronnen die beschikbaar zijn.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 2947f1c484f013c2d0bef9899b849f7ea761d89e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609220"
---
# <a name="log-analytics-agent-data-sources-in-azure-monitor"></a>Log Analytics agent-gegevens bronnen in Azure Monitor
De gegevens die Azure Monitor verzameld van virtuele machines met de [log Analytics](../platform/log-analytics-agent.md) -agent, worden gedefinieerd door de gegevens bronnen die u configureert in de [log Analytics-werk ruimte](../platform/data-platform-logs.md).   Elke gegevens bron maakt records van een bepaald type met elk type met een eigen set eigenschappen.

> [!IMPORTANT]
> In dit artikel worden gegevens bronnen behandeld voor de [log Analytics-agent](../platform/log-analytics-agent.md) , een van de agents die door Azure monitor worden gebruikt. Andere agents verzamelen verschillende gegevens en worden anders geconfigureerd. Zie [overzicht van Azure monitor agents](agents-overview.md) voor een lijst met beschik bare agents en de gegevens die ze kunnen verzamelen.

![Gegevens verzameling vastleggen](media/agent-data-sources/overview.png)

> [!IMPORTANT]
> De gegevens bronnen die in dit artikel worden beschreven, zijn alleen van toepassing op virtuele machines waarop de Log Analytics-agent wordt uitgevoerd. 

## <a name="summary-of-data-sources"></a>Samen vatting van gegevens bronnen
De volgende tabel geeft een lijst van de agent gegevens bronnen die op dit moment beschikbaar zijn met de Log Analytics-agent.  Elk heeft een koppeling naar een afzonderlijk artikel met Details voor die gegevens bron.   Het bevat ook informatie over de methode en frequentie van de verzameling. 


| Gegevensbron | Platform | Log Analytics-agent | Operations Manager-agent | Azure Storage | Operations Manager vereist? | Operations Manager agent gegevens die via een beheer groep zijn verzonden | Verzamelingsfrequentie |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Aangepaste logboeken](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | bij aankomst |
| [Aangepaste logboeken](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | bij aankomst |
| [IIS-logboeken](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |is afhankelijk van de instelling van het logboek bestand voor de rollover |
| [Prestatiemeteritems](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |zoals gepland, mini maal 10 seconden |
| [Prestatiemeteritems](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |zoals gepland, mini maal 10 seconden |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |uit Azure Storage: 10 minuten; van agent: op aankomst |
| [Windows-gebeurtenis logboeken](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | bij aankomst |


## <a name="configuring-data-sources"></a>Gegevens bronnen configureren
Als u gegevens bronnen voor Log Analytics agents wilt configureren, gaat u naar het menu **log Analytics-werk ruimten** in de Azure Portal en selecteert u een werk ruimte. Klik op **Geavanceerde instellingen** en vervolgens op **gegevens**. Selecteer de gegevens bron die u wilt configureren. U kunt de koppelingen in de bovenstaande tabel volgen voor documentatie voor elke gegevens bron en informatie over de configuratie.

Elke configuratie wordt afgeleverd bij alle agents die zijn verbonden met die werk ruimte.  U kunt geen verbonden agents uitsluiten van deze configuratie.

![Windows-gebeurtenissen configureren](media/agent-data-sources/configure-events.png)



## <a name="data-collection"></a>Gegevens verzamelen
Gegevens bron configuraties worden geleverd aan agents die rechtstreeks zijn verbonden met Azure Monitor binnen een paar minuten.  De opgegeven gegevens worden verzameld van de agent en direct bezorgd bij Azure Monitor met intervallen die specifiek zijn voor elke gegevens bron.  Zie de documentatie voor elke gegevens bron voor deze specifieke informatie.

Voor System Center Operations Manager agents in een verbonden beheer groep worden de gegevens bron configuraties omgezet in Management Packs en standaard elke vijf minuten aan de beheer groep geleverd.  De agent downloadt de management pack zoals de andere en verzamelt de opgegeven gegevens. Afhankelijk van de gegevens bron, worden de gegevens verzonden naar een beheer server die de gegevens doorstuurt naar de Azure Monitor, of de agent verzendt de gegevens naar Azure Monitor zonder via de beheer server te gaan. Zie [Details over het verzamelen van gegevens voor het controleren van oplossingen in azure](../monitor-reference.md) voor meer informatie.  U vindt meer informatie over het verbinden van Operations Manager en Azure Monitor en het wijzigen van de frequentie die de configuratie wordt geleverd bij het [configureren van de integratie met System Center Operations Manager](../platform/om-agents.md).

Als de agent geen verbinding kan maken met Azure Monitor of Operations Manager, worden er gegevens verzameld die worden geleverd bij het tot stand brengen van een verbinding.  Gegevens kunnen verloren gaan als de hoeveelheid gegevens de maximale cache grootte voor de client bereikt, of als de agent binnen 24 uur geen verbinding kan maken.

## <a name="log-records"></a>Logboek records
Alle door Azure Monitor verzamelde logboek gegevens worden opgeslagen in de werk ruimte als records.  Records die door verschillende gegevens bronnen worden verzameld, hebben hun eigen set eigenschappen en worden geïdentificeerd aan de hand van de eigenschap **type** .  Zie de documentatie voor elke gegevens bron en oplossing voor meer informatie over elk record type.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [controleren van oplossingen](../insights/solutions.md) voor het toevoegen van functionaliteit aan Azure monitor en het verzamelen van gegevens in de werk ruimte.
* Meer informatie over [logboek query's](../log-query/log-query-overview.md) voor het analyseren van de verzamelde gegevens van gegevens bronnen en bewakings oplossingen.  
* [Waarschuwingen](../platform/alerts-overview.md) configureren om u proactief te informeren over essentiële gegevens die worden verzameld uit gegevens bronnen en bewakings oplossingen.
