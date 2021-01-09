---
title: Problemen met Azure Load Balancer oplossen
description: Meer informatie over het oplossen van bekende problemen met Azure Load Balancer.
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
ms.openlocfilehash: bfe2b21a86f2ce4b4630ba69cde87796fd367f4b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029177"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>Problemen met de Azure Load Balancer back-end verkeer oplossen

Op deze pagina vindt u informatie over het oplossen van problemen voor Azure Load Balancer vragen.

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Vm's achter Load Balancer reageren niet op verkeer op de geconfigureerde gegevens poort

Als een virtuele machine in de back-end wordt weer gegeven als in orde en reageert op de status tests, maar nog steeds niet deelneemt aan de taak verdeling, of niet reageert op het gegevens verkeer, kan dit een van de volgende oorzaken hebben:
* Load Balancer VM van de back-end-pool luistert niet op de gegevens poort
* De netwerk beveiligings groep blokkeert de poort op de Load Balancer back-end-VM-groep  
* Toegang tot de Load Balancer vanaf dezelfde VM en NIC
* Toegang tot de internet-Load Balancer frontend van de deelnemende Load Balancer back-end-VM-groep

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Oorzaak 1: Load Balancer VM van de back-end-pool luistert niet op de gegevens poort

Als een virtuele machine niet reageert op het gegevens verkeer, komt dit mogelijk doordat de doel poort niet is geopend op de deelnemende virtuele machine, of de virtuele machine luistert niet op die poort. 

**Validatie en oplossing**

1. Meld u aan bij de back-end-VM. 
2. Open een opdracht prompt en voer de volgende opdracht uit om te controleren of er een toepassing luistert op de gegevens poort:  
            netstat: een 
3. Als de poort niet wordt vermeld met de status Luis TEREn, configureert u de juiste listener-poort 
4. Als de poort is gemarkeerd als luistert, controleert u de doel toepassing op die poort voor mogelijke problemen.

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Oorzaak 2: de netwerk beveiligings groep blokkeert de poort op de Load Balancer back-end-VM-groep  

Als een of meer netwerk beveiligings groepen die zijn geconfigureerd op het subnet of op de virtuele machine, de bron-IP of poort blokkeert, kan de virtuele machine niet reageren.

Voor de open bare load balancer wordt het IP-adres van de internetclients gebruikt voor communicatie tussen de clients en de load balancer back-end-Vm's. Zorg ervoor dat het IP-adres van de clients is toegestaan in de netwerk beveiligings groep van de back-end-VM.

1. De netwerk beveiligings groepen weer geven die zijn geconfigureerd op de back-end-VM. Zie [netwerk beveiligings groepen beheren](../virtual-network/manage-network-security-group.md) voor meer informatie.
1. Controleer in de lijst met netwerk beveiligings groepen of:
    - het binnenkomende of uitgaande verkeer op de gegevens poort heeft storingen. 
    - een regel voor het **weigeren van alle** netwerk beveiligings groepen op de NIC van de virtuele machine of het subnet met een hogere prioriteit dan de standaard regel waarmee Load Balancer tests en verkeer wordt toegestaan (netwerk beveiligings groepen moeten Load Balancer IP-adres van de 168.63.129.16 toestaan, dat wil zeggen test poort)
1. Als een van de regels het verkeer blokkeert, moet u deze regels verwijderen en opnieuw configureren om het gegevens verkeer toe te staan.  
1. Test of de VM nu heeft gereageerd op de status controles.

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Oorzaak 3: toegang tot de Load Balancer vanaf dezelfde VM en netwerk interface 

Als uw toepassing die wordt gehost in de back-end-VM van een Load Balancer probeert toegang te krijgen tot een andere toepassing die wordt gehost op dezelfde back-end-VM via dezelfde netwerk interface, is dit een niet-ondersteund scenario. 

