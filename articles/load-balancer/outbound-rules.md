---
title: Uitgaande regels Azure Load Balancer
description: In dit artikel wordt uitgelegd hoe u uitgaande regels configureert om uitgaand internetverkeer te Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 339bbd7edf48737113de360812165dc8148c5b93
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375862"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Uitgaande regels Azure Load Balancer

Met regels voor uitgaand verkeer kunt u SNAT (source network address translation) expliciet definiëren voor een openbare standaard load balancer. Met deze configuratie kunt u de openbare IP('s) van uw load balancer uitgaande internetverbinding bieden voor uw back-end-exemplaren.

Met deze configuratie kunt u het volgende doen:

* IP-adresmaskering
* Het vereenvoudigen van uw lijsten met toegestane lijsten.
* Vermindert het aantal openbare IP-resources voor implementatie.

Met uitgaande regels hebt u volledige declaratieve controle over uitgaande internetverbinding. Met uitgaande regels kunt u deze mogelijkheid schalen en afstemmen op uw specifieke behoeften. 

Uitgaande regels worden alleen gevolgd als de back-end-VM geen openbaar IP-adres (ILPIP) op exemplaarniveau heeft.

![Load Balancer regels voor uitgaand verkeer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Met uitgaande regels kunt u uitgaand **SNAT-gedrag expliciet** definiëren.

Met uitgaande regels kunt u het volgende bepalen:

* **Welke virtuele machines worden vertaald naar welke openbare IP-adressen.**
     * Twee regels waren back-endpool 1 en maakt gebruik van het blauwe IP-adres 1 en 2, back-en-pool 2 maakt gebruik van het gele IP-voorvoegsel.
* **Hoe uitgaande SNAT-poorten worden toegewezen.**
     * Als back-endpool 2 de enige pool is die uitgaande verbindingen maakt, geeft u alle SNAT-poorten naar back-en-pool 2 en geen aan back-endpool 1.
* **Voor welke protocollen uitgaande vertaling moet worden gebruikt.**
     * Als back-endpool 2 UDP-poorten nodig heeft voor uitgaand verkeer en back-en-pool 1 TCP nodig heeft, geeft u TCP-poorten naar 1 en UDP-poorten op 2.
* **Welke duur moet worden gebruikt voor time-out voor inactieve uitgaande verbindingen (4-120 minuten).**
     * Als er langdurige verbindingen met keepalives zijn, reserveert u niet-actieve poorten voor langlopende verbindingen tot 120 minuten. Stel dat verouderde verbindingen worden stopgezet en dat poorten binnen vier minuten worden losgelaten voor nieuwe verbindingen 
* **Hiermee wordt aangegeven of u een TCP-reset wilt verzenden bij een time-out voor inactieve gegevens.**
     * Wanneer er time-outs worden gemaakt voor niet-actieve verbindingen, verzenden we dan een TCP RST naar de client en server zodat ze weten dat de stroom is verlaten?

## <a name="outbound-rule-definition"></a>Definitie van uitgaande regel

Uitgaande regels volgen dezelfde vertrouwde syntaxis als load balancing en inkomende NAT-regels: **back-endpool** voor  +    +  **front-endparameters.** 

Een uitgaande regel configureert uitgaande NAT voor alle virtuele machines die worden geïdentificeerd door de _back-endpool_ om te worden vertaald naar _de front-end_.  

De _parameters_ bieden extra fijngranenteerde controle over het uitgaande NAT-algoritme.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Uitgaande NAT schalen met meerdere IP-adressen

Elk extra IP-adres dat door een front-end wordt geleverd, biedt 64.000 kortstondige poorten die door load balancer als SNAT-poorten kunnen worden gebruikt. 

Gebruik meerdere IP-adressen om grootschalige scenario's te plannen. Gebruik uitgaande regels om [SNAT-uitputting te beperken.](troubleshoot-outbound-connection.md#snatexhaust) 

U kunt ook een openbaar [IP-voorvoegsel rechtstreeks](./load-balancer-outbound-connections.md#outboundrules) gebruiken met een uitgaande regel. 

Een openbaar IP-voorvoegsel verhoogt de schaal van uw implementatie. Het voorvoegsel kan worden toegevoegd aan de lijst met toegestane stromen die afkomstig zijn van uw Azure-resources. U kunt een front-end-IP-configuratie in de load balancer om te verwijzen naar een voorvoegsel van een openbaar IP-adres.  

De load balancer heeft controle over het openbare IP-voorvoegsel. De uitgaande regel maakt automatisch gebruik van alle openbare IP-adressen in het openbare IP-voorvoegsel voor uitgaande verbindingen. 

Elk van de IP-adressen binnen het openbare IP-voorvoegsel biedt 64.000 extra kortstondige poorten per IP-adres voor load balancer die als SNAT-poorten moeten worden gebruikt.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Time-out voor inactief zijn van uitgaande stroom en TCP opnieuw instellen

Regels voor uitgaand verkeer bieden een configuratieparameter voor het bepalen van de time-out voor inactieve uitgaande stromen en komen overeen met de behoeften van uw toepassing. Time-outs voor inactieve uitgaand verkeer zijn standaard 4 minuten. Zie Time-outs voor [inactieve gegevens configureren voor meer informatie.](load-balancer-tcp-idle-timeout.md) 

Het standaardgedrag van load balancer is om de stroom op de stille plaats te laten vallen wanneer de time-out voor uitgaand inactief is bereikt. De `enableTCPReset` parameter maakt een voorspelbaar toepassingsgedrag en -besturingselement mogelijk. De parameter bepaalt of bidirectionele TCP Reset (TCP RST) moet worden gestuurd bij de time-out van de time-out voor uitgaande inactieve gegevens. 

Controleer [TCP opnieuw instellen bij time-out voor inactieve gegevens,](./load-balancer-tcp-reset.md) waaronder de beschikbaarheid van regio's.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Uitgaande connectiviteit expliciet beveiligen en beheren

Taakverdelingsregels bieden automatische programmering van uitgaande NAT. Sommige scenario's profiteren of vereisen dat u de automatische programmering van uitgaande NAT door de taakverdelingsregel uit te schakelen. Door het uitschakelen via de regel kunt u het gedrag bepalen of verfijnen.  

U kunt deze parameter op twee manieren gebruiken:

1. Preventie van het binnenkomende IP-adres voor uitgaande SNAT. Schakel uitgaand SNAT uit in de taakverdelingsregel.
  
2. De uitgaande **SNAT-parameters afstemmen** van een IP-adres dat wordt gebruikt voor zowel binnenkomende als uitgaande gegevens. De automatische uitgaande NAT moet worden uitgeschakeld om een uitgaande regel de controle te geven. Als u de toewijzing van de SNAT-poort wilt wijzigen van een adres dat ook wordt gebruikt voor inkomende verkeer, moet de `disableOutboundSnat` parameter worden ingesteld op true. 

De bewerking voor het configureren van een uitgaande regel mislukt als u probeert een IP-adres opnieuw te definiëren dat wordt gebruikt voor binnenkomende verkeer.  Schakel eerst de uitgaande NAT van de taakverdelingsregel uit.

>[!IMPORTANT]
> Uw virtuele machine heeft geen uitgaande connectiviteit als u deze parameter in stelt op true en geen uitgaande regel hebt om uitgaande connectiviteit te definiëren.  Sommige bewerkingen van uw VM of uw toepassing zijn mogelijk afhankelijk van uitgaande connectiviteit. Zorg ervoor dat u de afhankelijkheden van uw scenario begrijpt en rekening hebt gehouden met de gevolgen van het maken van deze wijziging.

Soms is het niet wenselijk dat een VM een uitgaande stroom maakt. Het kan nodig zijn om te beheren welke bestemmingen uitgaande stromen ontvangen of welke bestemmingen beginnen met binnenkomende stromen. Gebruik [netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md) om de doelen te beheren die de VM bereikt. Gebruik NSG's om te beheren welke openbare bestemmingen binnenkomende stromen starten.

Wanneer u een NSG op een VM met load-balanceding wilt toepassen, let dan op [de servicetags](../virtual-network/network-security-groups-overview.md#service-tags) en [standaardbeveiligingsregels.](../virtual-network/network-security-groups-overview.md#default-security-rules) 

Zorg ervoor dat de VM statustestaanvragen kan ontvangen van Azure Load Balancer.

Als een NSG statustestaanvragen van de standaardtag AZURE_LOADBALANCER blokkeert, mislukt de VM-statustest en wordt de VM gemarkeerd als niet beschikbaar. De load balancer stopt met het verzenden van nieuwe stromen naar die VM.

## <a name="scenarios-with-outbound-rules"></a>Scenario's met uitgaande regels
        

### <a name="outbound-rules-scenarios"></a>Scenario's voor uitgaande regels


* Configureer uitgaande verbindingen naar een specifieke set openbare IP's of voorvoegsel.
* Wijzig [SNAT-poorttoewijzing.](load-balancer-outbound-connections.md)
* Schakel alleen uitgaand in.
* Alleen uitgaande NAT voor VM's (geen inkomende).
* Uitgaande NAT voor interne load balancer.
* Schakel beide TCP & UDP-protocollen in voor uitgaande NAT met een openbare load balancer.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Scenario 1: Uitgaande verbindingen met een specifieke set openbare IP's of voorvoegsels configureren


#### <a name="details"></a>Details


Gebruik dit scenario om uitgaande verbindingen zo aan te passen dat ze afkomstig zijn van een set openbare IP-adressen. Voeg openbare IP's of voorvoegsels toe aan een lijst voor toestaan of weigeren op basis van oorsprong.


Dit openbare IP-adres of voorvoegsel kan hetzelfde zijn als dat wordt gebruikt door een taakverdelingsregel. 


Een ander openbaar IP-adres of voorvoegsel gebruiken dan wordt gebruikt door een taakverdelingsregel: 


1. Maak een openbaar IP-voorvoegsel of een openbaar IP-adres.
2. Een openbare standaard load balancer 
3. Maak een front-end die verwijst naar het openbare IP-voorvoegsel of het openbare IP-adres dat u wilt gebruiken. 
4. Hergebruik een back-endpool of maak een back-endpool en plaats de VM's in een back-en-load balancer
5. Configureer een uitgaande regel op de openbare load balancer uitgaande NAT in te stellen voor de VM's met behulp van de front-end. Het wordt afgeraden om een taakverdelingsregel te gebruiken voor uitgaand verkeer. Schakel uitgaande SNAT uit op de taakverdelingsregel.


### <a name="scenario-2-modify-snat-port-allocation"></a><a name="scenario2out"></a>Scenario 2: [Toewijzing van SNAT-poort](load-balancer-outbound-connections.md) wijzigen


#### <a name="details"></a>Details


U kunt uitgaande regels gebruiken om de automatische toewijzing van [de SNAT-poort af te stemmen op basis van de grootte van de back-endpool.](load-balancer-outbound-connections.md#preallocatedports) 


Als u SNAT-uitputting ervaart, verhoogt u het aantal [SNAT-poorten](load-balancer-outbound-connections.md) dat is opgegeven vanaf de standaardwaarde van 1024. 


Elk openbaar IP-adres draagt bij tot 64.000 kortstondige poorten. Het aantal VM's in de back-endpool bepaalt het aantal poorten dat wordt gedistribueerd naar elke VM. Eén VM in de back-endpool heeft toegang tot maximaal 64.000 poorten. Voor twee VM's kunnen maximaal 32.000 [SNAT-poorten](load-balancer-outbound-connections.md) worden opgegeven met een uitgaande regel (2x 32.000 = 64.000). 


U kunt uitgaande regels gebruiken om de standaard opgegeven SNAT-poorten af te stemmen. U geeft meer of minder dan de [standaard SNAT-poorttoewijzing](load-balancer-outbound-connections.md) biedt. Elk openbaar IP-adres van een front-end van een uitgaande regel draagt bij aan 64.000 kortstondige poorten voor gebruik als [SNAT-poorten.](load-balancer-outbound-connections.md) 


Load balancer biedt [SNAT-poorten](load-balancer-outbound-connections.md) in veelvouden van 8. Als u een waarde op geeft die niet deelbaar is door 8, wordt de configuratiebewerking geweigerd. Elke taakverdelingsregel en inkomende NAT-regel verbruiken een bereik van 8 poorten. Als een taakverdeling of inkomende NAT-regel hetzelfde bereik van 8 deelt als een andere, worden er geen extra poorten gebruikt.


Als u meer [SNAT-poorten wilt](load-balancer-outbound-connections.md) geven dan beschikbaar zijn op basis van het aantal openbare IP-adressen, wordt de configuratiebewerking geweigerd. Als u bijvoorbeeld 10.000 poorten per VM en zeven VM's in een back-endpool één openbaar IP-adres deelt, wordt de configuratie geweigerd. Zeven vermenigvuldigd met 10.000 overschrijdt de poortlimiet van 64.000. Voeg meer openbare IP-adressen toe aan de front-end van de uitgaande regel om het scenario mogelijk te maken. 


Keer terug naar de [standaardpoorttoewijzing](load-balancer-outbound-connections.md#preallocatedports) door 0 op te geven voor het aantal poorten. De eerste 50 VM-exemplaren krijgen 1024 poorten, 51-100 VM-exemplaren krijgen 512 tot het maximum aantal exemplaren. Zie toewijzingstabel voor SNAT-poorten voor meer informatie over standaardtoewijzing van [SNAT-poorten.](./load-balancer-outbound-connections.md#preallocatedports)


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>Scenario 3: alleen uitgaand inschakelen


#### <a name="details"></a>Details


Gebruik een openbare standaard load balancer uitgaande NAT te bieden voor een groep VM's. In dit scenario gebruikt u een uitgaande regel op zichzelf, zonder dat er aanvullende regels zijn geconfigureerd.


> [!NOTE]
> **Azure Virtual Network NAT** kunt uitgaande connectiviteit bieden voor virtuele machines zonder dat er een load balancer. Zie [Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md) voor meer informatie.

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Scenario 4: Alleen uitgaande NAT voor VM's (geen inkomende)


> [!NOTE]
> **Azure Virtual Network NAT** kunt uitgaande connectiviteit bieden voor virtuele machines zonder dat er een load balancer. Zie [Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md) voor meer informatie.

#### <a name="details"></a>Details


Voor dit scenario zijn Azure Load Balancer uitgaande regels en Virtual Network NAT opties beschikbaar voor uitgaand verkeer vanuit een virtueel netwerk.


1. Maak een openbaar IP-adres of voorvoegsel.
2. Maak een openbare standaard load balancer. 
3. Maak een front-out die is gekoppeld aan het openbare IP-adres of het voorvoegsel dat is toegewezen voor uitgaand verkeer.
4. Maak een back-endpool voor de VM's.
5. Plaats de VM's in de back-endpool.
6. Configureer een uitgaande regel om uitgaande NAT in teschakelen.



Gebruik een voorvoegsel of openbaar IP-adres om [SNAT-poorten te schalen.](load-balancer-outbound-connections.md) Voeg de bron van uitgaande verbindingen toe aan een lijst met toegestane of weigeren verbindingen.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Scenario 5: Uitgaande NAT voor interne standaard load balancer


> [!NOTE]
> **Azure Virtual Network NAT** kan uitgaande connectiviteit bieden voor virtuele machines met behulp van een interne standaard load balancer. Zie [Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md) voor meer informatie.

#### <a name="details"></a>Details


Uitgaande connectiviteit is niet beschikbaar voor een interne standaard-load balancer totdat deze expliciet is gedeclareerd via openbare IP's of Virtual Network NAT op exemplaarniveau, of door de leden van de back-load balancer te koppelen aan een load balancer-configuratie voor alleen uitgaand verkeer. 


Zie Alleen-uitgaand verkeer [voor load balancer configuratie voor meer informatie.](./egress-only.md)




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Scenario 6: schakel beide TCP-& UDP-protocollen in voor uitgaande NAT met een openbare standaard load balancer


#### <a name="details"></a>Details


Wanneer u een openbare standaard load balancer, komt de automatisch uitgaande NAT overeen met het transportprotocol van de taakverdelingsregel. 


1. Schakel uitgaande [SNAT uit](load-balancer-outbound-connections.md) op de taakverdelingsregel. 
2. Configureer een uitgaande regel op dezelfde load balancer.
3. Hergebruik de back-endpool die al door uw VM's wordt gebruikt. 
4. Geef 'protocol': 'Alle' op als onderdeel van de uitgaande regel. 


Wanneer alleen inkomende NAT-regels worden gebruikt, wordt er geen uitgaande NAT opgegeven. 


1. Plaats VM's in een back-endpool.
2. Definieer een of meer front-end-IP-configuraties met openbare IP-adressen of openbaar IP-voorvoegsel 
3. Configureer een uitgaande regel op dezelfde load balancer. 
4. Geef 'protocol' op: 'Alle' als onderdeel van de uitgaande regel


## <a name="limitations"></a>Beperkingen

- Het maximum aantal bruikbaare kortstondige poorten per front-end-IP-adres is 64.000.
- Het bereik van de configureerbare time-out voor inactieve uitgaande verkeer is 4 tot 120 minuten (240 tot 7200 seconden).
- Load balancer biedt geen ondersteuning voor ICMP voor uitgaande NAT.
- Uitgaande regels kunnen alleen worden toegepast op de primaire IP-configuratie van een NIC.  U kunt geen uitgaande regel maken voor het secundaire IP-adres van een VM of NVA. Er worden meerdere NIC's ondersteund.
- Alle virtuele machines in een **beschikbaarheidsset moeten** worden toegevoegd aan de back-endpool voor uitgaande connectiviteit. 
- Alle virtuele machines in een **virtuele-machineschaalset** moeten worden toegevoegd aan de back-endpool voor uitgaande connectiviteit.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Standard Load Balancer](load-balancer-overview.md)
- Zie onze [veelgestelde vragen over Azure Load Balancer](load-balancer-faqs.md)
