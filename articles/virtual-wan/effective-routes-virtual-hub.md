---
title: 'Efficiënte routes van een virtuele hub weer geven: Azure Virtual WAN | Microsoft Docs'
description: Efficiënte routes weer geven voor een virtuele hub in azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ad487aa7492abcf02988c78ccfa2ba7cd4798249
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983647"
---
# <a name="view-virtual-hub-effective-routes"></a>Efficiënte routes voor virtuele hubs weergeven

U kunt alle routes van uw virtuele WAN-hub weer geven in de Azure Portal. Dit artikel begeleidt u stapsgewijs door de stappen voor het weer geven van efficiënte routes. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>Verbindingen of route tabellen selecteren

1. Navigeer naar uw virtuele hub en selecteer vervolgens **route ring**. Op de pagina route ring selecteert u de **juiste routes**.
1. In de vervolg keuzelijst kunt u **route tabel** selecteren. Als u de optie route tabel niet ziet, betekent dit dat u geen aangepaste of standaard route tabel hebt ingesteld in deze virtuele hub.

## <a name="view-output"></a><a name="output"></a>Uitvoer weer geven

De pagina-uitvoer bevat de volgende velden:

* **Voor voegsel**: adres voorvoegsel bekend bij de huidige entiteit (informatie van de virtuele hub-router)
* **Type volgende hop**: kan Virtual Network verbinding, VPN_S2S_Gateway, ExpressRouteGateway, externe Hub of Azure firewall zijn.
* **Volgende hop**: dit is de koppeling naar de resource-id van de volgende hop, of u ziet gewoon on-link om de huidige hub te impliceren.
* **Oorsprong**: bron-id van de routerings bron.
* **Als pad**: BGP-kenmerk as (autonoom systeem) een lijst met alle as-nummers die moeten worden gepasseerd om de locatie te bereiken waar het voor voegsel waaraan het pad is gekoppeld, wordt geadverteerd.

### <a name="example"></a><a name="example"></a>Hierbij

De waarden in de volgende voorbeeld tabel impliceren dat de virtuele-hub-verbinding of route tabel de route van 10.2.0.0/24 (een vertakkings voorvoegsel) heeft geleerd. De route is geleerd door het type volgende hop van de **VPN-** VPN_S2S_Gateway met de **volgende hop** VPN gateway resource-id. **Route oorsprong** wijst naar de resource-id van de oorspronkelijke VPN-gateway/route tabel/-verbinding. **As Path** geeft het as-pad voor de vertakking aan.

Gebruik de schuif balk aan de onderkant van de tabel om het "AS-pad" weer te geven.

| **Voorvoegsel** |  **Volgend hoptype** | **Volgende hop** |  **Route oorsprong** |**ALS pad** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |/Subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw|/Subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20.000|

**Tot**

* Als u 0.0.0.0/0 ziet in de uitvoer van de **meest efficiënte routes** , betekent dit dat de route bestaat in een van de route tabellen. Als deze route echter is ingesteld voor Internet, is er een extra vlag **"enableInternetSecurity": True** is vereist voor de verbinding. In de actieve route op de VM-NIC wordt de route niet weer gegeven als de vlag ' enableInternetSecurity ' voor de verbinding ' false ' is.

* Het veld **standaard route door geven** wordt weer gegeven in de Azure Virtual WAN-Portal wanneer u een virtuele netwerk verbinding, een VPN-verbinding of een ExpressRoute-verbinding bewerkt. Dit veld geeft de **enableInternetSecurity** -vlag aan, die altijd standaard is ingesteld op ' false ' voor ExpressRoute-en VPN-verbindingen, maar ' True ' voor virtuele netwerk verbindingen.

* Bij het weer geven van efficiënte routes op een VM-NIC wordt de volgende hop als ' Virtual Network gateway ' weer gegeven, die de virtuele hub-router impliceert wanneer de VM zich in een spoke bevindt die is verbonden met een virtuele WAN-hub.

* Het weer geven van de actieve routes voor een virtuele-hub-route tabel wordt alleen ingevuld als er ten minste één type verbinding (VPN/er/VNET) is verbonden met de virtuele hub.

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over Virtual WAN het [Overzicht van Virtual WAN](virtual-wan-about.md).
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
