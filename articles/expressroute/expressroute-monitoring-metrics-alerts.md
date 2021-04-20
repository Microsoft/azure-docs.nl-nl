---
title: 'Azure ExpressRoute: bewaking, metrische gegevens en waarschuwingen'
description: Meer informatie Azure ExpressRoute bewaking, metrische gegevens en waarschuwingen met behulp van Azure Monitor, de enige winkel voor alle metrische gegevens, waarschuwingen en diagnostische logboeken in Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/07/2021
ms.author: duau
ms.openlocfilehash: 44ddf54aac61ab00009e7e2cc820b38074c5e8c3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725778"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Bewaking, metrische gegevens en waarschuwingen voor ExpressRoute

Dit artikel helpt u inzicht te krijgen in expressRoute-bewaking, metrische gegevens en waarschuwingen met behulp van Azure Monitor. Azure Monitor is één winkel voor alle metrische gegevens, waarschuwingen en diagnostische logboeken in heel Azure.
 
>[!NOTE]
>Het **gebruik van klassieke metrische** gegevens wordt niet aanbevolen.
>

## <a name="expressroute-metrics"></a>Metrische ExpressRoute-gegevens

Als u **metrische gegevens** wilt weergeven, gaat *u Azure Monitor* pagina en selecteert u *Metrische gegevens.* Als u **metrische ExpressRoute-gegevens** wilt weergeven, filtert u op *ExpressRoute-circuits van het resourcetype.* Als u de **Global Reach** wilt weergeven, filtert u op *ExpressRoute-circuits* van het resourcetype en selecteert u een ExpressRoute-circuitresource Global Reach ingeschakeld. Als u de **metrische ExpressRoute Direct** wilt weergeven, filtert u Resourcetype op *ExpressRoute-poorten.* 

Zodra een metriek is geselecteerd, wordt de standaardaggregatie toegepast. U kunt eventueel splitsen toepassen, zodat de metrische gegevens met verschillende dimensies worden weer gegeven.

### <a name="aggregation-types"></a>Aggregatietypen:

