---
title: 'Zelfstudie: Een VNet koppelen aan een ExpressRoute-circuit - Azure PowerShell'
description: Deze zelfstudie biedt een overzicht van hoe u VNet’s (virtuele netwerken) koppelt aan ExpressRoute-circuits met behulp van het Resource Manager-implementatiemodel en Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 69067ca34b231f1b14f8cc854288c3ed4c4ac82a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91855987"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit"></a>Zelfstudie: Een virtueel netwerk verbinden met een ExpressRoute-circuit
> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-CLI](howto-linkvnet-cli.md)
> * [Video: Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
>

Dit artikel helpt u bij het koppelen van VNet’s (virtuele netwerken) aan Azure ExpressRoute-circuits met behulp van het Resource Manager-implementatiemodel en PowerShell. Virtuele netwerken kunnen zich in hetzelfde abonnement bevinden of deel uitmaken van een ander abonnement. In dit artikel ziet u ook hoe u een koppeling naar een virtueel netwerk kunt bijwerken.

* U kunt tot 10 virtuele netwerken koppelen aan een standaard ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden wanneer een standaard ExpressRoute-circuit wordt gebruikt. 

* Eén VNet kan aan maximaal vier ExpressRoute-circuits worden gekoppeld. Gebruik de stappen in dit artikel om een nieuw verbindingsobject te maken voor elk ExpressRoute-circuit waarmee u verbinding maakt. De ExpressRoute-circuits kunnen zich in hetzelfde abonnement, in verschillende abonnementen of in een combinatie van beide bevinden.

* Als u de ExpressRoute Premium-invoegtoepassing inschakelt, kunt u virtuele netwerken koppelen die zich buiten de geopolitieke regio van het ExpressRoute-circuit bevinden. De Premium-invoegtoepassing biedt u ook de mogelijkheid om meer dan 10 virtuele netwerken te verbinden met uw ExpressRoute-circuit, afhankelijk van de gekozen bandbreedte. Raadpleeg de [Veelgestelde vragen](expressroute-faqs.md) voor meer informatie over de Premium-invoegtoepassing.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een virtueel netwerk in hetzelfde abonnement verbinden met een circuit
> - Een virtueel netwerk in een ander abonnement verbinden met een circuit
> - Een virtuele netwerkverbinding wijzigen
> - ExpressRoute FastPath configureren

## <a name="prerequisites"></a>Vereisten

* Bekijk de [vereisten](expressroute-prerequisites.md), de [routeringsvereisten](expressroute-routing.md) en de [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.

* U moet een actief ExpressRoute-circuit hebben. 
  * Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-arm.md) en laat het circuit inschakelen door de connectiviteitsprovider. 
  * Zorg ervoor dat u persoonlijke Azure-peering voor uw circuit hebt geconfigureerd. In het artikel [Routering configureren](expressroute-howto-routing-arm.md) vindt u instructies voor routering. 
  * Zorg ervoor dat persoonlijke Azure-peering is geconfigureerd en dat BGP-peering tussen uw netwerk en Microsoft wordt ingesteld voor end-to-end-connectiviteit.
  * Zorg ervoor dat u een virtueel netwerk en een virtuele netwerkgateway hebt gemaakt en volledig hebt ingericht. Volg de instructies om [een virtuele netwerkgateway voor ExpressRoute te maken](expressroute-howto-add-gateway-resource-manager.md). Een virtuele netwerkgateway voor ExpressRoute maakt gebruik van het GatewayType 'ExpressRoute', niet VPN.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Een virtueel netwerk in hetzelfde abonnement verbinden met een circuit
U kunt een virtueel netwerk met een ExpressRoute-circuit verbinden door de volgende cmdlet te gebruiken. Zorg ervoor dat de virtuele netwerkgateway is gemaakt en dat deze gereed is om te worden gekoppeld, voordat u de cmdlet uitvoert:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Een virtueel netwerk in een ander abonnement verbinden met een circuit
U kunt een ExpressRoute-circuit delen tussen meerdere abonnementen. De volgende afbeelding is een schematische weergave van hoe delen tussen meerdere abonnementen werkt voor ExpressRoute-circuits.

Elk van de kleinere clouds in de grote cloud staan voor abonnementen die tot verschillende afdelingen binnen een organisatie behoren. Elk van de afdelingen in de organisatie gebruikt een eigen abonnement voor het implementeren van hun services, maar ze kunnen één ExpressRoute-circuit delen om verbinding te maken terug naar uw on-premises netwerk. Eén afdeling (in dit voorbeeld: IT) kan eigenaar zijn van het ExpressRoute-circuit. Andere abonnementen binnen de organisatie kunnen gebruikmaken van het ExpressRoute-circuit.

> [!NOTE]
> Kosten voor connectiviteit en bandbreedte voor het ExpressRoute-circuit worden in rekening gebracht bij de eigenaar van het abonnement. Alle virtuele netwerken delen dezelfde bandbreedte.
> 

:::image type="content" source="./media/expressroute-howto-linkvnet-classic/cross-subscription.png" alt-text="Connectiviteit tussen abonnementen":::

### <a name="administration---circuit-owners-and-circuit-users"></a>Beheer: circuiteigenaars en circuitgebruikers

De 'circuiteigenaar' is een geautoriseerde hoofdgebruiker van de ExpressRoute-circuitresource. De circuiteigenaar kan autorisaties maken die kunnen worden ingewisseld door 'circuitgebruikers'. Circuitgebruikers zijn eigenaren van virtuele netwerkgateways die zich niet in hetzelfde abonnement bevinden als het ExpressRoute-circuit. Circuitgebruikers kunnen autorisaties inwisselen (één autorisatie per virtueel netwerk).

De circuiteigenaar heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Het intrekken van een autorisatie heeft tot gevolg dat alle koppelingsverbindingen uit het abonnement waarvan de toegangsrechten zijn ingetrokken, worden verwijderd.

### <a name="circuit-owner-operations"></a>Bewerkingen door circuiteigenaars

**Een autorisatie maken**

De circuiteigenaar maakt een autorisatie, waarmee een autorisatiesleutel wordt gemaakt die door een circuitgebruiker moet worden gebruikt om de virtuele netwerkgateways te verbinden met het ExpressRoute-circuit. Een autorisatie is slechts voor één verbinding geldig.

In het volgende cmdlet-fragment ziet u hoe u een autorisatie kunt maken:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```

Het antwoord op de vorige opdrachten bevatten de autorisatiesleutel en status:

```azurepowershell
Name                   : MyAuthorization1
Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
AuthorizationKey       : ####################################
AuthorizationUseStatus : Available
ProvisioningState      : Succeeded
```

**Autorisaties controleren**

De circuiteigenaar kan alle autorisaties controleren die voor een bepaald circuit worden uitgegeven, door de volgende cmdlet uit te voeren:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Autorisaties toevoegen**

De circuiteigenaar kan autorisaties toevoegen met behulp van de volgende cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Autorisaties verwijderen**

De circuiteigenaar kan autorisaties intrekken/verwijderen door de volgende cmdlet uit te voeren:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Bewerkingen door circuitgebruikers

De circuitgebruiker heeft de peer-id en een autorisatiesleutel nodig van de circuiteigenaar. De autorisatiesleutel is een GUID.

De peer-id kan worden gecontroleerd met de volgende opdracht:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Een autorisatie voor een verbinding inwisselen**

De circuitgebruiker kan de volgende cmdlet uitvoeren om een autorisatie voor een koppeling in te wisselen:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Een autorisatie voor een verbinding vrijgeven**

U kunt een autorisatie vrijgeven door de verbinding waarmee het ExpressRoute-circuit aan het virtuele netwerk wordt gekoppeld, te verwijderen.

## <a name="modify-a-virtual-network-connection"></a>Een virtuele netwerkverbinding wijzigen
U kunt bepaalde eigenschappen van een virtuele netwerkverbinding bijwerken. 

**Het gewicht van de verbinding bijwerken**

Uw virtuele netwerk kan worden verbonden met meerdere ExpressRoute-circuits. U kunt hetzelfde voorvoegsel van meer dan één ExpressRoute-circuit ontvangen. Als u wilt kiezen via welke verbinding verkeer voor dit voorvoegsel moet worden verzonden, kunt u *RoutingWeight* voor een verbinding wijzigen. Verkeer wordt verzonden via de verbinding met het hoogste *RoutingWeight*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Het bereik van *RoutingWeight* is 0 tot 32.000. De standaardwaarde is 0.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath configureren 
U kunt [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) inschakelen als uw virtuele netwerkgateway Ultra Performance of ErGw3AZ is. FastPath verbetert de prestaties van het gegevenspad, zoals pakketten per seconde en verbindingen per seconde tussen uw on-premises netwerk en het virtuele netwerk. 

**FastPath configureren voor een nieuwe verbinding**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Een bestaande verbinding bijwerken om FastPath te kunnen gebruiken**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="clean-up-resources"></a>Resources opschonen

Als u de ExpressRoute-verbinding niet meer nodig hebt, gebruikt u de opdracht `Remove-AzVirtualNetworkGatewayConnection` om de koppeling tussen de gateway en het circuit te verwijderen uit het abonnement waar de gateway zich bevindt.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection "MyConnection" -ResourceGroupName "MyRG"
```

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over ExpressRoute raadpleegt u de Veelgestelde vragen over ExpressRoute.

> [!div class="nextstepaction"]
> [Veelgestelde vragen ExpressRoute](expressroute-faqs.md)
