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
ms.openlocfilehash: a1a8df6d503ec5f5bf9c1e739e5ecf6486a85776
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697417"
---
# <a name="troubleshoot-azure-load-balancer"></a>Problemen met Azure Load Balancer oplossen

Op deze pagina vindt u informatie over het oplossen van problemen met basis-en standaard Veelgestelde vragen over Azure Load Balancer. Zie [Standard Load Balancer Overview](load-balancer-standard-diagnostics.md)voor meer informatie over Standard Load Balancer.

Wanneer de Load Balancer verbinding niet beschikbaar is, zijn de meest voorkomende symptomen als volgt: 

- Vm's achter de Load Balancer reageren niet op status controles 
- Vm's achter de Load Balancer reageren niet op het verkeer op de geconfigureerde poort

Wanneer de externe clients naar de back-end-Vm's via de load balancer gaan, wordt het IP-adres van de clients voor de communicatie gebruikt. Zorg ervoor dat het IP-adres van de clients wordt toegevoegd aan de acceptatie lijst NSG. 

## <a name="symptom-no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Symptoom: geen uitgaande verbinding van standaard interne load balancers (ILB)

**Validatie en oplossing**

Standaard ILBs zijn **standaard veilig**. Basis-ILBs die verbinding maken met Internet via een *verborgen* openbaar IP-adres. Dit is niet kunt voor productie werkbelastingen omdat het IP-adres niet statisch of vergrendeld is via Nsg's waarvan u de eigenaar bent. Als u onlangs van een Basic-ILB hebt overgezet naar een standaard-ILB, moet u een openbaar IP-adres maken via een [uitgaande alleen](egress-only.md) -configuratie die het IP-adres vergrendelt via nsg's. U kunt ook een [NAT-gateway](../virtual-network/nat-overview.md) op uw subnet gebruiken.

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
2. Open een opdracht prompt en voer de volgende opdracht uit om te controleren of er een toepassing luistert op de test poort:   
            netstat: een
3. Als de poort status niet wordt weer gegeven als **luistert**, configureert u de juiste poort. 
4. U kunt ook een andere poort selecteren, die wordt weer gegeven als **Luis teren** en Load Balancer configuratie dienovereenkomstig bijwerken.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Oorzaak 3: Firewall of een netwerk beveiligings groep blokkeert de poort op de load balancer Vm's van de back-end-groep  
Als de firewall op de virtuele machine de test poort blokkeert, of een of meer netwerk beveiligings groepen die zijn geconfigureerd op het subnet of op de VM, is de test niet in staat om de poort te bereiken, kan de virtuele machine niet reageren op de status test.          

**Validatie en oplossing**

* Als de firewall is ingeschakeld, controleert u of deze is geconfigureerd om de test poort toe te staan. Als dat niet het geval is, configureert u de firewall om verkeer toe te staan op de test poort en test u het opnieuw. 
* Controleer in de lijst met netwerk beveiligings groepen of het binnenkomende of uitgaande verkeer op de test poort interferentie heeft. 
* Controleer ook of een regel voor het **weigeren van alle** netwerk beveiligings groepen op de NIC van de virtuele machine of het subnet met een hogere prioriteit heeft dan de standaard regel waarmee lb-tests & verkeer worden toegestaan (netwerk beveiligings groepen moeten Load Balancer IP-adres van 168.63.129.16). 
* Als een van deze regels het test verkeer blokkeert, verwijdert u de regels en configureert u deze opnieuw om het test verkeer toe te staan.  
* Test of de VM nu heeft gereageerd op de status controles. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Oorzaak 4: andere onjuiste configuratie in Load Balancer
Als alle voor gaande oorzaken correct worden gevalideerd en opgelost en de back-end-VM nog steeds niet reageert op de status test, kunt u hand matig testen op connectiviteit en een aantal traceringen verzamelen om inzicht te krijgen in de connectiviteit.

**Validatie en oplossing**

