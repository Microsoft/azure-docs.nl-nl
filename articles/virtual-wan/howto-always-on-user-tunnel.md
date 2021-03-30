---
title: Een Always-On VPN-gebruikers tunnel configureren
titleSuffix: Azure Virtual WAN
description: In dit artikel wordt beschreven hoe u een always on-VPN-gebruikers tunnel configureert voor uw virtuele WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e83ca64d2b0e50ec02007a3cd878e6bf034d0961
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313583"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Een always on-VPN-gebruikers tunnel configureren voor virtuele WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Vereisten

U moet een punt-naar-site-configuratie maken en de toewijzing van de virtuele hub bewerken. Raadpleeg de volgende secties voor instructies:

* [Een P2S-configuratie maken](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Hub maken met P2S-gateway](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-a-user-tunnel"></a>Een gebruikers tunnel configureren

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Een profiel verwijderen

Als u een profiel wilt verwijderen, gebruikt u de volgende stappen:

1. Voer de volgende opdracht uit:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Verbreek de verbinding en schakel het selectie vakje **automatisch verbinding maken** uit.

   ![Opschonen](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
