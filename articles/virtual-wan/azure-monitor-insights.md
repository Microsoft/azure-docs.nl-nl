---
title: Virtuele WAN bewaken met Azure Monitor Insights
description: In dit artikel leert u hoe u Azure Virtual WAN kunt bewaken met behulp van Azure Monitor Insights.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 6ead00a0979d81ef11ac81fb13a1abe31317691d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571326"
---
# <a name="azure-monitor-insights-for-virtual-wan"></a>Inzichten Azure Monitor voor virtuele WAN

[Azure monitor Insights](../azure-monitor/insights/network-insights-overview.md) voor Azure Virtual WAN biedt gebruikers en Opera tors de mogelijkheid om de status en status van een virtueel WAN weer te geven, gepresenteerd via een automatisch gedetecteerde topologische kaart. Met de resource status-en status-overlays op de kaart krijgt u een moment opname van de algemene status van het virtuele WAN. U kunt door resources op de kaart navigeren via één klik toegang tot de resource configuratie pagina's van de virtuele WAN-Portal.

Virtuele WAN-gegevens op resource niveau worden verzameld en weer gegeven via een vooraf verpakte virtuele WAN-metrieke werkmap. In de werkmap worden de metrische gegevens van virtuele WAN-, hub-, gateway-en verbindings niveaus weer gegeven. In dit artikel worden de stappen beschreven voor het gebruik van Azure Monitor Insights voor virtuele WAN om uw virtuele WAN-topologie en metrische gegevens op één plek weer te geven.

> [!NOTE]
> De menu optie **inzichten** bevindt zich in de virtuele WAN-portal onder **bewaking**. U kunt ook toegang krijgen tot de virtuele WAN-topologie en de werk blad Metrics met behulp van Azure Monitor voor netwerken. Zie [Azure monitor voor netwerken](../azure-monitor/insights/network-insights-overview.md)voor meer informatie. 
>

## <a name="before-you-begin"></a>Voordat u begint

Als u de stappen in dit artikel wilt uitvoeren, moet u een virtueel WAN hebben met een of meer hubs. Volg de stappen in deze artikelen om een virtueel WAN en een hub te maken:

* [Een virtueel WAN maken](virtual-wan-site-to-site-portal.md#openvwan)
* [Een hub maken](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>VWAN-topologie weer geven

Ga naar **Azure Portal**  >  **virtuele WAN**. Selecteer **inzichten (preview)** in het menu **monitor** in het linkerdeel venster. De weer gave **inzichten** wordt weer gegeven. Hierin wordt de virtuele WAN-afhankelijkheids kaart en de mini maal bewerkings **gegevens** op hoog niveau weer gegeven.

**Afbeelding 1: > Insights-menu bewaken**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="Scherm opname van de weer gave inzichten (preview)." lightbox="./media/azure-monitor-insights/monitor-menu.png":::

In de weer gave **inzichten** kunt u de automatisch gedetecteerde virtuele WAN-resources weer geven. Deze resources omvatten hubs, gateways, firewalls, verbindingen en spoke Virtual Networks, Nva's van derden en vertakkingen in een end-to-end virtuele WAN. Zie **afbeelding 2** voor een voor beeld.

De resource status en-status zijn kleur codes en overlappen op de resource pictogrammen in de kaart. Virtuele WAN-metrische gegevens op hoog niveau, zoals hub-capaciteiten en gateway gebruik, worden weer gegeven aan de rechter kant van het venster in een mini werkmap.

**Afbeelding 2: Insights-weer gave**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="Scherm opname van de weer gave inzichten." lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Afhankelijkheids weergave

De **afhankelijkheids** weergave voor virtuele WAN helpt u bij het visualiseren van de onderling verbonden weer gave van alle virtuele WAN-bronnen die breed zijn georganiseerd in een hub-en-spoke-architectuur.

**Afbeelding 3: VWAN-afhankelijkheids weergave**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Scherm afbeelding waarin de afhankelijkheids weergave wordt weer gegeven." lightbox="./media/azure-monitor-insights/dependency-map.png":::

In de **afhankelijkheids** weergave kaart worden de volgende resources weer gegeven als verbonden grafiek:

* Virtuele WAN-hubs in de verschillende Azure-regio's.
* Spoke-virtuele netwerken die rechtstreeks zijn verbonden met de hub.
* VPN-en Azure ExpressRoute Branch-sites en P2S-gebruikers die zijn verbonden met elke hub via hun respectievelijke ExpressRoute-, S2S-en P2S-verbindingen en virtuele netwerk gateways.
* Azure-firewalls (inclusief firewall-proxy's van derden) geïmplementeerd in een hub (beveiligde hub).
* Nva's (virtuele netwerk apparaten) die worden geïmplementeerd in spoke-virtuele netwerken.

De afhankelijkheids toewijzing bevat ook indirect verbonden virtuele netwerken (virtuele netwerken die zijn gekoppeld aan virtuele WAN-spoke-netwerken).

De afhankelijkheids kaart maakt eenvoudig navigatie naar de configuratie-instellingen van elke bron mogelijk. U kunt bijvoorbeeld de muis aanwijzer over de resource van de hub bewegen om de basis configuratie van de resource, zoals hub en hub-voor voegsel, weer te geven. Klik met de rechter muisknop om de Azure Portal-pagina van de hub-resource te openen.

**Afbeelding 4: navigeren naar resource-specifieke informatie**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="Scherm afbeelding die laat zien hoe u navigeert naar informatie die specifiek is voor de resource.":::

De zoek-en filter balk in de **afhankelijkheids** weergave biedt een eenvoudige manier om de grafiek te doorzoeken. Verschillende filters bieden hulp om uw zoek opdracht te beperken tot een specifiek pad en een specifieke status.

**Afbeelding 5: zoeken en filteren**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="Scherm afbeelding waarin de zoek-en filter balk wordt weer gegeven." lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Gedetailleerde metrische gegevens

U kunt **gedetailleerde metrische gegevens weer geven** selecteren om toegang te krijgen tot de pagina gedetailleerde **metrische gegevens** . De pagina **metrische gegevens** is een dash board dat vooraf is geconfigureerd met afzonderlijke tabbladen. Deze tabbladen bieden inzicht in de capaciteit, prestaties en het gebruik van uw virtuele WAN-resource op het niveau van de virtuele WAN-verbinding en op het niveau van de hub, en op de niveaus van afzonderlijke verbindingen.

**Afbeelding 6: gedetailleerd dash board voor metrische gegevens**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="Scherm opname van het dash board met gedetailleerde metrische gegevens." lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Volgende stappen

* Zie [metrische gegevens in azure monitor](../azure-monitor/essentials/data-platform-metrics.md)voor meer informatie.
* Zie [virtuele WAN-logboeken en metrische](logs-metrics.md)gegevens voor een volledige beschrijving van alle virtuele WAN-metrische gegevens.
