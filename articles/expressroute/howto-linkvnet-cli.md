---
title: 'Zelfstudie: Een VNet koppelen aan een ExpressRoute-circuit - Azure CLI'
description: Deze zelfstudie laat u zien hoe u virtuele netwerken (VNets) koppelt aan ExpressRoute-circuits met behulp van het Resource Manager-implementatiemodel en de Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: b9dda384e2ef30808559d10012dea2909b2af0fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206931"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Zelfstudie: Een virtueel netwerk verbinden aan een ExpressRoute-circuit met behulp van de CLI

Deze zelfstudie laat u zien hoe u virtuele netwerken (VNets) koppelt aan ExpressRoute-circuits met behulp van de Azure CLI. Als u met de Azure CLI een koppeling wilt maken moeten de virtuele netwerken zijn gemaakt met behulp van het Resource Manager-implementatiemodel. Deze kunnen zich in hetzelfde abonnement bevinden of deel uitmaken van een ander abonnement. Als u een andere methode wilt gebruiken om uw VNet te verbinden met een ExpressRoute-circuit, kunt u een artikel selecteren in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-CLI](howto-linkvnet-cli.md)
> * [Video: Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
> 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een virtueel netwerk in hetzelfde abonnement verbinden met een circuit
> - Een virtueel netwerk in een ander abonnement verbinden met een circuit
> - Een virtuele netwerkverbinding wijzigen
> - ExpressRoute FastPath configureren

## <a name="prerequisites"></a>Vereisten

* U hebt de nieuwste versie van de opdrachtregelinterface (CLI) nodig. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie.
* Bekijk de [vereisten](expressroute-prerequisites.md), de [routeringsvereisten](expressroute-routing.md) en de [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.
* U moet een actief ExpressRoute-circuit hebben. 
  * Volg de instructies voor het [maken van een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inschakelen door de connectiviteitsprovider. 
  * Zorg ervoor dat u persoonlijke Azure-peering voor uw circuit hebt geconfigureerd. In het artikel [Routering configureren](howto-routing-cli.md) vindt u instructies voor routering. 
  * Zorg ervoor dat persoonlijke Azure-peering is geconfigureerd. De BGP-peering tussen uw netwerk en Microsoft moet tot stand worden gebracht zodat u end-to-end connectiviteit kunt inschakelen.
  * Zorg ervoor dat u een virtueel netwerk en een virtuele netwerkgateway hebt gemaakt en volledig hebt ingericht. Volg de instructies om [een virtuele netwerkgateway voor ExpressRoute te configureren](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md). Zorg ervoor dat u `--gateway-type ExpressRoute` gebruikt.
* U kunt tot 10 virtuele netwerken koppelen aan een standaard ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden wanneer een standaard ExpressRoute-circuit wordt gebruikt. 
* Eén VNet kan aan maximaal vier ExpressRoute-circuits worden gekoppeld. Gebruik de volgende procedure om een nieuw verbindingsobject te maken voor elk ExpressRoute-circuit waarmee u verbinding maakt. De ExpressRoute-circuits kunnen zich in hetzelfde abonnement, in verschillende abonnementen of in een combinatie van beide bevinden.
* Als u de ExpressRoute Premium-invoegtoepassing inschakelt, kunt u virtuele netwerken koppelen die zich buiten de geopolitieke regio van het ExpressRoute-circuit bevinden. De Premium-invoegtoepassing biedt u ook de mogelijkheid om meer dan 10 virtuele netwerken te verbinden met uw ExpressRoute-circuit, afhankelijk van de gekozen bandbreedte. Raadpleeg de [Veelgestelde vragen](expressroute-faqs.md) voor meer informatie over de Premium-invoegtoepassing.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Een virtueel netwerk in hetzelfde abonnement verbinden met een circuit

U kunt een virtueel netwerk met een ExpressRoute-circuit verbinden door het voorbeeld te gebruiken. Zorg ervoor dat de virtuele netwerkgateway is gemaakt en dat deze gereed is om te worden gekoppeld voordat u de opdracht uitvoert.

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Een virtueel netwerk in een ander abonnement verbinden met een circuit

U kunt een ExpressRoute-circuit delen tussen meerdere abonnementen. De volgende afbeelding is een schematische weergave van hoe delen tussen meerdere abonnementen werkt voor ExpressRoute-circuits.

Elk van de kleinere clouds in de grote cloud staan voor abonnementen die tot verschillende afdelingen binnen een organisatie behoren. Elk van de afdelingen in de organisatie gebruikt een eigen abonnement voor het implementeren van hun services, maar ze kunnen één ExpressRoute-circuit delen om verbinding te maken terug naar uw on-premises netwerk. Eén afdeling (in dit voorbeeld: IT) kan eigenaar zijn van het ExpressRoute-circuit. Andere abonnementen binnen de organisatie kunnen gebruikmaken van het ExpressRoute-circuit.

> [!NOTE]
> Kosten voor connectiviteit- en bandbreedte voor het toegewezen circuit zijn in rekening gebracht bij de eigenaar van het ExpressRoute-circuit. Alle virtuele netwerken delen dezelfde bandbreedte.
> 
> 

![Connectiviteit tussen abonnementen](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Beheer: circuiteigenaars en circuitgebruikers

De 'circuiteigenaar' is een geautoriseerde hoofdgebruiker van de ExpressRoute-circuitresource. De circuiteigenaar kan autorisaties maken die kunnen worden ingewisseld door 'circuitgebruikers'. Circuitgebruikers zijn eigenaren van virtuele netwerkgateways die zich niet in hetzelfde abonnement bevinden als het ExpressRoute-circuit. Circuitgebruikers kunnen autorisaties inwisselen (één autorisatie per virtueel netwerk).

De circuiteigenaar heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Als een autorisatie wordt ingetrokken, worden alle koppelingsverbindingen verwijderd uit het abonnement waarvan de toegangsrechten zijn ingetrokken.

### <a name="circuit-owner-operations"></a>Bewerkingen door circuiteigenaars

**Een autorisatie maken**

De circuiteigenaar maakt een autorisatie, waarmee een autorisatiesleutel wordt gemaakt die door een circuitgebruiker moet worden gebruikt om de virtuele netwerkgateways te verbinden met het ExpressRoute-circuit. Een autorisatie is slechts voor één verbinding geldig.

In het volgende voorbeeld ziet u hoe u een autorisatie kunt maken:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Het antwoord bevat de autorisatiesleutel en -status:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Autorisaties controleren**

De circuiteigenaar kan alle autorisaties controleren die voor een bepaald circuit worden uitgegeven, door het volgende voorbeeld uit te voeren:

```azurecli-interactive
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Autorisaties toevoegen**

De circuiteigenaar kan autorisaties toevoegen met behulp van het volgende voorbeeld:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Autorisaties verwijderen**

De circuiteigenaar kan autorisaties intrekken/verwijderen door het volgende voorbeeld uit te voeren:

```azurecli-interactive
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Bewerkingen door circuitgebruikers

De circuitgebruiker heeft de peer-id en een autorisatiesleutel nodig van de circuiteigenaar. De autorisatiesleutel is een GUID.

```azurecli-interactive
az network express-route show -n MyCircuit -g ExpressRouteResourceGroup
```

**Een autorisatie voor een verbinding inwisselen**

De circuitgebruiker kan het volgende voorbeeld uitvoeren om een autorisatie voor een koppeling in te wisselen:

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Een autorisatie voor een verbinding vrijgeven**

U kunt een autorisatie vrijgeven door de verbinding waarmee het ExpressRoute-circuit aan het virtuele netwerk wordt gekoppeld, te verwijderen.

## <a name="modify-a-virtual-network-connection"></a>Een virtuele netwerkverbinding wijzigen
U kunt bepaalde eigenschappen van een virtuele netwerkverbinding bijwerken. 

**Het gewicht van de verbinding bijwerken**

Uw virtuele netwerk kan worden verbonden met meerdere ExpressRoute-circuits. U kunt hetzelfde voorvoegsel van meer dan één ExpressRoute-circuit ontvangen. Als u wilt kiezen via welke verbinding verkeer voor dit voorvoegsel moet worden verzonden, kunt u *RoutingWeight* voor een verbinding wijzigen. Verkeer wordt verzonden via de verbinding met het hoogste *RoutingWeight*.

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Het bereik van *RoutingWeight* is 0 tot 32.000. De standaardwaarde is 0.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath configureren 
U kunt [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) inschakelen als uw virtuele netwerkgateway Ultra Performance of ErGw3AZ is. FastPath verbetert de prestaties van het gegevenspad, zoals pakketten per seconde en verbindingen per seconde tussen uw on-premises netwerk en het virtuele netwerk. 

**FastPath configureren voor een nieuwe verbinding**

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Een bestaande verbinding bijwerken om FastPath te kunnen gebruiken**

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```
## <a name="clean-up-resources"></a>Resources opschonen

Als u de ExpressRoute-verbinding niet meer nodig hebt, gebruikt u de opdracht `az network vpn-connection delete` om de koppeling tussen de gateway en het circuit te verwijderen uit het abonnement waar de gateway zich bevindt.

```azurecli-interactive
az network vpn-connection delete --name ERConnection --resource-group ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een virtueel netwerk verbindt met een circuit in hetzelfde abonnement en in een ander abonnement. Zie 

> [!div class="nextstepaction"]
> [Info over virtuele ExpressRoute-netwerkgateways](expressroute-about-virtual-network-gateways.md) voor meer informatie over de virtuele ExpressRoute-netwerkgateway