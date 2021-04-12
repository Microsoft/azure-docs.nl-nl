---
title: 'Zelfstudie: Peering configureren voor een ExpressRoute-circuit - Azure PowerShell'
description: In deze zelfstudie wordt beschreven hoe u een routeringsconfiguratie maakt en beheert voor een ExpressRoute-circuit in het Resource Manager-implementatiemodel met behulp van PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: 7cfd378ae621192cd98b482b66c85c3dcd3ca454
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721936"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Zelfstudie: Peering voor een ExpressRoute-circuit maken en wijzigen met PowerShell

In deze zelfstudie leert u hoe u een routeringsconfiguratie maakt en beheert voor een ExpressRoute-circuit in het Resource Manager-implementatiemodel met behulp van PowerShell. U kunt ook de status van de peering voor een ExpressRoute-circuit controleren en peerings bijwerken of verwijderen en de inrichting ervan ongedaan maken. Als u een andere methode voor uw circuit wilt gebruiken, selecteert u een artikel in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure-CLI](howto-routing-cli.md)
> * [Openbare peering](about-public-peering.md)
> * [Video - Persoonlijke peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-routing-classic.md)
> 

Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services met Laag-2-connectiviteit aanbieden. Als u gebruikmaakt van een serviceprovider die beheerde Laag-3-services aanbiedt (meestal een IPVPN, zoals MPLS), zal de connectiviteitsprovider routering voor u configureren en beheren.

> [!IMPORTANT]
> Op dit moment bieden we nog geen peerings aan die door serviceproviders worden geconfigureerd via de beheerportal van de service. Deze mogelijkheid zal binnenkort worden ingeschakeld. Neem contact op met uw serviceprovider voordat u BGP-peerings configureert.
> 

U kunt persoonlijke peering en Microsoft-peering configureren voor een ExpressRoute-circuit (openbare Azure-peering is afgeschaft voor nieuwe circuits). Peerings kunnen in elke gewenste volgorde worden geconfigureerd. U moet er echter wel voor zorgen dat u de configuratie van elke peering een voor een voltooit. Zie [ExpressRoute-routeringsdomeinen](expressroute-circuit-peerings.md) voor meer informatie over routeringsdomeinen en peerings. Zie [Openbare peering van ExpressRoute](about-public-peering.md) voor meer informatie over openbare peering.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Microsoft-peering voor een circuit configureren, bijwerken en verwijderen
> - Persoonlijke Azure-peering voor een circuit configureren, bijwerken en verwijderen

## <a name="prerequisites"></a>Vereisten

* Zorg ervoor dat u de volgende pagina's hebt gecontroleerd voordat u met de configuratie begint:
    * [Vereisten](expressroute-prerequisites.md) 
    * [Routeringsvereisten](expressroute-routing.md)
    * [Werkstromen](expressroute-workflows.md)
* U moet een actief ExpressRoute-circuit hebben. Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verdergaat. Het ExpressRoute-circuit moet zijn ingericht en zijn ingeschakeld om de cmdlets in dit artikel te kunnen uitvoeren.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-peering

Deze sectie helpt u bij het maken, verkrijgen, bijwerken en verwijderen van de configuratie voor Microsoft-peering voor een ExpressRoute-circuit.

> [!IMPORTANT]
> Microsoft-peering voor ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017, heeft alle servicevoorvoegsels die worden geadverteerd via de Microsoft-peering, zelfs als er geen routefilters zijn gedefinieerd. Microsoft-peering voor ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017, heeft geen voorvoegsels die worden geadverteerd totdat een routefilter aan het circuit is gekoppeld. Zie [Een routefilter configureren voor Microsoft-peering](how-to-routefilter-powershell.md) voor meer informatie.
> 

### <a name="to-create-microsoft-peering"></a>Microsoft-peering maken

