---
title: Problemen met de status test van Azure Load Balancer oplossen
description: Meer informatie over het oplossen van bekende problemen met de status van Azure Load Balancer Health probe.
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
ms.date: 12/02/2020
ms.author: allensu
ms.openlocfilehash: 28823c997cd974d5061829df88680ed52075caa0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98029183"
---
# <a name="troubleshoot-azure-load-balancer-health-probe-status"></a>Problemen met de status test van Azure Load Balancer oplossen

Op deze pagina vindt u informatie over het oplossen van problemen met veelgestelde vragen over de Azure Load Balancer status testen.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Symptoom: Vm's achter de Load Balancer reageren niet op status controles
Voor de back-endservers die deel uitmaken van de load balancerset, moeten ze de controle van de test door geven. Zie [informatie over Load Balancer probe](load-balancer-custom-probe-overview.md)(Engelstalig) voor meer informatie over status controles. 

De Load Balancer Vm's van de back-end-groep reageren mogelijk niet op de tests om een van de volgende redenen: 
- Load Balancer VM van back-end is beschadigd 
- Load Balancer VM van de back-end-pool luistert niet op de test poort 
- Firewall of een netwerk beveiligings groep blokkeert de poort op de Load Balancer Vm's van de back-end-groep 
- Andere onjuiste configuratie in Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Oorzaak 1: Load Balancer VM van back-end is beschadigd

**Validatie en oplossing**

U kunt dit probleem oplossen door u aan te melden bij de deelnemende Vm's en te controleren of de status van de virtuele machine in orde is en om te reageren op **PsPing** of **TCPing** van een andere virtuele machine in de groep. Als de virtuele machine niet in orde is of niet kan reageren op de test, moet u het probleem oplossen en de VM weer herstellen naar een goede status voordat deze kan deel nemen aan de taak verdeling.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Oorzaak 2: het Load Balancer VM van de back-end-groep luistert niet op de test poort
Als de virtuele machine in orde is, maar niet op de test reageert, kan het zijn dat de test poort niet op de deelnemende virtuele machine is geopend of dat de virtuele machine niet op die poort luistert.

**Validatie en oplossing**

1. Meld u aan bij de back-end-VM.
2. Open een opdracht prompt en voer de volgende opdracht uit om te controleren of er een toepassing luistert op de test poort: netstat-a
3. Als de poort status niet wordt weer gegeven als **luistert**, configureert u de juiste poort. 
4. U kunt ook een andere poort selecteren, die wordt weer gegeven als **Luis teren** en Load Balancer configuratie dienovereenkomstig bijwerken.

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Oorzaak 3: Firewall of een netwerk beveiligings groep blokkeert de poort op de load balancer Vm's van de back-end-groep
Als de firewall op de virtuele machine de test poort blokkeert, of een of meer netwerk beveiligings groepen die zijn geconfigureerd op het subnet of op de VM, is de test niet in staat om de poort te bereiken, kan de virtuele machine niet reageren op de status test.

**Validatie en oplossing**

1. Als de firewall is ingeschakeld, controleert u of deze is geconfigureerd om de test poort toe te staan. Als dat niet het geval is, configureert u de firewall om verkeer toe te staan op de test poort en test u het opnieuw.
2. Controleer in de lijst met netwerk beveiligings groepen of het binnenkomende of uitgaande verkeer op de test poort interferentie heeft.
3. Controleer ook of een regel voor het **weigeren van alle** netwerk beveiligings groepen op de NIC van de virtuele machine of het subnet met een hogere prioriteit heeft dan de standaard regel waarmee lb-tests & verkeer worden toegestaan (netwerk beveiligings groepen moeten Load Balancer IP-adres van 168.63.129.16).
4. Als een van deze regels het test verkeer blokkeert, verwijdert u de regels en configureert u deze opnieuw om het test verkeer toe te staan.  
5. Test of de VM nu heeft gereageerd op de status controles.

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Oorzaak 4: andere onjuiste configuratie in Load Balancer
Als alle voor gaande oorzaken correct worden gevalideerd en opgelost en de back-end-VM nog steeds niet reageert op de status test, kunt u hand matig testen op connectiviteit en een aantal traceringen verzamelen om inzicht te krijgen in de connectiviteit.

**Validatie en oplossing**

1. Gebruik **Psping** van een van de andere virtuele machines in het VNet om het antwoord van de test poort te testen (bijvoorbeeld: .\psping.exe-t 10.0.0.4:3389) en de resultaten op te nemen. 
2. Gebruik **TCPing** van een van de andere virtuele machines in het VNet om het test poort antwoord te testen (bijvoorbeeld: .\tcping.exe 10.0.0.4 3389) en de resultaten te registreren. 
3. Als er geen reactie wordt ontvangen in deze ping-tests,
    - Voer een gelijktijdige Netsh-tracering uit op de doel-back-end-VM-groep en een andere test-VM van hetzelfde VNet. Voer nu een PsPing-test uit, verzamel een aantal netwerk traceringen en stop de test vervolgens. 
    - Analyseer het netwerk vastleggen en controleer of er binnenkomende en uitgaande pakketten zijn gerelateerd aan de ping-query. 
        - Als er geen binnenkomende pakketten worden waargenomen op de back-end-VM-groep, is er mogelijk een netwerk beveiligings groep of een UDR mis-configuratie die het verkeer blokkeert. 
        - Als er geen uitgaande pakketten worden waargenomen op de back-end-VM-groep, moet de virtuele machine worden gecontroleerd op ongerelateerde problemen (de toepassing blokkeert bijvoorbeeld de test poort). 
    - Controleer of de test pakketten worden afgedwongen voor een andere bestemming (mogelijk via UDR-instellingen) voordat de load balancer wordt bereikt. Dit kan ervoor zorgen dat het verkeer nooit de back-end-VM bereikt. 
4. Wijzig het test type (bijvoorbeeld HTTP in TCP) en configureer de bijbehorende poort in de Acl's voor netwerk beveiligings groepen en de firewall om te controleren of het probleem wordt veroorzaakt door de configuratie van de test reactie. Zie [configuratie endpoint Load Balancing Health probe](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details)(Engelstalig) voor meer informatie over Health probe configuratie.

## <a name="next-steps"></a>Volgende stappen

Als de voor gaande stappen het probleem niet oplossen, opent u een [ondersteunings ticket](https://azure.microsoft.com/support/options/).