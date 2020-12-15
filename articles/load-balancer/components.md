---
title: Azure Load Balancer-componenten
description: Een overzicht van Azure Load Balancer-componenten
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2020
ms.author: allensu
ms.openlocfilehash: 6ddfe581bb3f2f584fdec0229981321297c9a77f
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97399188"
---
# <a name="azure-load-balancer-components"></a>Azure Load Balancer-componenten

Azure Load Balancer bevat enkele belangrijke componenten. Deze onderdelen kunnen worden geconfigureerd in uw abonnement via:

* Azure Portal
* Azure CLI
* Azure PowerShell
* Resource Manager-sjablonen

## <a name="frontend-ip-configuration"></a>Front-end-IP-configuratie<a name = "frontend-ip-configurations"></a>

Het IP-adres van uw Azure Load Balancer. Dit is het contactpunt voor clients. Deze IP-adressen kunnen een van de volgende zijn:

- **Openbaar IP-adres**
- **Privé IP-adres**

De aard van het IP-adres bepaalt het **type** van de gemaakte load balancer. Met de selectie privé-IP-adres wordt een interne load balancer gemaakt. Met de selectie openbaar-IP-adres wordt een openbare load balancer gemaakt.

|  | Openbare load balancer  | Interne load balancer |
| ---------- | ---------- | ---------- |
| **Front-end-IP-configuratie**| Openbaar IP-adres | Privé IP-adres|
| **Beschrijving** | Een openbare load balancer wijst het openbare IP-adres en de poort van binnenkomend verkeer toe aan het privé-IP-adres en de poort van de virtuele machine. Load balancer wijst verkeer de andere kant op in het geval van reactieverkeer vanaf de virtuele machine. Door het toepassen van regels voor taakverdeling, kunt u specifieke soorten verkeer verdelen voor meerdere virtuele machines of services. U kunt bijvoorbeeld de werkbelasting door webverkeeraanvragen over meerdere webservers spreiden.| Een interne load balancer distribueert verkeer naar resources die zich binnen een virtueel netwerk bevinden. Azure beperkt de toegang tot de front-end-IP-adressen van een virtueel netwerk met taakverdeling. Front-end-IP-adressen en virtuele netwerken communiceren nooit rechtstreeks met een interneteindpunt. Interne Line-Of-Business-toepassingen worden in Azure uitgevoerd en worden vanuit Azure of vanaf on-premises resources benaderd. |
| **Ondersteunde SKU's** | Basic, Standard | Basic, Standard |

![Voorbeeld van een gelaagde load balancer](./media/load-balancer-overview/load-balancer.png)

Load balancer kan meerdere front-end-IP's hebben. Meer informatie over [meerdere frontends](load-balancer-multivip-overview.md).

## <a name="backend-pool"></a>Back-end-pool

De groep virtuele machines of instanties in een schaalset voor virtuele machines die de inkomende aanvraag afhandelt. Kosteneffectief schalen om te voldoen aan grote volumes binnenkomend verkeer, wordt in computerrichtlijnen meestal aanbevolen om meer instanties toe te voegen aan de back-end-pool.

Load balancer herconfigureert zichzelf onmiddellijk via automatische herconfiguratie wanneer u instanties omhoog of omlaag schaalt. Als u virtuele machines toevoegt aan de back-endpool of eruit verwijdert, wordt de load balancer opnieuw geconfigureerd zonder dat er aanvullende bewerkingen worden uitgevoerd. Het bereik van de back-end-pool is een virtuele machine in het virtuele netwerk.

Wanneer u nadenkt over het ontwerp van uw back-end-pool, kunt u het beste ontwerpen voor het minst aantal afzonderlijke resources voor de back-end-pool om de duur van beheerbewerkingen te optimaliseren. Er is geen verschil in de prestaties of schaal van het gegevensvlak.

## <a name="health-probes"></a>Statuscontroles

Een statuscontrole wordt gebruikt om de status van de instanties in de back-end-pool te bepalen. Configureer tijdens het maken van uw load balancer een statuscontrole voor de load balancer die u wilt gebruiken.  Deze status test bepaalt of een exemplaar in orde is en verkeer kan ontvangen.

U kunt de drempelwaarde onjuiste status voor de statuscontroles definiëren. Wanneer een test niet reageert, stopt de load balancer met het verzenden van nieuwe verbindingen naar de slechte instanties. Een controlefout heeft geen invloed op bestaande verbindingen. De verbinding wordt vervolgd totdat de toepassing:

- De stroom beëindigt
- Time-out voor inactiviteit optreedt
- De VM wordt afgesloten

Load balancer biedt verschillende typen statustests voor eindpunten: TCP, HTTP en HTTPS. [Meer informatie over statustests van Load Balancer](load-balancer-custom-probe-overview.md).

Basic-load balancer biedt geen ondersteuning voor HTTPS-tests. Basic-load balancer sluit alle TCP-verbindingen (inclusief tot stand gebrachte verbindingen).

## <a name="load-balancing-rules"></a>Taakverdelingsregels

Een taakverdelingsregel wordt gebruikt om te bepalen hoe inkomend verkeer wordt gedistribueerd naar **alle** instanties binnen de back-endpool. Een taakverdelingsregel wijst een gegeven front-end-IP-configuratie en -poort toe aan meerdere back-end-IP-adressen en -poorten.

