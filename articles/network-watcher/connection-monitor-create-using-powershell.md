---
title: Een verbindings monitor maken-Power shell
titleSuffix: Azure Network Watcher
description: Meer informatie over het maken van een verbindings monitor met behulp van Power shell.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: 1d5f879ead35ef6d47b993ff833dc0b0595e3c6c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861915"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>Een verbindings monitor maken met behulp van Power shell

Meer informatie over het gebruik van de functie verbindings controle van Azure Network Watcher om de communicatie tussen uw resources te bewaken.


## <a name="before-you-begin"></a>Voordat u begint

In verbindings monitors die u maakt met verbindings beheer, kunt u on-premises machines en virtuele machines (Vm's) van Azure als bronnen toevoegen. Deze verbindings monitors kunnen ook de connectiviteit met eind punten bewaken. De eind punten kunnen zich op Azure of een andere URL of een ander IP-adres bevindt.

Een verbindings monitor bevat de volgende entiteiten:

* **Verbindingscontrole resource**: een regio-specifieke Azure-resource. Alle volgende entiteiten zijn eigenschappen van de bron van de verbindings monitor.
* **Eind punt**: een bron of doel die deel uitmaakt van connectiviteits controles. Voor beelden van eind punten zijn Azure-Vm's, on-premises agents, Url's en Ip's.
* **Test configuratie**: een protocol-specifieke configuratie voor een test. Op basis van het protocol dat u kiest, kunt u de poort, drempel waarden, test frequentie en andere para meters definiëren.
* **Test groep**: de groep die bron-eind punten, bestemmings eindpunten en test configuraties bevat. Een verbindings monitor kan meer dan één test groep bevatten.
* **Test**: de combi natie van een bron eindpunt, bestemmings eindpunt en test configuratie. Een test is het meest gedetailleerde niveau waarmee bewakings gegevens beschikbaar zijn. De bewakings gegevens omvatten het percentage controles dat is mislukt en de round-trip tijd (RTT).

    ![Diagram van een verbindings monitor, waarbij de relatie tussen test groepen en tests wordt gedefinieerd.](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>Stappen voor het maken van een verbindings monitor

Gebruik de volgende opdrachten om een verbindings monitor te maken met behulp van Power shell.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Beschrijving van eigenschappen

* **ConnectionMonitorName**: naam van de bron van de verbindings monitor.

* **Sub**: abonnements-id van het abonnement waarvoor u een verbindings monitor wilt maken.

* **NW**: Network Watcher bron-id waarin een verbindings monitor is gemaakt.

* **Locatie**: regio waarin een verbindings monitor is gemaakt.

* **Eindpunten**
    * **Naam**: unieke naam voor elk eind punt.
    * **Resource-id**: voor Azure-eind punten verwijst resource-id naar de Azure Resource Manager resource-id voor vm's. Voor niet-Azure-eind punten verwijst Resource-ID naar de Azure Resource Manager Resource-ID voor de Log Analytics-werk ruimte die is gekoppeld aan niet-Azure-agents.
    * **Adres**: alleen van toepassing als de resource-id niet is opgegeven of als de resource-id zich in de werk ruimte log Analytics bevindt. Als u geen Resource-ID gebruikt, kan dit de URL of het IP-adres van een openbaar eind punt zijn. Als deze wordt gebruikt met een Log Analytics Resource-ID, wordt hiermee verwezen naar de FQDN-naam van de bewakings agent.
    * **Filter**: voor niet-Azure-eind punten gebruikt u filters om bewakings agenten te selecteren in de log Analytics werk ruimte in de bron van de verbindings monitor. Als er geen filters zijn ingesteld, kunnen alle agents die deel uitmaken van de Log Analytics-werk ruimte worden gebruikt voor de bewaking.
        * **Type**: instellen als **Agent adres**.
        * **Adres**: ingesteld als de FQDN van uw on-premises agent.

* **Test groepen**
    * **Naam**: Geef een naam op voor de test groep.
    * **Bronnen**: Kies uit de eind punten die u eerder hebt gemaakt. Azure Network Watcher-extensie moet zijn geïnstalleerd op Azure-bron eindpunten. een Azure Log Analytics-agent moet zijn geïnstalleerd op bron-eind punten die niet op Azure zijn gebaseerd. Zie [bewakings agenten installeren](./connection-monitor-overview.md#install-monitoring-agents)voor informatie over het installeren van een agent voor uw bron.
    * **Doelen**: Kies uit de eind punten die u eerder hebt gemaakt. U kunt de verbinding met virtuele machines van Azure of een eind punt (een openbaar IP-adres, URL of FQDN) bewaken door ze als bestemming op te geven. In één test groep kunt u Azure-Vm's, Office 365 Url's, Dynamics 365-Url's en aangepaste eind punten toevoegen.
    * **Uitschakelen**: bewaking uitschakelen voor alle bronnen en doelen die door de test groep worden opgegeven.

* **Test configuraties**
    * **Naam**: Geef de test configuratie een naam.
    * **TestFrequencySec**: Geef op hoe vaak bronnen ping-bestemmingen hebben op het protocol en de poort die u hebt opgegeven. U kunt kiezen uit 30 seconden, 1 minuut, 5 minuten, 15 minuten of 30 minuten. Bronnen testen de connectiviteit met bestemmingen op basis van de waarde die u kiest. Als u bijvoorbeeld 30 seconden selecteert, controleren bronnen de connectiviteit met de bestemming ten minste één keer in een periode van dertig seconden.
    * **Protocol**: Kies TCP, ICMP, http of https. Afhankelijk van het protocol kunt u ook de volgende protocolspecifieke configuraties selecteren:
        * **preferHTTPS**: Geef op of HTTPS via http moet worden gebruikt.
        * **poort**: Geef de gewenste doel poort op.
        * **disableTraceRoute**: bronnen stoppen van het detecteren van topologie en hop-by-Hop RTT. Dit geldt voor test groepen met TCP of ICMP.
        * **methode**: Selecteer de HTTP-aanvraag methode (Get of post). Dit is van toepassing op test configuraties met HTTP.
        * **pad**: Geef de para meters voor het pad op om toe te voegen aan de URL.
        * **validStatusCodes**: Kies toepasselijke status codes. Als de antwoord code niet overeenkomt, wordt een diagnostisch bericht weer gegeven.
        * **RequestHeaders**: Geef de teken reeks voor aangepaste aanvraag headers op die aan het doel worden door gegeven.
    * **Drempel waarde voor geslaagde pogingen**: Stel drempel waarden in voor de volgende netwerk parameters:
        * **checksFailedPercent**: Stel het percentage controles in dat kan mislukken wanneer bronnen de connectiviteit met bestemmingen controleren met behulp van de criteria die u hebt opgegeven. Voor het TCP-of ICMP-protocol is het percentage mislukte controles mogelijk gelijk aan het percentage pakket verlies. Voor het HTTP-protocol vertegenwoordigt dit veld het percentage HTTP-aanvragen dat geen antwoord heeft ontvangen.
        * **roundTripTimeMs**: Stel in hoe lang bronnen kunnen duren om verbinding te maken met de bestemming via de test configuratie in milliseconden.

## <a name="scale-limits"></a>Schaal limieten

Verbindings monitors hebben de volgende schaal limieten:

* Maximum aantal verbindings monitors per abonnement per regio: 100
* Maximum aantal test groepen per verbindings monitor: 20
* Maximum aantal bronnen en bestemmingen per verbindings monitor: 100
* Maximum aantal test configuraties per verbindings monitor: 20

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het analyseren van bewakings gegevens en het instellen van waarschuwingen](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Meer informatie [over het vaststellen van problemen in uw netwerk](./connection-monitor-overview.md#diagnose-issues-in-your-network).