---
title: Concepten - Netwerkdiagram voor Azure Red Hat op OpenShift 4
description: Netwerkdiagram en overzicht voor netwerken van Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 11/23/2020
ms.openlocfilehash: 5d69aacb6e3f25e3414aa446c4c5ae7852cabdfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720899"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Netwerkconcepten voor Azure Red Hat OpenShift (ARO)

Deze handleiding bevat een overzicht van netwerken in Azure Red Hat OpenShift op OpenShift 4-clusters, en tevens een diagram en een lijst met belangrijke eindpunten. Zie de [Azure Red Hat OpenShift 4-netwerkdocumentatie](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html) (Engelstalig) voor meer informatie over de basisconcepten van OpenShift-netwerken.

![Azure Red Hat OpenShift 4-netwerkdiagram](./media/concepts-networking/aro4-networking-diagram.png)

Wanneer u Azure Red Hat implementeert op OpenShift 4, bevindt het hele cluster zich in een virtueel netwerk. Binnen dit virtuele netwerk bevinden uw hoofdknooppunten en werkknooppunten zich in een eigen subnet. Elk subnet maakt gebruik van een interne load balancer en een openbare load balancer.

## <a name="networking-components"></a>Netwerkonderdelen

De volgende lijst bevat belangrijke netwerkonderdelen in een Azure Red Hat OpenShift-cluster.

* **aro-pls**
    * Dit is een Azure Private Link-eindpunt dat door technici van Microsoft en Red Hat voor de betrouwbaarheid van de site wordt gebruikt om het cluster te beheren.
* **aro-internal-lb**
    * Met dit eindpunt wordt het verkeer naar de API-server gebalanceerd. Voor deze load balancer bevinden de hoofdknooppunten zich in de back-endpool.
* **aro-public-lb**
    * Als de API openbaar is, wordt het verkeer door dit eindpunt naar de API-server gerouteerd en gebalanceerd. Dit eindpunt wijst een openbaar, uitgaand IP-adres toe, zodat masters toegang hebben tot Azure Resource Manager en de status van het cluster terug kunnen rapporteren.
* **aro-internal**
    * Met dit eindpunt wordt het interne serviceverkeer gebalanceerd. Voor deze load balancer bevinden de werkknooppunten zich in de back-endpool.
    * Deze load balancer wordt standaard niet gemaakt. Deze load balancer wordt gemaakt nadat u een service van het type LoadBalancer met de juiste aantekeningen hebt gemaakt. Bijvoorbeeld: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **aro-internal-lb**
    * Dit eindpunt wordt gebruikt voor openbaar verkeer. Als u een toepassing en een route maakt, is dit het pad voor inkomend verkeer.
    * Deze load balancer is ook van toepassing op uitgaand internetconnectiviteit vanuit elke pod die via uitgaande Azure Load Balancer-regels op de werkknooppunten wordt uitgevoerd.
        * Momenteel kunnen uitgaande regels niet worden geconfigureerd. Ze wijzen aan elk knooppunt 1024 TCP-poorten toe.
        * DisableOutboundSnat wordt niet geconfigureerd in de LB-regels, zodat pods als uitgaand IP-adres een openbaar IP-adres kunnen krijgen dat in deze ALB is geconfigureerd.
        * Het gevolg van de twee vorige punten is dat de enige manier om tijdelijke SNAT-poorten toe te kunnen voegen, bestaat uit het toevoegen van openbare services van het type LoadBalancer aan ARO.
* **aro-outbound-pip**
    * Dit eindpunt fungeert als een openbaar IP-adres (PIP) voor de werkknooppunten.
    * Met dit eindpunt kunnen services een specifiek IP-adres dat afkomstig is van een Azure Red Hat OpenShift-cluster aan een acceptatielijst toevoegen.
* **aro-nsg**
    * Wanneer u een service beschikbaar stelt, maakt de API een regel in deze netwerkbeveiligingsgroep, zodat het verkeer kan doorstromen en de knooppunten en het besturingsvlak bereiken.
    * Deze netwerkbeveiligingsgroep staat standaard al het uitgaande verkeer toe. Op dit moment kan uitgaand verkeer alleen worden beperkt tot het besturingsvlak van Azure Red Hat OpenShift.