Gebruik bijvoorbeeld een taakverdelingsregel voor poort 80 om verkeer door te sturen van uw front-end-IP-adres naar poort 80 van uw back-endexemplaren.

:::image type="content" source="./media/load-balancer-components/lbrules.png" alt-text="Referentiediagram voor taakverdelingsregel" border="false":::

*Afbeelding: Taakverdelingsregels*

## <a name="high-availability-ports"></a>Poorten met hoge beschikbaarheid

Een Load Balancer-regel die is geconfigureerd met **'protocol - all en poort - 0'** . 

Met deze regel kunt u één regel gebruiken voor het laden van alle TCP-en UDP-stromen die binnenkomen op alle poorten van een interne Standard Load Balancer. 

Er wordt per stroom een beslissing voor taakverdeling gemaakt. Deze actie is gebaseerd op de volgende vijf-tupleverbinding: 

1. IP-adres van bron
2. bronpoort
3. IP-adres van doel
4. doelpoort
5. protocol

De taakverdelingsregels voor de HA-poorten helpen u bij kritieke scenario's, zoals hoge beschikbaarheid en schaal aanpassen voor NVA's (Network Virtual Appliance) in virtuele netwerken. De functie kan helpen wanneer over een groot aantal poorten taakverdeling moet worden uitgevoerd.

<p align="center">
  <img src="./media/load-balancer-components/harules.svg" alt="Figure depicts how Azure Load Balancer directs all frontend ports to three instances of all backend ports" width="512" title="HA-poortregels">
</p>

*Afbeelding: HA-poortregels*

Meer informatie over [HA-poorten](load-balancer-ha-ports-overview.md).

## <a name="inbound-nat-rules"></a>Inkomende NAT-regels

Via een inkomende NAT-regel wordt binnenkomend verkeer doorgestuurd naar een combinatie van een front-end-IP-adres en een geselecteerde poort. Het verkeer wordt verzonden naar een **specifieke** virtuele machine of exemplaar in de back-end-pool. Port forwarding wordt gedaan door de dezelfde hash-distributie als taakverdeling.

:::image type="content" source="./media/load-balancer-components/inboundnatrules.png" alt-text="Referentiediagram voor inkomende NAT-regel" border="false":::

*Afbeelding: Inkomende NAT-regels*

Inkomende NAT-regels in de context van Virtual Machine Scale Sets zijn binnenkomende NAT-groepen. Meer informatie over [Load Balancer-onderdelen en virtuele-machineschaalset](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#azure-virtual-machine-scale-sets-with-azure-load-balancer).

## <a name="outbound-rules"></a>Regels voor uitgaand verkeer

Met een regel voor uitgaand verkeer wordt de uitgaande netwerkadresomzetting (NAT) geconfigureerd voor alle virtuele machines of instanties die worden geïdentificeerd door de back-end-pool. Met deze regel kunnen instanties in de back-end communiceren (uitgaand) met internet of andere eindpunten.

Meer informatie over [uitgaande verbindingen en regels](load-balancer-outbound-connections.md).

Basic Load Balancer biedt geen ondersteuning voor uitgaande regels.

:::image type="content" source="./media/load-balancer-components/outbound-rules.png" alt-text="Referentiediagram voor uitgaande regel" border="false":::

*Afbeelding: Uitgaande regels*

## <a name="limitations"></a>Beperkingen

- Meer informatie over load balancer-[limieten](../azure-resource-manager/management/azure-subscription-service-limits.md) 
- Voor deze specifieke TCP- of UDP-protocollen biedt Load Balancer taakverdeling en port forwarding. Taakverdelingsregels en inkomende NAT-regels bieden ondersteuning voor TCP en UDP, maar niet voor andere IP-protocollen, waaronder ICMP.
- Uitgaande stroom van een back-end-VM naar een front-end van een interne Load Balancer mislukt.
- Een regel voor een load balancer kan niet twee virtuele netwerken omvatten.  Front-ends en de bijbehorende back-endinstanties moeten zich in hetzelfde virtuele netwerk bevinden.  
- Het doorsturen van IP-fragmenten wordt niet ondersteund voor taakverdelingsregels. IP-fragmentatie van UDP- en TCP-pakketten wordt niet ondersteund in taakverdelingsregels. De taakverdelingsregels voor HA-poorten kunnen worden gebruikt voor het doorsturen van bestaande IP-fragmenten. Zie [Overzicht van poorten met hoge beschikbaarheid](load-balancer-ha-ports-overview.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een openbare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met een load balancer.
- Meer informatie over [Azure Load Balancer](load-balancer-overview.md).
- Meer informatie over [Openbaar IP-adres](../virtual-network/virtual-network-public-ip-address.md)
- Meer informatie over [Privé-IP-adres](../virtual-network/private-ip-addresses.md)
- Meer informatie over [Standard Load Balancer en beschikbaarheidszones](load-balancer-standard-availability-zones.md).
- Meer informatie over [Diagnostische tests van Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Meer informatie over [TCP opnieuw instellen bij inactiviteit](load-balancer-tcp-reset.md).
- Meer informatie over [Standard Load Balancer met taakverdelingsregels voor poorten met hoge beschikbaarheid](load-balancer-ha-ports-overview.md).
- Meer informatie over [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md).
- Meer informatie over [Beperkingen van Load Balancer](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer).
- Meer informatie over het gebruik van [Port forwarding](./tutorial-load-balancer-port-forwarding-portal.md).