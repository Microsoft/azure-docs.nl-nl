---
title: Het voor voegsel van een openbaar IPv6-adres in een virtueel Azure-netwerk
titlesuffix: Azure Virtual Network
description: Meer informatie over het voor voegsel van een openbaar IPv6-adres in het virtuele netwerk van Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 3e3d3c01ba1190871da39ce27de6a95952d09be1
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538203"
---
# <a name="reserved-public-ipv6-address-prefix"></a>Voor voegsel van gereserveerd openbaar IPv6-adres

Virtuele netwerken (VNet) en virtuele machines (Vm's) in azure zijn standaard veilig, omdat er geen Internet verbinding is. U kunt eenvoudig een IPv6-Internet verbinding toevoegen aan uw Azure load balancers en Vm's met open bare IPv6-adressen die u van Azure aanschaft.

Open bare Ip's die u reserveert, zijn gekoppeld aan een Azure-regio van uw keuze en met uw Azure-abonnement. U kunt een gereserveerd (statisch) IPv6-openbaar IP-adres verplaatsen tussen een van de Azure load balancers of Vm's in uw abonnement. U kunt het open bare IPv6-IP-adres volledig ontkoppelen en deze wordt voor uw gebruik behouden wanneer u klaar bent.

> [!WARNING]
> Wees voorzichtig om uw open bare IP-adressen niet per ongeluk te verwijderen. Als u een openbaar IP-adres verwijdert, wordt dit verwijderd uit uw abonnement en kunt u het niet herstellen (zelfs niet met de ondersteuning van Azure support).

Naast het reserveren van afzonderlijke IPv6-adressen, kunt u aaneengesloten bereiken van Azure IPv6-adressen (IP-voor voegsel) voor uw gebruik behouden.  Net als bij afzonderlijke IP-adressen zijn gereserveerde voor voegsels gekoppeld aan een Azure-regio van uw keuze en met uw Azure-abonnement. Het reserveren van een voorspelbaar, aaneengesloten adres bereik heeft veel toepassingen. U kunt bijvoorbeeld de IP- *filtering* van uw door Azure gehoste toepassingen door uw bedrijf en uw klanten aanzienlijk vereenvoudigen, omdat uw vaste IP-bereiken gemakkelijk kunnen worden geprogrammeerd in on-premises firewalls.  U kunt indien nodig afzonderlijke open bare Ip's van uw IP-voor voegsel maken en wanneer u deze afzonderlijke open bare Ip's verwijdert, worden ze *teruggestuurd* naar het gereserveerde bereik, zodat u deze later opnieuw kunt gebruiken. Alle IP-adressen in uw IP-voor voegsel zijn gereserveerd voor exclusief gebruik totdat u het voor voegsel verwijdert.



## <a name="ipv6-prefix-sizes"></a>Grootte van IPv6-voor voegsel
De volgende open bare IP-voor voegsel grootten zijn beschikbaar:

-  Minimale IPv6-voorvoegsel grootte:/127 = 2 adressen
-  Maximale grootte van IPv6-voor voegsel:/124 = 16 adressen

De voorvoegsel grootte is opgegeven als een CIDR-masker grootte (Klasseloze Inter-Domain route ring). Zo vertegenwoordigt een masker van/128 een afzonderlijk IPv6-adres als IPv6-adressen bestaan uit 128 bits.

## <a name="pricing"></a>Prijzen
 
Zie de [prijzen voor open bare IP-](https://azure.microsoft.com/pricing/details/ip-addresses/)adressen voor kosten die zijn gekoppeld aan het gebruik van open bare Azure-ip's, zowel afzonderlijke IP-adressen als IP-bereiken.

## <a name="limitations"></a>Beperkingen
IPv6 wordt alleen ondersteund voor algemene open bare Ip's met ' dynamische ' toewijzing. Dit betekent dat het IPv6-adres wordt gewijzigd als u uw toepassing (VM of load balancers) in azure verwijdert en opnieuw implementeert. Standaard-IPv6-ondersteuning voor het open bare IP-adres alleen statische (gereserveerde) toewijzing, hoewel standaard interne load balancers ook dynamische toewijzing kunnen ondersteunen vanuit het subnet waaraan ze zijn toegewezen.  

Als best practice wordt u aangeraden standaard open bare Ip's en standaard load balancers te gebruiken voor uw IPv6-toepassingen.

## <a name="next-steps"></a>Volgende stappen
- Reserveer een openbaar [IPv6-adres voorvoegsel](ipv6-reserve-public-ip-address-prefix.md).
- Meer informatie over [IPv6-adressen](ipv6-overview.md).
- Meer informatie over [het maken en gebruiken van open bare ip's](virtual-network-public-ip-address.md) (zowel IPv4 als IPv6) in Azure.
