---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 04/08/2021
ms.author: allensu
ms.openlocfilehash: 062cdb7c442ee5556aa454c978bd4aa47058aaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107529580"
---
> [!NOTE]
> Azure biedt een kortstondig IP-adres voor Azure Virtual Machines waaraan geen openbaar IP-adres is toegewezen of die zich in de back-endpool van een interne Basic-Azure Load Balancer. Het kortstondige IP-mechanisme biedt een uitgaand IP-adres dat niet kan worden geconfigureerd. 
>
>Het kortstondige IP-adres wordt uitgeschakeld wanneer een openbaar IP-adres wordt toegewezen aan de virtuele machine of de virtuele machine wordt geplaatst in de back-Standard Load Balancer van een Standard Load Balancer met of zonder uitgaande regels. Als [](../articles/virtual-network/nat-overview.md) een Azure Virtual Network NAT-gatewayresource wordt toegewezen aan het subnet van de virtuele machine, wordt het kortstondige IP-adres uitgeschakeld.
>
> Zie Using Source Network Address Translation [(SNAT) for outbound connections (Bronnetwerkadresvertaling (SNAT) gebruiken voor uitgaande verbindingen) voor meer](../articles/load-balancer/load-balancer-outbound-connections.md)informatie over uitgaande verbindingen in Azure.