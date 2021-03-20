---
title: Een Always-On VPN-tunnel configureren
titleSuffix: Azure Virtual WAN
description: Stappen voor het configureren van de tunnel voor altijd op VPN-apparaten voor virtuele WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e814487cb4dab9c8c19daab2ea3bb81391d4a98f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90983680"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Een tunnel voor altijd op VPN-apparaat configureren voor virtuele WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Vereisten

U moet een punt-naar-site-configuratie maken en de toewijzing van de virtuele hub bewerken. Raadpleeg de volgende secties voor instructies:

* [Een P2S-configuratie maken](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Hub maken met P2S-gateway](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>De tunnel van het apparaat configureren

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Een profiel verwijderen

Als u het profiel wilt verwijderen, voert u de volgende opdracht uit:

![Scherm afbeelding toont een Power shell-venster met de opdracht Remove-VpnConnection naam MachineCertTest.](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.