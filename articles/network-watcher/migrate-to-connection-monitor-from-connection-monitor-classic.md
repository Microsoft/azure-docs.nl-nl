---
title: Migreren naar verbindings monitor vanuit verbindings monitor
titleSuffix: Azure Network Watcher
description: Meer informatie over hoe u kunt migreren naar verbindings monitor vanuit verbindings monitor.
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
ms.openlocfilehash: d4ab5361d245ad1ee10d43184cc0a2d65fed2054
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730028"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>Migreren naar verbindings monitor vanuit verbindings monitor (klassiek)

> [!IMPORTANT]
> Vanaf 1 juli 2021 kunt u geen nieuwe monitors voor verbinding toevoegen in de verbindings monitor (klassiek), maar bestaande verbindings monitors die zijn gemaakt vóór 1 juli 2021 blijven gebruiken. Als u de service onderbreking voor uw huidige workloads wilt minimaliseren, [migreert u van verbindings monitor (klassiek) naar de nieuwe verbindings monitor](migrate-to-connection-monitor-from-connection-monitor-classic.md)  in azure Network Watcher vóór 29 februari 2024.

U kunt bestaande verbindings monitors migreren naar nieuwe, verbeterde verbindings controle met slechts enkele klikken en met een downtime van nul. Zie [verbindings monitor](./connection-monitor-overview.md)voor meer informatie over de voor delen.

## <a name="key-points-to-note"></a>Belangrijkste punten om te noteren

De migratie helpt de volgende resultaten te produceren:

* De agents en Firewall instellingen werken zoals. Er zijn geen wijzigingen vereist. 
* Bestaande verbindings monitors worden toegewezen aan de verbindings monitor > test groep > test-indeling. Als u **bewerken** selecteert, kunt u de eigenschappen van de nieuwe verbindings monitor weer geven en wijzigen, een sjabloon downloaden om wijzigingen aan te brengen in de verbindings monitor en deze verzenden via Azure Resource Manager. 
* Virtuele Azure-machines met de extensie Network Watcher verzenden gegevens naar zowel de werk ruimte als de metrieken. Met verbindings monitor worden de gegevens beschikbaar via de nieuwe metrieken (ChecksFailedPercent en RoundTripTimeMs) in plaats van de oude meet waarden (ProbesFailedPercent en AverageRoundtripMs). De oude metrische gegevens worden gemigreerd naar nieuwe metrische gegevens als ProbesFailedPercent-> ChecksFailedPercent en AverageRoundtripMs-> RoundTripTimeMs.
* Gegevens bewaking:
   * **Waarschuwingen**: automatisch gemigreerd naar de nieuwe metrische gegevens.
   * **Dash boards en integraties**: vereisen hand matig bewerken van de metrische gegevens sets. 
    
## <a name="prerequisites"></a>Vereisten

Als u een aangepaste werk ruimte gebruikt, zorg er dan voor dat Network Watcher is ingeschakeld in uw abonnement en in de regio van uw Log Analytics-werk ruimte. 

## <a name="migrate-the-connection-monitors"></a>De verbindings monitors migreren

1. Als u de oudere verbindings monitors naar de nieuwere versie wilt migreren, selecteert u **verbindings monitor** en selecteert u vervolgens verbindings monitors **migreren**.

    ![Scherm opname van de migratie van verbindings monitors naar verbindings monitor.](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Selecteer uw abonnement en de monitors voor de verbinding die u wilt migreren en selecteer vervolgens **migreren geselecteerd**. 

Met slechts een paar muis klikken hebt u de bestaande verbindings monitors gemigreerd naar de verbindings monitor. Nadat u hebt gemigreerd van CM (klassiek) naar CM, kunt u de monitor niet zien onder CM (klassiek)

U kunt nu eigenschappen van de verbindings monitor aanpassen, de standaardwerk ruimte wijzigen, sjablonen downloaden en de migratie status controleren. 

Nadat de migratie is gestart, worden de volgende wijzigingen doorgevoerd: 
* De Azure Resource Manager bron wordt gewijzigd in de nieuwere verbindings monitor.
    * De naam, de regio en het abonnement van de verbindings monitor blijven ongewijzigd. De resource-ID wordt niet beïnvloed.
    * Tenzij de verbindings monitor is aangepast, wordt er een standaard Log Analytics werkruimte gemaakt in het abonnement en in de regio van de verbindings monitor. In deze werk ruimte worden bewakings gegevens opgeslagen. De gegevens van de test resultaten worden ook opgeslagen in de metrieken.
    * Elke test wordt gemigreerd naar een test groep met de naam *defaultTestGroup*.
    * De bron-en doel-eind punten worden gemaakt en gebruikt in de nieuwe test groep. De standaard namen zijn *defaultSourceEndpoint* en *defaultDestinationEndpoint*.
    * De doel poort en het probing-interval worden verplaatst naar een test configuratie met de naam *defaultTestConfiguration*. Het protocol wordt ingesteld op basis van de poort waarden. De drempel waarden voor geslaagde pogingen en andere optionele eigenschappen blijven leeg.
* Waarschuwingen voor metrische gegevens worden gemigreerd naar waarschuwingen over metrische gegevens van verbindings controle. De metrische gegevens zijn anders, dus de wijziging. Zie [netwerk connectiviteit controleren met verbindings monitor](./connection-monitor-overview.md#metrics-in-azure-monitor)voor meer informatie.
* De gemigreerde verbindings monitors worden niet meer weer gegeven als de oudere oplossing van de verbindings monitor. Ze zijn nu alleen beschikbaar voor gebruik in de verbindings monitor.
* Externe integraties, zoals Dash boards in Power BI en Grafana en integraties met Security Information and Event Management-systemen (SIEM), moeten hand matig worden gemigreerd. Dit is de enige hand matige stap die u moet uitvoeren om uw installatie te migreren.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over verbindings monitor:
* [Migreren van Netwerkprestatiemeter naar verbindings monitor](./migrate-to-connection-monitor-from-network-performance-monitor.md)
* [Verbindings monitor maken met behulp van de Azure Portal](./connection-monitor-create-using-portal.md)
