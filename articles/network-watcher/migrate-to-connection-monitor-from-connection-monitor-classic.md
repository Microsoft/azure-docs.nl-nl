---
title: Migreren naar Verbindingsmonitor van Verbindingsmonitor
titleSuffix: Azure Network Watcher
description: Meer informatie over migreren naar Verbindingsmonitor van Verbindingsmonitor.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: fc5bcc7f0cd11160b33bb6501526fce9f29d710b
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366382"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>Migreren naar Verbindingsmonitor van Verbindingsmonitor (klassiek)

> [!IMPORTANT]
> Vanaf 1 juli 2021 kunt u geen nieuwe verbindingsmonitors toevoegen in Verbindingsmonitor (klassiek), maar u kunt bestaande verbindingsmonitors die vóór 1 juli 2021 zijn gemaakt, blijven gebruiken. Als u serviceonderbrekingen voor uw huidige workloads wilt minimaliseren, migreert u vóór 29 februari 2024 van [Verbindingsmonitor (klassiek)](migrate-to-connection-monitor-from-connection-monitor-classic.md)  naar de nieuwe Verbindingsmonitor in Azure Network Watcher.

U kunt bestaande verbindingsmonitors migreren naar nieuwe, verbeterde Verbindingsmonitor slechts enkele klikken en zonder downtime. Zie voor meer informatie over de voordelen [Verbindingsmonitor.](./connection-monitor-overview.md)

## <a name="key-points-to-note"></a>Belangrijke punten om op te merken

De migratie helpt bij het produceren van de volgende resultaten:

* Agents en firewallinstellingen werken zoals ze zijn. Er zijn geen wijzigingen vereist. 
* Bestaande verbindingsmonitors worden aan de Verbindingsmonitor > testgroep > testindeling. Door Bewerken te **selecteren,** kunt u de eigenschappen van de nieuwe Verbindingsmonitor weergeven en wijzigen, een sjabloon downloaden om wijzigingen aan te brengen in Verbindingsmonitor en deze verzenden via Azure Resource Manager. 
* Virtuele Azure-machines met de extensie Network Watcher verzenden gegevens naar zowel de werkruimte als de metrische gegevens. Verbindingsmonitor maakt de gegevens beschikbaar via de nieuwe metrische gegevens (ChecksFailedPercent en RoundTripTimeMs) in plaats van de oude metrische gegevens (ProbesFailedPercent en AverageRoundtripMs). De oude metrische gegevens worden gemigreerd naar nieuwe metrische gegevens als ProbesFailedPercent -> ChecksFailedPercent en AverageRoundtripMs -> RoundTripTimeMs.
* Gegevensbewaking:
   * **Waarschuwingen:** automatisch gemigreerd naar de nieuwe metrische gegevens.
   * **Dashboards en integraties:** u moet de metrische gegevensset handmatig bewerken. 
    
## <a name="prerequisites"></a>Vereisten

1. Als u een aangepaste werkruimte gebruikt, controleert u of Network Watcher is ingeschakeld in uw abonnement en in de regio van uw Log Analytics-werkruimte. Als dat niet het beste is, wordt er een foutbericht weergegeven met de tekst 'Voordat u probeert te migreren, moet u de Network Watcher-extensie inschakelen in het selectieabonnement en de locatie van de geselecteerde LA-werkruimte.'
1. Als voor virtuele machines die worden gebruikt als bronnen in de verbindingsmonitor (klassiek) de Network Watcher-extensie niet meer is ingeschakeld, wordt er een foutbericht weergegeven met de mededeling dat verbindingsmonitors met de volgende tests niet kunnen worden geïmporteerd als een of meer virtuele Azure-machines waarop geen Network Watcher-extensie is geïnstalleerd. Installeer de Network Watcher-extensie en klik op Vernieuwen om ze te importeren.



## <a name="migrate-the-connection-monitors"></a>De verbindingsmonitors migreren

1. Als u de oudere verbindingsmonitors naar de nieuwere wilt migreren, selecteert **Verbindingsmonitor** en selecteert u **vervolgens Verbindingsmonitors migreren.**

    ![Schermopname van de migratie van verbindingsmonitors naar Verbindingsmonitor.](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Selecteer uw abonnement en de verbindingsmonitors die u wilt migreren en selecteer vervolgens **Migratie geselecteerd.** 

Met slechts een paar klikken hebt u de bestaande verbindingsmonitors gemigreerd naar Verbindingsmonitor. Zodra de monitor is gemigreerd van CM (klassiek) naar CM, kunt u de monitor niet meer zien onder CM (klassiek)

U kunt nu de Verbindingsmonitor aanpassen, de standaardwerkruimte wijzigen, sjablonen downloaden en de migratiestatus controleren. 

Nadat de migratie is gestart, worden de volgende wijzigingen doorgevoerd: 
* De Azure Resource Manager resource wordt gewijzigd in de nieuwere verbindingsmonitor.
    * De naam, regio en het abonnement van de verbindingsmonitor blijven ongewijzigd. De resource-id wordt niet beïnvloed.
    * Tenzij de verbindingsmonitor is aangepast, wordt er een standaard Log Analytics-werkruimte gemaakt in het abonnement en in de regio van de verbindingsmonitor. In deze werkruimte worden bewakingsgegevens opgeslagen. De testresultaten worden ook opgeslagen in de metrische gegevens.
    * Elke test wordt gemigreerd naar een testgroep met de *naam defaultTestGroup.*
    * Bron- en doel-eindpunten worden gemaakt en gebruikt in de nieuwe testgroep. De standaardnamen zijn *defaultSourceEndpoint* en *defaultDestinationEndpoint.*
    * De doelpoort en het testinterval worden verplaatst naar een testconfiguratie met de naam *defaultTestConfiguration.* Het protocol wordt ingesteld op basis van de poortwaarden. Drempelwaarden voor succes en andere optionele eigenschappen blijven leeg.
* Waarschuwingen voor metrische gegevens worden gemigreerd Verbindingsmonitor waarschuwingen voor metrische gegevens. De metrische gegevens verschillen, vandaar de wijziging. Zie Bewaking van netwerkconnectiviteit met Verbindingsmonitor [voor meer Verbindingsmonitor.](./connection-monitor-overview.md#metrics-in-azure-monitor)
* De gemigreerde verbindingsmonitors worden niet meer weergegeven als de oudere oplossing voor verbindingsmonitors. Ze zijn nu alleen beschikbaar voor gebruik in Verbindingsmonitor.
* Alle externe integraties, zoals dashboards in Power BI en Grafana, en integraties met Security Information and Event Management(SIEM)-systemen, moeten handmatig worden gemigreerd. Dit is de enige handmatige stap die u moet uitvoeren om uw installatie te migreren.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Verbindingsmonitor, zie:
* [Migreren van Netwerkprestatiemeter naar Verbindingsmonitor](./migrate-to-connection-monitor-from-network-performance-monitor.md)
* [Maak Verbindingsmonitor met behulp van de Azure Portal](./connection-monitor-create-using-portal.md)
