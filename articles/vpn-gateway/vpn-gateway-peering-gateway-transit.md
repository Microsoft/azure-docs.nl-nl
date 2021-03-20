---
title: VPN-gatewayoverdracht configureren voor peering voor virtuele netwerken
description: Configureer de gateway-Transit voor peering van virtuele netwerken om naadloos twee virtuele netwerken van Azure met elkaar te verbinden voor connectiviteits doeleinden.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc
ms.openlocfilehash: 73a7d76de34d29b2d51c54569b234cd8221b08f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98872176"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>VPN-gatewayoverdracht configureren voor peering voor virtuele netwerken

Dit artikel helpt u bij het configureren van gatewayoverdracht voor peering voor virtuele netwerken. [Peering voor virtuele netwerken](../virtual-network/virtual-network-peering-overview.md) maakt naadloos een verbinding tussen twee virtuele Azure-netwerken, waarbij de twee virtuele netwerken worden samengevoegd tot één netwerk voor connectiviteitsdoeleinden. [Gateway-door Voer](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) is een peering-eigenschap waarmee één virtueel netwerk de VPN-gateway in het gekoppelde virtuele netwerk gebruikt voor cross-premises of vnet-naar-vnet-connectiviteit. Het volgende diagram toont hoe gatewayoverdracht werkt met peering voor virtuele netwerken.