**Oplossing** U kunt dit probleem oplossen met behulp van een van de volgende methoden:
* Configureer afzonderlijke Vm's van de back-end-groep per toepassing. 
* Configureer de toepassing in dual NIC-Vm's zodat elke toepassing een eigen netwerk interface en IP-adres gebruikt. 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Oorzaak 4: toegang tot de interne Load Balancer-frontend van de deelnemende Load Balancer back-end-VM-groep

Als er een intern Load Balancer is geconfigureerd in een VNet en een van de back-end-Vm's van de deel nemer probeert toegang te krijgen tot de interne Load Balancer frontend, kunnen er fouten optreden wanneer de stroom wordt toegewezen aan de oorspronkelijke virtuele machine. Een dergelijk scenario wordt niet ondersteund.

**Oplossing** Er zijn verschillende manieren om dit scenario op te heffen, met inbegrip van het gebruik van een proxy. Evalueer Application Gateway of andere proxy's van derden (bijvoorbeeld nginx of haproxy). Zie [overzicht van Application Gateway](../application-gateway/overview.md) voor meer informatie over Application Gateway.

**Details** Interne load balancers vertalen geen uitgaande oorspronkelijke verbindingen met de front-end van een interne Load Balancer, omdat beide zich in een privé-IP-adres ruimte bevinden. Open bare load balancers bieden [uitgaande verbindingen](load-balancer-outbound-connections.md) van privé-IP-adressen in het virtuele netwerk naar open bare IP-adressen. Voor interne load balancers vermijdt deze benadering de potentiële SNAT-poort uitputting binnen een unieke interne IP-adres ruimte, waarbij omzetting niet vereist is.

Een neven effect is dat als een uitgaande stroom van een virtuele machine in de back-end-pool een stroom naar front-end van de interne Load Balancer in de groep probeert te maken _en_ wordt teruggekoppeld aan zichzelf, de twee zijden van de stroom niet overeenkomen. Omdat ze niet overeenkomen, mislukt de stroom. De stroom slaagt als de stroom niet is gekoppeld aan dezelfde virtuele machine in de back-end-pool die de stroom heeft gemaakt naar de front-end.

Wanneer de stroom wordt teruggeleid naar zichzelf, lijkt de uitgaande stroom van de virtuele machine naar de front-end en de bijbehorende binnenkomende stroom lijkt afkomstig van de VM naar zichzelf. Vanuit het weergave punt van het gast besturingssysteem komen de inkomende en uitgaande delen van dezelfde stroom niet overeen in de virtuele machine. De TCP-stack herkent deze helften van dezelfde stroom niet als onderdeel van dezelfde stroom. De bron en het doel komen niet overeen. Wanneer de stroom wordt toegewezen aan een andere virtuele machine in de back-end-pool, komen de helften van de stroom overeen en kan de virtuele machine reageren op de stroom.

Het symptoom voor dit scenario bestaat uit terugkerende verbindingstime-outs wanneer de stroom naar dezelfde back-end terugkeert als de stroom. Veelvoorkomende tijdelijke oplossingen omvatten het invoegen van een proxy laag achter de interne Load Balancer en het gebruik van regels voor het door sturen van direct server return-stijl (DSR). Zie meerdere front-ends [voor Azure Load Balancer](load-balancer-multivip-overview.md)voor meer informatie.

U kunt een intern Load Balancer combi neren met elke proxy van derden of interne [Application Gateway](../application-gateway/overview.md) gebruiken voor proxy SCENARIO'S met http/https. U kunt een open bare Load Balancer gebruiken om dit probleem te verhelpen, maar het resulterende scenario is gevoelig voor de [uitputting](load-balancer-outbound-connections.md)van de SNAT. Vermijd deze tweede benadering, tenzij u deze zorgvuldig beheert.

## <a name="next-steps"></a>Volgende stappen

Als de voor gaande stappen het probleem niet oplossen, opent u een [ondersteunings ticket](https://azure.microsoft.com/support/options/).