---
title: OpenVPN configureren voor Azure VPN Gateway
description: Meer informatie over het inschakelen van OpenVPN-protocol op Azure VPN Gateway voor een punt-naar-site-omgeving.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 137e4e1372ef1af3319c0b9af7ba965fffcb9e34
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584037"
---
# <a name="configure-openvpn-for-point-to-site-vpn-gateways"></a>OpenVPN configureren voor punt-naar-site-VPN-gateways

Dit artikel helpt u bij het instellen van **OpenVPN®-protocol** op Azure VPN gateway. U kunt de portal of de Power shell-instructies gebruiken.

## <a name="prerequisites"></a>Vereisten

* In dit artikel wordt ervan uitgegaan dat u al een werk punt-naar-site-omgeving hebt. Als u dit niet doet, maakt u er een met een van de volgende methoden.

  * [Portal: punt-naar-site maken](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [Power shell: punt-naar-site maken](vpn-gateway-howto-point-to-site-rm-ps.md)

* Controleer of de VPN-gateway geen gebruik maakt van de basis-SKU. De basis-SKU wordt niet ondersteund voor OpenVPN.

## <a name="portal"></a>Portal

1. Navigeer in de portal naar de gateway van het **virtuele netwerk > punt-naar-site-configuratie**.
1. Selecteer voor **Tunnel Type** **openvpn (SSL)** in de vervolg keuzelijst.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="OpenVPN SSL selecteren in de vervolg keuzelijst":::
1. Sla de wijzigingen op en ga door met de **volgende stappen**.

## <a name="powershell"></a>PowerShell

1. Schakel OpenVPN in op uw gateway met behulp van het volgende voor beeld:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Ga door met de **volgende stappen**.

## <a name="next-steps"></a>Volgende stappen

Zie [openvpn-clients configureren](vpn-gateway-howto-openvpn-clients.md)voor meer informatie over het configureren van clients voor openvpn.

**' OpenVPN ' is een handels merk van OpenVPN Inc.**
