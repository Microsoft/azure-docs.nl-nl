---
title: Migreren naar Verbindingsmonitor van Netwerkprestatiemeter
titleSuffix: Azure Network Watcher
description: Meer informatie over migreren naar Verbindingsmonitor van Netwerkprestatiemeter.
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
ms.openlocfilehash: be12a9054fd67b243530ff671c10fa53acafc308
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366348"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migreren naar Verbindingsmonitor van Netwerkprestatiemeter

> [!IMPORTANT]
> Vanaf 1 juli 2021 kunt u geen nieuwe tests meer toevoegen aan een bestaande werkruimte of een nieuwe werkruimte inschakelen met Netwerkprestatiemeter. U kunt de tests die vóór 1 juli 2021 zijn gemaakt, blijven gebruiken. Migreert uw tests vóór 29 februari 2024 van Netwerkprestatiemeter naar de nieuwe Verbindingsmonitor in Azure Network Watcher om serviceonderbrekingen voor uw huidige workloads te minimaliseren.

U kunt tests migreren van Netwerkprestatiemeter (NPM) naar nieuwe, verbeterde Verbindingsmonitor met één klik en zonder downtime. Zie voor meer informatie over de voordelen [Verbindingsmonitor.](./connection-monitor-overview.md)


## <a name="key-points-to-note"></a>Belangrijke punten om op te merken

De migratie helpt bij het produceren van de volgende resultaten:

* On-premises agents en firewallinstellingen werken zoals ze zijn. Er zijn geen wijzigingen vereist. Log Analytics-agents die zijn geïnstalleerd op virtuele Azure-machines, moeten worden vervangen door [de Network Watcher extensie](../virtual-machines/extensions/network-watcher-windows.md).
* Bestaande tests worden aan de Verbindingsmonitor > testgroep > testindeling. Door Bewerken **te selecteren,** kunt u de eigenschappen van de nieuwe Verbindingsmonitor weergeven en wijzigen, een sjabloon downloaden om wijzigingen aan te brengen en de sjabloon verzenden via Azure Resource Manager.
* Agents verzenden gegevens naar zowel de Log Analytics-werkruimte als de metrische gegevens.
* Gegevensbewaking:
   * **Gegevens in Log Analytics:** vóór de migratie blijven de gegevens in de werkruimte waarin NPM is geconfigureerd in de tabel NetworkMonitoring. Na de migratie gaan de gegevens naar de tabel NetworkMonitoring, de tabel NWConnectionMonitorTestResult en de tabel NWConnectionMonitorPathResult in dezelfde werkruimte. Nadat de tests in NPM zijn uitgeschakeld, worden de gegevens alleen opgeslagen in de tabel NWConnectionMonitorTestResult en de tabel NWConnectionMonitorPathResult.
   * **Waarschuwingen, dashboards en integraties** op basis van logboeken: u moet de query's handmatig bewerken op basis van de nieuwe tabel NWConnectionMonitorTestResult en de tabel NWConnectionMonitorPathResult. Zie Bewaking van netwerkconnectiviteit met Verbindingsmonitor als u de waarschuwingen in metrische [gegevens opnieuw wilt Verbindingsmonitor.](./connection-monitor-overview.md#metrics-in-azure-monitor)
* Voor ExpressRoute-bewaking:
    * **End-to-end verlies** en latentie: Verbindingsmonitor zorgt voor stroom en is eenvoudiger dan NPM, omdat gebruikers niet hoeven te configureren welke circuits en peerings moeten worden bewaakt. Circuits in het pad worden automatisch ontdekt, gegevens zijn beschikbaar in metrische gegevens (sneller dan LA waar NPM de resultaten heeft opgeslagen). Topologie werkt ook.
    * **Bandbreedtemetingen:** Met de introductie van bandbreedtegerelateerde metrische gegevens was de op logboekanalyse gebaseerde benadering van NPM niet effectief in bandbreedtebewaking voor ExpressRoute-klanten. Deze mogelijkheid is nu niet beschikbaar in Verbindingsmonitor.
    
## <a name="prerequisites"></a>Vereisten

* Zorg ervoor Network Watcher is ingeschakeld in uw abonnement en de regio van de Log Analytics-werkruimte. Als u dit niet hebt gedaan, ziet u een foutmelding met de tekst 'Voordat u probeert te migreren, moet u de Network Watcher-extensie inschakelen in het selectieabonnement en de locatie van de geselecteerde LA-werkruimte.'
* Als een Azure-VM die tot een andere regio/ander abonnement behoort dan die van de Log Analytics-werkruimte wordt gebruikt als eindpunt, moet u ervoor zorgen dat Network Watcher is ingeschakeld voor dat abonnement en die regio.   
* Virtuele Azure-machines met Log Analytics-agents moeten zijn ingeschakeld met de Network Watcher extensie.