![Transport diagram van de gateway](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

In het diagram zorgt gatewayoverdracht dat de als peer ingestelde virtuele netwerken de Azure VPN-gateway kunnen gebruiken in Hub-RM. De connectiviteit beschikbaar op de VPN-gateway, met inbegrip van de S2S-, P2S- en VNet-naar-VNet-verbindingen, geldt voor alle drie de virtuele netwerken. De optie door Voer is beschikbaar voor peering tussen dezelfde of verschillende implementatie modellen. Als u door Voer configureert tussen verschillende implementatie modellen, moet het virtuele netwerk van de hub en de gateway van het virtuele netwerk zich in het Resource Manager-implementatie model bevinden, niet op het klassieke implementatie model.
>

In de hub en spoke-netwerkarchitectuur zorgt gatewayoverdracht dat spoke virtuele netwerken de VPN-gateway in de hub kunnen delen, in plaats van VPN-gateways in elk spoke virtueel netwerk te moeten implementeren. Routes naar met de gateway verbonden virtuele netwerken of on-premises netwerken worden doorgegeven aan de routeringstabellen voor de als peer ingestelde virtuele netwerken met behulp van gatewayoverdracht. U kunt de automatische routedoorgifte van de VPN-gateway uitschakelen. Maak een routeringstabel met de optie **Doorgifte van BGP-route uitschakelen** en koppel de routeringstabel met de subnets om de routedistributie naar die subnetten te voorkomen. Zie [Tabel voor routering van virtuele netwerken](../virtual-network/manage-route-table.md) voor meer informatie.

Dit artikel bevat twee scenario's:

* **Hetzelfde implementatie model**: beide virtuele netwerken worden gemaakt in het Resource Manager-implementatie model.
* **Verschillende implementatie modellen**: het virtuele spoke-netwerk wordt gemaakt in het klassieke implementatie model en het virtuele netwerk en de gateway van de hub bevinden zich in het Resource Manager-implementatie model.

>[!NOTE]
> Als u een wijziging aanbrengt in de topologie van uw netwerk en Windows VPN-clients hebt, moet het VPN-client pakket voor Windows-clients opnieuw worden gedownload en geïnstalleerd om de wijzigingen toe te passen op de client.
>

## <a name="prerequisites"></a>Vereisten

Controleer voordat u begint of u de volgende virtuele netwerken en machtigingen hebt:

### <a name="virtual-networks"></a><a name="vnet"></a>Virtuele netwerken

|VNet|Implementatiemodel| Gateway van een virtueel netwerk|
|---|---|---|---|
| Hub-RM| [Resource Manager](./tutorial-site-to-site-portal.md)| [Ja](tutorial-create-gateway-portal.md)|
| Spoke-RM | [Resource Manager](./tutorial-site-to-site-portal.md)| Nee |
| Spoke-Classic | [Klassieke](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | Nee |

### <a name="permissions"></a><a name="permissions"></a>Machtigingen

De accounts die u gebruikt voor het maken van peering voor virtuele netwerken, moeten de benodigde rollen of machtigingen hebben. Als u in het onderstaande voor beeld de twee virtuele netwerken met de naam **hub-RM** en **spoke-Classic** hebt gepeerd, moet uw account beschikken over de volgende rollen of machtigingen voor elk virtueel netwerk:

|VNet|Implementatiemodel|Rol|Machtigingen|
|---|---|---|---|
|Hub-RM|Resource Manager|[Inzender voor netwerken](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klassiek|[Inzender voor klassieke netwerken](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N.v.t.|
|Spoke-Classic|Resource Manager|[Inzender voor netwerken](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klassiek|[Inzender voor klassieke netwerken](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Meer informatie over [ingebouwde rollen](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) en het toewijzen van specifieke machtigingen voor [aangepaste rollen](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (alleen Resource Manager).

## <a name="same-deployment-model"></a><a name="same"></a>Hetzelfde implementatie model

In dit scenario zijn de virtuele netwerken beide in het Resource Manager-implementatie model. Voer de volgende stappen uit om de peering van het virtuele netwerk te maken of bij te werken om Gateway doorvoer in te scha kelen.

### <a name="to-add-a-peering-and-enable-transit"></a>Een peering toevoegen en door Voer inschakelen

1. In de [Azure Portal](https://portal.azure.com)maakt of werkt u de peering van het virtuele netwerk vanuit de hub-RM. Navigeer naar het virtuele **hub-RM-** netwerk. Selecteer **peerings** en vervolgens **+ toevoegen** om **peering toevoegen** te openen.
1. Configureer op de pagina **peering toevoegen** de waarden voor **dit virtuele netwerk**.

   * Naam van peering-koppeling: Geef de koppeling een naam. Voor beeld: **HubRMToSpokeRM**
   * Verkeer naar extern virtueel netwerk: **toestaan**
   * Verkeer dat wordt doorgestuurd vanuit extern virtueel netwerk: **toestaan**
   * Gateway van virtueel netwerk: **Gebruik de gateway van dit virtuele netwerk**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="Scherm opname toont het toevoegen van peering.":::

1. Ga op dezelfde pagina verder met om de waarden voor het **externe virtuele netwerk** te configureren.

   * Naam van peering-koppeling: Geef de koppeling een naam. Voor beeld: **SpokeRMtoHubRM**
   * Implementatie model: **Resource Manager**
   * Virtual Network: **spoke-RM**
   * Verkeer naar extern virtueel netwerk: **toestaan**
   * Verkeer dat wordt doorgestuurd vanuit extern virtueel netwerk: **toestaan**
   * Gateway van virtueel netwerk: **Gebruik de gateway van het externe virtuele netwerk**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="Scherm opname toont waarden voor extern virtueel netwerk.":::

1. Selecteer **toevoegen** om de peering te maken.
1. Controleer de status van de peering als **verbonden** op beide virtuele netwerken.

### <a name="to-modify-an-existing-peering-for-transit"></a>Een bestaande peering voor door Voer wijzigen

Als de peering al is gemaakt, kunt u de peering voor door Voer wijzigen.

1. Navigeer naar het virtuele netwerk. Selecteer **peerings** en selecteer de peering die u wilt wijzigen.

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="Scherm opname toont het selecteren van peerings.":::

1. Werk de VNet-peering bij.

   * Verkeer naar extern virtueel netwerk: **toestaan**
   * Verkeer dat wordt doorgestuurd naar het virtuele netwerk; **Toestaan**
   * Gateway van virtueel netwerk: **de gateway van het externe virtuele netwerk gebruiken**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="Scherm afbeelding toont peering-gateway wijzigen.":::

1. **Sla** de peering-instellingen op.

### <a name="powershell-sample"></a><a name="ps-same"></a>Voorbeeld van PowerShell

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

## <a name="different-deployment-models"></a><a name="different"></a>Verschillende implementatie modellen

In deze configuratie bevindt de spoke VNet **-spoke-klassiek** zich in het klassieke implementatie model en de hub Vnet **-hub-RM** bevindt zich in het Resource Manager-implementatie model. Bij het configureren van de overdracht tussen implementatie modellen moet de gateway van het virtuele netwerk worden geconfigureerd voor de Resource Manager VNet, niet op het klassieke VNet.

Voor deze configuratie hoeft u alleen het virtuele **hub-RM-** netwerk te configureren. U hoeft niets te configureren op het **spoke-klassiek** VNet.

1. Ga in het Azure Portal naar het virtuele netwerk **hub-RM** , selecteer **peerings** en selecteer **+ toevoegen**.
1. Configureer op de pagina **peering toevoegen** de volgende waarden:

   * Naam van peering-koppeling: Geef de koppeling een naam. Voor beeld: **HubRMToClassic**
   * Verkeer naar extern virtueel netwerk: **toestaan**
   * Verkeer dat wordt doorgestuurd vanuit extern virtueel netwerk: **toestaan**
   * Gateway van virtueel netwerk: **Gebruik de gateway van dit virtuele netwerk**
   * Extern virtueel netwerk: **klassiek**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="Peering-pagina toevoegen voor spoke-klassiek":::

1. Controleer of het abonnement juist is en selecteer vervolgens het virtuele netwerk in de vervolg keuzelijst.
1. Selecteer **toevoegen** om de peering toe te voegen.
1. Controleer de status van de peering als **verbonden** op het virtuele hub-RM-netwerk. 

Voor deze configuratie hoeft u niets te configureren op het virtuele **spoke-klassiek-** netwerk. Zodra de status **verbonden is**, kan het virtuele spoke-netwerk gebruikmaken van de connectiviteit via de VPN-gateway in het virtuele hub-netwerk.

### <a name="powershell-sample"></a><a name="ps-different"></a>Voorbeeld van PowerShell

U kunt ook PowerShell gebruiken voor het maken of bijwerken van de peering in het bovenstaande voorbeeld. Vervang de variabelen en de abonnements-ID door de waarden van uw virtuele netwerk, resourcegroepen en abonnement. U hoeft alleen peering voor virtuele netwerken te maken op het hub virtuele netwerk.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg [beperkingen en gedrag van peering voor virtuele netwerken](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) en [instellingen voor peering voor virtuele netwerken](../virtual-network/virtual-network-manage-peering.md#create-a-peering) voordat u peering voor virtuele netwerken instelt voor gebruik in productie.
* Meer informatie over het [maken van een hub en spoke netwerktopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) met peering voor virtuele netwerken en gatewayoverdracht
* [Maak een peering voor het virtuele netwerk met hetzelfde implementatie model](../virtual-network/tutorial-connect-virtual-networks-portal.md).
* [Maak peering voor het virtuele netwerk met verschillende implementatie modellen](../virtual-network/create-peering-different-deployment-models.md).