* Gebruik **Psping** van een van de andere virtuele machines in het VNet om het antwoord van de test poort te testen (bijvoorbeeld: .\psping.exe-t 10.0.0.4:3389) en de resultaten op te nemen. 
* Gebruik **TCPing** van een van de andere virtuele machines in het VNet om het test poort antwoord te testen (bijvoorbeeld: .\tcping.exe 10.0.0.4 3389) en de resultaten te registreren. 
* Als er geen reactie wordt ontvangen in deze ping-tests,
    - Voer een gelijktijdige Netsh-tracering uit op de doel-back-end-VM-groep en een andere test-VM van hetzelfde VNet. Voer nu een PsPing-test uit, verzamel een aantal netwerk traceringen en stop de test vervolgens. 
    - Analyseer het netwerk vastleggen en controleer of er binnenkomende en uitgaande pakketten zijn gerelateerd aan de ping-query. 
        - Als er geen binnenkomende pakketten worden waargenomen op de back-end-VM-groep, is er mogelijk een netwerk beveiligings groep of een UDR mis-configuratie die het verkeer blokkeert. 
        - Als er geen uitgaande pakketten worden waargenomen op de back-end-VM-groep, moet de virtuele machine worden gecontroleerd op ongerelateerde problemen (de toepassing blokkeert bijvoorbeeld de test poort). 
    - Controleer of de test pakketten worden afgedwongen voor een andere bestemming (mogelijk via UDR-instellingen) voordat de load balancer wordt bereikt. Dit kan ervoor zorgen dat het verkeer nooit de back-end-VM bereikt. 
* Wijzig het test type (bijvoorbeeld HTTP in TCP) en configureer de bijbehorende poort in de Acl's voor netwerk beveiligings groepen en de firewall om te controleren of het probleem wordt veroorzaakt door de configuratie van de test reactie. Zie [configuratie endpoint Load Balancing Health probe](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details)(Engelstalig) voor meer informatie over Health probe configuratie.

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Symptoom: Vm's achter Load Balancer reageren niet op verkeer op de geconfigureerde gegevens poort

Als een virtuele machine in de back-end wordt weer gegeven als in orde en reageert op de status tests, maar nog steeds niet deelneemt aan de taak verdeling, of niet reageert op het gegevens verkeer, kan dit een van de volgende oorzaken hebben: 
* Load Balancer VM van de back-end-pool luistert niet op de gegevens poort 
* De netwerk beveiligings groep blokkeert de poort op de Load Balancer back-end-VM-groep  
* Toegang tot de Load Balancer vanaf dezelfde VM en NIC 
* Toegang tot de internet-Load Balancer frontend van de deelnemende Load Balancer back-end-VM-groep 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Oorzaak 1: Load Balancer VM van de back-end-pool luistert niet op de gegevens poort 
Als een virtuele machine niet reageert op het gegevens verkeer, komt dit mogelijk doordat de doel poort niet is geopend op de deelnemende virtuele machine, of de virtuele machine luistert niet op die poort. 

**Validatie en oplossing**

1. Meld u aan bij de back-end-VM. 
2. Open een opdracht prompt en voer de volgende opdracht uit om te controleren of er een toepassing luistert op de gegevens poort:  
            netstat: een 
3. Als de poort niet wordt vermeld met de status Luis TEREn, configureert u de juiste listener-poort 
4. Als de poort is gemarkeerd als luistert, controleert u de doel toepassing op die poort voor mogelijke problemen.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Oorzaak 2: de netwerk beveiligings groep blokkeert de poort op de Load Balancer back-end-VM-groep  

Als een of meer netwerk beveiligings groepen die zijn geconfigureerd op het subnet of op de virtuele machine, de bron-IP of poort blokkeert, kan de virtuele machine niet reageren.

Voor de open bare load balancer wordt het IP-adres van de internetclients gebruikt voor communicatie tussen de clients en de load balancer back-end-Vm's. Zorg ervoor dat het IP-adres van de clients is toegestaan in de netwerk beveiligings groep van de back-end-VM.

