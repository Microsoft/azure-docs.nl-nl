---
title: Azure Load Balancer voor uitgaande regels
description: In dit artikel wordt uitgelegd hoe u regels voor uitgaande verbindingen configureert voor het beheren van de uitgaand verkeer van Internet met Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 98bc962c0c57716cee9339056b0793bfe4bcb0ea
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694725"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Azure Load Balancer voor uitgaande regels

Met uitgaande regels kunt u voor een open bare standaard load balancer expliciet SNAT (bron Network Address Translation) definiëren. Met deze configuratie kunt u de open bare IP ('s) van uw load balancer gebruiken om uitgaande internet connectiviteit te bieden voor uw back-end-exemplaren.

Deze configuratie maakt het volgende mogelijk:

* IP-masker
* Vereenvoudig uw acceptatie lijsten.
* Hiermee wordt het aantal open bare IP-resources voor implementatie verminderd.

Met uitgaande regels hebt u volledige declaratieve controle over uitgaande internet connectiviteit. Met uitgaande regels kunt u deze mogelijkheid schalen en afstemmen op uw specifieke behoeften. 

Uitgaande regels worden alleen gevolgd als de back-end-VM geen openbaar IP-adres op exemplaar niveau (ILPIP) heeft.

![Load Balancer uitgaande regels](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Met uitgaande regels kunt u het gedrag voor uitgaande **SNAT** expliciet definiëren.

Met uitgaande regels kunt u het volgende beheren:

* **Welke virtuele machines worden omgezet naar de open bare IP-adressen.**
     * Twee regels zijn back-end-pool A maakt gebruik van IP-adres A en B. back-end-pool B gebruikt IP-adres C en D.
* **Hoe uitgaand SNAT-poorten worden toegewezen.**
     * Back-end-groep B is de enige groep die uitgaande verbindingen maakt, alle SNAT-poorten naar back-end-groep B en geen naar back-end-groep A heeft.
* **De protocollen waarvoor een uitgaande vertaling moet worden geboden.**
     * Back-end-pool B heeft UDP-poorten voor uitgaand verkeer nodig. Back-end-groep A vereist TCP. Geef TCP-poorten naar een UDP-poort aan B.
* **Welke duur moet worden gebruikt voor de time-out voor uitgaande verbindingen (4-120 minuten).**
     * Als er langlopende verbindingen met keepalives zijn, reserveert u niet-actieve poorten voor langlopende verbindingen gedurende 120 minuten. Aannemen dat verouderde verbindingen in 4 minuten worden afgebroken en de poorten voor nieuwe verbindingen worden vrijgegeven 
* **Hiermee wordt aangegeven of een TCP Reset moet worden verzonden bij een time-out voor inactiviteit.**
     * Wanneer er een time-out optreedt voor niet-actieve verbindingen, sturen we een eerste TCP-client naar de klant en server zodat ze weten dat de stroom wordt afgebroken?

## <a name="outbound-rule-definition"></a>Definitie van uitgaande regel

Uitgaande regels volgen dezelfde vertrouwde syntaxis als taak verdeling en binnenkomende NAT-regels: **frontend**-  +  **para meters**  +  **back-end-pool**. 

Met een uitgaande regel wordt uitgaande NAT geconfigureerd voor _alle virtuele machines die door de back-endadresgroep worden geïdentificeerd_ om te worden vertaald naar de front- _End_.  

De _para meters_ bieden extra nauw keurige controle over de uitgaande NAT-algoritme.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Uitgaand NAT schalen met meerdere IP-adressen

Elk extra IP-adres dat wordt geleverd door een front-end biedt extra 64.000 tijdelijke poorten voor load balancer die als SNAT-poorten kunnen worden gebruikt. 

Gebruik meerdere IP-adressen voor het plannen van grootschalige scenario's. Gebruik regels voor uitgaande verbindingen om de [SNAT-uitputting](troubleshoot-outbound-connection.md#snatexhaust)te verhelpen. 

U kunt ook een [openbaar IP-voor voegsel](./load-balancer-outbound-connections.md#outboundrules) rechtstreeks met een uitgaande regel gebruiken. 

Een openbaar IP-voor voegsel verg root de schaal van uw implementatie. Het voor voegsel kan worden toegevoegd aan de lijst met toegestane stromen die afkomstig zijn van uw Azure-resources. U kunt een front-end-IP-configuratie in de load balancer configureren om te verwijzen naar een openbaar IP-adres voorvoegsel.  

De load balancer heeft controle over het open bare IP-voor voegsel. Voor de uitgaande regel worden automatisch alle open bare IP-adressen gebruikt die zich in het open bare IP-voor voegsel voor uitgaande verbindingen bevinden. 

Elk van de IP-adressen binnen het open bare IP-voor voegsel biedt een extra 64.000 tijdelijke poorten per IP-adres voor load balancer om te gebruiken als SNAT-poorten.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Uitgaande time-out voor niet-actieve stroom en TCP Reset

Uitgaande regels bieden een configuratie parameter voor het beheren van de uitgaande time-out voor niet-actieve stroom en om deze te vergelijken met de behoeften van uw toepassing. Uitgaande time-outs voor inactiviteit zijn standaard ingesteld op 4 minuten. Zie een [time-out voor inactiviteit configureren](load-balancer-tcp-idle-timeout.md)voor meer informatie. 

Het standaard gedrag van load balancer is om de stroom op de achtergrond neer te zetten wanneer de uitgaande time-out voor inactiviteit is bereikt. De `enableTCPReset` para meter maakt een voorspelbaar toepassings gedrag en-beheer mogelijk. De para meter bepaalt of bidirectionele TCP Reset (TCP RST) moet worden verzonden bij de time-out van de uitgaande time-out voor inactiviteit. 

[TCP Reset controleren bij time-out voor inactiviteit](./load-balancer-tcp-reset.md) voor meer informatie over de beschik baarheid van regio's.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>De uitgaande connectiviteit expliciet beveiligen en beheren

Taakverdelings regels bieden automatische programmering van uitgaande NAT. Sommige scenario's profiteren of vereisen dat u de automatische programmering van uitgaande NAT door de taakverdelings regel uitschakelt. Door via de regel uit te scha kelen, kunt u het gedrag beheren of verfijnen.  

U kunt deze para meter op twee manieren gebruiken:

1. Het inkomende IP-adres voor uitgaande SNAT wordt voor komen. Schakel uitgaande SNAT uit in de taakverdelings regel.
  
2. Stem de uitgaande **SNAT** -para meters af van een IP-adres dat tegelijkertijd wordt gebruikt voor inkomend en uitgaand. De automatische uitgaande NAT moet worden uitgeschakeld zodat een regel voor uitgaand verkeer kan worden beheerd. Als u de toewijzing van de SNAT-poort van een adres dat ook wordt gebruikt voor inkomend, wilt wijzigen, `disableOutboundSnat` moet u de para meter instellen op True. 

De bewerking voor het configureren van een uitgaande regel mislukt als u probeert een IP-adres te definiëren dat wordt gebruikt voor inkomend verkeer.  Schakel eerst de uitgaande NAT van de taakverdelings regel uit.

>[!IMPORTANT]
> De virtuele machine heeft geen uitgaande verbinding als u deze para meter instelt op True en geen uitgaande regel hebt om uitgaande connectiviteit te definiëren.  Sommige bewerkingen van uw virtuele machine of uw toepassing kunnen afhankelijk zijn van een uitgaande verbinding. Zorg ervoor dat u bekend bent met de afhankelijkheden van uw scenario en dat dit gevolgen heeft voor het maken van deze wijziging.

Soms is het niet wenselijk dat een virtuele machine een uitgaande stroom maakt. Er is mogelijk een vereiste voor het beheren van welke doelen uitgaande stromen worden ontvangen of die doelen beginnen met binnenkomende stromen. Gebruik [netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md) om de doelen te beheren die de virtuele machine bereikt. Gebruik Nsg's om te beheren welke open bare bestemmingen binnenkomende stromen starten.

Wanneer u een NSG toepast op een virtuele machine met taak verdeling, moet u rekening houden met de [service Tags](../virtual-network/network-security-groups-overview.md#service-tags) en de [standaard beveiligings regels](../virtual-network/network-security-groups-overview.md#default-security-rules). 

Zorg ervoor dat de VM Health probe-aanvragen van Azure Load Balancer kan ontvangen.

Als een NSG de status test aanvragen blokkeert vanuit het standaard label AZURE_LOADBALANCER, mislukt de VM-status test en wordt de VM gemarkeerd als niet beschikbaar. De load balancer stopt met het verzenden van nieuwe stromen naar die virtuele machine.

## <a name="scenarios-with-outbound-rules"></a>Scenario's met uitgaande regels
        

### <a name="outbound-rules-scenarios"></a>Scenario's voor uitgaande regels


* Configureer uitgaande verbindingen naar een specifieke set open bare Ip's of voor voegsel.
* Wijzig de toewijzing van de [SNAT](load-balancer-outbound-connections.md) -poort.
* Alleen uitgaand verkeer inschakelen.
* Alleen uitgaand NAT voor Vm's (geen inkomend).
* Uitgaand NAT voor interne standaard load balancer.
* Schakel TCP & UDP-protocollen in voor uitgaande NAT met een open bare standaard load balancer.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Scenario 1: uitgaande verbindingen configureren voor een specifieke set open bare Ip's of voor voegsel


#### <a name="details"></a>Details


Gebruik dit scenario om uitgaande verbindingen op basis van een set open bare IP-adressen aan te passen. Open bare Ip's of voor voegsels toevoegen aan een lijst voor toestaan of weigeren op basis van de oorsprong.


Dit open bare IP-adres of voor voegsel kan hetzelfde zijn als het gebruik van een regel voor taak verdeling. 


Een ander openbaar IP-adres of-voor voegsel gebruiken dan wordt gebruikt door een regel voor taak verdeling: 


1. Openbaar IP-voor voegsel of openbaar IP-adres maken.
2. Een open bare standaard load balancer maken 
3. Een frontend maken die verwijst naar het open bare IP-voor voegsel of het open bare IP-adres dat u wilt gebruiken. 
4. Hergebruik een back-end-pool of maak een back-end-pool en plaats de virtuele machines in een back-end-groep van de open bare load balancer
5. Configureer een uitgaande regel op de open bare load balancer om uitgaande NAT voor de virtuele machines in te scha kelen met behulp van de front-end. Het is niet raadzaam om een taakverdelings regel te gebruiken voor uitgaand verkeer, uitgaande SNAT uit te scha kelen op de regel voor taak verdeling.


### <a name="scenario-2-modify-snatport-allocation"></a><a name="scenario2out"></a>Scenario 2: toewijzing van de [SNAT](load-balancer-outbound-connections.md)-poort wijzigen


#### <a name="details"></a>Details


U kunt uitgaande regels gebruiken voor het afstemmen van de [automatische toewijzing van de SNAT-poort op basis van de back-endadresgroep](load-balancer-outbound-connections.md#preallocatedports). 


Als u een SNAT-uitputting ondervindt, verhoogt u het aantal [SNAT](load-balancer-outbound-connections.md)-poorten dat is opgegeven in de standaard waarde van 1024. 


Elk openbaar IP-adres draagt bij aan Maxi maal 64.000 tijdelijke poorten. Het aantal virtuele machines in de back-endadresgroep bepaalt het aantal poorten dat naar elke VM wordt gedistribueerd. Een virtuele machine in de back-end-pool heeft toegang tot Maxi maal 64.000 poorten. Voor twee virtuele machines kunnen Maxi maal 32.000 [SNAT](load-balancer-outbound-connections.md)-poorten worden opgegeven met een uitgaande regel (2x 32.000 = 64.000). 


U kunt regels voor uitgaande verbindingen gebruiken voor het afstemmen van de maximale SNAT-poorten. U geeft meer of minder dan de standaard toewijzing van de [SNAT](load-balancer-outbound-connections.md)-poort. Elk openbaar IP-adres van een front-end van een uitgaande regel draagt bij aan Maxi maal 64.000 tijdelijke poorten voor gebruik als [SNAT](load-balancer-outbound-connections.md)-poorten. 


Load Balancer geeft [SNAT](load-balancer-outbound-connections.md)-poorten in veelvouden van 8. Als u een waarde opgeeft die niet deelbaar is door 8, wordt de configuratie bewerking geweigerd. Voor elke taakverdelings regel en binnenkomende NAT-regel wordt een bereik van 8 poorten gebruikt. Als een taak verdeling of binnenkomende NAT-regel hetzelfde bereik van 8 deelt als een andere, worden er geen extra poorten gebruikt.


Als u meer [SNAT](load-balancer-outbound-connections.md)-poorten probeert te geven dan beschikbaar zijn op basis van het aantal open bare IP-adressen, wordt de configuratie bewerking geweigerd. Als u bijvoorbeeld 10.000 poorten per VM geeft en zeven Vm's in een back-end-pool een enkel openbaar IP-adres delen, wordt de configuratie geweigerd. Zeven vermenigvuldigd met 10.000 overschrijdt de limiet voor 64.000 poorten. Voeg meer open bare IP-adressen toe aan de front-end van de regel voor uitgaande verbindingen om het scenario in te scha kelen. 


Keer terug naar de [standaard poort toewijzing](load-balancer-outbound-connections.md#preallocatedports) door 0 op te geven voor het aantal poorten. De eerste 50 VM-exemplaren krijgen 1024 poorten, 51-100 VM-instanties krijgen 512 tot Maxi maal het maximum aantal exemplaren. Zie [toewijzings tabel voor SNAT-poorten](./load-balancer-outbound-connections.md#preallocatedports)voor meer informatie over de standaard toewijzing van de SNAT-poort.


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>Scenario 3: alleen uitgaand verkeer inschakelen


#### <a name="details"></a>Details


Gebruik een open bare standaard load balancer om uitgaande NAT te bieden voor een groep Vm's. In dit scenario gebruikt u een uitgaande regel zelf, zonder dat er extra regels zijn geconfigureerd.


> [!NOTE]
> **Azure Virtual Network NAT** kan uitgaande connectiviteit bieden voor virtuele machines zonder dat hiervoor een Load Balancer nodig is. Zie [Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md) voor meer informatie.

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Scenario 4: uitgaand NAT voor alleen Vm's (geen inkomend)


> [!NOTE]
> **Azure Virtual Network NAT** kan uitgaande connectiviteit bieden voor virtuele machines zonder dat hiervoor een Load Balancer nodig is. Zie [Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md) voor meer informatie.

#### <a name="details"></a>Details


Voor dit scenario: Azure Load Balancer regels voor uitgaande verbindingen en Virtual Network NAT zijn opties beschikbaar voor het afbreken van een virtueel netwerk.


1. Maak een openbaar IP-adres of een voor voegsel.
2. Maak een open bare standaard load balancer. 
3. Maak een front-end dat is gekoppeld aan het open bare IP-adres of het voor voegsel dat voor uitgaand
4. Maak een back-end-pool voor de Vm's.
5. Plaats de virtuele machines in de back-end-pool.
6. Een uitgaande regel configureren om uitgaande NAT in te scha kelen.



Gebruik een voor voegsel of openbaar IP-adres om [SNAT](load-balancer-outbound-connections.md)-poorten te schalen. Voeg de bron van uitgaande verbindingen toe aan een lijst voor toestaan of weigeren.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Scenario 5: uitgaand NAT voor interne standaard load balancer


> [!NOTE]
> **Azure Virtual Network NAT** kan uitgaande connectiviteit bieden voor virtuele machines die gebruikmaken van een interne standaard Load Balancer. Zie [Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md) voor meer informatie.

#### <a name="details"></a>Details


Er is geen uitgaande verbinding beschikbaar voor een interne standaard load balancer tot deze expliciet is gedeclareerd via open bare Ip's op exemplaar niveau of Virtual Network NAT of door de back-endadresgroep te koppelen aan een load balancer configuratie met uitgaand verkeer. 


Zie voor meer informatie [Load Balancer configuratie voor alleen-uitgaand verkeer](./egress-only.md).




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Scenario 6: Schakel TCP & UDP-protocollen in voor uitgaande NAT met een open bare standaard load balancer


#### <a name="details"></a>Details


Wanneer u een open bare standaard load balancer gebruikt, komt de automatische uitgaande NAT die overeenkomt met het transport protocol van de taakverdelings regel. 


1. Schakel uitgaande [SNAT](load-balancer-outbound-connections.md)uit op de taakverdelings regel. 
2. Een uitgaande regel configureren op hetzelfde load balancer.
3. Gebruik de back-end-groep die al door uw virtuele machines wordt gebruikt. 
4. Geef ' Protocol ': ' all ' op als onderdeel van de uitgaande regel. 


Wanneer alleen binnenkomende NAT-regels worden gebruikt, wordt er geen uitgaand NAT-verkeer gegeven. 


1. Virtuele machines in een back-end-groep plaatsen.
2. Een of meer frontend-IP-configuraties met open bare IP-adressen of open bare IP-voor voegsel definiëren 
3. Een uitgaande regel configureren op hetzelfde load balancer. 
4. Geef ' Protocol ': ' all ' op als onderdeel van de uitgaande regel


## <a name="limitations"></a>Beperkingen

- Het maximale aantal bruikbare tijdelijke poorten per frontend-IP-adres is 64.000.
- Het bereik van de Configureer bare uitgaande time-out voor inactiviteit is 4 tot 120 minuten (240 tot 7200 seconden).
- De Load Balancer biedt geen ondersteuning voor ICMP voor uitgaande NAT.
- Uitgaande regels kunnen alleen worden toegepast op de primaire IP-configuratie van een NIC.  U kunt geen uitgaande regel maken voor het secundaire IP-adres van een virtuele machine of NVA. Er worden meerdere Nic's ondersteund.
- Alle virtuele machines in een **beschikbaarheidsset** moeten worden toegevoegd aan de back-end-groep voor uitgaande verbindingen. 
- Alle virtuele machines in een **schaalset voor virtuele** machines moeten worden toegevoegd aan de back-end-groep voor uitgaande verbindingen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Standard Load Balancer](load-balancer-overview.md)
- Bekijk onze [Veelgestelde vragen over Azure Load Balancer](load-balancer-faqs.md)