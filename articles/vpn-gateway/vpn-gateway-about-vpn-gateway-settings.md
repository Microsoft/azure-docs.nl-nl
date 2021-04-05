---
title: 'Azure-VPN Gateway: configuratie-instellingen'
description: Meer informatie over het VPN Gateway van bronnen en instellingen voor een virtueel netwerk dat is gemaakt in het Resource Manager-implementatie model.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 1aba87b2139fb8a7d395fb3180d2074e47310fa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010819"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Over VPN Gateway configuratie-instellingen

Een VPN-gateway is een type virtuele netwerk gateway die versleuteld verkeer verzendt tussen uw virtuele netwerk en uw on-premises locatie via een open bare verbinding. U kunt ook een VPN-gateway gebruiken om verkeer tussen virtuele netwerken over de Azure-backbone te verzenden.

Een VPN-gateway verbinding is afhankelijk van de configuratie van meerdere bronnen, die elk Configureer bare instellingen bevat. In de secties in dit artikel worden de bronnen en instellingen besproken die betrekking hebben op een VPN-gateway voor een virtueel netwerk dat is gemaakt in het Resource Manager-implementatie model. In het artikel [over VPN gateway](vpn-gateway-about-vpngateways.md) vindt u beschrijvingen en topologie diagrammen voor elke verbindings oplossing.

Met de waarden in dit artikel worden VPN-gateways (virtuele netwerk gateways die gebruikmaken van de-gateway type VPN) toegepast. Dit artikel geldt niet voor alle gateway typen of zone-redundante gateways.

* Zie [Virtual Network gateways voor ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md)voor waarden die van toepassing zijn op-Gateway type ' ExpressRoute '.

* Zie [over zone-redundant gateways](about-zone-redundant-vnet-gateways.md)voor zone-redundante gateways.

* Zie [about Virtual WAN](../virtual-wan/virtual-wan-about.md)(Engelstalig) voor virtuele WAN.

## <a name="gateway-types"></a><a name="gwtype"></a>Gatewaytypen

Elk virtueel netwerk kan slechts één virtuele netwerk gateway van elk type hebben. Wanneer u een virtuele netwerk gateway maakt, moet u ervoor zorgen dat het gateway type juist is voor uw configuratie.

De beschik bare waarden voor-gateway type zijn:

* VPN
* ExpressRoute

Een VPN-gateway vereist het `-GatewayType` *VPN*.

Voorbeeld:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU's

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Een gateway-SKU configureren

**Azure-portal**

Als u de Azure Portal gebruikt om een virtuele netwerk gateway van Resource Manager te maken, kunt u de gateway-SKU selecteren met behulp van de vervolg keuzelijst. De opties die u opgeeft, komen overeen met het gateway type en het VPN-type dat u selecteert.

**PowerShell**

In het volgende Power shell-voor beeld wordt de `-GatewaySku` as-VpnGw1 opgegeven. Wanneer u Power shell gebruikt om een gateway te maken, moet u eerst de IP-configuratie maken en vervolgens een variabele gebruiken om ernaar te verwijzen. In dit voor beeld is de configuratie variabele $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

**Azure-CLI**

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizing-or-changing-a-sku"></a><a name="resizechange"></a>Verg Roten of verkleinen of wijzigen van een SKU

Als u een VPN-gateway hebt en u een andere gateway-SKU wilt gebruiken, moet u de grootte van de gateway-SKU aanpassen of een andere SKU kiezen. Wanneer u overschakelt naar een andere gateway-SKU, verwijdert u de bestaande gateway volledig en maakt u een nieuwe. Het bouwen van een gateway kan tot 45 minuten duren. Ter vergelijking: wanneer u het formaat van een gateway-SKU wijzigt, is er weinig downtime, omdat u de gateway niet hoeft te verwijderen en opnieuw op te bouwen. Als u de mogelijkheid hebt om de grootte van de gateway-SKU te wijzigen in plaats van deze aan te passen, moet u dat doen. Er zijn echter regels voor het wijzigen van de grootte:

1. Met uitzonde ring van de basis-SKU kunt u de grootte van een VPN gateway-SKU wijzigen in een andere VPN gateway-SKU binnen dezelfde generatie (Generation1 of Generation2). Zo kan VpnGw1 van Generation1 worden gewijzigd in VpnGw2 van Generation1, maar niet op VpnGw2 van Generation2.
2. Als u met de oude gateway-SKU's werkt, kunt u wisselen tussen Basic-, Standard- en HighPerformance-SKU's.
3. U **kunt** de grootte van de sku's Basic/Standard/high performance niet wijzigen in VpnGw-sku's. U moet in plaats daarvan [overschakelen](#change) naar de nieuwe sku's.

#### <a name="to-resize-a-gateway"></a><a name="resizegwsku"></a>Het formaat van een gateway wijzigen

**Azure-portal**

[!INCLUDE [Resize a SKU - portal](../../includes/vpn-gateway-resize-gw-portal-include.md)]

**PowerShell**

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="to-change-from-an-old-legacy-sku-to-a-new-sku"></a><a name="change"></a>Wijzigen van een oude (verouderde) SKU naar een nieuwe SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connection-types"></a><a name="connectiontype"></a>Verbindingstypen

In het Resource Manager-implementatie model vereist elke configuratie een specifiek verbindings type voor het virtuele netwerk gateway. De beschikbare Resource Manager PowerShell-waarden voor `-ConnectionType` zijn:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

In het volgende Power shell-voor beeld maken we een S2S-verbinding waarvoor het verbindings type *IPSec* is vereist.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpn-types"></a><a name="vpntype"></a>VPN-typen

Wanneer u de virtuele netwerk gateway voor een VPN-gateway configuratie maakt, moet u een VPN-type opgeven. Welk VPN-type u kiest, is afhankelijk van de verbindings topologie die u wilt maken. Voor een P2S-verbinding is bijvoorbeeld een RouteBased VPN-type vereist. Een VPN-type kan ook afhankelijk zijn van de hardware die u gebruikt. Voor S2S-configuraties is een VPN-apparaat vereist. Sommige VPN-apparaten bieden alleen ondersteuning voor een bepaald VPN-type.

Het VPN-type dat u selecteert, moet voldoen aan alle verbindings vereisten voor de oplossing die u wilt maken. Als u bijvoorbeeld een S2S VPN-gateway verbinding en een P2S VPN-gateway verbinding wilt maken voor hetzelfde virtuele netwerk, gebruikt u VPN-type *RouteBased* omdat P2S een RouteBased VPN-type vereist. U moet ook controleren of uw VPN-apparaat een RouteBased-VPN-verbinding ondersteunt. 

Nadat een virtuele netwerk gateway is gemaakt, kunt u het VPN-type niet wijzigen. U moet de gateway van het virtuele netwerk verwijderen en een nieuwe maken. Er zijn twee VPN-typen:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

In het volgende Power shell-voor beeld wordt de `-VpnType` as- *RouteBased* opgegeven. Wanneer u een gateway maakt, moet u het juiste VPN-type voor uw configuratie kiezen.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="gateway-requirements"></a><a name="requirements"></a>Gatewayvereisten

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gateway-subnet"></a><a name="gwsub"></a>Gatewaysubnet

Voordat u een VPN-gateway maakt, moet u een gateway-subnet maken. Het gateway-subnet bevat de IP-adressen die door de virtuele netwerk gateway-Vm's en-services worden gebruikt. Wanneer u de virtuele netwerk gateway maakt, worden gateway-Vm's geïmplementeerd naar het gateway-subnet en geconfigureerd met de vereiste VPN gateway-instellingen. Implementeer nooit iets anders (bijvoorbeeld extra Vm's) naar het gateway-subnet. Het gateway-subnet moet de naam ' GatewaySubnet ' hebben om goed te kunnen werken. Als u het gateway-subnet ' GatewaySubnet ' noemt, weet Azure dat dit het subnet is voor het implementeren van de virtuele netwerk gateway-Vm's en-services naar.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Wanneer u het gatewaysubnet maakt, geeft u op hoeveel IP-adressen het subnet bevat. De IP-adressen in het gateway-subnet worden toegewezen aan de gateway-Vm's en Gateway Services. Sommige configuraties vereisen meer IP-adressen dan andere. 

Wanneer u de grootte van het gateway-subnet plant, raadpleegt u de documentatie voor de configuratie die u wilt maken. Zo is voor de configuratie ExpressRoute/VPN Gateway naast elkaar een groter gateway-subnet vereist dan de meeste andere configuraties. Daarnaast wilt u er mogelijk voor zorgen dat uw gateway subnet voldoende IP-adressen bevat voor mogelijke toekomstige extra configuraties. Hoewel u een gateway-subnet kunt maken als/29, raden we u aan een gateway-subnet van/27 of groter te maken (/27,/26 enzovoort) als u over de beschik bare adres ruimte beschikt. Dit is geschikt voor de meeste configuraties.

In het volgende voor beeld van Resource Manager Power shell wordt een gateway-subnet met de naam GatewaySubnet weer gegeven. U kunt in de CIDR-notatie een/27 opgeven, zodat er voldoende IP-adressen zijn voor de meeste configuraties die momenteel bestaan.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="local-network-gateways"></a><a name="lng"></a>Lokale netwerkgateways

Een lokale netwerk gateway wijkt af van de gateway van een virtueel netwerk. Bij het maken van een VPN-gateway configuratie vertegenwoordigt de lokale netwerk gateway meestal uw on-premises netwerk en het bijbehorende VPN-apparaat. In het klassieke implementatiemodel werd de lokale gateway een lokale site genoemd.

U geeft de lokale netwerk gateway een naam, het open bare IP-adres of de Fully Qualified Domain Name (FQDN) van het on-premises VPN-apparaat en geeft de adres voorvoegsels op die zich op de on-premises locatie bevinden. Azure zoekt naar de voor voegsels van het doel adres voor netwerk verkeer, raadpleegt de configuratie die u hebt opgegeven voor uw lokale netwerk gateway en routeert pakketten dienovereenkomstig. Als u Border Gateway Protocol (BGP) op uw VPN-apparaat gebruikt, geeft u het IP-adres van de BGP-peer op van uw VPN-apparaat en het autonome systeem nummer (ASN) van uw on-premises netwerk. U geeft ook lokale netwerk gateways op voor VNet-naar-VNet-configuraties die gebruikmaken van een VPN-gateway verbinding.

In het volgende Power shell-voor beeld wordt een nieuwe lokale netwerk gateway gemaakt:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Soms moet u de instellingen van de lokale netwerk gateway wijzigen. Wanneer u bijvoorbeeld het adres bereik toevoegt of wijzigt, of als het IP-adres van het VPN-apparaat wordt gewijzigd. Zie [instellingen voor lokale netwerk gateway wijzigen met behulp van Power shell](vpn-gateway-modify-local-network-gateway.md).

## <a name="rest-apis-powershell-cmdlets-and-cli"></a><a name="resources"></a>REST Api's, Power shell-cmdlets en CLI

Zie de volgende pagina's voor aanvullende technische bronnen en specifieke syntaxis vereisten bij het gebruik van REST Api's, Power shell-cmdlets of Azure CLI voor VPN Gateway configuraties:

| **Klassieke** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/az.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [REST API](/previous-versions/azure/reference/jj154113(v=azure.100)) |[REST API](/rest/api/network/virtualnetworkgateways) |
| Niet ondersteund | [Azure-CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Volgende stappen

Zie [over VPN gateway](vpn-gateway-about-vpngateways.md)voor meer informatie over de beschik bare verbindings configuraties.