1. Meld u aan en selecteer uw abonnement.

   Als u PowerShell lokaal hebt geïnstalleerd, meldt u zich aan. U kunt deze stap overslaan als u Azure Cloud Shell gebruikt.

   ```azurepowershell
   Connect-AzAccount
   ```

   Selecteer het abonnement waarmee u een ExpressRoute-circuit wilt maken.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Maak een ExpressRoute-circuit.

   Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit inrichten door de connectiviteitsprovider. Als uw connectiviteitsprovider beheerde Laag-3-services biedt, kunt u de connectiviteitsprovider vragen om Microsoft-peering voor u in te schakelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteitsprovider echter niet routering voor u beheert, gaat u na het maken van het circuit verder met de volgende configuratiestappen. 

3. Controleer of het ExpressRoute-circuit is ingericht en ingeschakeld. Gebruik het volgende voorbeeld:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Het antwoord is vergelijkbaar met het volgende voorbeeld:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
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
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Configureer Microsoft-peering voor het circuit. Zorg ervoor dat u over de volgende informatie beschikt voordat u verdergaat.

   * Een /30- of /126-subnet voor de primaire link. Het adresblok moet een geldig openbaar IPv4- of IPv6-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR.
   * Een /30- of /126-subnet voor de secundaire link. Het adresblok moet een geldig openbaar IPv4- of IPv6-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.
   * Geadverteerde voorvoegsels: U verstrekt een lijst van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. U kunt een met komma's gescheiden lijst verzenden als u een set voorvoegsels wilt verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR. Voor IPv4 BGP-sessies zijn geadverteerde voorvoegsels voor IPv4 vereist en voor IPv6 BGP-sessies zijn geadverteerde voorvoegsels voor IPv6 vereist. 
   * Naam van routeringsregister: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd.
   * Optioneel:
     * Klant-ASN: Als u voorvoegsels adverteert die niet zijn geregistreerd op het AS-nummer van de peering, kunt u het AS-nummer opgeven waarbij ze zijn geregistreerd.
     * Een MD5-hash, als u er een wilt gebruiken.

> [!IMPORTANT]
> Microsoft controleert of de opgegeven geadverteerde openbare voorvoegsels en peer-ASN (of klant-ASN) aan u zijn toegewezen in het IRR (Internet Routing Registry). Als u de openbare voorvoegsels van een andere entiteit krijgt en de toewijzing niet is vastgelegd met het routeringsregister, wordt de automatische validatie niet voltooid en is handmatige validatie vereist. Als de automatische validatie mislukt, wordt voor 'AdvertisedPublicPrefixesState' als 'Validatie vereist' weergegeven bij de uitvoer van 'Get-AzExpressRouteCircuitPeeringConfig' (Zie 'De details van Microsoft-peering ophalen' in de volgende sectie). 
> 
> Als u het bericht 'Validatie vereist' ziet, verzamelt u de documenten met de openbare voorvoegsels die zijn toegewezen aan uw organisatie door de entiteit die wordt vermeld als de eigenaar van de voorvoegsels in het routeringsregister en verzendt u deze documenten voor handmatige validatie door een ondersteuningsticket te openen. 
> 

   Gebruik het volgende voorbeeld om Microsoft-peering voor uw circuit te configureren:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>De details van Microsoft-peering ophalen

Gebruik het volgende voorbeeld als u de configuratiegegevens wilt weergeven:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Configuratie van Microsoft-peering bijwerken

U kunt elk deel van de configuratie bijwerken met het volgende voorbeeld:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Persoonlijke Azure-peering

Deze sectie helpt u bij het maken, verkrijgen, bijwerken en verwijderen van de configuratie voor een persoonlijke Azure-peering voor een ExpressRoute-circuit.

> [!IMPORTANT]
> IPv6-ondersteuning voor persoonlijke peering is momenteel beschikbaar als **open bare preview**. 
> 
> 

### <a name="to-create-azure-private-peering"></a>Persoonlijke Azure-peering maken

