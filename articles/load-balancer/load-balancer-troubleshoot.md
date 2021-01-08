---
title: Veelvoorkomende problemen oplossen Azure Load Balancer
description: Meer informatie over het oplossen van veelvoorkomende problemen met Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: cddaf1bde84d7e60eb59bd4c58c65fa889e06ae3
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028808"
---
# <a name="troubleshoot-azure-load-balancer"></a>Problemen met Azure Load Balancer oplossen

Op deze pagina vindt u informatie over het oplossen van problemen met basis-en standaard Veelgestelde vragen over Azure Load Balancer. Zie [Standard Load Balancer Overview](load-balancer-standard-diagnostics.md)voor meer informatie over Standard Load Balancer.

Wanneer de Load Balancer verbinding niet beschikbaar is, zijn de meest voorkomende symptomen als volgt:

- Vm's achter de Load Balancer reageren niet op status controles 
- Vm's achter de Load Balancer reageren niet op het verkeer op de geconfigureerde poort

Wanneer de externe clients naar de back-end-Vm's via de load balancer gaan, wordt het IP-adres van de clients voor de communicatie gebruikt. Zorg ervoor dat het IP-adres van de clients wordt toegevoegd aan de acceptatie lijst NSG.

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Geen uitgaande verbindingen van standaard interne load balancers (ILB)

**Validatie en oplossing**

Standaard ILBs zijn **standaard veilig**. Basis-ILBs die verbinding maken met Internet via een *verborgen* openbaar IP-adres. Dit wordt niet aanbevolen voor productie werkbelastingen omdat het IP-adres niet statisch of vergrendeld is via Nsg's waarvan u de eigenaar bent. Als u onlangs van een Basic-ILB naar een standaard-ILB hebt verplaatst, moet u een open bare IP-adres maken via een [alleen-uitgaand](egress-only.md) configuratie, waardoor het IP-adres wordt vergrendeld via nsg's. U kunt ook een [NAT-gateway](../virtual-network/nat-overview.md) op uw subnet gebruiken.

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>Kan de back-endserver niet wijzigen voor een bestaande LB-regel van een load balancer waarvan de schaalset voor virtuele machines in de back-end-groep is geïmplementeerd.

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>Oorzaak: de backend-poort kan niet worden gewijzigd voor een taakverdelings regel die wordt gebruikt door een status test voor load balancer waarnaar wordt verwezen door de schaalset voor virtuele machines

**Oplossing** Als u de poort wilt wijzigen, kunt u de status test verwijderen door de schaalset van de virtuele machine bij te werken, de poort bij te werken en de status test vervolgens opnieuw te configureren.

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Klein verkeer gaat door load balancer na het verwijderen van Vm's uit de back-end-groep van de load balancer.

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Oorzaak: Vm's die worden verwijderd uit de back-end-groep mogen geen verkeer meer ontvangen. De kleine hoeveelheid netwerk verkeer kan zijn gerelateerd aan opslag, DNS en andere functies in Azure.

Als u wilt controleren, kunt u een netwerk tracering uitvoeren. De FQDN die wordt gebruikt voor uw Blob Storage-accounts worden weer gegeven in de eigenschappen van elk opslag account.  Vanaf een virtuele machine binnen uw Azure-abonnement kunt u Nslookup uitvoeren om te bepalen welk Azure IP-adres is toegewezen aan dat opslag account.

## <a name="additional-network-captures"></a>Aanvullende netwerk opnamen

Als u besluit een ondersteunings aanvraag te openen, verzamelt u de volgende informatie voor een snellere oplossing. Kies één back-end-VM om de volgende tests uit te voeren:

- Gebruik PS Ping vanaf een van de back-end-Vm's binnen het VNet om de test poort respons te testen (bijvoorbeeld: PS ping 10.0.0.4:3389) en de resultaten op te nemen. 
- Als er geen antwoord wordt ontvangen in deze ping-tests, voert u een gelijktijdige Netsh-tracering uit op de back-end-VM en de VNet-test-VM terwijl u PsPing uitvoert en stopt u de Netsh-tracering.

## <a name="load-balancer-in-failed-state"></a>Load Balancer met de status mislukt

**Oplossing**

- Wanneer u de resource met de status mislukt hebt geïdentificeerd, gaat u naar [Azure resource Explorer](https://resources.azure.com/) en identificeert u de resource in deze status.
- De wissel knop in de rechter bovenhoek bijwerken om te lezen/schrijven.
- Klik op bewerken voor de resource met de status mislukt.
- Klik op PUT gevolgd door GET om ervoor te zorgen dat de inrichtings status is bijgewerkt naar geslaagd.
- U kunt vervolgens door gaan met andere acties als de resource de status Mislukt heeft.

## <a name="next-steps"></a>Volgende stappen

Als de voor gaande stappen het probleem niet verhelpen, opent u een [ondersteunings ticket](https://azure.microsoft.com/support/options/).
