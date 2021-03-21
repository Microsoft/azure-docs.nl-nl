---
title: Virtuele netwerken ontwerpen met NAT-gatewayresources
titleSuffix: Azure Virtual Network NAT
description: Meer informatie over hoe u virtuele netwerken ontwerpt met NAT-gatewayresources.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: 5c70c575464d82f714022291406418cdd1ca0f8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102094963"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Virtuele netwerken ontwerpen met NAT-gatewayresources

NAT-gatewayresources maken deel uit van [Virtual Network NAT](nat-overview.md) en bieden uitgaande internetconnectiviteit voor een of meer subnetten van een virtueel netwerk. Het subnet van het virtuele netwerk geeft aan welke NAT-gateway wordt gebruikt. NAT biedt Source Network Address Translation (SNAT) voor een subnet.  NAT-gatewayresources geven aan welke statische IP-adressen virtuele machines gebruiken bij het maken van uitgaande stromen. Statische IP-adressen zijn afkomstig van open bare IP-adres bronnen (PIP), open bare IP-prefix bronnen of beide. Als er een resource voor een openbaar IP-voorvoegsel wordt gebruikt, worden alle IP-adressen van de volledige resource voor het openbare IP-voorvoegsel verbruikt door een NAT-gatewayresource. Een NAT-gatewayresource kan in totaal tot zestien statische IP-adressen gebruiken.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="Virtual Network NAT voor uitgaand verkeer naar internet">
</p>

*Afbeelding: Virtual Network NAT voor uitgaand verkeer naar internet*

## <a name="how-to-deploy-nat"></a>NAT implementeren

Het configureren en gebruiken van NAT-gateway is opzettelijk eenvoudig gemaakt:  

