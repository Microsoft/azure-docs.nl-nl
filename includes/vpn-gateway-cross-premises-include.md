---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b283add6cff1400cc3141f4fba3f0f3939ee34aa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97665009"
---
|  | **Point-to-Site** | **Site-to-Site** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Door Azure ondersteunde services** |Cloudservices en virtuele machines |Cloudservices en virtuele machines |[Lijst met services](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Typische bandbreedten** |Gebaseerd op de gateway-SKU |Doorgaans < 1 Gbps gecombineerd |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps |
| **Ondersteunde protocollen** |Secure Sockets Tunneling Protocol (SSTP), OpenVPN en IPsec |IPsec |Directe verbinding via VLAN's, NSP’s VPN-technologieën (MPLS, VPLS,...) |
| **Routering** |RouteBased (dynamisch) |We ondersteunen PolicyBased (statische routering) en RouteBased (dynamische routering via VPN) |BGP |
| **Verbindingstolerantie** |actief-passief |actief-passief of actief-actief |actief-actief |
| **Typische gebruiksscenario's** |Toegang tot virtuele Azure-netwerken beveiligen voor externe gebruikers |Dev-/test-/labscenario's en kleinschalige tot middelgrote productieworkloads voor cloudservices en virtuele machines |Toegang tot alle Azure-services (gevalideerde lijst), workloads op bedrijfsniveau en bedrijfskritieke taken, back-up, Big Data, Azure als een DR-site |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Prijzen** |[Prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Prijzen](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Technische documentatie** |[VPN Gateway-documentatie](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[VPN Gateway-documentatie](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentatie voor ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **Veelgestelde vragen** |[Veelgestelde vragen VPN-gateways](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Veelgestelde vragen VPN-gateways](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Veelgestelde vragen ExpressRoute](../articles/expressroute/expressroute-faqs.md) |
