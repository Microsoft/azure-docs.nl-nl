---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010806"
---
Wanneer u een virtuele netwerkgateway maakt, moet u de gewenste gateway-SKU opgeven. Selecteer de SKU die aan uw vereisten voldoet op basis van de typen werkbelasting, doorvoer, functies en SLA's. Raadpleeg [Gateway-SKU's van Azure-beschikbaarheidszones](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md) voor virtuele netwerkgateway-SKU's in Azure-beschikbaarheidszones.

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Gateway-SKU's per tunnel, verbinding en doorvoer

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> VpnGw-SKU's (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 en VpnGw5AZ) worden alleen ondersteund voor het Resource Manager-implementatiemodel. Voor klassieke virtuele netwerken moeten de oude (verouderde) SKU's nog altijd worden gebruikt.
>  * Raadpleeg [Werken met VPN-gateway-SKU's (verouderde SKU's)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md) voor informatie over het werken met de verouderde gateway-SKU's (Basic, Standard en HighPerformance).
>  * Raadpleeg [Virtuel Network Gateways voor ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) voor ExpressRoute-gateway-SKU's.
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>Gateway-SKU's per functieset

De nieuwe VPN-gateway-SKU's stroomlijnen de functiesets die worden aangeboden op de gateways:

| **SKU**| **Functies**|
| ---    | ---         |
|**Basic** (**)   | **VPN op basis van route**: 10 tunnels voor S2S/verbindingen; geen RADIUS-verificatie voor P2S; geen IKEv2 voor P2S<br>**VPN op basis van beleid**: (IKEv1): 1 S2S/verbindingstunnel; geen P2S|
| **Alle Generation1- en Generation2-SKU's, behalve Basic** | **Op route gebaseerd VPN**: maximaal 30 tunnels (*), P2S, BGP, actief-actief, aangepast IPsec/IKE-beleid, ExpressRoute/VPN samen |
|        |             |

(*) U kunt "PolicyBasedTrafficSelectors" configureren om een op route gebaseerde VPN-gateway te verbinden met meerdere on-premises, op beleid gebaseerde firewallapparaten. Raadpleeg [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (VPN-gateways verbinden met meerdere on-premises,op beleid gebaseerde VPN-apparaten met behulp van PowerShell) voor meer informatie.

(\*\*) De Basic-SKU wordt beschouwd als verouderd. Deze SKU kent bepaalde functiebeperkingen. Zo kunt u een gateway die gebruikmaakt van een Basic-SKU, niet overzetten naar een van de nieuwe gateway-SKU's. In plaats daarvan moet u een nieuwe SKU gebruiken, wat betekent dat u uw VPN-gateway eerst moet verwijderen en vervolgens opnieuw moet maken.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>Gateway-SKU's - Productie versus werkbelastingen voor ontwikkelen en testen

Vanwege de verschillen in SLA's en functiesets, raden we de volgende SKU's aan voor productie vs. ontwikkelen en testen:

| **Workload**                       | **SKU's**               |
| ---                                | ---                    |
| **Productie, kritieke werkbelastingen** | Alle Generation1- en Generation2-SKU's, behalve Basic |
| **Ontwikkelen en testen of conceptontwerpen**   | Basic (**)                 |
|                                    |                        |

(\*\*) De Basic-SKU wordt beschouwd als een verouderde SKU waarvoor beperkingen in de functionaliteit gelden. Controleer of de functie die u nodig hebt, wordt ondersteund voordat u kiest voor de Basic-SKU.

Als u de oude SKU's (verouderd) gebruikt, zijn de aanbevelingen voor de productie-SKU Standard en HighPerformance. Raadpleeg [Gateway-SKU's (verouderd)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md) voor meer informatie en instructies voor oude SKU's.
