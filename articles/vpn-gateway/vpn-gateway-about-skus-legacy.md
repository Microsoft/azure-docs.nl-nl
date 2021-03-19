---
title: Verouderde Azure Virtual Network VPN gateway-Sku's
description: Werken met de oude Sku's van de virtuele netwerk gateway; Basic, Standard en high performance.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84687783"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Werken met virtuele netwerk gateway-Sku's (verouderde Sku's)

Dit artikel bevat informatie over de verouderde (oude) Sku's van de virtuele netwerk gateway. De oudere Sku's werken nog steeds in beide implementatie modellen voor VPN-gateways die al zijn gemaakt. Klassieke VPN-gateways blijven de oudere Sku's gebruiken, zowel voor bestaande gateways als voor nieuwe gateways. Gebruik de nieuwe gateway-Sku's bij het maken van nieuwe VPN-gateways voor Resource Manager. Zie [over VPN gateway](vpn-gateway-about-vpngateways.md)voor meer informatie over de nieuwe sku's.

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU's

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

U kunt de prijs van een verouderde gateway bekijken in het gedeelte **Virtual Network gateways** , dat zich bevindt op de [pagina met prijzen voor ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>Geschatte geaggregeerde doorvoer per SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>Ondersteunde configuraties per SKU en VPN-type

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>Het formaat van een gateway wijzigen

U kunt de grootte van uw gateway aanpassen aan een gateway-SKU binnen dezelfde SKU-familie. Als u bijvoorbeeld een standaard-SKU hebt, kunt u de grootte wijzigen in een high performance-SKU. U kunt de grootte van uw VPN-gateway echter niet wijzigen tussen de oude Sku's en de nieuwe SKU-families. U kunt bijvoorbeeld niet van een standaard-SKU naar een VpnGw2-SKU of een basis-SKU naar VpnGw1 gaan.

### <a name="resource-manager"></a>Resource Manager

Als u het formaat van een gateway voor het Resource Manager-implementatie model met behulp van Power shell wilt wijzigen, gebruikt u de volgende opdracht:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

U kunt ook het formaat van een gateway in de Azure Portal wijzigen.

### <a name="classic"></a><a name="classicresize"></a>Klassieke

Als u het formaat van een gateway voor het klassieke implementatie model wilt wijzigen, moet u de service management Power shell-cmdlets gebruiken. Gebruik de volgende opdracht:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>Overschakelen naar de nieuwe gateway-Sku's

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [Gateway-sku's](vpn-gateway-about-vpngateways.md#gwsku)voor meer informatie over de nieuwe gateway-sku's.

Zie [over VPN gateway configuratie-instellingen](vpn-gateway-about-vpn-gateway-settings.md)voor meer informatie over configuratie-instellingen.
