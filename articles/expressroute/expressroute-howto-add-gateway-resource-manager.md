---
title: 'Zelfstudie - Azure ExpressRoute: Een gateway toevoegen aan een VNet - Azure PowerShell'
description: Deze zelfstudie helpt u bij het toevoegen van VNet-gateway aan een al gemaakt Resource Manager VNet voor ExpressRoute met behulp van Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 2b75e6e0a8b79f374900e6cb2dfc49680d3d0190
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739055"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Zelfstudie: Een virtuele netwerkgateway configureren voor ExpressRoute met behulp van PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassiek - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video: Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

In deze zelfstudie vindt u instructies voor het toevoegen, vergroten/verkleinen en verwijderen van een gateway voor een virtueel netwerk (VNet) voor een al bestaand VNet. De stappen voor deze configuratie gelden voor VNets die zijn gemaakt met behulp van het Resource Manager-implementatiemodel voor een ExpressRoute-configuratie. Zie [Virtuele netwerkgateways voor ExpressRoute](expressroute-about-virtual-network-gateways.md) voor meer informatie.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een gatewaysubnet maken.
> - De virtuele-netwerkgateway maken.

## <a name="prerequisites"></a>Vereisten

### <a name="configuration-reference-list"></a>Configuratiereferentielijst

In de stappen voor deze taak gebruiken we een VNet dat is gebaseerd op de waarden in de volgende configuratiereferentielijst. Aanvullende instellingen en namen worden ook beschreven in deze lijst. Deze lijst wordt niet rechtstreeks in een van de stappen gebruikt, maar er worden wel variabelen toegevoegd op basis van de waarden in deze lijst. U kunt de lijst kopiëren om deze als referentie te gebruiken, waarbij u de waarden vervangt door uw eigen waarden.

| Instelling                   | Waarde                                              |
| ---                       | ---                                                |
| Naam van virtueel netwerk | *TestVNet* |    
| Adresruimte van virtueel netwerk | *192.168.0.0/16* |
| Resourcegroep | *TestRG* |
| Naam van Subnet1 | *FrontEnd* |   
| Adresruimte van Subnet1 | *192.168.1.0/24* |
| Naam van Subnet1 | *FrontEnd* |
| Naam van gatewaysubnet | *GatewaySubnet* |    
| Adresruimte van gatewaysubnet | *192.168.200.0/26* |
| Regio | *VS - oost* |
| Naam van de gateway | *GW* |   
| IP-adres van gateway | *GWIP* |
| Naam van IP-configuratie gateway | *gwipconf* |
| Type | *ExpressRoute* |
| Naam van openbare IP van gateway  | *gwpip* |

> [!IMPORTANT]
> IPv6-ondersteuning voor persoonlijke peering is momenteel beschikbaar als **open bare preview**. Als u uw virtuele netwerk wilt verbinden met een ExpressRoute-circuit met op IPv6 gebaseerde privé-peering geconfigureerd, zorgt u ervoor dat uw virtuele netwerk dual stack is en volgt u de richt lijnen die [hier](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)worden beschreven.
> 
> 

## <a name="add-a-gateway"></a>Een gateway toevoegen

1. Voer `Connect-AzAccount` uit om verbinding te maken met Azure.

1. Declareer de variabelen voor deze oefening. Vergeet niet om het voorbeeld aan te passen met de instellingen die u wilt gebruiken.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. Sla het virtuele-netwerkobject op als een variabele.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. Voeg een gatewaysubnet toe aan het virtuele netwerk. Het gatewaysubnet moet de naam "GatewaySubnet" hebben. Het gatewaysubnet moet een bereik van /27 of groter hebben (/26, /25, enzovoort).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
    Als u een virtueel netwerk met dubbele stack gebruikt en op IPv6 gebaseerde privé-peering wilt gebruiken via ExpressRoute, maakt u in plaats daarvan een dual stack gateway-subnet.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26","ace:daa:daaa:deaa::/64"
   ```
1. Stel de configuratie in.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. Sla het gatewaysubnet op als een variabele.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. Vraag een openbaar IP-adres aan. Het IP-adres wordt aangevraagd voordat de gateway wordt gemaakt. U kunt het IP-adres dat u wilt gebruiken niet zelf opgeven aangezien het dynamisch wordt toegewezen. U gebruikt dit IP-adres in de volgende configuratiesectie. AllocationMethod moet zijn ingesteld op Dynamic.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
1. Definieer de configuratie voor uw gateway. De gatewayconfiguratie bepaalt welk subnet en openbaar IP-adres moeten worden gebruikt. In deze stap geeft u de configuratie op die wordt gebruikt bij het maken van de gateway. Gebruik het volgende voorbeeld om de gatewayconfiguratie te maken.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. Maak de gateway. In deze stap is **-GatewayType** erg belangrijk. U moet hiervoor de waarde **ExpressRoute** gebruiken. Na het uitvoeren van deze cmdlets kan het 45 minuten of langer duren voordat de gateway is gemaakt.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```
> [!IMPORTANT]
> Als u van plan bent om op IPv6 gebaseerde privé-peering te gebruiken via ExpressRoute, moet u ervoor zorgen dat u een AZ SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) voor **-GatewaySku** selecteert.
> 
> 

## <a name="verify-the-gateway-was-created"></a>Controleren of de gateway is gemaakt
Gebruik de volgende opdrachten om te controleren of de gateway is gemaakt:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Het formaat van een gateway wijzigen
Er zijn verschillende [gateway-SKU's](expressroute-about-virtual-network-gateways.md). U kunt de volgende opdracht gebruiken om de gateway-SKU op elk gewenst moment te wijzigen.

> [!IMPORTANT]
> Deze opdracht werkt niet voor een UltraPerformance-gateway. Als u uw gateway wilt wijzigen in een UltraPerformance-gateway, verwijdert u eerst de bestaande ExpressRoute-gateway en maakt u vervolgens een nieuwe UltraPerformance-gateway. Als u uw UltraPerformance-gateway wilt downgraden, verwijdert u eerst de UltraPerformance-gateway en maakt u vervolgens een nieuwe gateway.
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>Resources opschonen
Gebruik de volgende opdracht om de gateway te verwijderen:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>Volgende stappen
Als u de VNet-gateway hebt gemaakt, kunt u uw VNet koppelen aan een ExpressRoute-circuit. 

> [!div class="nextstepaction"]
> [Een virtueel netwerk koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
