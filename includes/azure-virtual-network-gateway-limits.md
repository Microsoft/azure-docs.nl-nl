---
title: bestand opnemen
description: bestand opnemen
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 9fe9ef5549ced3b73d18d553fa0b62ec019684fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95555240"
---
| Resource                                | Limiet        |
|-----------------------------------------|------------------------------|
| VNet-adres voorvoegsels                   | 600 per VPN-gateway          |
| Aggregatie van BGP-routes                    | 4.000 per VPN-gateway        |
| Adres voorvoegsels van het lokale netwerk gateway  | 1000 per lokale netwerk gateway               |
| S2S-verbindingen                         | [Is afhankelijk van de gateway-SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)|
| P2S-verbindingen                         | [Is afhankelijk van de gateway-SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |
| P2S-route limiet-IKEv2                 | 256 voor niet-Windows **/** 25 voor Windows           |
| P2S-route limiet-OpenVPN               | 1000                         |
| Met maximaal flows                              | 100.000 voor VpnGw1/AZ  **/**  512k voor VpnGw2-4/AZ|