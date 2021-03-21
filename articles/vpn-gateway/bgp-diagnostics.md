---
title: BGP-status en-metrische gegevens weer geven
titleSuffix: Azure VPN Gateway
description: Belang rijke informatie over BGP weer geven voor het oplossen van problemen.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: sample
ms.date: 03/10/2021
ms.author: alzam
ms.openlocfilehash: f97bbccc980705699af822ba2730233239cdfd5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103148799"
---
# <a name="view-bgp-metrics-and-status"></a>BGP-metrische gegevens en-status weer geven

U kunt de BGP-metrische gegevens en-status weer geven met behulp van de Azure Portal of door gebruik te maken van Azure PowerShell.

## <a name="azure-portal"></a>Azure Portal

In de Azure Portal kunt u BGP-peers, geleerde routes en geadverteerde routes weer geven. U kunt ook CSV-bestanden met deze gegevens downloaden.

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar de gateway van uw virtuele netwerk.
1. Onder **bewaking** selecteert u **BGP-peers** om de pagina BGP-peers te openen.

   :::image type="content" source="./media/bgp-diagnostics/bgp-portal.jpg" alt-text="Scherm opname van metrische gegevens in de Azure Portal.":::

### <a name="learned-routes"></a>Geleerde routes

1. U kunt Maxi maal 50 geleerde routes weer geven in de portal.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes.jpg" alt-text="Scherm opname van geleerde routes.":::

1. U kunt ook het geleerde routes-bestand downloaden. Als u meer dan 50 geleerde routes hebt, is de enige manier om al deze te bekijken, het CSV-bestand te downloaden en weer te geven. Selecteer **geleerde routes downloaden** om deze te downloaden.

   :::image type="content" source="./media/bgp-diagnostics/download-routes.jpg" alt-text="Scherm opname van het downloaden van geleerde routes.":::
1. Bekijk vervolgens het bestand.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes-file.jpg" alt-text="Scherm opname van gedownloade geleerde routes.":::

### <a name="advertised-routes"></a>Aangekondigde routes

1. Als u aangekondigde routes wilt weer geven, selecteert u de **..** . aan het einde van het netwerk dat u wilt weer geven en klikt u vervolgens op **aangekondigde routes weer geven**.

   :::image type="content" source="./media/bgp-diagnostics/select-route.png" alt-text="Scherm afbeelding die laat zien hoe aangekondigde routes worden weer gegeven." lightbox="./media/bgp-diagnostics/route-expand.png":::
1. Op de pagina **routes die zijn geadverteerd naar peer** kunt u maxi maal 50 geadverteerde routes weer geven.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes.jpg" alt-text="Scherm opname van geadverteerde routes.":::
1. U kunt ook het geadverteerde routes-bestand downloaden. Als u meer dan 50 geadverteerde routes hebt, is de enige manier om al deze te bekijken, het CSV-bestand te downloaden en weer te geven. Selecteer **geadverteerde routes downloaden** om deze te downloaden.

   :::image type="content" source="./media/bgp-diagnostics/download-advertised.jpg" alt-text="Scherm afbeelding van gedownloade geadverteerde routes selecteren.":::
1. Bekijk vervolgens het bestand.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes-file.jpg" alt-text="Scherm opname van gedownloade geadverteerde routes.":::

### <a name="bgp-peers"></a>BGP-peers

1. U kunt Maxi maal 50 BGP-peers weer geven in de portal.

   :::image type="content" source="./media/bgp-diagnostics/peers.jpg" alt-text="Scherm opname van BGP-peers.":::
1. U kunt ook het BGP-peers-bestand downloaden. Als u meer dan 50 BGP-peers hebt, is de enige manier om al deze weer te geven, door het CSV-bestand te downloaden en weer te geven. Selecteer **BGP-peers downloaden** op de portal pagina om te downloaden.

   :::image type="content" source="./media/bgp-diagnostics/download-peers.jpg" alt-text="Scherm opname van het downloaden van BGP-peers.":::
1. Bekijk vervolgens het bestand.

   :::image type="content" source="./media/bgp-diagnostics/peers-file.jpg" alt-text="Scherm opname van gedownloade BGP-peers.":::

## <a name="powershell"></a>PowerShell

Gebruik **Get-AzVirtualNetworkGatewayBGPPeerStatus** om alle BGP-peers en de status weer te geven.

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

Gebruik **Get-AzVirtualNetworkGatewayLearnedRoute** om alle routes weer te geven die de gateway via BGP heeft geleerd.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

Gebruik **Get-AzVirtualNetworkGatewayAdvertisedRoute** om alle routes weer te geven die de gateway adverteert naar de bijbehorende peers via BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="next-steps"></a>Volgende stappen

Zie [BGP configureren voor VPN gateway](bgp-howto.md)voor meer informatie over BGP.
