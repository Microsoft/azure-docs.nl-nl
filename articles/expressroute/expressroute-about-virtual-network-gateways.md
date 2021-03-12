---
title: Over ExpressRoute virtuele netwerk gateways-Azure | Microsoft Docs
description: Meer informatie over virtuele netwerk gateways voor ExpressRoute. Dit artikel bevat informatie over Gateway-Sku's en-typen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: duau
ms.openlocfilehash: 038e018a22af3546f5d3c66f6d8ee3963483cce1
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615054"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Info over virtuele ExpressRoute-netwerkgateways voor meer informatie over de virtuele ExpressRoute-netwerkgateway

Als u uw virtuele Azure-netwerk en uw on-premises netwerk wilt verbinden via ExpressRoute, moet u eerst een virtuele netwerk gateway maken. Een virtuele netwerk gateway fungeert twee doelen: IP-routes uitwisselen tussen de netwerken en netwerk verkeer routeren. In dit artikel worden de gateway typen, gateway-Sku's en geschatte prestaties per SKU uitgelegd. In dit artikel wordt ook ExpressRoute [FastPath](#fastpath)beschreven, een functie waarmee het netwerk verkeer van uw on-premises netwerk wordt omzeild om de virtuele netwerk gateway te omzeilen om de prestaties te verbeteren.

## <a name="gateway-types"></a>Gatewaytypen

Wanneer u een virtuele netwerk gateway maakt, moet u verschillende instellingen opgeven. Een van de vereiste instellingen, '-gateway type ', geeft aan of de gateway wordt gebruikt voor ExpressRoute of VPN-verkeer. De twee gateway typen zijn:

* **VPN** : als u versleuteld verkeer via het open bare Internet wilt verzenden, gebruikt u het gateway type ' VPN '. Dit wordt ook wel een VPN-gateway genoemd. Site-naar-site-, punt-naar-site- en VNet-naar-VNet-verbindingen gebruiken allemaal een VPN-gateway.

* **ExpressRoute** : als u netwerk verkeer wilt verzenden op een particuliere verbinding, gebruikt u het gateway type ' ExpressRoute '. Dit wordt ook wel een ExpressRoute-gateway genoemd en is het type gateway dat wordt gebruikt bij het configureren van ExpressRoute.

Elk virtueel netwerk kan maar één virtuele netwerkgateway per type gateway hebben. U kunt voor een virtueel netwerk bijvoorbeeld één gateway gebruiken die -GatewayType Vpn gebruikt en één die -GatewayType ExpressRoute gebruikt.

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU's
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Als u uw gateway wilt bijwerken naar een krachtigere gateway-SKU, kunt u in de meeste gevallen de Power shell-cmdlet ' resize-AzVirtualNetworkGateway ' gebruiken. Dit werkt voor upgrades naar Standard-en high performance-Sku's. Als u echter een upgrade naar de Ultra Performance-SKU wilt uitvoeren, moet u de gateway opnieuw maken. Het opnieuw maken van een gateway leidt tot uitval tijd.

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>Geschatte prestaties per gateway-SKU
In de volgende tabel ziet u de gateway typen en de geschatte prestaties. Deze tabel is van toepassing op de Resource Manager en de klassieke implementatiemodellen.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Toepassings prestaties zijn afhankelijk van meerdere factoren, zoals de end-to-end-latentie en het aantal verkeer dat de toepassing opent. De getallen in de tabel vertegenwoordigen de bovengrens die de toepassing kan betreffen in een ideale omgeving.
>
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>Gatewaysubnet