## <a name="migrate-the-tests"></a>De tests migreren

Ga als volgt te werk om de tests Netwerkprestatiemeter naar Verbindingsmonitor migreren:

1. Selecteer Network Watcher **in** Verbindingsmonitor en selecteer vervolgens het tabblad Tests migreren **vanuit NPM.** 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Tests migreren van Netwerkprestatiemeter naar Verbindingsmonitor" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. Selecteer in de vervolgkeuzelijsten uw abonnement en werkruimte en selecteer vervolgens de NPM-functie die u wilt migreren. 
1. Selecteer **Importeren om** de tests te migreren.
* Als NPM niet is ingeschakeld in de werkruimte, ziet u de foutmelding 'Er is geen geldige NPM-configuratie gevonden'. 
* Als er geen tests bestaan in de functie die u in stap 2 hebt gekozen, wordt de foutmelding 'Werkruimte geselecteerd heeft geen <feature> configuratie' weergegeven.
* Als er geen geldige tests zijn, ziet u de foutmelding 'Werkruimte geselecteerd heeft geen geldige tests'
* Uw tests bevatten mogelijk agents die niet meer actief zijn, maar in het verleden actief waren. Er wordt een foutbericht weergegeven met de tekst 'Enkele tests bevatten agents die niet meer actief zijn. Lijst met inactieve agents - {0} . Deze agents worden mogelijk uitgevoerd in het verleden, maar worden afgesloten/niet meer uitgevoerd. Agents inschakelen en migreren naar Verbindingsmonitor. Klik op Doorgaan om de tests te migreren die geen agents bevatten die niet actief zijn.

Nadat de migratie is gestart, worden de volgende wijzigingen doorgevoerd: 
* Er wordt een nieuwe verbindingsmonitorresource gemaakt.
   * Er wordt één verbindingsmonitor per regio en abonnement gemaakt. Voor tests met on-premises agents is de naam van de nieuwe verbindingsmonitor opgemaakt als `<workspaceName>_"workspace_region_name"` . Voor tests met Azure-agents is de naam van de nieuwe verbindingsmonitor opgemaakt als `<workspaceName>_<Azure_region_name>` .
   * Bewakingsgegevens worden nu opgeslagen in dezelfde Log Analytics-werkruimte waarin NPM is ingeschakeld, in nieuwe tabellen met de naam NWConnectionMonitorTestResult-tabel en NWConnectionMonitorPathResult-tabel. 
   * De naam van de test wordt als de naam van de testgroep overgedragen. De beschrijving van de test wordt niet gemigreerd.
   * Bron- en doel-eindpunten worden gemaakt en gebruikt in de nieuwe testgroep. Voor on-premises agents zijn de eindpunten opgemaakt als `<workspaceName>_<FQDN of on-premises machine>` . De beschrijving van de agent wordt niet gemigreerd.
   * Doelpoort en testinterval worden verplaatst naar een testconfiguratie met de naam `TC_<protocol>_<port>` en `TC_<protocol>_<port>_AppThresholds` . Het protocol wordt ingesteld op basis van de poortwaarden. Voor ICMP hebben de testconfiguraties de naam `TC_<protocol>` en `TC_<protocol>_AppThresholds` . Drempelwaarden voor succes en andere optionele eigenschappen, indien ingesteld, worden gemigreerd, anders worden leeg gelaten.
   * Als de migratietests agents bevatten die niet worden uitgevoerd, moet u de agents inschakelen en opnieuw migreren.
* NPM is niet uitgeschakeld, dus de gemigreerde tests kunnen gegevens blijven verzenden naar de tabel NetworkMonitoring, de tabel NWConnectionMonitorTestResult en de tabel NWConnectionMonitorPathResult. Deze aanpak zorgt ervoor dat bestaande waarschuwingen en integraties op basis van logboeken niet worden beïnvloed.
* De zojuist gemaakte verbindingsmonitor is zichtbaar in Verbindingsmonitor.

Zorg ervoor dat u na de migratie het volgende moet doen:
* Schakel de tests in NPM handmatig uit. Totdat u dit doet, worden er nog steeds kosten voor in rekening gebracht. 
* Terwijl u NPM uitstuurt, maakt u de waarschuwingen opnieuw in de tabellen NWConnectionMonitorTestResult en NWConnectionMonitorPathResult of gebruikt u metrische gegevens. 
* Migreert alle externe integraties naar de tabellen NWConnectionMonitorTestResult en NWConnectionMonitorPathResult. Voorbeelden van externe integraties zijn dashboards in Power BI en Grafana en integraties met Security Information and Event Management (SIEM)-systemen.


## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Verbindingsmonitor, zie:
* [Migreren van Verbindingsmonitor (klassiek) naar Verbindingsmonitor](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Maak Verbindingsmonitor met behulp van de Azure Portal](./connection-monitor-create-using-portal.md)
