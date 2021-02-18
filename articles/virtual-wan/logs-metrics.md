---
title: Logboeken en metrische gegevens
titleSuffix: Azure Virtual WAN
description: Meer informatie over Azure Virtual WAN-logboeken en-metrische gegevens
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: a5bbcc4c07826a5093da3f51c709f49ceb231753
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596083"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Virtuele WAN-logboeken en-metrische gegevens van Azure

U kunt Azure Virtual WAN bewaken met behulp van Azure Monitor. Virtual WAN is een netwerk service die veel netwerk-, beveiligings-en routerings functionaliteit biedt om één operationele interface te bieden. Virtuele WAN-VPN-gateways, ExpressRoute-gateways en Azure Firewall hebben logboek registratie en metrische gegevens die via Azure Monitor kunnen worden weer gegeven.

In dit artikel worden metrische gegevens en diagnostische gegevens beschreven die beschikbaar zijn via de portal. Metrische gegevens zijn licht gewicht en kunnen bijna realtime-scenario's ondersteunen, waardoor ze nuttig zijn voor waarschuwingen en snelle detectie van problemen.

## <a name="monitoring-secured-hub-azure-firewall"></a>Bewaken van beveiligde hub (Azure Firewall) 

U kunt de beveiligde hub bewaken met Azure Firewall-Logboeken. U kunt ook activiteitenlogboeken gebruiken om bewerkingen in Azure Firewall-resources te controleren.

Als u ervoor hebt gekozen om uw virtuele hub te beveiligen met Azure Firewall, zijn relevante logboeken en metrische gegevens hier beschikbaar: [Azure firewall logboeken en metrische gegevens](../firewall/logs-and-metrics.md).

## <a name="metrics"></a>Metrische gegevens

Metrische gegevens in Azure Monitor zijn numerieke waarden die een aspect van een systeem op een bepaald moment beschrijven. Metrische gegevens worden elke minuut verzameld en zijn nuttig voor waarschuwingen omdat ze regel matig kunnen worden gesampled. Een waarschuwing kan snel worden geactiveerd met relatief eenvoudige logica.

### <a name="site-to-site-vpn-gateways"></a>Site-naar-site-VPN-gateways

De volgende metrische gegevens zijn beschikbaar voor site-naar-site VPN-gateways van Azure:

* **Band breedte van Gateway** – gemiddelde site-naar-site geaggregeerde band breedte van een gateway in bytes per seconde.
* **Tunnel bandbreedte** – gemiddelde band breedte van een tunnel in bytes per seconde.
* **Aantal bytes** in de tunnel: uitgaande bytes van een tunnel. 
* Uitstaande **tunnel pakketten** : aantal uitgaande pakketten van een tunnel. 
* **Tunnel-uitgangs ingang die niet overeenkomt met niet** -uitgaand pakket aantal uitgaande pakketten van Traffic selector komt niet overeen met een tunnel. 
* Binnenkomende bytes van de **tunnel** van een tunnel. 
* **Pakket met tunnel ingangen** : inkomend pakket aantal van een tunnel. 
* **Tunnel ingress komt niet overeen met niet** -binnenkomend pakket-aantal inkomende pakketten van de selectie van een tunnel die niet overeenkomt met de verkeers kiezer. 

### <a name="point-to-site-vpn-gateways"></a>Punt-naar-site-VPN-gateways

De volgende metrische gegevens zijn beschikbaar voor Azure Point-to-site VPN-gateways:

* **Gateway P2S-band breedte** – gemiddelde Point-to-site geaggregeerde band breedte van een gateway in bytes per seconde.
* **Aantal P2S-verbindingen** : punt-naar-site-verbindings telling van een gateway.

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute-gateways

De volgende metrische gegevens zijn beschikbaar voor Azure ExpressRoute-gateways:

* **BitsInPerSecond** : bits die Azure per seconde ontzeggen.
* **BitsOutPerSecond** – bits egressing Azure per seconde.

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Metrische gateway gegevens weer geven

De volgende stappen helpen u metrische gegevens te zoeken en weer te geven:

1. Navigeer in de portal naar de virtuele hub met de gateway.

2. Selecteer **VPN (site naar site)** om een site-naar-site-gateway te zoeken, **ExpressRoute** te zoeken naar een ExpressRoute-gateway of **gebruikers VPN (punt naar site)** om een punt-naar-site-gateway te vinden. Op de pagina ziet u de gateway gegevens. Kopieer deze gegevens. U kunt deze later gebruiken om diagnostische gegevens weer te geven met behulp van Azure Monitor.