1. De netwerk beveiligings groepen weer geven die zijn geconfigureerd op de back-end-VM. Zie [netwerk beveiligings groepen beheren](../virtual-network/manage-network-security-group.md) voor meer informatie.
1. Controleer in de lijst met netwerk beveiligings groepen of:
    - het binnenkomende of uitgaande verkeer op de gegevens poort heeft storingen. 
    - een regel voor het **weigeren van alle** netwerk beveiligings groepen op de NIC van de virtuele machine of het subnet met een hogere prioriteit dan de standaard regel waarmee Load Balancer tests en verkeer wordt toegestaan (netwerk beveiligings groepen moeten Load Balancer IP-adres van de 168.63.129.16 toestaan, dat wil zeggen test poort)
1. Als een van de regels het verkeer blokkeert, moet u deze regels verwijderen en opnieuw configureren om het gegevens verkeer toe te staan.  
1. Test of de VM nu heeft gereageerd op de status controles.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Oorzaak 3: toegang tot de Load Balancer vanaf dezelfde VM en netwerk interface 

Als uw toepassing die wordt gehost in de back-end-VM van een Load Balancer probeert toegang te krijgen tot een andere toepassing die wordt gehost op dezelfde back-end-VM via dezelfde netwerk interface, is dit een niet-ondersteund scenario. 

**Oplossing** U kunt dit probleem oplossen met behulp van een van de volgende methoden:
* Configureer afzonderlijke Vm's van de back-end-groep per toepassing. 
* Configureer de toepassing in dual NIC-Vm's zodat elke toepassing een eigen netwerk interface en IP-adres gebruikt. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Oorzaak 4: toegang tot de interne Load Balancer-frontend van de deelnemende Load Balancer back-end-VM-groep

Als er een intern Load Balancer is geconfigureerd in een VNet en een van de back-end-Vm's van de deel nemer probeert toegang te krijgen tot de interne Load Balancer frontend, kunnen er fouten optreden wanneer de stroom wordt toegewezen aan de oorspronkelijke virtuele machine. Een dergelijk scenario wordt niet ondersteund.

**Oplossing** Er zijn verschillende manieren om dit scenario op te heffen, met inbegrip van het gebruik van een proxy. Evalueer Application Gateway of andere proxy's van derden (bijvoorbeeld nginx of haproxy). Zie [overzicht van Application Gateway](../application-gateway/overview.md) voor meer informatie over Application Gateway.

**Details** Interne load balancers vertalen geen uitgaande oorspronkelijke verbindingen met de front-end van een interne Load Balancer, omdat beide zich in een privé-IP-adres ruimte bevinden. Open bare load balancers bieden [uitgaande verbindingen](load-balancer-outbound-connections.md) van privé-IP-adressen in het virtuele netwerk naar open bare IP-adressen. Voor interne load balancers vermijdt deze benadering de potentiële SNAT-poort uitputting binnen een unieke interne IP-adres ruimte, waarbij omzetting niet vereist is.

Een neven effect is dat als een uitgaande stroom van een virtuele machine in de back-end-pool een stroom naar front-end van de interne Load Balancer in de groep probeert te maken _en_ wordt teruggekoppeld aan zichzelf, de twee zijden van de stroom niet overeenkomen. Omdat ze niet overeenkomen, mislukt de stroom. De stroom slaagt als de stroom niet is gekoppeld aan dezelfde virtuele machine in de back-end-pool die de stroom heeft gemaakt naar de front-end.

Wanneer de stroom wordt teruggeleid naar zichzelf, lijkt de uitgaande stroom van de virtuele machine naar de front-end en de bijbehorende binnenkomende stroom lijkt afkomstig van de VM naar zichzelf. Vanuit het weergave punt van het gast besturingssysteem komen de inkomende en uitgaande delen van dezelfde stroom niet overeen in de virtuele machine. De TCP-stack herkent deze helften van dezelfde stroom niet als onderdeel van dezelfde stroom. De bron en het doel komen niet overeen. Wanneer de stroom wordt toegewezen aan een andere virtuele machine in de back-end-pool, komen de helften van de stroom overeen en kan de virtuele machine reageren op de stroom.