* **aro-controlplane-nsg**
  * Dit eindpunt staat alleen verkeer toe via poort 6443 voor de hoofdknooppunten.
* **Azure Container Registry**
    * Dit is een containerregister dat intern door Microsoft wordt verstrekt en gebruikt. Het register is alleen-lezen en niet bedoeld voor gebruikers van Azure Red Hat OpenShift.
        * Dit register bevat platforminstallatiekopieën van de host en clusteronderdelen. Bijvoorbeeld containers voor bewaking of logboekregistratie.
        * Verbindingen met dit register vinden plaats via het service-eindpunt (interne connectiviteit tussen Azure-services).
        * Dit interne register is standaard niet beschikbaar buiten het cluster.
* **Private Link**
    * Hiermee is verbinding met het netwerk mogelijk vanaf het beheervlak in een cluster voor technici van Microsoft en Red Hat voor de betrouwbaarheid van de site om het cluster te beheren.

## <a name="networking-policies"></a>Netwerkbeleid

* **Inkomend verkeer**: Het beleid voor inkomend verkeer wordt ondersteund als onderdeel van [OpenShift SDN](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html). Dit netwerkbeleid is standaard ingeschakeld en het afdwingen ervan wordt door gebruikers uitgevoerd. Hoewel het beleid voor inkomend verkeer compatibel is met V1 NetworkPolicy, worden de typen Egress (uitgaand verkeer) en IPBlock niet ondersteund.

* **Uitgaand verkeer**: Beleidsregels voor uitgaand verkeer worden ondersteund door gebruik te maken van de [firewallfunctie voor uitgaand verkeer](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html) in OpenShift. Er is slechts één beleidsregel voor uitgaand verkeer per naamruimte/project. Uitgaand beleid wordt niet ondersteund voor de ' standaard ' naam ruimte en worden geëvalueerd in volg orde (voor het eerst op de laatste).

## <a name="networking-basics-in-openshift"></a>Basisprincipes van netwerken in OpenShift