Metrics Explorer ondersteunt SUM, MAX, MIN, AVG en COUNT als [aggregatietypen](../azure-monitor/essentials/metrics-charts.md#aggregation). Gebruik het aanbevolen aggregatietype bij het controleren van de inzichten voor elke ExpressRoute-metrische gegevens.

* Som: de som van alle waarden die zijn vastgelegd tijdens het aggregatie-interval. 
* Aantal: het aantal metingen dat is vastgelegd tijdens het aggregatie-interval. 
* Gemiddelde: het gemiddelde van de metrische waarden die zijn vastgelegd tijdens het aggregatie-interval. 
* Min: de kleinste waarde die is vastgelegd tijdens het aggregatie-interval. 
* Max: de grootste waarde die is vastgelegd tijdens het aggregatie-interval. 

### <a name="available-metrics"></a>Beschikbare metrische gegevens

|**Meting**|**Categorie**|**Dimensie(s)**|**Functie(en)**|
| --- | --- | --- | --- |
|ARP-beschikbaarheid|Beschikbaarheid|<ui><li>Peer (primaire/secundaire ExpressRoute-router)</ui></li><ui><li> Peeringtype (privé/openbaar/Microsoft)</ui></li>|ExpressRoute|
|BGP-beschikbaarheid|Beschikbaarheid|<ui><li> Peer (primaire/secundaire ExpressRoute-router)</ui></li><ui><li> Peeringtype</ui></li>|ExpressRoute|
|BitsInPerSecond|Verkeer|<ui><li> Peeringtype (ExpressRoute)</ui></li><ui><li>Koppeling (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>ExpressRoute-gatewayverbinding</ui></li>|
|BitsOutPerSecond|Verkeer| <ui><li>Peeringtype (ExpressRoute)</ui></li><ui><li> Koppeling (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>ExpressRoute-gatewayverbinding</ui></li>|
|CPU-gebruik|Prestaties| <ui><li>Exemplaar</ui></li>|ExpressRoute Virtual Network Gateway|
|Pakketten per seconde|Prestaties| <ui><li>Exemplaar</ui></li>|ExpressRoute Virtual Network-gateway|
|Aantal routes dat naar peer is geadverteerd |Beschikbaarheid| <ui><li>Exemplaar</ui></li>|ExpressRoute Virtual Network-gateway|
|Aantal routes dat is geleerd van peer |Beschikbaarheid| <ui><li>Exemplaar</ui></li>|ExpressRoute Virtual Network-gateway|
|Frequentie van routes wijzigen |Beschikbaarheid| <ui><li>Exemplaar</ui></li>|ExpressRoute Virtual Network-gateway|
|Aantal VM's in de Virtual Network |Beschikbaarheid| N.v.t. |ExpressRoute Virtual Network-gateway|
|GlobalReachBitsInPerSecond|Verkeer|<ui><li>Peered Circuit Skey (servicesleutel)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Verkeer|<ui><li>Peered Circuit Skey (servicesleutel)</ui></li>|Global Reach|
|AdminState|Fysieke connectiviteit|Koppeling|ExpressRoute Direct|
|LineProtocol|Fysieke connectiviteit|Koppeling|ExpressRoute Direct|
|RxLightLevel|Fysieke connectiviteit|<ui><li>Link</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fysieke connectiviteit|<ui><li>Link</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Het *gebruik van GlobalGlobalReachBitsInPerSecond* en *GlobalGlobalReachBitsOutPerSecond* is alleen zichtbaar als er ten minste één Global Reach tot stand is gebracht.
>

## <a name="circuits-metrics"></a>Metrische gegevens over circuits

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits in en uit - Metrische gegevens voor alle peerings

Aggregatietype: *Gemiddeld*

U kunt metrische gegevens weergeven voor alle peerings in een bepaald ExpressRoute-circuit.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="Metrische circuitgegevens":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits in en uit - metrische gegevens per peering

Aggregatietype: *Gemiddeld*

U kunt metrische gegevens voor privé-, openbare en Microsoft-peering weergeven in bits per seconde.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="metrische gegevens per peering":::

### <a name="bgp-availability---split-by-peer"></a>BGP-beschikbaarheid : splitsen op peer  

Aggregatietype: *Gemiddeld*

U kunt bijna realtime beschikbaarheid van BGP (Layer-3-connectiviteit) bekijken voor peerings en peers (primaire en secundaire ExpressRoute-routers). Dit dashboard toont de status van de primaire BGP-sessie is voor privé-peering en de status van de tweede BGP-sessie is niet voor privé-peering. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="BGP-beschikbaarheid per peer":::

### <a name="arp-availability---split-by-peering"></a>ARP-beschikbaarheid : splitsen op peering  

Aggregatietype: *Gemiddeld*

U kunt bijna realtime beschikbaarheid van [ARP](./expressroute-troubleshooting-arp-resource-manager.md) (Layer-3-connectiviteit) bekijken voor peerings en peers (primaire en secundaire ExpressRoute-routers). In dit dashboard ziet u dat de ARP-sessiestatus van privépeering voor beide peers is, maar niet voor Microsoft-peering voor beide peers. De standaardaggregatie (gemiddeld) is gebruikt voor beide peers.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="ARP-beschikbaarheid per peer":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct Metrische gegevens

### <a name="admin-state---split-by-link"></a>Admin State - Splitsen op koppeling

Aggregatietype: *Gemiddeld*

U kunt de beheerderstoestand weergeven voor elke koppeling van het ExpressRoute Direct poortpaar. De status Beheerder geeft aan of de fysieke poort is in- of uitgeschakeld. Deze status is vereist om verkeer door te geven via ExpressRoute Direct verbinding.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="Status van directe ER-beheerder":::

### <a name="bits-in-per-second---split-by-link"></a>Bits in per seconde - Splitsen op koppeling

Aggregatietype: *Gemiddeld*

U kunt de bits per seconde weergeven via beide koppelingen van ExpressRoute Direct poortpaar. Controleer dit dashboard om de binnenkomende bandbreedte voor beide koppelingen te vergelijken.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER Direct-bits in per seconde":::

### <a name="bits-out-per-second---split-by-link"></a>Bits Out Per Second - Splitsen op koppeling

Aggregatietype: *Gemiddeld*

U kunt de bits ook per seconde weergeven via beide koppelingen van ExpressRoute Direct poortpaar. Controleer dit dashboard om de uitgaande bandbreedte voor beide koppelingen te vergelijken.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direct bits out per seconde":::

### <a name="line-protocol---split-by-link"></a>Regelprotocol - Splitsen op koppeling

Aggregatietype: *Gemiddeld*

U kunt het regelprotocol weergeven via elke koppeling van ExpressRoute Direct poortpaar. Het regelprotocol geeft aan of de fysieke koppeling actief is en wordt uitgevoerd via ExpressRoute Direct. Controleer dit dashboard en stel waarschuwingen in om te weten wanneer de fysieke verbinding is uitgegaan.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct Line-protocol":::

### <a name="rx-light-level---split-by-link"></a>Rx Light Level - Splitsen op koppeling

Aggregatietype: *Gemiddeld*

U kunt het Rx-lichtniveau (het lichte niveau dat de ExpressRoute Direct poort **ontvangt)** voor elke poort weergeven. Gezonde Rx-lichtniveaus vallen doorgaans binnen een bereik van -10 dBm tot 0 dBm. Stel waarschuwingen in om een melding te ontvangen als het Rx-lichtniveau buiten het gezonde bereik valt.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="Lichtniveau van ER Direct Line Rx":::

### <a name="tx-light-level---split-by-link"></a>Tx Light Level - Splitsen op koppeling

Aggregatietype: *Gemiddeld*

U kunt het lichtniveau van Tx (het lichtniveau dat door de ExpressRoute Direct poort wordt **verzonden)** voor elke poort weergeven. Gezonde Tx-lichtniveaus vallen doorgaans binnen een bereik van -10 dBm tot 0 dBm. Stel waarschuwingen in om een melding te ontvangen als het Tx-lichtniveau buiten het gezonde bereik valt.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="Lichtniveau van ER Direct Line Tx":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Metrische gegevens Virtual Network ExpressRoute-gateway

Aggregatietype: *Gemiddeld*

Wanneer u een ExpressRoute-gateway implementeert, beheert Azure de berekening en functies van uw gateway. Er zijn zes metrische gegevens van de gateway beschikbaar om de prestaties van uw gateway beter te begrijpen:

* CPU-gebruik
* Pakketten per seconde
* Aantal routes dat wordt geadverteerd aan peers
* Aantal routes dat is geleerd van peers
* Frequentie van routes gewijzigd
* Aantal VM's in het virtuele netwerk  

Het wordt ten zeerste aanbevolen om waarschuwingen in te stellen voor elk van deze metrische gegevens, zodat u weet wanneer er prestatieproblemen kunnen optreden in uw gateway.

### <a name="cpu-utilization---split-instance"></a>CPU-gebruik - Split Instance

Aggregatietype: *Gemiddeld*

U kunt het CPU-gebruik van elk gateway-exemplaar bekijken. Het CPU-gebruik kan kort pieken tijdens routineonderhoud van de host, maar een hoog CPU-gebruik kan erop wijzen dat uw gateway een prestatieknelpunt bereikt. Het vergroten van de grootte van de ExpressRoute-gateway kan dit probleem oplossen. Stel een waarschuwing in voor hoe vaak het CPU-gebruik een bepaalde drempelwaarde overschrijdt.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Schermopname van CPU-gebruik - metrische gegevens splitsen.":::

### <a name="packets-per-second---split-by-instance"></a>Pakketten per seconde: gesplitst per exemplaar

Aggregatietype: *Gemiddeld*

Met deze metrische gegevens wordt het aantal binnenkomende pakketten dat via de ExpressRoute-gateway wordt doorgestuurd, vastleggen. U kunt hier een consistente gegevensstroom verwachten als uw gateway verkeer van uw on-premises netwerk ontvangt. Stel een waarschuwing in voor wanneer het aantal pakketten per seconde onder een drempelwaarde komt die aangeeft dat uw gateway geen verkeer meer ontvangt.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Schermopname van pakketten per seconde: metrische gegevens splitsen.":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Aantal routes dat wordt geadverteerd naar peer - Gesplitst per exemplaar

Aggregatietype: *Aantal*

Deze metrische gegevens zijn het aantal routes dat de ExpressRoute-gateway naar het circuit adverteert. De adresruimten kunnen virtuele netwerken bevatten die zijn verbonden met behulp van VNet-peering en die gebruikmaken van een externe ExpressRoute-gateway. U kunt ervan uit gaan dat het aantal routes consistent blijft, tenzij de adresruimten van het virtuele netwerk regelmatig worden gewijzigd. Stel een waarschuwing in voor wanneer het aantal geadverteerde routes onder de drempelwaarde komt voor het aantal virtuele-netwerkadresruimten dat u weet.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Schermopname van het aantal routes dat naar peer wordt geadverteerd.":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Aantal routes dat is geleerd van peer - Splitsen per exemplaar

Aggregatietype: *Max.*

Deze metriek toont het aantal routes dat de ExpressRoute-gateway leert van peers die zijn verbonden met het ExpressRoute-circuit. Deze routes kunnen afkomstig zijn van een ander virtueel netwerk dat is verbonden met hetzelfde circuit of afkomstig zijn van on-premises. Stel een waarschuwing in voor wanneer het aantal geleerde routes onder een bepaalde drempelwaarde komt. Dit kan erop wijzen dat de gateway een prestatieprobleem heeft of dat externe peers geen routes meer adverteren naar het ExpressRoute-circuit. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Schermopname van het aantal routes dat is geleerd van peer.":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Frequentie van routes wijzigen - Splitsen per exemplaar

Aggregatietype: *Som*

Deze metrische gegevens tonen de frequentie van routes die worden geleerd van of worden geadverteerd aan externe peers. Onderzoek eerst uw on-premises apparaten om te begrijpen waarom het netwerk zo vaak verandert. Een hoge frequentie in routes kan duiden op een prestatieprobleem op de ExpressRoute-gateway, waarbij het probleem kan worden opgelost door de gateway-SKU omhoog te schalen. Stel een waarschuwing in voor een frequentiedrempelwaarde waarmee u rekening moet houden wanneer uw ExpressRoute-gateway abnormale routewijzigingen ziet.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Schermopname van de frequentie van routes die metriek zijn gewijzigd.":::

### <a name="number-of-vms-in-the-virtual-network"></a>Aantal VM's in de Virtual Network

Aggregatietype: *Max.*

Deze metrische gegevens geven het aantal virtuele machines weer dat gebruik maakt van de ExpressRoute-gateway. Het aantal virtuele machines kan bestaan uit VM's van virtuele peernetwerken die gebruikmaken van dezelfde ExpressRoute-gateway. Stel een waarschuwing in voor deze metrische gegevens als het aantal VM's boven een bepaalde drempelwaarde komt die van invloed kan zijn op de prestaties van de gateway. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Schermopname van het aantal virtuele machines in de metrische gegevens van het virtuele netwerk.":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute-gatewayverbindingen in bits/seconden

Aggregatietype: *Gemiddeld*

Deze metriek toont het bandbreedtegebruik voor een specifieke verbinding met een ExpressRoute-circuit.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Schermopname van de metrische gegevens over bandbreedtegebruik van gatewayverbindingen.":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Waarschuwingen voor ExpressRoute-gatewayverbindingen

1. Als u waarschuwingen wilt configureren, gaat **u Azure Monitor** en selecteert u **vervolgens Waarschuwingen.**

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="waarschuwingen":::
2. Selecteer **+Doel selecteren en** selecteer de expressRoute-gatewayverbindingsresource.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="Doel":::
3. De details van de waarschuwing definiëren.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="actiegroep":::
4. De actiegroep definiëren en toevoegen.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="actiegroep toevoegen":::

## <a name="alerts-based-on-each-peering"></a>Waarschuwingen op basis van elke peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="elke peering":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Waarschuwingen configureren voor activiteitenlogboeken op circuits

In de **waarschuwingscriteria** kunt u **Activiteitenlogboek selecteren** als signaaltype en het signaal selecteren.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="activiteitenlogboeken":::

## <a name="more-metrics-in-log-analytics"></a>Meer metrische gegevens in Log Analytics

U kunt ook metrische ExpressRoute-gegevens weergeven door naar uw ExpressRoute-circuitresource te gaan en het tabblad *Logboeken te* selecteren. Voor alle metrische gegevens die u opvraagt, bevat de uitvoer de onderstaande kolommen.

|**Kolom**|**Type**|**Beschrijving**|
| --- | --- | --- |
|TimeGrain|tekenreeks|PT1M (metrische waarden worden elke minuut pushen)|
|Count|werkelijk|Meestal gelijk aan 2 (elke MSEE pusht elke minuut één metrische waarde)|
|Minimum|werkelijk|Het minimum van de twee metrische waarden die door de twee MDE's worden pushen|
|Maximum|werkelijk|Het maximum van de twee metrische waarden die door de twee MDE's worden pushen|
|Gemiddeld|werkelijk|Gelijk aan (minimum + maximum)/2|
|Totaal|werkelijk|De som van de twee metrische waarden van beide MSEE's (de belangrijkste waarde waar u zich op moet richten voor de metrische query's)|
  
## <a name="next-steps"></a>Volgende stappen

Configureer uw ExpressRoute-verbinding.
  
* [Een circuit maken en wijzigen](expressroute-howto-circuit-arm.md)
* [Een peeringconfiguratie maken en wijzigen](expressroute-howto-routing-arm.md)
* [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