Het symptoom voor dit scenario bestaat uit terugkerende verbindingstime-outs wanneer de stroom naar dezelfde back-end terugkeert als de stroom. Veelvoorkomende tijdelijke oplossingen omvatten het invoegen van een proxy laag achter de interne Load Balancer en het gebruik van regels voor het door sturen van direct server return-stijl (DSR). Zie meerdere front-ends [voor Azure Load Balancer](load-balancer-multivip-overview.md)voor meer informatie.

U kunt een intern Load Balancer combi neren met elke proxy van derden of interne [Application Gateway](../application-gateway/overview.md) gebruiken voor proxy SCENARIO'S met http/https. U kunt een open bare Load Balancer gebruiken om dit probleem te verhelpen, maar het resulterende scenario is gevoelig voor de [uitputting](load-balancer-outbound-connections.md)van de SNAT. Vermijd deze tweede benadering, tenzij u deze zorgvuldig beheert.

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Symptoom: kan de back-uppoort niet wijzigen voor een bestaande LB-regel van een load balancer die een VM-Schaalset in de back-endadresgroep heeft geïmplementeerd. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Oorzaak: de backend-poort kan niet worden gewijzigd voor een taakverdelings regel die wordt gebruikt door een status test voor load balancer waarnaar wordt verwezen door de VM-Schaalset.
**Oplossing** Als u de poort wilt wijzigen, kunt u de status test verwijderen door de VM-Schaalset bij te werken, de poort bij te werken en de status test vervolgens opnieuw te configureren.

## <a name="symptom-small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Symptoom: klein verkeer gaat door load balancer na het verwijderen van Vm's uit de back-end-groep van de load balancer. 
### <a name="cause--vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Oorzaak: Vm's die worden verwijderd uit de back-end-groep mogen geen verkeer meer ontvangen. De kleine hoeveelheid netwerk verkeer kan zijn gerelateerd aan opslag, DNS en andere functies in Azure. 
Als u wilt controleren, kunt u een netwerk tracering uitvoeren. De FQDN die wordt gebruikt voor uw Blob Storage-accounts worden weer gegeven in de eigenschappen van elk opslag account.  Vanaf een virtuele machine binnen uw Azure-abonnement kunt u een nslookup uitvoeren om het Azure IP-adres te bepalen dat aan het opslag account is toegewezen.

## <a name="additional-network-captures"></a>Aanvullende netwerk opnamen
Als u besluit een ondersteunings aanvraag te openen, verzamelt u de volgende informatie voor een snellere oplossing. Kies één back-end-VM om de volgende tests uit te voeren:
- Gebruik Psping van een van de back-end-Vm's binnen het VNet om de test poort reactie te testen (bijvoorbeeld: Psping 10.0.0.4:3389) en de resultaten op te nemen. 
- Als er geen antwoord wordt ontvangen in deze ping-tests, voert u een gelijktijdige Netsh-tracering uit op de back-end-VM en de VNet-test-VM terwijl u PsPing uitvoert en stopt u de Netsh-tracering. 
 
## <a name="symptom-load-balancer-in-failed-state"></a>Symptoom: Load Balancer met de status mislukt 

**Oplossing**

- Wanneer u de resource met de status mislukt hebt geïdentificeerd, gaat u naar [Azure resource Explorer](https://resources.azure.com/) en identificeert u de resource in deze status. 
- Werk de wissel knop in de rechter bovenhoek bij om te lezen/schrijven.
- Klik op bewerken voor de resource met de status mislukt.
- Klik op PUT gevolgd door GET om ervoor te zorgen dat de inrichtings status is bijgewerkt naar geslaagd.
- U kunt vervolgens door gaan met andere acties als de resource de status Mislukt heeft.


## <a name="next-steps"></a>Volgende stappen

Als de voor gaande stappen het probleem niet oplossen, opent u een [ondersteunings ticket](https://azure.microsoft.com/support/options/).