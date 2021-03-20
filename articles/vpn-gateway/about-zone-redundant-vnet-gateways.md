---
title: Over zone-redundante virtuele netwerk gateways in Azure-beschikbaarheidszones
description: Implementeer VPN-en ExpressRoute-gateways in Azure-beschikbaarheidszones om tolerantie, schaal baarheid en hogere Beschik baarheid van virtuele netwerk gateways te brengen.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 2030469262baf406635fd170af384e154fec6ae6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89401109"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Over zone-redundante virtuele netwerk gateways in Azure-beschikbaarheidszones

U kunt VPN-en ExpressRoute-gateways implementeren in [Azure-beschikbaarheidszones](../availability-zones/az-overview.md). Dit zorgt in een virtuele netwerkgateway voor tolerantie, schaalbaarheid en hoge beschikbaarheid. Gateways fysiek en logisch implementeren in Azure-beschikbaarheidszones scheidt gateways binnen een regio, terwijl uw on-premises netwerkconnectiviteit met Azure wordt beschermd tegen fouten op zoneniveau.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Zone-redundante gateways

Als u uw virtuele netwerk gateways automatisch wilt implementeren in beschikbaarheids zones, kunt u zone-redundante virtuele netwerk gateways gebruiken. Met zone-redundante gateways kunt u profiteren van zone tolerantie om toegang te krijgen tot uw essentiële, schaal bare Services op Azure.

<br>
<br>

![grafische zone-redundante gateways](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Zonegebonden-gateways

Als u gateways in een specifieke zone wilt implementeren, kunt u zonegebonden-gateways gebruiken. Wanneer u een zonegebonden-gateway implementeert, worden alle exemplaren van de gateway geïmplementeerd in dezelfde beschikbaarheids zone.

<br>
<br>

![afbeelding van zonegebonden-gateways](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>Gateway-SKU's

Zone-redundante en zonegebonden-gateways zijn beschikbaar als nieuwe gateway-Sku's. Er zijn nieuwe virtuele netwerk gateway-Sku's toegevoegd in azure AZ-regio's. Deze Sku's zijn vergelijkbaar met de bijbehorende bestaande Sku's voor ExpressRoute en VPN Gateway, behalve dat ze specifiek zijn voor zone-redundante en zonegebonden gateways. U kunt deze Sku's identificeren met ' AZ ' in de SKU-naam.

Zie [VPN gateway-](vpn-gateway-about-vpngateways.md#gwsku) Sku's en [ExpressRoute gateway-sku's](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku)voor meer informatie over Gateway-sku's.

## <a name="public-ip-skus"></a><a name="pipskus"></a>Open bare IP-Sku's

Zone-redundant gateways en zonegebonden gateways zijn beide afhankelijk van de *standaard* -SKU van de open bare IP van Azure. De configuratie van de open bare IP-resource van Azure bepaalt of de gateway die u implementeert zone-redundant of zonegebonden is. Als u een open bare IP-resource met een *basis* -SKU maakt, heeft de gateway geen zone redundantie en zijn de gateway bronnen regionaal.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Zone-redundante gateways

Wanneer u een openbaar IP-adres maakt met behulp van de **standaard** open bare IP-SKU zonder een zone op te geven, verschilt het gedrag, afhankelijk van het feit of de gateway een VPN-gateway of een ExpressRoute-gateway is. 

* Voor een VPN-gateway worden de twee gateway-exemplaren geïmplementeerd in elke 2 van deze drie zones om zone-redundantie te bieden. 
* Voor een ExpressRoute-gateway kan het voor komen dat de gateway meer dan twee exemplaren kan beslaan in alle drie de zones.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Zonegebonden-gateways

Wanneer u een openbaar IP-adres maakt met behulp van de **standaard** open bare IP-SKU en de zone (1, 2 of 3) opgeeft, worden alle instanties van de gateway in dezelfde zone geïmplementeerd.

### <a name="regional-gateways"></a><a name="piprg"></a>Regionale gateways

Wanneer u een openbaar IP-adres maakt met behulp van de **algemene** open bare IP-SKU, wordt de gateway geïmplementeerd als een regionale gateway en heeft deze geen zone-redundantie ingebouwd in de gateway.

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Wat verandert er wanneer ik deze nieuwe Sku's Implementeer?

Vanuit uw perspectief kunt u uw gateways implementeren met zone-redundantie. Dit betekent dat alle exemplaren van de gateways worden geïmplementeerd over Azure-beschikbaarheidszones en elke beschikbaarheids zone is een ander fout-en update domein. Dit maakt het mogelijk dat uw gateways betrouwbaarder, beschikbaar en robuust zijn voor zone storingen.

### <a name="can-i-use-the-azure-portal"></a>Kan ik de Azure Portal gebruiken?

Ja, u kunt de Azure Portal gebruiken om de nieuwe Sku's te implementeren. Deze nieuwe Sku's worden echter alleen in die Azure-regio's met Azure-beschikbaarheidszones weer geven.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Welke regio's zijn er beschikbaar voor mij om de nieuwe Sku's te gebruiken?

De nieuwe Sku's zijn beschikbaar in azure-regio's met Azure-beschikbaarheidszones-centrale Amerikaanse, Frankrijk-centraal, Europa-noord, Europa-west en VS-West 2 regio's, VS-Oost, VS-Oost 2, Zuidoost-Azië, Japan-Oost, UK-zuid. In de toekomst zullen we zone-redundante gateways voor u beschikbaar maken in andere open bare Azure-regio's.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kan ik mijn bestaande virtuele netwerk gateways wijzigen/migreren/upgraden naar zone-redundante of zonegebonden-gateways?

Het migreren van uw bestaande virtuele netwerk gateways naar zone-redundante of zonegebonden-gateways wordt momenteel niet ondersteund. U kunt echter uw bestaande gateway verwijderen en een zone-redundante of zonegebonden-Gateway opnieuw maken.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kan ik zowel VPN-als Express route gateways in hetzelfde virtuele netwerk implementeren?

Zowel VPN-als Express route-gateways in hetzelfde virtuele netwerk worden ondersteund. U moet echter een/27 IP-adres bereik reserveren voor het gateway-subnet.

## <a name="next-steps"></a>Volgende stappen

[Een zone-redundante virtuele netwerkgateway maken](create-zone-redundant-vnet-gateway.md)
