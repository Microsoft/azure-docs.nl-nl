---
title: Veelgestelde vragen-Azure Load Balancer
description: Antwoorden op veelgestelde vragen over de Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: e9f46b11d9c0b5251ee4d52f64d657926f6f9c5e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222986"
---
# <a name="load-balancer-frequently-asked-questions"></a>Veelgestelde vragen over Load Balancer

## <a name="what-types-of-load-balancer-exist"></a>Welke soorten Load Balancer bestaan?
Interne load balancers die verkeer verdelen binnen een VNET en externe load balancers die verkeer van en naar een Internet verbonden eind punt balanceren. Zie [Load Balancer typen](components.md#frontend-ip-configurations)voor meer informatie. 

Voor beide typen biedt Azure een basis-SKU en een standaard-SKU met verschillende functies voor functionaliteit, prestaties, beveiliging en status bijhouden. Deze verschillen worden beschreven in het artikel over het vergelijken van de [SKU](skus.md) .

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Hoe kan ik een upgrade uitvoeren van een basis versie naar een Standard Load Balancer?
Zie de [upgrade van Basic naar Standard](upgrade-basic-standard.md) -artikel voor een geautomatiseerd script en richt lijnen voor het bijwerken van een load BALANCER-SKU.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Wat zijn de verschillende opties voor taak verdeling in azure?
Zie de [Load Balancer technologie handleiding](/azure/architecture/guide/technology-choices/load-balancing-overview)  voor de beschik bare taakverdelings Services en aanbevolen gebruik voor elk.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Waar vind ik Load Balancer ARM-sjablonen?
Bekijk de [lijst met Azure Load Balancer Quick](/azure/templates/microsoft.network/loadbalancers#quickstart-templates) start-sjablonen voor arm-sjablonen van algemene implementaties.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Wat is het verschil tussen binnenkomende NAT-regels en de regels voor taak verdeling?
NAT-regels worden gebruikt om een back-end-bron op te geven voor het routeren van verkeer naar. U kunt bijvoorbeeld een specifieke load balancer poort configureren voor het verzenden van RDP-verkeer naar een specifieke virtuele machine. Taakverdelings regels worden gebruikt om een pool van back-endservers op te geven voor het routeren van verkeer naar, waarbij de belasting voor elk exemplaar wordt verdeeld. Een load balancer regel kan bijvoorbeeld TCP-pakketten routeren op poort 80 van de load balancer in een groep webservers.

## <a name="what-is-ip-1686312916"></a>Wat is IP-168.63.129.16?
Het virtuele IP-adres voor de host die als Azure-infra structuur is gelabeld Load Balancer waar de Azure Health-tests afkomstig zijn. Bij het configureren van back-end-exemplaren, moeten ze verkeer van dit IP-adres toestaan om te reageren op status controles. Deze regel communiceert niet met toegang tot uw Load Balancer frontend. Als u geen gebruik maakt van de Azure Load Balancer, kunt u deze regel onderdrukken. Meer informatie over service Tags vindt u [hier](../virtual-network/service-tags-overview.md#available-service-tags).

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>Kan ik globale VNet-peering gebruiken met basis Load Balancer?
Nee. Basis Load Balancer biedt geen ondersteuning voor globale VNET-peering. U kunt in plaats daarvan een Standard Load Balancer gebruiken. Zie de [upgrade van Basic naar Standard](upgrade-basic-standard.md) -artikel voor naadloze upgrade.

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>Hoe kan ik het open bare IP-adres ontdekken dat een Azure-VM gebruikt?

Er zijn veel manieren om het IP-adres van de open bare bron van een uitgaande verbinding te bepalen. OpenDNS biedt een service waarmee u het open bare IP-adres van uw virtuele machine kunt weer geven.
Met de opdracht nslookup kunt u een DNS-query voor de naam myip.opendns.com verzenden naar de OpenDNS-resolver. De service retourneert het bron-IP-adres dat is gebruikt om de query te verzenden. Wanneer u de volgende query uitvoert vanaf uw virtuele machine, is het antwoord het open bare IP-adres dat voor die virtuele machine wordt gebruikt:

 ```nslookup myip.opendns.com resolver1.opendns.com```
 
## <a name="can-i-add-a-vm-from-the-same-availability-set-to-different-backend-pools-of-a-load-balancer"></a>Kan ik een virtuele machine van dezelfde beschikbaarheidsset toevoegen aan verschillende back-endservers van een Load Balancer?
Nee, dit is niet mogelijk.

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>Hoe werken de verbindingen met Azure Storage in dezelfde regio?
Het is niet nodig om verbinding te maken met opslag in dezelfde regio als de virtuele machine als u uitgaande verbindingen hebt via de bovenstaande scenario's. Als u dit niet wilt, gebruikt u netwerk beveiligings groepen (Nsg's) zoals hierboven is uitgelegd. Voor connectiviteit met opslag in andere regio's is uitgaande connectiviteit vereist. Houd er rekening mee dat wanneer u verbinding maakt met de opslag van een virtuele machine in dezelfde regio, het bron-IP-adres in de logboeken voor diagnostische gegevens van de opslag locatie van de interne provider is en niet het open bare IP-adres van uw virtuele machine. Als u de toegang tot uw opslag account wilt beperken tot Vm's in een of meer Virtual Network subnetten in dezelfde regio, gebruikt u [Virtual Network Service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) en niet uw open bare IP-adres bij het configureren van de firewall van uw opslag account. Zodra service-eind punten zijn geconfigureerd, ziet u uw Virtual Network privé-IP-adres in uw logboeken voor opslag diagnose en niet het adres van de interne provider.

## <a name="what-are-best-practises-with-respect-to-outbound-connectivity"></a>Wat zijn de aanbevolen procedures voor uitgaande verbindingen?
Standard Load Balancer en standaard open bare IP introduceert mogelijkheden en verschillende gedragingen voor uitgaande connectiviteit. Ze zijn niet hetzelfde als de basis-Sku's. Als u een uitgaande verbinding wilt gebruiken bij het werken met standaard-Sku's, moet u deze expliciet definiëren met een openbaar IP-adres of standaard open bare Load Balancer. Dit omvat het maken van uitgaande connectiviteit bij het gebruik van een interne Standard Load Balancer. U wordt aangeraden om altijd uitgaande regels te gebruiken op een standaard open bare Load Balancer. Dit betekent dat als er een interne Standard Load Balancer wordt gebruikt, u stappen moet nemen om uitgaande connectiviteit te maken voor de virtuele machines in de back-endadresgroep als uitgaande connectiviteit gewenst is. In de context van uitgaande connectiviteit, één zelfstandige virtuele machine, alle virtuele machines in een Beschikbaarheidsset, werken alle exemplaren in een VMSS als groep. Dit betekent dat als er één virtuele machine in een Beschikbaarheidsset is gekoppeld aan een standaard-SKU, alle VM-exemplaren binnen deze Beschikbaarheidsset nu dezelfde regels gedragen als voor de standaard-SKU, zelfs als een afzonderlijke instantie niet rechtstreeks is gekoppeld. Dit gedrag wordt ook waargenomen in het geval van een zelfstandige virtuele machine met meerdere netwerk interface kaarten die aan een load balancer zijn gekoppeld. Als één NIC als zelfstandige wordt toegevoegd, heeft deze hetzelfde gedrag. Lees dit volledige document aandachtig door om inzicht te krijgen in de algemene concepten, Lees [Standard Load Balancer](./load-balancer-overview.md) voor verschillen tussen sku's en controleer de [regels voor uitgaande verbindingen](load-balancer-outbound-connections.md#outboundrules).
Met uitgaande regels kunt u de controle over alle aspecten van de uitgaande connectiviteit nauw keuriger maken.
 
## <a name="next-steps"></a>Volgende stappen
Als uw vraag hier niet wordt vermeld, kunt u met uw vraag feedback over deze pagina sturen. Hiermee maakt u een GitHub-probleem voor het product team om ervoor te zorgen dat al onze vragen van de gewaardeerde klant worden beantwoord.
