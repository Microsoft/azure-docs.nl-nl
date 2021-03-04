---
title: 'Azure ExpressRoute: IPv6-ondersteuning toevoegen met behulp van Azure PowerShell'
description: Meer informatie over het toevoegen van IPv6-ondersteuning om verbinding te maken met Azure-implementaties met behulp van Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 20b8e354d0c8e2e04cf22d1b8014f5b8e33a860c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038863"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>IPv6-ondersteuning voor privé-peering toevoegen met behulp van Azure PowerShell (preview-versie)

In dit artikel wordt beschreven hoe u IPv6-ondersteuning kunt toevoegen om via ExpressRoute verbinding te maken met uw resources in azure met behulp van Azure PowerShell.

> [!Note]
> Deze functie is momenteel beschikbaar voor preview in [Azure-regio's met Beschikbaarheidszones](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones). Uw ExpressRoute-circuit kan daarom worden gemaakt met behulp van een wille keurige locatie, maar de IPv6-implementaties waarmee deze verbinding maakt, moeten zich in een regio met Beschikbaarheidszones bevinden.

## <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="register-for-public-preview"></a>Registreren voor open bare preview
Voordat u IPv6-ondersteuning toevoegt, moet u uw abonnement eerst registreren. Ga als volgt te werk om de registratie uit te voeren via Azure PowerShell:
1.  Meld u aan bij Azure en selecteer het abonnement. U moet dit doen voor het abonnement dat uw ExpressRoute-circuit bevat, evenals het abonnement dat uw Azure-implementaties bevat (als deze verschillend zijn).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Vraag om uw abonnement voor open bare preview te registreren met behulp van de volgende opdracht:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Uw aanvraag wordt binnen 2-3 werk dagen goedgekeurd door het ExpressRoute-team.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Persoonlijke IPv6-peering toevoegen aan uw ExpressRoute-circuit

1. [Een ExpressRoute-circuit maken](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-arm) of een bestaand circuit gebruiken. Haal het circuit op door de opdracht **Get-AzExpressRouteCircuit** uit te voeren:

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Haal de persoonlijke peering-configuratie voor het circuit op door **Get-AzExpressRouteCircuitPeeringConfig** uit te voeren:

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. Een persoonlijke IPv6-peering toevoegen aan uw bestaande IPv4-configuratie voor persoonlijke peering. Geef een combi natie van/126 IPv6-subnetten op die u voor uw primaire koppeling en secundaire koppelingen hebt. Vanuit deze subnetten wijst u het eerste bruikbare IP-adres toe aan uw router, aangezien Microsoft de tweede bruikbare IP voor de eigen router gebruikt.

    > [!Note]
    > De peer-ASN en VlanId moeten overeenkomen met die in de configuratie van de persoonlijke IPv4-peering.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. Nadat de configuratie is opgeslagen, haalt u het circuit opnieuw op door de opdracht **Get-AzExpressRouteCircuit** uit te voeren. Het antwoord moet er ongeveer uitzien als in het volgende voor beeld:

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Uw verbinding met een bestaand virtueel netwerk bijwerken

Voer de volgende stappen uit als u een bestaande omgeving van Azure-resources in een regio hebt met Beschikbaarheidszones waarvoor u uw IPv6-persoonlijke peering wilt gebruiken.

1. Haal het virtuele netwerk op waarmee uw ExpressRoute-circuit is verbonden.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Voeg een IPv6-adres ruimte toe aan het virtuele netwerk.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. Voeg IPv6-adres ruimte toe aan het subnet van de gateway. Het IPv6-subnet van de gateway moet/64 of groter zijn.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. Als u een bestaande zone-redundante gateway hebt, voert u de volgende handelingen uit om IPv6-connectiviteit in te scha kelen. Als dat niet het geval is, [maakt u de gateway van het virtuele netwerk](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager) met behulp van een zone-redundante SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```

## <a name="create-a-connection-to-a-new-virtual-network"></a>Een verbinding maken met een nieuw virtueel netwerk

Volg de onderstaande stappen als u van plan bent om verbinding te maken met een nieuwe set Azure-resources in een regio met Beschikbaarheidszones met behulp van uw IPv6-persoonlijke peering.

1. Maak een virtueel netwerk met dubbele stack met IPv4-en IPv6-adres ruimte. Zie [een virtueel netwerk maken](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network)voor meer informatie.

2. [Maak het subnet met dubbele stack-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway).

3. [Maak de gateway van het virtuele netwerk](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway) met behulp van een zone-redundante SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ). Als u van plan bent FastPath te gebruiken, gebruikt u ErGw3AZ.

4. [Koppel uw virtuele netwerk aan uw ExpressRoute-circuit](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm).

## <a name="limitations"></a>Beperkingen
Hoewel IPv6-ondersteuning beschikbaar is voor verbindingen met implementaties in regio's met Beschikbaarheidszones, worden de volgende use-cases niet ondersteund:

* Verbindingen met implementaties in azure via een niet-AZ ExpressRoute gateway SKU
* Verbindingen met implementaties in niet-AZ regio's
* Global Reach verbindingen tussen ExpressRoute-circuits
* Gebruik van ExpressRoute met vWAN

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor het oplossen van problemen met ExpressRoute:

* [Connectiviteit ExpressRoute controleren](expressroute-troubleshooting-expressroute-overview.md)
* [Problemen met netwerk prestaties oplossen](expressroute-troubleshooting-network-performance.md)
