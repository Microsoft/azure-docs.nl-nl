---
title: Een Always-On VPN-gebruikers tunnel configureren
titleSuffix: Azure VPN Gateway
description: In dit artikel wordt beschreven hoe u een always on-VPN-gebruikers tunnel configureert voor uw VPN-gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 99d21222a62ed6b27a6a1b2a73b704f4cb26457b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89435793"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Een AlwaysOn-VPN-gebruikerstunnel configureren

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>De gateway configureren

 Gebruik de instructies in het artikel [een punt-naar-site-VPN-verbinding configureren](vpn-gateway-howto-point-to-site-resource-manager-portal.md) om de VPN-gateway te configureren voor het gebruik van IKEv2 en verificatie op basis van een certificaat.

## <a name="configure-a-user-tunnel"></a>Een gebruikers tunnel configureren

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Een profiel verwijderen

Als u een profiel wilt verwijderen, gebruikt u de volgende stappen:

1. Voer de volgende opdracht uit:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Verbreek de verbinding en schakel het selectie vakje **automatisch verbinding maken** uit.

   ![Opschonen](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met Azure Point-to-site-verbindingen](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)voor informatie over het oplossen van verbindings problemen die zich kunnen voordoen.
