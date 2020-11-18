---
title: VPN-gatewayoverdracht configureren voor peering voor virtuele netwerken
description: Configureer de gateway-Transit voor peering van virtuele netwerken om naadloos twee virtuele netwerken van Azure met elkaar te verbinden voor connectiviteits doeleinden.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.tgt_pltfrm: na
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 3f90ccbc98dac957fcb9fb81cd24c7ae6c9ac664
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657547"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>VPN-gatewayoverdracht configureren voor peering voor virtuele netwerken

Dit artikel helpt u bij het configureren van gatewayoverdracht voor peering voor virtuele netwerken. [Peering voor virtuele netwerken](../virtual-network/virtual-network-peering-overview.md) maakt naadloos een verbinding tussen twee virtuele Azure-netwerken, waarbij de twee virtuele netwerken worden samengevoegd tot één netwerk voor connectiviteitsdoeleinden. [Gatewayoverdracht](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) is een peeringeigenschap die zorgt dat één virtueel netwerk de VPN-gateway in het als peer ingestelde virtuele netwerk kan gebruiken voor cross-premises- of VNet-naar-VNet-connectiviteit. Het volgende diagram toont hoe gatewayoverdracht werkt met peering voor virtuele netwerken.

![gatewayoverdracht](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

In het diagram zorgt gatewayoverdracht dat de als peer ingestelde virtuele netwerken de Azure VPN-gateway kunnen gebruiken in Hub-RM. De connectiviteit beschikbaar op de VPN-gateway, met inbegrip van de S2S-, P2S- en VNet-naar-VNet-verbindingen, geldt voor alle drie de virtuele netwerken. De overdrachtoptie is beschikbaar voor peering tussen de dezelfde of verschillende implementatiemodellen. De beperking is dat de VPN-gateway zich alleen mag bevinden in het virtuele netwerk met het Resource Manager-implementatiemodel, zoals wordt weergegeven in het diagram.

In de hub en spoke-netwerkarchitectuur zorgt gatewayoverdracht dat spoke virtuele netwerken de VPN-gateway in de hub kunnen delen, in plaats van VPN-gateways in elk spoke virtueel netwerk te moeten implementeren. Routes naar met de gateway verbonden virtuele netwerken of on-premises netwerken worden doorgegeven aan de routeringstabellen voor de als peer ingestelde virtuele netwerken met behulp van gatewayoverdracht. U kunt de automatische routedoorgifte van de VPN-gateway uitschakelen. Maak een routeringstabel met de optie **Doorgifte van BGP-route uitschakelen** en koppel de routeringstabel met de subnets om de routedistributie naar die subnetten te voorkomen. Zie [Tabel voor routering van virtuele netwerken](../virtual-network/manage-route-table.md) voor meer informatie.

In dit document worden twee scenario's beschreven:

1. Beide virtuele netwerken gebruiken het implementatiemodel van Resource Manager
2. Het spoke virtuele netwerk is Classic en het hub virtuele netwerk met de gateway is in de Resource Manager


>[!NOTE]
> Als u een wijziging aanbrengt in de topologie van uw netwerk en Windows VPN-clients hebt, moet het VPN-client pakket voor Windows-clients opnieuw worden gedownload en geïnstalleerd om de wijzigingen toe te passen op de client.
>

## <a name="requirements"></a>Vereisten



In het voorbeeld in dit document moeten de volgende resources worden gemaakt:

1. Hub-RM virtueel netwerk met een VPN-gateway
2. Spoke-RM virtueel netwerk
3. Spoke-Classic virtueel netwerk met het klassieke implementatiemodel
4. Het account dat u gebruikt moet de vereiste rollen en machtigingen hebben. Zie de sectie [Machtigingen](#permissions) van dit artikel voor meer informatie.

Raadpleeg de volgende documenten voor instructies:

1. [Een VPN-gateway in een virtueel netwerk maken](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
2. [Peering voor virtuele netwerken maken met hetzelfde implementatiemodel](../virtual-network/tutorial-connect-virtual-networks-portal.md)
3. [Peering voor virtuele netwerken maken met verschillende implementatiemodellen](../virtual-network/create-peering-different-deployment-models.md)

## <a name="permissions"></a><a name="permissions"></a>Machtigingen

De accounts die u gebruikt voor het maken van peering voor virtuele netwerken, moeten de benodigde rollen of machtigingen hebben. In het onderstaande voorbeeld, waar twee virtuele netwerken genaamd Hub-RM en Spoke-Classic als peer worden ingesteld, moet uw account de volgende rollen of machtigingen hebben voor elke virtueel netwerk:
    
|Virtueel netwerk|Implementatiemodel|Rol|Machtigingen|
|---|---|---|---|
|Hub-RM|Resource Manager|[Inzender voor netwerken](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klassiek|[Inzender voor klassieke netwerken](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N.v.t.|
|Spoke-Classic|Resource Manager|[Inzender voor netwerken](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klassiek|[Inzender voor klassieke netwerken](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Meer informatie over [ingebouwde rollen](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) en het toewijzen van specifieke machtigingen voor [aangepaste rollen](../role-based-access-control/custom-roles.md?toc=%252fazure%252fvirtual-network%252ftoc.json) (alleen Resource Manager).

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>Peering van Resource Manager naar Resource Manager met gatewayoverdracht

Volg de instructies voor het maken of bijwerken van peering voor virtuele netwerken om gatewayoverdracht in te schakelen.

1. Maak of wijzig de peering van Spoke-RM naar Hub-RM via de Azure Portal. Navigeer naar de virtuele netwerkresource Spoke-RM, klik op Peerings en vervolgens op Toevoegen:
    - Stel de optie Resource Manager in
    - Selecteer het virtuele netwerk Hub-RM in het desbetreffende abonnement
    - Zorg dat Toegang tot het virtuele netwerk toestaan is ingeschakeld
    - Stel de optie **Externe gateways gebruiken** in
    - Klik op OK

      ![spokerm-to-hubrm](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. Als peering al is gemaakt, gaat u naar de peeringresource en schakelt u **Externe gateways gebruiken** in (vergelijkbaar met de optie die wordt weergegeven in de schermafbeelding in stap 1)

3. Maak of wijzig de peering van Hub-RM naar Spoke-RM via de Azure Portal. Navigeer naar de virtuele netwerkresource Hub-RM, klik op Peerings en vervolgens op Toevoegen:
    - Stel de optie Resource Manager in
    - Zorg dat Toegang tot het virtuele netwerk toestaan is ingeschakeld
    - Selecteer het virtuele netwerk Spoke-RM in het desbetreffende abonnement
    - Stel de optie **Gatewayoverdracht toestaan** in
    - Klik op OK

      ![hubrm-to-spokerm](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. Als peering al is gemaakt, gaat u naar de peeringresource en schakelt u **Gatewayoverdracht toestaan** in (vergelijkbaar met de optie die wordt weergegeven in de schermafbeelding in stap 3)

5. Controleer of beide virtuele netwerken de status **Verbonden** hebben

### <a name="powershell-sample"></a>Voorbeeld van PowerShell

U kunt ook PowerShell gebruiken voor het maken of bijwerken van de peering in het bovenstaande voorbeeld. Vervang de variabelen door de namen van uw virtuele netwerken en resourcegroepen.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>Peering van Classic naar Resource Manager met gatewayoverdracht

De stappen zijn vergelijkbaar met de stappen in het voorbeeld van Resource Manager, behalve dat de bewerkingen alleen op het Hub-RM virtuele netwerk worden toegepast.

1. Maak of wijzig de peering van Hub-RM naar Spoke-RM via de Azure Portal. Navigeer naar de virtuele netwerkresource Hub-RM, klik op Peerings en vervolgens op Toevoegen:
   - Stel de optie Classic in voor het implementatiemodel van virtuele netwerken
   - Selecteer het virtuele netwerk Spoke-Classic in het desbetreffende abonnement
   - Zorg dat Toegang tot het virtuele netwerk toestaan is ingeschakeld
   - Stel de optie **Gatewayoverdracht toestaan** in
   - Klik op OK

     ![hubrm-to-spokeclassic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. Als peering al is gemaakt, gaat u naar de peeringresource en schakelt u **Gatewayoverdracht toestaan** in (vergelijkbaar met de optie die wordt weergegeven in de schermafbeelding in stap 1)

3. Er is geen bewerking op het virtuele netwerk Spoke-Classic

4. Controleer of het Hub-RM virtuele netwerk de status **Verbonden** heeft

Zodra de status 'Verbonden' aangeeft, kunnen de spoke virtuele netwerken starten met behulp van VNet-naar-VNet- of cross-premises-connectiviteit via de VPN-gateway in het hub virtuele netwerk.

### <a name="powershell-sample"></a>Voorbeeld van PowerShell

U kunt ook PowerShell gebruiken voor het maken of bijwerken van de peering in het bovenstaande voorbeeld. Vervang de variabelen en de abonnements-ID door de waarden van uw virtuele netwerk, resourcegroepen en abonnement. U hoeft alleen peering voor virtuele netwerken te maken op het hub virtuele netwerk.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg [beperkingen en gedrag van peering voor virtuele netwerken](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) en [instellingen voor peering voor virtuele netwerken](../virtual-network/virtual-network-manage-peering.md#create-a-peering) voordat u peering voor virtuele netwerken instelt voor gebruik in productie.
* Meer informatie over het [maken van een hub en spoke netwerktopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) met peering voor virtuele netwerken en gatewayoverdracht