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
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96010765"
---
|**VPN-<br>gateway-<br>generatie** |**SKU**   | **S2S-/VNet-naar-VNet-<br>tunnels** | **P2S<br> SSTP-verbindingen** | **P2S<br> IKEv2/OpenVPN-verbindingen** | **Benchmark cumulatieve<br>doorvoer** | **BGP** | **Zone-redundant** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generatie1**|**Basic**   | Met maximaal 10    | Met maximaal 128  | Niet ondersteund  | 100 Mbps  | Niet ondersteund| Nee |
|**Generatie1**|**VpnGw1**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 250       | 650 Mbps  | Ondersteund | Nee |
|**Generatie1**|**VpnGw2**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1 Gbps    | Ondersteund | Nee |
|**Generatie1**|**VpnGw3**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 1,25 Gbps | Ondersteund | Nee |
|**Generatie1**|**VpnGw1AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 250       | 650 Mbps  | Ondersteund | Ja |
|**Generatie1**|**VpnGw2AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1 Gbps    | Ondersteund | Ja |
|**Generatie1**|**VpnGw3AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 1,25 Gbps | Ondersteund | Ja |
|        |            |            |           |                |           |           |     |
|**Generatie2**|**VpnGw2**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1,25 Gbps | Ondersteund | Nee |
|**Generatie2**|**VpnGw3**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 2,5 Gbps  | Ondersteund | Nee |
|**Generatie2**|**VpnGw4**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 5000      | 5 Gbps    | Ondersteund | Nee |
|**Generatie2**|**VpnGw5**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 10.000      | 10 Gbps   | Ondersteund | Nee |
|**Generatie2**|**VpnGw2AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1,25 Gbps | Ondersteund | Ja |
|**Generatie2**|**VpnGw3AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 2,5 Gbps  | Ondersteund | Ja |
|**Generatie2**|**VpnGw4AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 5000      | 5 Gbps    | Ondersteund | Ja |
|**Generatie2**|**VpnGw5AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 10.000      | 10 Gbps   | Ondersteund | Ja |

(*) Gebruik [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) als u meer dan 30 S2S VPN-tunnels nodig hebt.

* Het wijzigen van de grootte van VpnGw-SKU's is toegestaan binnen dezelfde generatie, behalve het wijzigen van de grootte van de Basic-SKU. De Basic-SKU is een verouderde SKU waarvoor beperkingen in de functionaliteit gelden. Als u van de Basic-SKU wilt overstappen op een andere VpnGw-SKU, moet u de VPN-gateway van de Basic-SKU verwijderen en een nieuwe gateway maken met de gewenste combinatie van generatie en SKU-grootte.

* Er gelden afzonderlijke verbindingslimieten. U kunt bijvoorbeeld 128 SSTP-verbindingen en ook 250 IKEv2-verbindingen hebben in een SKU van het type VpnGw1.

* Prijsinformatie vindt u op de pagina [Prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Gegevens over de SLA (Service Level Agreement) vindt u op de pagina [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* In één tunnel kan een maximale doorvoer van 1 Gbps worden bereikt. De benchmark voor cumulatieve doorvoer in de bovenstaande tabel is gebaseerd op metingen van meerdere tunnels die via één gateway worden gecombineerd. De Benchmark cumulatieve doorvoer voor een VPN-gateway is gebaseerd op de combinatie S2S + P2S. **Als u veel P2S-verbindingen gebruikt, kan dit in verband met doorvoerbeperkingen een negatieve invloed hebben op S2S-verbindingen.** In verband met omstandigheden in het internetverkeer en het gedrag van uw toepassing geeft de Benchmark cumulatieve doorvoer geen gegarandeerde doorvoer aan.

We willen onze klanten graag helpen om inzicht te krijgen in de relatieve prestaties van SKU's die gebruikmaken van verschillende algoritmen. Daarom hebben we de prestaties gemeten met de openbaar beschikbare hulpprogramma's iPerf en CTSTraffic. De volgende tabel toont de resultaten van de prestatietests voor de VpnGw-SKU's van generatie 1. Zoals u kunt zien, worden de beste prestaties gerealiseerd bij gebruik van het GCMAES256-algoritme voor zowel IPsec-versleuteling als integriteit. Gemiddelde prestaties worden gerealiseerd bij gebruik van AES256 voor IPsec-versleuteling en SHA256 voor integriteit. De minst goede prestaties worden gerealiseerd bij gebruik van DES3 voor IPsec-versleuteling en SHA256 voor integriteit.

|**Generatie**|**SKU**   | **Gebruikte<br>algoritmen** | **Waargenomen<br>doorvoer** | **Waargenomen aantal<br>pakketten per seconde** |
|---           |---       | ---                 | ---            | ---                    |
|**Generatie1**|**VpnGw1**| GCMAES256<br>AES256 en SHA256<br>DES3 en SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50,000<br>50,000|
|**Generatie1**|**VpnGw2**| GCMAES256<br>AES256 en SHA256<br>DES3 en SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55.000|
|**Generatie1**|**VpnGw3**| GCMAES256<br>AES256 en SHA256<br>DES3 en SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90,000<br>60.000|
|**Generatie1**|**VpnGw1AZ**| GCMAES256<br>AES256 en SHA256<br>DES3 en SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50,000<br>50,000|
|**Generatie1**|**VpnGw2AZ**| GCMAES256<br>AES256 en SHA256<br>DES3 en SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55.000|
|**Generatie1**|**VpnGw3AZ**| GCMAES256<br>AES256 en SHA256<br>DES3 en SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90,000<br>60.000|