OpenShift Software Defined Networking [(SDN)](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/about-openshift-sdn.html) wordt gebruikt voor het configureren van een overlay-netwerk met behulp van Open vSwitch [(OVS)](https://www.openvswitch.org/), een OpenFlow-implementatie op basis van de Container Network Interface-specificatie (CNI). De SDN ondersteunt verschillende invoegtoepassingen; Network Policy is de invoegtoepassing die in Azure Red Hat in OpenShift 4 wordt gebruikt. Alle netwerkcommunicatie wordt beheerd door de SDN, dus er zijn geen extra routes nodig in uw virtuele netwerken om communicatie tussen pods te bewerkstelligen.

## <a name="networking--for-azure-red-hat-openshift"></a>Netwerken voor Azure Red Hat OpenShift

De volgende netwerkfuncties zijn specifiek voor Azure Red Hat OpenShift:  
* Gebruikers kunnen hun ARO-cluster maken in een bestaand virtueel netwerk of een virtueel netwerk maken tijdens het maken van hun ARO-cluster.
* CIDR's voor pods en servicenetwerken kunnen worden geconfigureerd.
* Knooppunten en masters bevinden zich in verschillende subnetten.
* Virtuele netwerksubnetten van knooppunten en masters moeten minimaal /27 zijn.
* Standaard pod CIDR is 10.128.0.0/14.
* Standaard service-CIDR is 172.30.0.0/16.
* Pod en service netwerk-CIDR mogen niet overlappen met andere adresbereiken die worden gebruikt in uw netwerk en mogen zich niet binnen het IP-adres bereik van het virtuele netwerk van uw cluster bevallen.
* Pod CIDR moet mini maal/18 groot zijn. (Het Pod-netwerk is niet-routeerbaar bare Ip's en wordt alleen gebruikt in de open Shift SDN.)
* Aan elk knooppunt wordt een /23-subnet (512 IP's) voor de pods toegewezen. Deze waarde kan niet worden gewijzigd.
* U kunt geen pod aan meer dan één netwerk koppelen.
* U kunt geen uitgaand statisch IP-adres configureren. (Dit is een OpenShift-functie. Zie [configuring egress IPs](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/assigning-egress-ips.html)) (Uitgaande IP-adressen configureren) voor meer informatie).

## <a name="network-settings"></a>Netwerkinstellingen

De volgende netwerkinstellingen zijn beschikbaar in Azure Red Hat OpenShift 4-clusters:

* **API-zichtbaarheid**: stel de API-zichtbaarheid in wanneer u de [opdracht az aro create](tutorial-create-cluster.md#create-the-cluster) uitvoert.
    * 'Openbaar': API-server is toegankelijk voor externe netwerken.
    * 'Privé': aan API-server is een persoonlijk IP-adres toegewezen vanuit het masterssubnet, alleen toegankelijk via verbonden netwerken (gekoppelde virtuele netwerken, overige subnetten in het cluster). Namens de klant wordt een persoonlijke DNS-zone gemaakt.
* **Zichtbaarheid van inkomend verkeer**: stel de API-zichtbaarheid in wanneer u de [opdracht az aro create](tutorial-create-cluster.md#create-the-cluster) uitvoert.
    * 'Openbare' routes worden standaard ingesteld op een openbare Standard Load Balancer (dit kan worden gewijzigd).
    * 'Privé' routes worden standaard ingesteld op de interne load balancer (dit kan worden gewijzigd).

## <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
Netwerkbeveiligingsgroepen worden gemaakt in de resourcegroep van het knooppunt; deze is vergrendeld voor gebruikers. De netwerkbeveiligingsgroepen worden rechtstreeks toegewezen aan de subnetten, niet aan de NIC's van het knooppunt. De netwerkbeveiligingsgroepen zijn onveranderbaar. Gebruikers beschikken niet over de juiste machtigingen om ze te wijzigen.

Met een openlijk zichtbare API-server kunt u geen netwerkbeveiligingsgroepen maken en deze toewijzen aan de NIC's.

## <a name="domain-forwarding"></a>Doorsturen van domeinen
Azure Red Hat OpenShift gebruikt CoreDNS. Doorsturen van domeinen kan worden geconfigureerd. U kunt uw eigen DNS niet naar uw virtuele netwerken meenemen. Zie de documentatie over [het gebruik van DNS doorsturen](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html#nw-dns-forward_dns-operator) (Engelstalig) voor meer informatie.

## <a name="whats-new-in-openshift-45"></a>Nieuw in OpenShift 4.5

Met de ondersteuning van OpenShift 4.5 heeft Azure Red Hat OpenShift enkele belangrijke wijzigingen in de architectuur geïntroduceerd. Deze wijzigingen zijn alleen van toepassing op nieuw gemaakte clusters waarop OpenShift 4.5 wordt uitgevoerd. Voor bestaande clusters die zijn geupgraded naar OpenShift 4.5, is de netwerkarchitectuur niet gewijzigd. Gebruikers moeten hun clusters opnieuw maken om deze nieuwe architectuur te gebruiken.

![Azure Red Hat OpenShift 4.5-netwerkdiagram](./media/concepts-networking/aro-4-5-networking-diagram.png)

Zoals u in het bovenstaande diagram kunt zien, zijn er enkele wijzigingen aangebracht:
* Voorheen maakte ARO gebruik van twee openbare load balancers: een voor de API-server en een voor de werkknooppuntpool. Nu de architectuur is bijgewerkt, is dit geconsolideerd onder één load balancer. 
* Teneinde de complexiteit te reduceren, zijn de toegewezen uitgaande IP-adresresources verwijderd.
* Het ARO-besturingsvlak deelt nu dezelfde netwerkbeveiligingsgroep als de ARO-werkknooppunten.

Zie de [releaseopmerkingen voor OpenShift 4.5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html) (Engelstalig) voor meer informatie over OpenShift 4.5.

## <a name="next-steps"></a>Volgende stappen
Zie de documentatie over [ondersteuningsbeleid](support-policies-v4.md) voor meer informatie over uitgaand verkeer en wat Azure Red Hat OpenShift ondersteunt voor uitgaand verkeer.
