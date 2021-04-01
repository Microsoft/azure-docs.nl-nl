---
title: OpenVPN-clients configureren voor Azure VPN Gateway | Microsoft Docs
description: Meer informatie over het configureren van OpenVPN voor Azure VPN Gateway voor clients met Windows, Linux en Mac-besturings systemen.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4be903fa17ce95e96c82241249b421e1d794c80f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89435776"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>OpenVPN-clients configureren voor Azure VPN Gateway

Dit artikel helpt u bij het configureren van **openvpn- &reg; protocol** -clients.

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u de stappen hebt voltooid voor het configureren van OpenVPN voor uw VPN-gateway. Zie [Configure openvpn for Azure VPN gateway](vpn-gateway-howto-openvpn.md)voor meer informatie.

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Volgende stappen

Als u wilt dat de VPN-clients toegang kunnen krijgen tot bronnen in een ander VNet, volgt u de instructies in het artikel [vnet-naar-vnet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) om een vnet-naar-vnet-verbinding in te stellen. Zorg ervoor dat BGP op de gateways en de verbindingen inschakelt, anders wordt het verkeer niet stroomt.

**' OpenVPN ' is een handels merk van OpenVPN Inc.**