1. Importeer de PowerShell-module voor ExpressRoute.

   Installeer het meest recente PowerShell-installatieprogramma uit [PowerShell Gallery](https://www.powershellgallery.com/). Importeer vervolgens de Azure Resource Manager-modules in de PowerShell-sessie om de ExpressRoute-cmdlets te kunnen gebruiken. U moet PowerShell uitvoeren als beheerder.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importeer alle Az.\*-modules binnen het bereik van de bekende semantische versie.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   U kunt ook gewoon een bepaalde module binnen het bereik van de bekende semantische versie importeren.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Meld u aan bij uw account.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Selecteer het abonnement waarmee u een ExpressRoute-circuit wilt maken.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Maak een ExpressRoute-circuit.

   Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit inrichten door de connectiviteitsprovider. Als uw connectiviteitsprovider beheerde Laag-3-services biedt, kunt u de connectiviteitsprovider vragen om persoonlijke Azure-peering voor u in te schakelen. In dat geval hoeft u de instructies in de volgende secties niet te volgen. Als uw connectiviteitsprovider echter niet routering voor u beheert, gaat u na het maken van het circuit verder met de volgende configuratiestappen.

3. Controleer of het ExpressRoute-circuit is ingericht en ingeschakeld. Gebruik het volgende voorbeeld:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Het antwoord is vergelijkbaar met het volgende voorbeeld:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
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
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Configureer persoonlijke Azure-peering voor het circuit. Zorg ervoor dat u de volgende items hebt voordat u verdergaat met de volgende stappen:

   * Een paar subnetten die geen deel uitmaken van een adres ruimte die is gereserveerd voor virtuele netwerken. Eén /30-subnet wordt gebruikt voor de primaire koppeling en het andere wordt gebruikt voor de secundaire koppeling. Vanuit deze subnetten wijst u het eerste bruikbare IP-adres toe aan uw router, aangezien Microsoft de tweede bruikbare IP voor de eigen router gebruikt. Er zijn drie opties voor dit paar subnetten:
       * IPv4: twee/30 subnetten.
       * IPv6: twee/126 subnetten.
       * Beide: twee/30 subnetten en twee/126 subnetten.
   * Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt.
   * AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken. U kunt een persoonlijk AS-nummer voor deze peering gebruiken. Gebruik niet 65515.
   * Optioneel:
     * Een MD5-hash, als u er een wilt gebruiken.

   Gebruik het volgende voorbeeld om persoonlijke Azure-peering voor uw circuit te configureren:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Als u een MD5-hash wilt gebruiken, gebruikt u het volgende voorbeeld:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6 -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Zorg dat u uw AS-nummer als peering-ASN opgeeft, niet als klant-ASN.
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>De details van persoonlijke Azure-peering weergeven

U kunt de configuratiegegevens weergeven met behulp van het volgende voorbeeld:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>De configuratie van persoonlijke Azure-peering bijwerken

U kunt elk deel van de configuratie bijwerken met het volgende voorbeeld. In dit voor beeld wordt de VLAN-ID van het circuit bijgewerkt van 200 naar 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 500

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft-peering verwijderen

U kunt een peeringconfiguratie verwijderen door de volgende cmdlet uit te voeren:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Persoonlijke Azure-peering verwijderen

U kunt een peeringconfiguratie verwijderen door het volgende voorbeeld uit te voeren:

> [!WARNING]
> Zorg ervoor dat alle virtuele netwerken en ExpressRoute Global Reach-verbindingen zijn verwijderd voordat u dit voorbeeld uitvoert. 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Volgende stappen

Nadat u persoonlijke Azure-peering hebt geconfigureerd, kunt u een ExpressRoute-gateway maken om een virtueel netwerk aan het circuit te koppelen. 

> [!div class="nextstepaction"]
> [Een virtuele netwerkgateway configureren voor ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
