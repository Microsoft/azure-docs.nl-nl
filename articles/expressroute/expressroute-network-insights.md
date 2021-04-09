---
title: Azure ExpressRoute Insights met behulp van netwerk inzichten
description: Meer informatie over Azure ExpressRoute Insights met behulp van netwerk inzichten.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/23/2021
ms.author: duau
ms.openlocfilehash: 7033ea6a1ba6d85f9aa15e14bb9577b2439c59a8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105050489"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>Azure ExpressRoute Insights met behulp van netwerk inzichten

In dit artikel wordt uitgelegd hoe u met Network Insights uw ExpressRoute-metrische gegevens en configuraties op één plek kunt bekijken. Via netwerk inzichten kunt u topologische kaarten en status dashboards met belang rijke ExpressRoute-informatie weer geven zonder dat u extra instellingen hoeft te volt ooien.

:::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="Scherm afbeelding van de landings pagina van de ExpressRoute-monitor." lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>Functionele afhankelijkheden visualiseren

Als u deze oplossing wilt weer geven, gaat u naar de pagina *Azure monitor* , selecteert u *netwerken* en selecteert u vervolgens de *ExpressRoute-circuit* kaart. Selecteer vervolgens de knop topologie voor het circuit dat u wilt weer geven.

De weer gave functionele afhankelijkheid biedt een duidelijke afbeelding van uw ExpressRoute-installatie, met een overzicht van de relatie tussen verschillende ExpressRoute-onderdelen (peerings, verbindingen, gateways).

:::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="Scherm opname van topologie weergave voor netwerk inzichten." lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

Beweeg de muis aanwijzer over een onderdeel in de topologie kaart om configuratie-informatie weer te geven. Beweeg bijvoorbeeld de muis aanwijzer over een ExpressRoute-peering-onderdeel om details weer te geven, zoals circuit bandbreedte en Global Reach activering.

:::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="Scherm afbeelding van het aanwijzen van de topologie weergave resources." lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>Een gedetailleerd en vooraf geladen gegevens dashboard weer geven

Nadat u de topologie van uw ExpressRoute-instellingen hebt gecontroleerd met behulp van de functionele afhankelijkheids weergave, selecteert u **gedetailleerde metrische gegevens weer geven** om te navigeren naar de weer gave gedetailleerde metrische gegevens om de prestaties van uw circuit te begrijpen. Deze weer gave biedt een geordende lijst met gekoppelde resources en een uitgebreid dash board met belang rijke ExpressRoute-metrische gegevens.

De sectie **gekoppelde resources** bevat een lijst met de verbonden ExpressRoute-gateways en geconfigureerde peerings, die u kunt selecteren om naar de bijbehorende resource pagina te navigeren.

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="Scherm opname van gekoppelde resource op de pagina monitor.":::


De **sectie ExpressRoute metrische gegevens** bevat grafieken met belang rijke metrische gegevens over het circuit over **de beschik baarheid**, **door Voer**, **pakket drupt** en **Gateway gegevens**.

### <a name="availability"></a>Beschikbaarheid

Op het tabblad *Beschik baarheid* worden ARP-en BGP-Beschik baarheid bijgehouden, waarbij de gegevens voor beide circuits worden getekend als een volledige en een afzonderlijke verbinding (primair en secundair). 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="Scherm afbeelding van grafieken met beschikbaarheids metriek." lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>Doorvoer

Op dezelfde manier wordt op het tabblad *door Voer* de totale door Voer van binnenkomende en uitgaande verkeer voor het circuit in bits/seconde getekend. U kunt ook de door Voer voor afzonderlijke verbindingen en elk type geconfigureerde peering weer geven.

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="Scherm afbeelding van metrische gegevens voor door voer." lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>Pakkettleveringen

Het tabblad verloren gegane *pakketten* per seconde voor binnenkomend en uitgaand verkeer via het circuit wordt uitgezet. Dit tabblad biedt een eenvoudige manier om prestatie problemen te bewaken die zich kunnen voordoen als u regel matig de band breedte van het circuit nodig hebt of overschrijdt.

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="Scherm opname van grafieken van verwijderde pakketten." lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>Metrische gegevens van gateway

Ten slotte vullen we het tabblad Gateway gegevens in met de belangrijkste grafieken voor een geselecteerde ExpressRoute-gateway (in de sectie gekoppelde resources). Gebruik dit tabblad als u de verbinding met specifieke virtuele netwerken wilt bewaken.

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="Scherm opname van gateway-door Voer en CPU-metrische gegevens." lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>Volgende stappen

Configureer uw ExpressRoute-verbinding.
  
* Meer informatie over [Azure ExpressRoute](expressroute-introduction.md), [Network Insights](../azure-monitor/insights/network-insights-overview.md)en [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)
* [Een circuit maken en wijzigen](expressroute-howto-circuit-arm.md)
* [Een peeringconfiguratie maken en wijzigen](expressroute-howto-routing-arm.md)
* [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
* [Uw metrische gegevens aanpassen](expressroute-monitoring-metrics-alerts.md) en een [verbindings monitor](../network-watcher/connection-monitor-overview.md) maken
