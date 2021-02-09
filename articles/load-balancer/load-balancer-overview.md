---
title: Wat is Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Overzicht van Azure Load Balancer-functies, -architectuur en -implementatie. Informatie over hoe de Load Balancer werkt en hoe u deze kunt gebruiken in de cloud.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/25/2021
ms.author: allensu
ms.openlocfilehash: 6f83df22465a2dc5fb871ae4e2c6dedd75e00075
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834218"
---
# <a name="what-is-azure-load-balancer"></a>Wat is Azure Load Balancer?

*Taakverdeling* verwijst naar het gelijkmatig verdelen van belasting (binnenkomend netwerkverkeer) in een groep back-endservers of servers. 

Azure Load Balancer werkt op laag vier van het OSI-model (Open Systems Interconnection). Dit is het enige contactpunt voor clients. Load Balancer verdeelt inkomende stromen die aankomen op de front-end-naar-back-endpoolinstanties van de Load Balancer. Deze stromen zijn gebaseerd op geconfigureerde taakverdelingsregels en statuscontroles. De exemplaren van de back-endadresgroep kunnen virtuele Azure-machines of exemplaren in een schaalset voor virtuele machines zijn.

Een **[openbare load balancer](./components.md#frontend-ip-configurations)** kan uitgaande verbindingen bieden voor virtuele machines (VM's) in uw virtuele netwerk. Deze verbindingen worden tot stand gebracht door de privé-IP-adressen te vertalen naar openbare IP-adressen. Openbare load balancers worden gebruikt voor het verdelen van internetverkeer naar uw VM's.

Een **[interne (of privé) load balancer](./components.md#frontend-ip-configurations)** wordt alleen gebruikt wanneer privé-IP's alleen op de front-end vereist zijn. Interne load balancers worden gebruikt voor het verdelen van verkeer binnen een virtueel netwerk. In een hybride scenario kan een front-end van een load balancer ook worden bereikt vanaf een on-premises netwerk.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*Afbeelding: verdeling die wordt toegepast op toepassingen met meerdere lagen waarvoor zowel een openbare als een interne load balancer wordt gebruikt*

Zie [Azure Load Balancer-componenten](./components.md) voor meer informatie over de afzonderlijke componenten van de load balancer.

## <a name="why-use-azure-load-balancer"></a>Waarom Azure Load Balancer gebruiken?
Met Standard Load Balancer kunt u uw toepassingen schalen en services met hoge beschikbaarheid realiseren. Load Balancer biedt ondersteuning voor scenario's met inkomend en uitgaand verkeer. Load Balancer biedt lage latentie en hoge doorvoer en kan omhoog worden geschaald tot miljoenen stromen voor alle TCP- en UDP-toepassingen.

De belangrijkste scenario's die u kunt uitvoeren met Standard Load Balancer zijn onder meer:

- Taakverdeling van **[intern](./quickstart-load-balancer-standard-internal-portal.md)** en **[extern](./tutorial-load-balancer-standard-manage-portal.md)** verkeer naar virtuele machines van Azure.

- De beschikbaarheid vergroten door resources **[binnen](./tutorial-load-balancer-standard-public-zonal-portal.md)** en **[tussen](./tutorial-load-balancer-standard-public-zone-redundant-portal.md)** zones te distribueren.

- **[Uitgaande connectiviteit](./load-balancer-outbound-connections.md)** configureren voor virtuele machines van Azure.

- **[Statuscontroles](./load-balancer-custom-probe-overview.md)** gebruiken voor het bewaken van resources met taakverdeling.

- **[Port forwarding](./tutorial-load-balancer-port-forwarding-portal.md)** gebruiken om toegang te krijgen tot virtuele machines in een virtueel netwerk via een openbaar IP-adres en poort.

- Ondersteuning voor **[taakverdeling](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** van **[IPv6](../virtual-network/ipv6-overview.md)** inschakelen.

- Standard Load Balancer biedt multidimensionale metrische gegevens via [Azure Monitor](../azure-monitor/overview.md).  Deze metrische gegevens kunnen worden gefilterd, gegroepeerd en uitgesplitst voor een bepaalde dimensie.  Ze bieden actuele en historische inzichten in de prestaties en status van uw service. [Inzichten voor Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-insights) bieden een vooraf geconfigureerd dash board met handige visualisaties voor deze metrische gegevens.  Resource Health wordt ook ondersteund. Raadpleeg **[Diagnostische tests van Standard Load Balancer](load-balancer-standard-diagnostics.md)** voor meer informatie.

- Taakverdeling van services toepassen op **[meerdere poorten, meerdere IP-adressen of allebei](./load-balancer-multivip-overview.md)** .

- **[Interne](./move-across-regions-internal-load-balancer-portal.md)** en **[externe](./move-across-regions-external-load-balancer-portal.md)** resources tussen Azure-regio's verplaatsen.

- Taakverdeling toepassen van de TCP- en UDP-stroom op alle poorten tegelijk met **[HA-poorten](./load-balancer-ha-ports-overview.md)** .

### <a name="secure-by-default"></a><a name="securebydefault"></a>Standaardbeveiliging

Standard Load Balancer is gebouwd op basis van het beveiligingsmodel voor vertrouwensrelaties van het netwerk. Standard Load Balancer is standaard beveiligd en maakt deel uit van uw virtuele netwerk. Het virtuele netwerk is een privé- en geïsoleerd netwerk.  Dit betekent dat Standard Load Balancers en standaard openbare IP-adressen zijn gesloten voor inkomende stromen, tenzij ze worden geopend door netwerkbeveiligingsgroepen. Netwerkbeveiligingsgroepen worden gebruikt om toegestaan verkeer expliciet toe te staan.  Als u geen netwerkbeveiligingsgroep op een subnet of NIC van uw virtuele-machineresource hebt, mag het verkeer deze bron niet bereiken. Zie [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md) voor meer informatie over netwerkbeveiligingsgroepen en hoe u deze toepast voor uw scenario.
Basis Load Balancer is standaard open voor internet. Daarnaast slaat Load Balancer geen klantgegevens op.

## <a name="pricing-and-sla"></a>Prijzen en SLA

Ga naar de pagina [Prijzen voor Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) voor prijsinformatie over Standard Load Balancer.
Basic Load Balancer wordt gratis aangeboden.
Zie [SLA voor Load Balancer](https://aka.ms/lbsla). Basic Load Balancer heeft geen SLA.

## <a name="whats-new"></a>Wat is nieuw?

Abonneer u op de RSS-feed en bekijk de nieuwste updates voor Azure Load Balancer-functies op de pagina [Azure-updates](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="next-steps"></a>Volgende stappen

Zie [Een openbare standaard load balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met een load balancer.

Zie [Azure Load Balancer-componenten](./components.md) en [Azure Load Balancer-concepten](./concepts.md) voor meer informatie over beperkingen en componenten van Azure Load Balancer
