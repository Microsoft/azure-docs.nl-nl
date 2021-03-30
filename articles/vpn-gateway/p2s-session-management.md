---
title: 'Azure VPN Gateway: beheer van punt-naar-site-VPN-sessie'
description: Dit artikel helpt u punt-naar-site-VPN-sessies weer te geven en te verbreken.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: cherylmc
ms.openlocfilehash: 2f2184507e17e3ecae40bb33be4202c183d32b77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91274230"
---
# <a name="point-to-site-vpn-session-management"></a>Beheer van punt-naar-site-VPN-sessie

Virtuele netwerk gateways van Azure bieden een eenvoudige manier om huidige punt-naar-site-VPN-sessies weer te geven en te verbreken. Dit artikel helpt u bij het weer geven en verbreken van huidige sessies.

>[!NOTE]
>De sessie status wordt elke vijf minuten bijgewerkt. Het wordt niet onmiddellijk bijgewerkt.
>

## <a name="portal"></a>Portal

Een sessie in de portal weer geven en verbreken:

1. Ga naar de VPN-gateway.
1. Onder de sectie **bewaking** selecteert u **punt-naar-site-sessies**.

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="Portal-voor beeld":::
1. U kunt alle huidige sessies weer geven in de Windowpane.
1. Selecteer **'... '** voor de sessie die u wilt verbreken en selecteer vervolgens **verbinding verbreken**.

## <a name="powershell"></a>PowerShell

Een sessie weer geven en verbreken met Power shell:

1. Voer de volgende Power shell-opdracht uit om actieve sessies weer te geven:

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. Kopieer de **VpnConnectionId** van de sessie die u wilt verbreken.

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="PowerShell-voorbeeld":::
1. Voer de volgende opdracht uit om de sessie te verbreken:

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>Volgende stappen

Zie [about Point-to-site VPN](point-to-site-about.md)(Engelstalig) voor meer informatie over punt-naar-site-verbindingen.