NAT-gatewayresource:
- Maak een regionale of zonegebonden (zone-geïsoleerde) NAT-gatewayresource,
- Wijs IP-adressen toe,
- Wijzig, indien nodig, de TCP-time-out voor inactiviteit (optioneel).  Bekijk de [timers](#timers) <ins>voordat</ins> u de standaardinstelling wijzigt.

Virtueel netwerk:
- Configureer het subnet van het virtuele netwerk om een NAT-gateway te gebruiken.

Door de gebruiker gedefinieerde routes zijn niet nodig.

## <a name="resource"></a>Resource

De resource is zo ontworpen dat deze eenvoudig is, zoals u kunt zien in het volgende Azure Resource Manager-voorbeeld in een op een sjabloon lijkende indeling.  Deze op een sjabloon lijkende indeling wordt hier weergegeven om de concepten en de structuur te illustreren.  Pas het voorbeeld aan uw behoeften aan.  Dit document is niet bedoeld als een zelfstudie.

In het volgende diagram ziet u de beschrijfbare verwijzingen tussen de verschillende Azure Resource Manager-resources.  De pijl geeft de richting van de verwijzing aan, afkomstig van de locatie waar deze kan worden geschreven. Beoordelen 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP and an IP prefix." width="256" title="Virtual Network NAT-objectmodel">
</p>

*Afbeelding: Virtual Network NAT-objectmodel*

NAT wordt aanbevolen voor de meeste workloads, tenzij u een specifieke afhankelijkheid hebt op [uitgaande connectiviteit van Load Balancer op basis van pools](../load-balancer/load-balancer-outbound-connections.md).  

U kunt migreren vanaf scenario's met loads balancers van het type Standard, waaronder [uitgaande regels](../load-balancer/load-balancer-outbound-connections.md#outboundrules), naar de NAT-gateway. Als u wilt migreren, verplaatst u de openbare IP en de resources voor het openbare IP-voorvoegsel van load balancer-frontends naar de NAT-gateway. Nieuwe IP-adressen voor NAT-gateway zijn niet vereist. Standaardresources voor het openbare IP-adres en resources voor het openbare IP-voorvoegsel kunnen opnieuw worden gebruikt zolang het totaal niet groter is dan zestien IP-adressen. Plan voor migratie met service-onderbreking tijdens de overgang in uw achterhoofd.  U kunt de onderbreking minimaliseren door het proces te automatiseren. Test eerst de migratie in een faseringsomgeving.  Tijdens de overgang worden de inkomende stromen niet beïnvloed.


Het volgende voorbeeld is een fragment uit een Azure Resource Manager-sjabloon.  Met deze sjabloon worden verschillende resources geïmplementeerd, waaronder een NAT-gateway.  In dit voorbeeld bevat de sjabloon de volgende parameters:

- **natgatewaynaam**: naam van de NAT-gateway.
- **locatie**: Azure-regio waar de resource zich bevindt.
- **naamopenbareip**: de naam van het uitgaande openbare IP-adres dat is gekoppeld aan de NAT-gateway.
- **vnetnaam**: de naam van het virtuele netwerk.
- **subnetnaam**: de naam van het uitgaande openbare IP-adres dat is gekoppeld aan de NAT-gateway.

Het totale aantal IP-adressen dat door alle resources van IP-adressen en voorvoegsels wordt verschaft, mag niet groter zijn dan zestien IP-adressen. Elk willekeurig aantal IP-adressen tussen 1 en 16 is toegestaan.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

Wanneer de NAT-gatewayresource is gemaakt, kan deze worden gebruikt voor een of meer subnetten van een virtueel netwerk. Geef op welke subnetten deze NAT-gatewayresource gebruiken. Een NAT-gateway kan niet meer dan één virtueel netwerk omvatten. Het is niet nodig om dezelfde NAT-gateway toe te wijzen aan alle subnetten van een virtueel netwerk. Afzonderlijke subnetten kunnen worden geconfigureerd met verschillende NAT-gatewayresources.

Scenario's waarin geen beschikbaarheidszones worden gebruikt, zijn regionaal (geen zone opgegeven). Als u beschikbaarheidszones gebruikt, kunt u een zone opgeven om NAT te isoleren voor een specifieke zone. Zone-redundantie wordt niet ondersteund. Bestudeer de [beschikbaarheidszones](#availability-zones) van NAT.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

NAT-gateways worden gedefinieerd met een eigenschap in een subnet binnen een virtueel netwerk. Stromen die zijn gemaakt door virtuele machines op subnet **subnetnaam** van het virtuele netwerk **vnetnaam** gebruiken de NAT-gateway. Alle uitgaande connectiviteit gebruikt de IP-adressen die zijn gekoppeld aan **natgatewaynaam** als bron-IP-adres.

Voor meer informatie over de Azure Resource Manager-sjabloon die in dit voorbeeld wordt gebruikt, raadpleegt u:

- [Snelstart: Een NAT-gateway maken: Resource Manager-sjabloon](quickstart-create-nat-gateway-template.md)
- [Virtual Network NAT](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Begeleiding bij ontwerp

Lees deze sectie om vertrouwd te raken met overwegingen bij het ontwerpen van virtuele netwerken met NAT.  

1. [Kostenoptimalisatie](#cost-optimization)
1. [Co-existentie van inkomend en uitgaand](#coexistence-of-inbound-and-outbound)
2. [Basisresources beheren](#managing-basic-resources)
3. [Beschikbaarheidszones](#availability-zones)

### <a name="cost-optimization"></a>Kostenoptimalisatie

[Service-eindpunten](virtual-network-service-endpoints-overview.md) en [Private Link](../private-link/private-link-overview.md) zijn opties waarmee u rekening moet houden om de kosten te optimaliseren. NAT is niet nodig voor deze services. Verkeer dat is bestemd voor service-eindpunten of Private Link, wordt niet verwerkt door de NAT van het virtuele netwerk.  

Service-eindpunten koppelen Azure-serviceresources aan uw virtuele netwerk en beheren de toegang tot uw Azure-serviceresources. Als u bijvoorbeeld toegang hebt tot Azure Storage, gebruikt u een service-eindpunt voor opslag om NAT-kosten voor verwerkte gegevens te voorkomen. Service-eindpunten zijn gratis.

Met Private link wordt de Azure PaaS-service (of andere services die worden gehost met Private Link) weergegeven als een privé-eindpunt in een virtueel netwerk.  Private Link wordt gefactureerd op basis van de duur en de verwerkte gegevens.

Bepaal of een van beide benaderingen goed past bij uw scenario en gebruik waar nodig.

### <a name="coexistence-of-inbound-and-outbound"></a>Co-existentie van inkomend en uitgaand

De NAT-gateway is compatibel met:

 - Load balancer van het type Standard
 - Standaard openbaar IP
 - Standaard openbaar IP-voorvoegsel

Wanneer u een nieuwe implementatie gaat ontwikkelen, begint u met standaard-SKU's.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network." width="256" title="Virtual Network NAT voor uitgaand verkeer naar internet">
</p>

*Afbeelding: Virtual Network NAT voor uitgaand verkeer naar internet*

Het scenario voor alleen uitgaand verkeer naar internet van de NAT-gateway kan worden uitgebreid met inkomend verkeer vanaf internetfunctionaliteit. Elke resource is op de hoogte van de richting waaruit een stroom afkomstig is. In een subnet met een NAT-gateway worden alle naar internet uitgaande scenario's vervangen door de NAT-gateway. Van internet inkomende scenario's worden door de betreffende resource verschaft.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT en VM met openbaar IP-adres op instantieniveau

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP." width="300" title="Virtual Network NAT en VM met openbaar IP-adres op instantieniveau">
</p>

*Afbeelding: Virtual Network NAT en VM met openbaar IP-adres op instantieniveau*

| Richting | Resource |
|:---:|:---:|
| Inkomend | VM met openbaar IP-adres op instantieniveau |
| Uitgaand | NAT-gateway |

VM gebruikt NAT-gateway voor uitgaand verkeer.  Dit is niet van invloed op inkomend verkeer.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT en VM met openbare Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="Virtual Network NAT en VM met openbare Load Balancer">
</p>

*Afbeelding: Virtual Network NAT en VM met openbare Load Balancer*

| Richting | Resource |
|:---:|:---:|
| Inkomend | openbare Load Balancer |
| Uitgaand | NAT-gateway |

Elke uitgaande configuratie van een taakverdelingsregel of uitgaande regels wordt vervangen door de NAT-gateway.  Dit is niet van invloed op inkomend verkeer.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT en VM met openbaar IP en openbare Load Balancer op instantieniveau

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="425" title="Virtual Network NAT en VM met openbaar IP en openbare Load Balancer op instantieniveau">
</p>

*Afbeelding: Virtual Network NAT en VM met openbaar IP en openbare Load Balancer op instantieniveau*

| Richting | Resource |
|:---:|:---:|
| Inkomend | VM met openbaar IP en openbare Load Balancer op instantieniveau |
| Uitgaand | NAT-gateway |

Elke uitgaande configuratie van een taakverdelingsregel of uitgaande regels wordt vervangen door de NAT-gateway.  De VM gebruikt ook de NAT-gateway voor uitgaand verkeer.  Dit is niet van invloed op inkomend verkeer.

### <a name="managing-basic-resources"></a>Basic-resources beheren

De load balancer van het type Standard, het openbare IP en het openbare IP-voorvoegsel zijn compatibel met de NAT-gateway. NAT-gateways kunnen worden gebruikt binnen het bereik van een subnet. De basis-SKU van deze services moet worden geïmplementeerd op een subnet zonder een NAT-gateway. Met deze scheiding kunnen beide SKU-varianten naast elkaar bestaan in hetzelfde virtuele netwerk.

NAT-gateways hebben voorrang boven uitgaande scenario's van het subnet. Load balancer van het type Basic of openbaar IP (en elke beheerde service die ermee is gebouwd) kan niet worden aangepast met de juiste omzettingen. NAT-gateway neemt de controle over uitgaand internetverkeer over op een subnet. Inkomend verkeer naar een load balancer van het type Basic en openbaar IP is niet beschikbaar. Inkomend verkeer naar een load balancer van het type Basic en/of een openbaar IP dat op een VM is geconfigureerd, is niet beschikbaar.

### <a name="availability-zones"></a>Beschikbaarheidszones

#### <a name="zone-isolation-with-zonal-stacks"></a>Zone-isolatie met zonegebonden stacks

<p align="center">
  <img src="media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="425" title="Virtual Network NAT met zone-isolatie, die meerdere 'zonegebonden stacks' maakt "zonal stacks"">
</p>

*Afbeelding: Virtual Network NAT met zone-isolatie, die meerdere 'zonegebonden stacks' maakt*

Zelfs zonder beschikbaarheidszones is NAT robuust en kan meerdere storingen in infrastructuuronderdelen overleven.  Beschikbaarheidszones die zijn gebouwd op deze tolerantie met zone-isolatiescenario's voor NAT.

Virtuele netwerken en hun subnetten zijn regionale constructies.  Subnetten zijn niet beperkt tot een zone.

Er bestaat een zonegebonden promise voor zone-isolatie wanneer een instantie van een virtuele machine met een NAT-gatewayresource zich in dezelfde zone bevindt als de NAT-gatewayresource en de bijbehorende openbare IP-adressen. Het patroon dat u het beste kunt gebruiken voor zone-isolatie is een 'zonegebonden stack' per beschikbaarheidszone.  Deze 'zonegebonden stack' bestaat uit VM-instanties, NAT-gatewayresources, resources van openbaar IP-adres en/of voorvoegsel in een subnet waarvoor alleen dezelfde zone wordt gebruikt.   De besturingsvlakbewerkingen en het gegevensvlak worden er vervolgens mee uitgelijnd en beperkt tot de opgegeven zone. 

Een fout in een andere zone dan waar uw scenario bestaat, heeft naar verwachting geen invloed op NAT. Uitgaand verkeer vanaf virtuele machines in dezelfde zone kan niet worden uitgevoerd vanwege zone-isolatie.  

#### <a name="integrating-inbound-endpoints"></a>Binnenkomende eindpunten integreren

Als voor uw scenario inkomende eindpunten zijn vereist, hebt u twee opties:

| Optie | Patroon | Voorbeeld | Pluspunt | Minpunt |
|---|---|---|---|---|
| (1) | U moet de inkomende eindpunten **uitlijnen** met de respectieve **zonegebonden stacks** die u maakt voor uitgaand. | Maak een load balancer van het type Standard met een zonegebonden frontend. | Hetzelfde statusmodel en dezelfde foutmodus voor inkomend en uitgaand. Eenvoudiger in gebruik. | Afzonderlijke IP-adressen per zone moeten mogelijk worden gemaskeerd met een algemene DNS-naam. |
| (2) | U moet een **overlay** toevoegen aan de zonegebonden stacks met een inkomend eindpunt **over meerdere zones**. | Maak een load balancer van het type Standard met een zone-redundante frontend. | Eén IP-adres voor inkomend eindpunt. | Verschillende statusmodellen en foutmodi voor inkomend en uitgaand.  Ingewikkelder in gebruik. |

>[!NOTE]
> Voor een zone-geïsoleerde NAT-gateway moeten IP-adressen overeenkomen met de zone van de NAT-gateway. NAT-gatewayresources met IP-adressen uit een andere zone of zonder een zone zijn niet toegestaan.

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>Uitgaande scenario's over meerdere zones worden niet ondersteund

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet, with the connections between to of the gateways and their subnets broken." width="425" title="Virtual Network NAT is niet compatibel met het zone-omvattende subnet">
</p>

*Afbeelding: Virtual Network NAT is niet compatibel met het zone-omvattende subnet*

U kunt geen zonegebonden promise bereiken met NAT-gatewayresources wanneer instanties van virtuele machines worden geïmplementeerd in meerdere zones binnen hetzelfde subnet.   En zelfs als er meerdere zonegebonden NAT-gateways zijn gekoppeld aan een subnet, weet de instantie van de virtuele machine niet welke NAT-gatewayresource moet worden geselecteerd.

Er bestaat een zonegebonden Promise does't wanneer de zone van een exemplaar van een virtuele machine en de zone van een zonegebonden NAT-gateway niet zijn uitgelijnd of b) een regionale NAT-gateway resource wordt gebruikt met zonegebonden exemplaren van virtuele machines.

Hoewel het scenario lijkt te werken, zijn het statusmodel en de foutmodus niet gedefinieerd vanuit het oogpunt van de beschikbaarheidszone. U kunt in plaats daarvan werken met zonegebonden stacks of volledig regionaal.

>[!NOTE]
>De zone-eigenschap van een NAT-gatewayresource is niet veranderlijk.  Implementeer de NAT-gatewayresource opnieuw met de beoogde regionale of zonale voorkeur.

>[!NOTE] 
>IP-adressen op zichzelf zijn niet zone-redundant als er geen zone is opgegeven.  De frontend van een [Standard Load Balancer is zone-redundant](../load-balancer/load-balancer-standard-availability-zones.md) als er geen IP-adres in een specifieke zone wordt gemaakt.  Dit is niet van toepassing op NAT.  Alleen regionale of zonale isolatie wordt ondersteund.

## <a name="performance"></a>Prestaties

Elke NAT-gatewayresource kan tot 50 Gbps aan doorvoer bieden. U kunt uw implementaties splitsen in meerdere subnetten en een NAT-gateway toewijzen aan elk subnet of groepen subnetten om uit te schalen.

Elke NAT-gateway kan 64.000 stromen voor TCP en UDP respectievelijk ondersteunen per toegewezen uitgaand IP-adres.  Raadpleeg de volgende sectie over SNAT (Source Network Address Translation) en het [artikel over problemen oplossen](./troubleshoot-nat.md) voor specifieke richtlijnen voor probleemoplossing.

## <a name="source-network-address-translation"></a>Source Network Address Translation

Met Source Network Address Translation (SNAT) wordt de bron van een stroom herschreven om van een ander IP-adres te komen.  NAT-gatewayresources gebruiken een variant van SNAT die meestal wordt aangeduid met Port Address Translation (PAT). Met PAT worden het bronadres en de bronpoort herschreven. Met SNAT is er geen vaste relatie tussen het aantal privé-adressen en de omgezette openbare adressen.  

### <a name="fundamentals"></a>Basisprincipes

Laten we eens kijken naar een voorbeeld van vier stromen om het basisconcept uit te leggen.  De NAT-gateway maakt gebruik van de resource 65.52.1.1 van het open bare IP-adres en de virtuele machine maakt verbinding met 65.52.0.1.

| Stroom | Bron-tuple | Doel-tuple |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

Deze stromen kunnen er als volgt uitzien nadat er een PAT heeft plaatsgevonden:

| Stroom | Bron-tuple | Bron-tuple met SNAT | Doel-tuple | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

De doel bron van de stroom wordt weer gegeven als 65.52.0.1 (SNAT-bron-tuple) met de toegewezen poort.  PAT zoals weergegeven in de voorgaande tabel wordt ook een poortmaskerende SNAT genoemd.  Meerdere privé-bronnen worden achter een IP en poort gemaskeerd.  

#### <a name="source-snat-port-reuse"></a>Bron (SNAT) opnieuw gebruiken van poort

NAT-gateways gebruiken de bron (SNAT) poorten voor het gebruik van opportunistisch.  Hieronder ziet u dit concept als een extra stroom voor de aan-set stromen.  De virtuele machine in het voor beeld is een stroom naar 65.52.0.2.

| Stroom | Bron-tuple | Doel-tuple |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Een NAT-gateway zal stroom 4 waarschijnlijk omzetten naar een poort die ook voor andere bestemmingen kan worden gebruikt.  Zie [schalen](#scaling) voor aanvullende discussies over het correct aanpassen van de grootte van uw IP-adres.

| Stroom | Bron-tuple | Bron-tuple met SNAT | Doel-tuple | 
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

Maak geen afhankelijk van de specifieke manier waarop bron poorten zijn toegewezen in het bovenstaande voor beeld.  Het voorgaande is alleen maar een illustratie van het fundamentele concept.

De door NAT verschafte SNAT wijkt in verschillende opzichten af van [Load Balancer](../load-balancer/load-balancer-outbound-connections.md).

### <a name="on-demand"></a>Op aanvraag

NAT biedt SNAT-poorten op aanvraag voor nieuwe uitgaande verkeersstromen. Alle beschikbare SNAT-poorten in de voorraad worden gebruikt door een virtuele machine op subnetten die zijn geconfigureerd met NAT. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T." width="550" title="Virtual Network NAT op aanvraag uitgaande SNAT">
</p>

*Afbeelding: Virtual Network NAT op aanvraag uitgaande SNAT*

Elke IP-configuratie van een virtuele machine kan waar nodig uitgaande stromen op aanvraag maken.  Toewijzing vooraf, planning per instantie inclusief overinrichting per instantie voor het ongunstigste geval, is niet vereist.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T with exhaustion threshold." width="550" title="Verschillen in uitputtingsscenario's">
</p>

*Afbeelding: Verschillen in uitputtingsscenario's*

Wanneer een SNAT-poort eenmaal is vrijgegeven, kan deze worden gebruikt door elke virtuele machine op subnetten die zijn geconfigureerd met NAT.  Bij toewijzing op aanvraag kunnen dynamische en uiteenlopende workloads op subnet(ten) waar nodig gebruikmaken van SNAT-poorten.  Zolang er een SNAT-poortvoorraad beschikbaar is, slagen de SNAT-stromen. In plaats daarvan profiteren de hotspots van SNAT-poorten van de grotere voorraad. De SNAT-poorten worden niet ongebruikt gelaten voor virtuele machines die ze niet actief nodig hebben.

### <a name="scaling"></a>Schalen

Het schalen van NAT is hoofdzakelijk een functie voor het beheren van de gedeelde, beschikbare SNAT-poortvoorraad. NAT heeft voldoende SNAT-poortvoorraad nodig voor verwachte uitgaande piekstromen voor alle subnetten die zijn gekoppeld aan een NAT-gatewayresource.  U kunt resources voor openbare IP-adressen, resources voor openbare IP-voorvoegsels of beide gebruiken om een SNAT-poortvoorraad te maken.  

>[!NOTE]
>Als u een resource voor een openbaar IP-voorvoegsel toewijst, wordt het volledige openbare IP-voorvoegsel gebruikt.  U kunt geen resource voor een openbaar IP-voorvoegsel toewijzen en vervolgens afzonderlijke IP-adressen splitsen om ze toe te wijzen aan andere resources.  Als u afzonderlijke IP-adressen van een openbaar IP-voorvoegsel aan meerdere resources wilt toewijzen, moet u afzonderlijke openbare IP-adressen maken op basis van de resource van het openbare IP-voorvoegsel en deze waar nodig toewijzen in plaats van de resource van het openbare IP-voorvoegsel zelf.

Met SNAT worden privé-adressen toegewezen aan een of meer openbare IP-adressen, waarbij het bronadres en de bronpoort worden herschreven in de processen. Een NAT-gatewayresource maakt voor deze omzetting gebruik van 64.000 poorten (SNAT-poorten) per geconfigureerd openbaar IP-adres. NAT-gatewayresources kunnen tot 16 IP-adressen en 1 miljoen SNAT-poorten schalen. Als er een resource voor een openbaar IP-voorvoegsel wordt opgegeven, levert elk IP-adres in het voorvoegsel SNAT-poortvoorraad. En wanneer meer openbare IP-adressen worden toegevoegd, wordt de beschikbare voorraad van SNAT-poorten ook verhoogd. TCP en UDP zijn afzonderlijke SNAT-poortvoorraden en zijn niet gerelateerd.

NAT-gateway bronnen gebruiken de bron (SNAT)-poorten voor een opportunistisch gebruik. Als ontwerp richtlijnen voor schaal baarheid moet u aannemen dat elke stroom een nieuwe SNAT-poort vereist en het totale aantal beschik bare IP-adressen voor uitgaand verkeer kan schalen.  U moet zorgvuldig rekening houden met de schaal die u ontwerpt en het inrichten van IP-adres aantallen overeenkomstig.

De SNAT-poorten naar verschillende bestemmingen worden waarschijnlijk als mogelijk opnieuw gebruikt. En als de methoden voor het afzuigen van de SNAT-poort, kunnen stromen mislukken.  

Zie de [basis principes van SNAT](#source-network-address-translation) voor beeld.


### <a name="protocols"></a>Protocollen

NAT-gatewayresources communiceren met IP- en IP-transportheaders van UDP- en TCP-stromen en zijn agnostisch ten opzichte van nettoladingen van toepassingslagen.  Andere IP-protocollen worden niet ondersteund.

### <a name="timers"></a>Timers

>[!IMPORTANT]
>Een lange timer voor inactiviteit kan de kans op SNAT-uitputting onnodig vergroten. Hoe langer de timer is die u opgeeft, hoe langer NAT SNAT-poorten vasthoudt, totdat er uiteindelijk een time-out voor inactiviteit optreedt. Als er een time-out voor inactiviteit optreedt voor uw stromen, zullen ze uiteindelijk toch niet kunnen worden uitgevoerd en wordt onnodig SNAT-poortinventaris verbruikt.  Stromen die na 2 uur mislukken, zouden ook met de standaard 4 minuten zijn mislukt. Het vergroten van de time-out voor inactiviteit is een laatste redmiddel dat spaarzaam moet worden gebruikt. Als een stroom nooit inactief is, wordt deze niet beïnvloed door de niet-actieve timer.

De time-out voor inactiviteit van TCP kan worden aangepast van 4 minuten (standaard) tot 120 minuten (2 uur) voor alle stromen.  Daarnaast kunt u de timer voor inactiviteit opnieuw instellen met verkeer op de stroom.  Een aanbevolen patroon voor het vernieuwen van langlopende inactieve verbindingen en detectie van eindpuntactiviteit zijn TCP-keepalives.  TCP-keepalives worden weergegeven als dubbele ACK's naar de eindpunten, hebben weinig overhead en zijn onzichtbaar voor de toepassingslaag.

De volgende timers worden gebruikt voor de SNAT-poortvrijgave:

| Timer | Waarde |
|---|---|
| TCP FIN | 60 seconden |
| TCP RST | 10 seconden |
| TCP half open | 30 seconden |

Een SNAT-poort kan na vijf seconden opnieuw worden gebruikt voor hetzelfde doel-IP-adres en dezelfde doelpoort.

>[!NOTE] 
>Deze timerinstellingen zijn onderhevig aan wijzigingen. De waarden worden gegeven om te helpen bij het oplossen van problemen; u moet op dit moment niet al te veel aandacht schenken aan specifieke timers.

## <a name="limitations"></a>Beperkingen

- NAT is compatibel met een standaard-SKU openbaar IP-adres, openbaar IP-voorvoegsel en load balancer-resources.   Basisresources (bijvoorbeeld load balancer van het type Basic) en alle producten die hiervan zijn afgeleid, zijn niet compatibel met NAT.  Basisresources moeten worden geplaatst op een subnet dat niet is geconfigureerd met NAT.
- De IPv4-adresfamilie wordt ondersteund.  NAT communiceert niet met een IPv6-adresfamilie.  NAT kan niet worden geïmplementeerd op een subnet met een IPv6-voorvoegsel.
- NAT kan niet meerdere virtuele netwerken omvatten.
- IP-fragmentatie wordt niet ondersteund.

## <a name="suggestions"></a>Suggesties

We willen graag weten hoe we de service kunnen verbeteren. Mist u een mogelijkheid? Houd uw pleidooi voor wat we hierna moeten bouwen op [UserVoice voor NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Virtual Network NAT](nat-overview.md).
* Meer informatie over [metrische gegevens en waarschuwingen voor NAT-gatewayresources](nat-metrics.md).
* Meer informatie over [het oplossen van problemen met NAT-gatewayresources](troubleshoot-nat.md).