3. Selecteer **Metrische gegevens**.

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="Scherm afbeelding toont een deel venster site naar site V P N met weer gave in Azure Monitor geselecteerd.":::

4. Op de pagina **metrische gegevens** kunt u de metrische gegevens bekijken die u interesseren.

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="Scherm opname van de pagina metrische gegevens waarop de gemarkeerde categorieën worden weer gegeven.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Diagnostische logboeken

### <a name="site-to-site-vpn-gateways"></a>Site-naar-site-VPN-gateways

De volgende diagnostische gegevens zijn beschikbaar voor Azure site-naar-site VPN-gateways:

* **Diagnostische logboeken** voor de gateway – specifieke diagnostische gegevens over de gateway, zoals de status, configuratie, service-updates en aanvullende diagnostische gegevens.
* **Diagnostische logboeken voor tunnels** : Dit zijn de logboeken met betrekking tot IPSec-tunnels, zoals verbinding maken en verbreken van gebeurtenissen voor een site-naar-site-IPSec-tunnel, onderhandelde sa's, redenen om de verbinding te verbreken, evenals aanvullende diagnoses.
* **Diagnostische logboeken voor routes** : Dit zijn logboeken die betrekking hebben op gebeurtenissen voor statische routes, BGP, route-updates en aanvullende diagnostische gegevens.
* **IKE Diagnostische logboeken** : IKE-specifieke diagnostische gegevens voor IPSec-verbindingen.

### <a name="point-to-site-vpn-gateways"></a>Punt-naar-site-VPN-gateways

De volgende diagnostische gegevens zijn beschikbaar voor Azure Point-to-site VPN-gateways:

* **Diagnostische logboeken** voor de gateway – specifieke diagnostische gegevens over de gateway, zoals status, configuratie, service-updates en andere diagnostische gegevens.
* **IKE Diagnostische logboeken** : IKE-specifieke diagnostische gegevens voor IPSec-verbindingen.
* **Diagnostische logboeken voor P2S** : Dit zijn gebruikers VPN (punt-naar-site) P2S configuratie-en client gebeurtenissen. Ze omvatten client verbinding maken/verbreken, adres toewijzing van VPN-client en andere diagnostische gegevens.

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>Diagnostische logboeken weergeven

De volgende stappen helpen u bij het zoeken en weer geven van diagnostische gegevens:

1. Navigeer in de portal naar uw virtuele WAN-resource. Selecteer in de sectie **overzicht** van de virtuele WAN-pagina in de Portal de optie **essentiële** items om de weer gave uit te vouwen en de informatie over de resource groep te verkrijgen. Kopieer de gegevens van de resource groep.

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="Scherm afbeelding met de sectie overzicht met een pijl die verwijst naar de knop kopiëren.":::

2. Navigeer in het gedeelte bewaking naar de resource groep. Selecteer **Diagnostische instellingen** en voer vervolgens de resource gegevens in. Dit zijn de resource gegevens die u hebt gekopieerd in stap 2 van de sectie [statistieken van gateway weer geven](#metrics-steps) eerder in dit artikel.

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="Scherm afbeelding met de sectie bewaking met een pijl die verwijst naar de vervolg keuzelijst resource.":::

3. Selecteer op de pagina resultaten **+ Diagnostische instelling toevoegen** en selecteer vervolgens een optie. U kunt ervoor kiezen om te verzenden naar Log Analytics, streamen naar een Event Hub of eenvoudigweg te archiveren naar een opslag account.

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="pagina metrische gegevens":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Voorbeeld query Log Analytics

De logboeken bevinden zich in **Azure log Analytics-werk ruimte**. U kunt een query instellen in Log Analytics. Het volgende voor beeld bevat een query voor het verkrijgen van site-naar-site-route diagnostiek.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

Vervang de onderstaande waarden na de **= =**, indien nodig.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog"
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>Activiteiten logboeken

Vermeldingen in het **activiteiten logboek** worden standaard verzameld en kunnen worden weer gegeven in de Azure Portal. U kunt Azure-activiteiten Logboeken (voorheen bekend als *operationele logboeken* en *audit logboeken*) gebruiken om alle bewerkingen weer te geven die zijn verzonden naar uw Azure-abonnement.

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: Azure firewall logboeken controleren](../firewall/firewall-diagnostics.md)voor meer informatie over het bewaken van Azure firewall logboeken en metrische gegevens.
* Zie [metrische gegevens in azure monitor](../azure-monitor/essentials/data-platform-metrics.md)voor meer informatie over metrische gegevens in azure monitor.
