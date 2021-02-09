---
title: Migreren naar verbindings monitor vanaf Netwerkprestatiemeter
titleSuffix: Azure Network Watcher
description: Meer informatie over het migreren naar verbindings monitor vanuit Netwerkprestatiemeter.
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
ms.openlocfilehash: 0bb46c17ece9a38d9f1e10c79a4b026efa0ece4c
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833793"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migreren naar verbindings monitor vanaf Netwerkprestatiemeter

> [!IMPORTANT]
> Vanaf 1 juli 2021 kunt u geen nieuwe tests toevoegen in een bestaande werk ruimte of een nieuwe werk ruimte inschakelen in Netwerkprestatiemeter. U kunt de tests die zijn gemaakt vóór 1 juli 2021 blijven gebruiken. Als u de service onderbreking voor uw huidige workloads wilt minimaliseren, migreert u uw tests van Netwerkprestatiemeter naar de nieuwe verbindings monitor in azure Network Watcher vóór 29 februari 2024.

U kunt tests migreren van Netwerkprestatiemeter (NPM) naar een nieuwe, verbeterde verbindings monitor met één klik en met een downtime van nul. Zie [verbindings monitor](./connection-monitor-overview.md)voor meer informatie over de voor delen.


## <a name="key-points-to-note"></a>Belangrijkste punten om te noteren

De migratie helpt de volgende resultaten te produceren:

* On-premises agents en Firewall instellingen werken op dezelfde locatie. Er zijn geen wijzigingen vereist. Log Analytics agents die zijn geïnstalleerd op virtuele machines van Azure, moeten worden vervangen door de Network Watcher extensie.
* Bestaande testen worden toegewezen aan de verbindings monitor > test groep > test-indeling. Als u **bewerken** selecteert, kunt u de eigenschappen van de nieuwe verbindings monitor weer geven en wijzigen, een sjabloon downloaden om deze wijzigingen aan te brengen en de sjabloon indienen via Azure Resource Manager.
* Agents verzenden gegevens naar de Log Analytics-werk ruimte en de metrieken.
* Gegevens bewaking:
   * **Gegevens in log Analytics**: vóór de migratie blijven de gegevens in de werk ruimte waarin NPM is geconfigureerd in de tabel NetworkMonitoring. Na de migratie worden de gegevens verplaatst naar de tabel NetworkMonitoring en de tabel ConnectionMonitor_CL in dezelfde werk ruimte. Nadat de tests zijn uitgeschakeld in NPM, worden de gegevens alleen opgeslagen in de tabel ConnectionMonitor_CL.
   * **Waarschuwingen op basis van Logboeken, Dash boards en integraties**: u moet de query's hand matig bewerken op basis van de nieuwe ConnectionMonitor_CL tabel. Zie [netwerk connectiviteit controleren met verbindings monitor](./connection-monitor-overview.md#metrics-in-azure-monitor)als u de waarschuwingen in metrische gegevens opnieuw wilt maken.
    
## <a name="prerequisites"></a>Vereisten

* Zorg ervoor dat Network Watcher is ingeschakeld in uw abonnement en de regio van de Log Analytics-werk ruimte.
* Virtuele Azure-machines waarop Log Analytics-agents zijn geïnstalleerd, moeten worden ingeschakeld met de Network Watcher extensie.

## <a name="migrate-the-tests"></a>De tests migreren

Ga als volgt te werk om de tests te migreren van Netwerkprestatiemeter naar verbindings monitor:

1. Selecteer in Network Watcher **verbindings monitor** en selecteer vervolgens het tabblad **tests migreren van NPM** . 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Testen van Netwerkprestatiemeter naar verbindings monitor migreren" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. Selecteer uw abonnement en werk ruimte in de vervolg keuzelijst en selecteer vervolgens de NPM-functie die u wilt migreren. 
1. Selecteer **importeren** om de tests te migreren.

Nadat de migratie is gestart, worden de volgende wijzigingen doorgevoerd: 
* Er wordt een nieuwe bron voor verbindings bewaking gemaakt.
   * Er wordt één verbindings monitor per regio en abonnement gemaakt. Voor testen met on-premises agents wordt de naam van de nieuwe verbindings monitor opgemaakt als `<workspaceName>_"on-premises"` . Voor testen met Azure-agents wordt de naam van de nieuwe verbindings monitor opgemaakt als `<workspaceName>_<Azure_region_name>` .
   * Bewakings gegevens worden nu opgeslagen in dezelfde Log Analytics werk ruimte waarin NPM is ingeschakeld, in een nieuwe tabel met de naam Connectionmonitor_CL. 
   * De test naam wordt getransporteerd als de naam van de test groep. De beschrijving van de test wordt niet gemigreerd.
   * De bron-en doel-eind punten worden gemaakt en gebruikt in de nieuwe test groep. Voor on-premises agents worden de eind punten opgemaakt als `<workspaceName>_"endpoint"_<FQDN of on-premises machine>` . Als de migratie tests agents bevatten die niet worden uitgevoerd in azure, moet u de agents inschakelen en opnieuw migreren.
   * De doel poort en het probing-interval worden verplaatst naar een test configuratie met de naam *TC_ \<testname>* en *TC_ \<testname> _AppThresholds*. Het protocol wordt ingesteld op basis van de poort waarden. De drempel waarden voor geslaagde pogingen en andere optionele eigenschappen blijven leeg.
* NPM is niet uitgeschakeld, dus de gemigreerde tests kunnen gegevens blijven verzenden naar de NetworkMonitoring en ConnectionMonitor_CL tabellen. Deze aanpak zorgt ervoor dat bestaande waarschuwingen en integraties op basis van het logboek niet worden beïnvloed.
* De zojuist gemaakte verbindings monitor is zichtbaar in de verbindings monitor.

Na de migratie, moet u het volgende doen:
* Schakel de tests in NPM hand matig uit. Totdat u dit doet, worden er nog steeds kosten in rekening gebracht. 
* Wanneer u NPM uitschakelt, moet u de waarschuwingen opnieuw maken in de tabel ConnectionMonitor_CL of metrische gegevens gebruiken. 
* Externe integraties migreren naar de ConnectionMonitor_CL tabel. Voor beelden van externe integraties zijn Dash boards in Power BI en Grafana en integraties met Security Information and Event Management-systemen (SIEM).


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over verbindings monitor:
* [Migreren van verbindings monitor (klassiek) naar verbindings monitor](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Verbindings monitor maken met behulp van de Azure Portal](./connection-monitor-create-using-portal.md)
