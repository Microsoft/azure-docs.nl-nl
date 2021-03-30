---
title: Een Always-On VPN-tunnel configureren
titleSuffix: Azure VPN Gateway
description: Meer informatie over het gebruik van gateways met Windows 10 altijd aan om permanente tunnels in te stellen en te configureren in Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7688e42175b2b4e35b63979f5df25702f3bb869d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90986584"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Een AlwaysOn-VPN-apparaattunnel configureren

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>De gateway configureren

Configureer de VPN-gateway voor het gebruik van IKEv2 en verificatie op basis van certificaten met behulp van het artikel [een punt-naar-site-VPN-verbinding configureren](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="configure-the-device-tunnel"></a>De tunnel van het apparaat configureren

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Een profiel verwijderen

Als u het profiel wilt verwijderen, voert u de volgende opdracht uit:

![Scherm afbeelding toont een Power shell-venster met de opdracht Remove-VpnConnection naam MachineCertTest.](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met Azure Point-to-site-verbindingen](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) voor probleem oplossing
