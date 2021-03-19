---
title: 'Snelstartgids: route server maken en configureren met behulp van Azure PowerShell'
description: In deze Quick Start leert u hoe u een route server maakt en configureert met behulp van Azure PowerShell.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: a3ab3a801872cc20b4e41bbff02ad6474c3bab8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655203"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Snelstartgids: route server maken en configureren met behulp van Azure PowerShell

Dit artikel helpt u bij het configureren van Azure route server naar peer met een virtueel netwerk apparaat (NVA) in uw virtuele netwerk met behulp van Power shell. Er worden door Azure route server routes van de NVA en Program ma's op de virtuele machines in het virtuele netwerk beschreven. Azure route server adverteert ook de virtuele netwerk routes naar het NVA. Lees [Azure route server](overview.md)voor meer informatie.

> [!IMPORTANT]
> Azure route server (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Zorg ervoor dat u de nieuwste Power shell-modules hebt, of gebruik Azure Cloud Shell in de portal.
* Controleer de [service limieten voor Azure route server](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Een route server maken

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Een resource groep en een virtueel netwerk maken

Voordat u een Azure-route server kunt maken, hebt u een virtueel netwerk nodig om de implementatie te hosten. Gebruik de volgende opdracht om een resource groep en een virtueel netwerk te maken. Als u al een virtueel netwerk hebt, kunt u door gaan naar de volgende sectie.

```azurepowershell-interactive
New-AzResourceGroup –Name "RouteServerRG” -Location “West US"
New-AzVirtualNetwork –ResourceGroupName "RouteServerRG" -Location "West US" -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Een subnet toevoegen

1. Voeg een subnet met de naam *RouteServerSubnet* toe om de Azure route server in te implementeren. Dit subnet is alleen een specifiek subnet voor Azure route server. De RouteServerSubnet moet/27 of een kortere voor voegsel (zoals/26,/25) zijn of u ontvangt een fout bericht wanneer u de Azure route server toevoegt.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "myVirtualNetwork" - ResourceGroupName "RouteServerRG"
    Add-AzVirtualNetworkSubnetConfig –Name "RouteServerSubnet" -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. Haal de RouteServerSubnet-ID op. Als u de resource-ID van alle subnetten in het virtuele netwerk wilt zien, gebruikt u deze opdracht:

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "vnet_name" -ResourceGroupName "RouteServerRG"
    $vnet.Subnets
    ```

De RouteServerSubnet-ID ziet er als volgt uit:

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>De route server maken

Maak de route server met de volgende opdracht:

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US" -HostedSubnet "RouteServerSubnet_ID"
```

De locatie moet overeenkomen met de locatie van het virtuele netwerk. De HostedSubnet is de RouteServerSubnet-ID die u hebt verkregen in de vorige sectie.

## <a name="create-peering-with-an-nva"></a>Peering maken met een NVA

Gebruik de volgende opdracht om BGP-peering te maken van de route server naar de NVA:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

"nva_ip" is het IP-adres van het virtuele netwerk dat is toegewezen aan de NVA. "nva_asn" is het autonome systeem nummer (ASN) dat is geconfigureerd in de NVA. Het ASN kan een wille keurig 16-bits nummer zijn dan het aantal in het bereik van 65515-65520. Dit bereik van Asn's is gereserveerd door micro soft.

Gebruik deze opdracht om peering in te stellen met een ander NVA of een ander exemplaar van hetzelfde NVA voor redundantie:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>De configuratie op de NVA volt ooien

Als u de configuratie wilt volt ooien op de NVA en de BGP-sessies wilt inschakelen, hebt u het IP-adres en de ASN van Azure route server nodig. U kunt deze informatie ophalen met behulp van de volgende opdracht:

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

De uitvoer bevat de volgende informatie:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Route uitwisseling configureren

Als u een ExpressRoute-gateway en een Azure VPN-gateway in hetzelfde VNet hebt en u wilt dat ze routes uitwisselen, kunt u route Exchange inschakelen op de Azure route server.

1. Als u route uitwisseling tussen Azure route server en de gateway (s) wilt inschakelen, gebruikt u de volgende opdracht:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Als u route uitwisseling tussen Azure route server en de gateway (s) wilt uitschakelen, gebruikt u de volgende opdracht:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>Problemen oplossen

Met deze opdracht kunt u de routes weer geven die worden geadverteerd en ontvangen door Azure route server:

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up-resources"></a>Resources opschonen

Als u de Azure route server niet meer nodig hebt, gebruikt u deze opdrachten om de BGP-peering te verwijderen en de route server vervolgens te verwijderen. 

1. Verwijder de BGP-peering tussen Azure route server en een NVA met deze opdracht:

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName "nva_name" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. Azure route server verwijderen met deze opdracht:

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>Volgende stappen

Nadat u de Azure route server hebt gemaakt, gaat u verder met meer informatie over hoe Azure route server communiceert met ExpressRoute en VPN-gateways: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute-en Azure VPN-ondersteuning](expressroute-vpn-support.md)