Voordat u een ExpressRoute-gateway maakt, moet u een gateway-subnet maken. Het gateway-subnet bevat de IP-adressen die door de virtuele netwerk gateway-Vm's en-services worden gebruikt. Wanneer u de virtuele netwerk gateway maakt, worden gateway-Vm's geïmplementeerd naar het gateway-subnet en geconfigureerd met de vereiste ExpressRoute-gateway-instellingen. Implementeer nooit iets anders (bijvoorbeeld extra Vm's) naar het gateway-subnet. Het gateway-subnet moet de naam ' GatewaySubnet ' hebben om goed te kunnen werken. Als u het gateway-subnet ' GatewaySubnet ' noemt, weet Azure dat dit het subnet is voor het implementeren van de virtuele netwerk gateway-Vm's en-services naar.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Wanneer u het gatewaysubnet maakt, geeft u op hoeveel IP-adressen het subnet bevat. De IP-adressen in het gateway-subnet worden toegewezen aan de gateway-Vm's en Gateway Services. Sommige configuraties vereisen meer IP-adressen dan andere. 

Wanneer u de grootte van het gateway-subnet plant, raadpleegt u de documentatie voor de configuratie die u wilt maken. Zo is voor de configuratie ExpressRoute/VPN Gateway naast elkaar een groter gateway-subnet vereist dan de meeste andere configuraties. Daarnaast wilt u er mogelijk voor zorgen dat uw gateway subnet voldoende IP-adressen bevat voor mogelijke toekomstige extra configuraties. Hoewel u een gateway-subnet kunt maken als/29, raden we u aan een gateway-subnet van/27 of groter te maken (/27,/26 enzovoort) als u over de beschik bare adres ruimte beschikt. Als u een gateway-subnet met dubbele stacks maakt, raden we u aan om ook een IPv6-bereik van/64 of groter te gebruiken. Dit is geschikt voor de meeste configuraties.

In het volgende voor beeld van Resource Manager Power shell wordt een gateway-subnet met de naam GatewaySubnet weer gegeven. U kunt in de CIDR-notatie een/27 opgeven, zodat er voldoende IP-adressen zijn voor de meeste configuraties die momenteel bestaan.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>Zone-redundante gateway-Sku's

U kunt ook ExpressRoute-gateways implementeren in Azure-beschikbaarheidszones. Deze worden fysiek en logisch gescheiden in verschillende Beschikbaarheidszones, waarbij uw on-premises netwerk connectiviteit met Azure wordt beschermd tegen storingen op zone niveau.

![Zone-redundante ExpressRoute-gateway](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zone-redundante gateways gebruiken specifieke nieuwe gateway-Sku's voor ExpressRoute-gateway.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

De nieuwe gateway-Sku's bieden ook ondersteuning voor andere implementatie opties die het beste aansluiten bij uw behoeften. Wanneer u een virtuele netwerk gateway maakt met behulp van de nieuwe gateway-Sku's, hebt u ook de mogelijkheid om de gateway in een specifieke zone te implementeren. Dit wordt een zonegebonden-gateway genoemd. Wanneer u een zonegebonden-gateway implementeert, worden alle exemplaren van de gateway geïmplementeerd in dezelfde beschikbaarheids zone.

> [!IMPORTANT]
> Als u van plan bent om op IPv6 gebaseerde privé-peering te gebruiken via ExpressRoute, moet u ervoor zorgen dat u een AZ-SKU selecteert voor de gateway die u in een subnet met dubbele stack gateway implementeert.
> 
>

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

ExpressRoute virtuele netwerk gateway is ontworpen voor het uitwisselen van netwerk routes en het routeren van netwerk verkeer. FastPath is ontworpen om de prestaties van het gegevenspad tussen uw on-premises netwerk en het virtuele netwerk te verbeteren. Als deze functie is ingeschakeld, verzendt FastPath netwerk verkeer rechtstreeks naar virtuele machines in het virtuele netwerk, waarbij de gateway wordt omzeild.

Zie [about FastPath](about-fastpath.md)(Engelstalig) voor meer informatie over FastPath, met inbegrip van beperkingen en vereisten.

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST Api's en Power shell-cmdlets
Zie de volgende pagina's voor aanvullende technische bronnen en specifieke syntaxis vereisten voor het gebruik van REST Api's en Power shell-cmdlets voor configuraties van virtuele netwerk gateways:

| **Klassieke** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/servicemanagement/azure.service/#azure) |[PowerShell](/powershell/module/az.network#networking) |
| [REST API](/previous-versions/azure/reference/jj154113(v=azure.100)) |[REST API](/rest/api/virtual-network/) |

## <a name="next-steps"></a>Volgende stappen

Zie [ExpressRoute Overview](expressroute-introduction.md)(Engelstalig) voor meer informatie over de beschik bare verbindings configuraties.

Zie [een virtuele netwerk gateway maken voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)voor meer informatie over het maken van ExpressRoute-gateways.

Zie [een zone maken-redundante virtuele netwerk gateway](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)voor meer informatie over het configureren van zone-redundante gateways.

Zie [about FastPath](about-fastpath.md)(Engelstalig) voor meer informatie over FastPath.