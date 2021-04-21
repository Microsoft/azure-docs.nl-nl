---
title: Door de gebruiker gedefinieerde routes (UDR) aanpassen in Azure Kubernetes Service (AKS)
description: Meer informatie over het definiëren van een aangepaste route voor Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: e9433978c8ee855ec66901c7692e4d2b59261fd3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773041"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Cluster egress aanpassen met een User-Defined Route

Egress van een AKS-cluster kan worden aangepast aan specifieke scenario's. AKS zal standaard een Standard-SKU inrichten Load Balancer moet worden ingesteld en gebruikt voorgressie. De standaardinstelling voldoet echter mogelijk niet aan de vereisten van alle scenario's als openbare IP's niet zijn toegestaan of als er extra hops vereist zijn voor degress.

In dit artikel wordt beschreven hoe u de route voor het uit te gaan van een cluster kunt aanpassen ter ondersteuning van aangepaste netwerkscenario's, zoals scenario's waarin openbare IP's niet zijn toe te staan en waarvoor het cluster achter een virtueel netwerkapparaat (NVA) moet zitten.

## <a name="prerequisites"></a>Vereisten
* Azure CLI versie 2.0.81 of hoger
* API-versie van of `2020-01-01` hoger


## <a name="limitations"></a>Beperkingen
* OutboundType kan alleen worden gedefinieerd tijdens het maken van het cluster en kan later niet worden bijgewerkt.
* Instelling `outboundType` vereist AKS-clusters met een `vm-set-type` van en van `VirtualMachineScaleSets` `load-balancer-sku` `Standard` .
* Voor `outboundType` het instellen op een waarde van is een door de gebruiker `UDR` gedefinieerde route met geldige uitgaande connectiviteit voor het cluster vereist.
* Als u in op een waarde van instelt, betekent dit dat het bron-IP-adres voor binnenkomende gegevens dat naar de load balancer wordt gerouteerd, mogelijk niet overeen komt met het uitgaande doeladres van `outboundType` `UDR` het uitgaande uitgaand cluster. 

## <a name="overview-of-outbound-types-in-aks"></a>Overzicht van uitgaande typen in AKS

Een AKS-cluster kan worden aangepast met een uniek `outboundType` type of `loadBalancer` `userDefinedRouting` .

> [!IMPORTANT]
> Uitgaand type heeft alleen gevolgen voor het uitgaande verkeer van uw cluster. Zie voor meer informatie [het instellen van ingress-controllers.](ingress-basic.md)

> [!NOTE]
> U kunt uw eigen [routetabel gebruiken met][byo-route-table] UDR- en kubenet-netwerken. Zorg ervoor dat uw clusteridentiteit (service-principal of beheerde identiteit) inzendermachtigingen heeft voor de aangepaste routetabel.

### <a name="outbound-type-of-loadbalancer"></a>Uitgaand type loadBalancer

Als `loadBalancer` is ingesteld, voltooit AKS automatisch de volgende configuratie. De load balancer wordt gebruikt voor een egress via een openbaar IP-adres dat is toegewezen via AKS. Een uitgaand type van ondersteunt Kubernetes-services van het type , die uitgaand verkeer verwachten van de load balancer door de `loadBalancer` `loadBalancer` AKS-resourceprovider is gemaakt.

De volgende configuratie wordt uitgevoerd door AKS.
   * Er wordt een openbaar IP-adres ingericht voor het uit te gaan van het cluster.
   * Het openbare IP-adres wordt toegewezen aan de load balancer resource.
   * Back-uppools voor de load balancer zijn ingesteld voor agentknooppunten in het cluster.

Hieronder vindt u een netwerktopologie die standaard is geïmplementeerd in AKS-clusters, die gebruikmaken van `outboundType` een van `loadBalancer` .

![Diagram met ingress I P en Egress I P, waarbij de I P van het toegangsbeheerpunt verkeer om leidt naar een load balancer, die verkeer door stuurt naar en van een intern cluster en ander verkeer naar de I P van het verkeer dat verkeer omleiding naar internet, M C R, azure-vereiste services en het A K S-besturingsvlak.](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Uitgaand type gebruikerDefinedRouting

> [!NOTE]
> Het gebruik van uitgaand type is een geavanceerd netwerkscenario en vereist de juiste netwerkconfiguratie.

Als `userDefinedRouting` is ingesteld, configureert AKS niet automatisch de paden voor uit te gaan. De instelling van het egress moet door u worden uitgevoerd.

Het AKS-cluster moet worden geïmplementeerd in een bestaand virtueel netwerk met een subnet dat eerder is geconfigureerd, omdat wanneer u geen standaard SLB-architectuur (load balancer) gebruikt, u expliciete uitding moet instellen. Daarom moet voor deze architectuur expliciet verkeer worden verzonden naar een apparaat zoals een firewall, gateway of proxy, of moet de NAT (Network Address Translation) kunnen worden uitgevoerd door een openbaar IP-adres dat is toegewezen aan de standaard-load balancer of het standaardapparaat.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>Load balancer maken met userDefinedRouting

AKS-clusters met een uitgaand type UDR ontvangen alleen een standaard load balancer (SLB) wanneer de eerste Kubernetes-service van het type 'loadBalancer' wordt geïmplementeerd. De load balancer is geconfigureerd met een openbaar IP-adres voor *binnenkomende* aanvragen en een back-en-pool voor *binnenkomende* aanvragen. Binnenkomende regels worden geconfigureerd door de Azure-cloudprovider, maar er worden geen uitgaande openbare **IP-adressen** of uitgaande regels geconfigureerd als gevolg van een uitgaand type UDR. Uw UDR is nog steeds de enige bron voor het wegverkeer.

Voor Azure-load balancers [worden geen kosten in rekening brengen totdat er een regel wordt geplaatst.](https://azure.microsoft.com/pricing/details/load-balancer/)

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Een cluster implementeren met uitgaand type UDR en Azure Firewall

Ter illustratie van de toepassing van een cluster met uitgaand type met behulp van een door de gebruiker gedefinieerde route, kan een cluster worden geconfigureerd in een virtueel netwerk met een Azure Firewall op een eigen subnet. Zie dit voorbeeld in het [voorbeeld van het beperken van het toegangsverkeer met Azure Firewall.](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)

> [!IMPORTANT]
> Voor het uitgaande type UDR is een route vereist voor 0.0.0.0/0 en de volgende hopbestemming van NVA (virtueel netwerkapparaat) in de routetabel.
> De routetabel heeft al een standaardwaarde van 0.0.0.0/0 naar internet, zonder een openbaar IP-adres aan SNAT dat alleen deze route toevoegt, geeft u geen toegang tot degress. AKS valideert dat u geen 0.0.0.0/0-route maakt die verwijst naar internet, maar naar NVA of gateway, enzovoort. Wanneer u een uitgaand type UDR gebruikt, wordt load balancer openbaar IP-adres voor **binnenkomende** aanvragen niet gemaakt, tenzij een service van het type *loadbalancer* is geconfigureerd. Een openbaar IP-adres **voor uitgaande aanvragen** wordt nooit door AKS gemaakt als een uitgaand type UDR is ingesteld.

## <a name="next-steps"></a>Volgende stappen

Zie [Overzicht van Azure Networking UDR](../virtual-network/virtual-networks-udr-overview.md).

Bekijk [hoe u een routetabel maakt, wijzigt of verwijdert.](../virtual-network/manage-route-table.md)

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
