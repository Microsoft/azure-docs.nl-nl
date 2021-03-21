---
title: 'Azure Virtual WAN: profielen voor gebruikers VPN-client'
description: Zo kunt u werken met het client profiel bestand
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980912"
---
# <a name="working-with-user-vpn-client-profile-files"></a>Werken met VPN-client profiel bestanden voor gebruikers

De profiel bestanden bevatten informatie die nodig is voor het configureren van een VPN-verbinding. Dit artikel helpt u bij het verkrijgen en begrijpen van de informatie die nodig is voor een VPN-client profiel voor gebruikers.

## <a name="download-the-profile"></a>Het profiel downloaden

U kunt de stappen in het artikel [Download profielen](global-hub-profile.md) gebruiken om het zip-bestand van het client profiel te downloaden.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* De **map openvpn** bevat het *ovpn* -profiel dat moet worden gewijzigd om de sleutel en het certificaat op te laten bevatten. Zie [Configure openvpn clients](../virtual-wan/howto-openvpn-clients.md#windows)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [een VPN-verbinding voor gebruikers maken](virtual-wan-point-to-site-portal.md)voor meer informatie over virtuele WAN-gebruikers-VPN-verbindingen.
