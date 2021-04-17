---
title: 'Quickstart: Routeserver maken en configureren met behulp van Azure PowerShell'
description: In deze quickstart leert u hoe u een routeserver maakt en configureert met behulp van Azure PowerShell.
services: route-server
author: duongau
ms.author: duau
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: route-server
ms.custom:
- mode-api
ms.openlocfilehash: 608ec3755fcd231d5cc89bbc28a01ce172978144
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538718"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Quickstart: Routeserver maken en configureren met behulp van Azure PowerShell

Dit artikel helpt u bij het configureren van Azure Route Server voor peering met een NVA (Network Virtual Appliance) in uw virtuele netwerk met behulp van PowerShell. Azure Route Server leert routes van de NVA en programmeert deze op de virtuele machines in het virtuele netwerk. Azure Route Server zal ook de virtuele netwerkroutes naar de NVA adverteren. Lees Azure [Route Server voor meer informatie.](overview.md)

> [!IMPORTANT]
> Azure Route Server (preview) is momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Zorg ervoor dat u de nieuwste PowerShell-modules hebt of gebruik de Azure Cloud Shell in de portal.
* Bekijk de [servicelimieten voor Azure Route Server.](route-server-faq.md#limitations)

## <a name="create-a-route-server"></a>Een routeserver maken

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Een resourcegroep en virtueel netwerk maken

Voordat u een Azure Route Server kunt maken, hebt u een virtueel netwerk nodig om de implementatie te hosten. Gebruik de volgende opdracht om een resourcegroep en een virtueel netwerk te maken. Als u al een virtueel netwerk hebt, kunt u naar de volgende sectie gaan.

```azurepowershell-interactive
New-AzResourceGroup –Name "RouteServerRG” -Location “West US"
New-AzVirtualNetwork –ResourceGroupName "RouteServerRG" -Location "West US" -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Een subnet toevoegen

1. Voeg een subnet met de *naam RouteServerSubnet toe om* de Azure Route Server in te implementeren. Dit subnet is alleen een toegewezen subnet voor Azure Route Server. Het RouteServerSubnet moet /27 of een korter voorvoegsel zijn (zoals /26, /25), anders ontvangt u een foutbericht wanneer u de Azure Route Server toevoegt.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "myVirtualNetwork" - ResourceGroupName "RouteServerRG"
    Add-AzVirtualNetworkSubnetConfig –Name "RouteServerSubnet" -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. Verkrijg de RouteServerSubnet-id. Als u de resource-id van alle subnetten in het virtuele netwerk wilt zien, gebruikt u deze opdracht:

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "vnet_name" -ResourceGroupName "RouteServerRG"
    $vnet.Subnets
    ```

De RouteServerSubnet-id ziet er als volgt uit:

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>De routeserver maken

Maak de routeserver met deze opdracht:

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US" -HostedSubnet "RouteServerSubnet_ID"
```

De locatie moet overeenkomen met de locatie van uw virtuele netwerk. HostedSubnet is de RouteServerSubnet-id die u in de vorige sectie hebt verkregen.

## <a name="create-peering-with-an-nva"></a>Peering maken met een NVA

Gebruik de volgende opdracht om BGP-peering van de routeserver naar de NVA tot stand te laten komen:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

'nva_ip' is het IP-adres van het virtuele netwerk dat is toegewezen aan de NVA. 'nva_asn' is het autonome systeemnummer (ASN) dat is geconfigureerd in de NVA. De ASN kan een ander 16-bits getal zijn dan dat van 65515-65520. Dit bereik van ASN's is gereserveerd door Microsoft.

Als u peering wilt instellen met een andere NVA of een ander exemplaar van dezelfde NVA voor redundantie, gebruikt u deze opdracht:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>Voltooi de configuratie op de NVA

Als u de configuratie op de NVA wilt voltooien en de BGP-sessies wilt inschakelen, hebt u het IP-adres en de ASN van Azure Route Server nodig. U kunt deze informatie verkrijgen met behulp van deze opdracht:

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

De uitvoer heeft de volgende informatie:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Route-uitwisseling configureren

Als u een ExpressRoute-gateway en een Azure VPN-gateway in hetzelfde VNet hebt en u wilt dat deze routes uitwisselen, kunt u routeuitwisseling inschakelen op de Azure Route Server.

1. Gebruik deze opdracht om route-uitwisseling tussen Azure Route Server en de gateway(s) in te stellen:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Gebruik deze opdracht om route-uitwisseling tussen Azure Route Server en de gateway(s) uit te schakelen:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>Problemen oplossen

U kunt de routes weergeven die zijn geadverteerd en ontvangen door Azure Route Server met deze opdracht:

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up-resources"></a>Resources opschonen

Als u de Azure Route Server niet meer nodig hebt, gebruikt u deze opdrachten om de BGP-peering te verwijderen en vervolgens de Route-server te verwijderen. 

1. Verwijder de BGP-peering tussen Azure Route Server en een NVA met deze opdracht:

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName "nva_name" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. Verwijder Azure Route Server met deze opdracht:

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>Volgende stappen

Nadat u de Azure Route-server hebt maken, gaat u verder met informatie over hoe Azure Route Server communiceert met ExpressRoute en VPN Gateways: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute en Azure VPN-ondersteuning](expressroute-vpn-support.md)
