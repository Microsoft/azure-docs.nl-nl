---
title: Concepten - Netwerkdiagram voor Azure Red Hat op OpenShift 4
description: Netwerkdiagram en overzicht voor netwerken van Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 06/22/2020
ms.openlocfilehash: 3417b59d0be9e285f8793ef598abb7f98bda7549
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95527986"
---
# <a name="networking-in-azure-red-hat-on-openshift-4"></a>Netwerken in Azure Red Hat op OpenShift 4

Deze handleiding bevat een overzicht van netwerken in Azure Red Hat op OpenShift 4-clusters, en tevens een diagram en een lijst met belangrijke eindpunten.

Zie de [Azure Red Hat OpenShift 4-netwerkdocumentatie](https://docs.openshift.com/aro/4/networking/understanding-networking.html) voor meer informatie over de basisconcepten van OpenShift-netwerken.

## <a name="networking-concepts-in-azure-red-hat-openshift"></a>Netwerkconcepten in Azure Red Hat OpenShift

![Azure Red Hat OpenShift 4-netwerkdiagram](./media/concepts-networking/aro4-networking-diagram.png)

Wanneer u Azure Red Hat implementeert op OpenShift 4, bevindt het hele cluster zich in een virtueel netwerk. Binnen dit virtuele netwerk bevinden uw hoofdknooppunten en werkknooppunten zich in een eigen subnet. Elk subnet maakt gebruik van een openbare en interne load balancer.

## <a name="explanation-of-endpoints"></a>Eindpunten verklaard

De volgende lijst bevat belangrijke eindpunten in een Azure Red Hat OpenShift-cluster.

* **aro-pls**
    * Dit is een Azure Private Link-eindpunt dat door technici van Microsoft en Red Hat voor de betrouwbaarheid van de site wordt gebruikt om het cluster te beheren.
* **aro-internal-lb**
    * Met dit eindpunt wordt het verkeer naar de API-server gebalanceerd. Voor deze load balancer bevinden de hoofdknooppunten zich in de back-endpool.
* **aro-public-lb**
    * Als de API openbaar is, wordt het verkeer door dit eindpunt naar de API-server gerouteerd en gebalanceerd. Dit eindpunt wijst een openbaar, uitgaand IP-adres toe, zodat masters toegang hebben tot Azure Resource Manager en de status van het cluster terug kunnen rapporteren.
* **aro-internal**
    * Met dit eindpunt wordt het interne serviceverkeer gebalanceerd. Voor deze load balancer bevinden de werkknooppunten zich in de back-endpool.
    * Deze load balancer wordt standaard niet gemaakt. Deze load balancer wordt gemaakt nadat u een service van het type LoadBalancer met de juiste aantekeningen hebt gemaakt. Bijvoorbeeld: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **Netwerkbeleid (inkomend)**
    * Ondersteund als onderdeel van de OpenShift SDN
    * Standaard ingeschakeld, afgedwongen door klanten
    * V1 NetworkPolicy-conform, maar de typen Egress en IPBlock worden nog niet ondersteund
    * **aro**
    * Met dit eindpunt wordt het verkeer naar de API-server gebalanceerd. Voor deze load balancer bevinden de hoofdknooppunten zich in de back-endpool.
  * **aro-internal-lb**
    * Dit eindpunt wordt gebruikt voor openbaar verkeer. Als u een toepassing en een route maakt, is dit het pad voor inkomend verkeer.
    * Deze load balancer is ook van toepassing op uitgaand internetconnectiviteit vanuit elke pod die via uitgaande Azure Load Balancer-regels op de werkknooppunten wordt uitgevoerd.
        * Momenteel kunnen uitgaande regels niet worden geconfigureerd. Ze wijzen aan elk knooppunt 1024 TCP-poorten toe.
        * DisableOutboundSnat wordt niet geconfigureerd in de LB-regels, zodat pods als uitgaand IP-adres een openbaar IP-adres kunnen krijgen dat in deze ALB is geconfigureerd.
        * Het gevolg van de twee vorige punten is dat de enige manier om tijdelijke SNAT-poorten toe te kunnen voegen, bestaat uit het toevoegen van openbare services van het type LoadBalancer aan ARO.
* **Netwerkbeleid (uitgaand)**
    * Beleid voor uitgaand verkeer wordt ondersteund door gebruik te maken van de functie Egress Firewall in OpenShift.
    * Slechts één per naamruimte/project.
    * Beleid voor uitgaand verkeer wordt ondersteund voor een 'standaard' naamruimte.
    * Beleidsregels voor uitgaand verkeer worden op volgorde geëvalueerd (van de eerste naar de laatste).
    * **aro-outbound-pip**
        * Dit eindpunt fungeert als een openbaar IP-adres (PIP) voor de werkknooppunten.
        * Met dit eindpunt kunnen services een specifiek IP-adres dat afkomstig is van een Azure Red Hat OpenShift-cluster aan een acceptatielijst toevoegen.
* **aro-node-nsg**
    * Wanneer u een service beschikbaar stelt, maakt de API een regel in deze netwerkbeveiligingsgroep, zodat het verkeer kan doorstromen en de knooppunten bereiken.
    * Deze netwerkbeveiligingsgroep staat standaard al het uitgaande verkeer toe. Op dit moment kan uitgaand verkeer alleen worden beperkt tot het besturingsvlak van Azure Red Hat OpenShift.
* **aro-controlplane-nsg**
    * Dit eindpunt staat alleen verkeer toe via poort 6443 voor de hoofdknooppunten.
* **Azure Container Registry**
    * Dit is een containerregister dat intern door Microsoft wordt verstrekt en gebruikt.
        * Dit register bevat platforminstallatiekopieën van de host en clusteronderdelen. Bijvoorbeeld containers voor bewaking of logboekregistratie.
        * Niet bedoeld voor gebruik door klanten van Azure Red Hat OpenShift.  
        * Alleen-lezen.
        * Verbindingen met dit register vinden plaats via het service-eindpunt (interne connectiviteit tussen Azure-services).
        * Dit interne register is standaard niet beschikbaar buiten het cluster.
* **Private Link**
    * Hierdoor wordt netwerkconnectiviteit vanaf het beheervlak in een cluster voor clusterbeheer toegestaan.
    * Technici van Microsoft en Red Hat voor de betrouwbaarheid van de site om uw cluster te helpen beheren.

## <a name="networking-basics-in-openshift"></a>Basisprincipes van netwerken in OpenShift

OpenShift Software Defined Networking (SDN) wordt gebruikt voor het configureren van een overlay-netwerk met behulp van Open vSwitch (OVS), een OpenFlow-implementatie op basis van de Container Network Interface-specificatie (CNI). De SDN ondersteunt verschillende invoegtoepassingen en Network Policy is de invoegtoepassing die in Azure Red Hat op OpenShift 4 wordt gebruikt. Alle netwerkcommunicatie wordt beheerd door de SDN, dus er zijn geen extra routes nodig in uw virtuele netwerken om communicatie tussen pods te bewerkstelligen.

## <a name="azure-red-hat-openshift-networking-specifics"></a>Netwerkdetails van Azure Red Hat OpenShift

De volgende functies zijn specifiek voor Azure Red Hat OpenShift:
* Bring Your Own Virtual Network wordt ondersteund.
* CIDR's voor pods en servicenetwerken kunnen worden geconfigureerd.
* Knooppunten en masters bevinden zich in verschillende subnetten.
* Virtuele netwerksubnetten van knooppunten en masters moeten minimaal /27 zijn.
* Pod CIDR moet minimaal /18 groot zijn (het podnetwerk bestaat uit niet-routeerbare IP's en wordt alleen gebruikt in de OpenShift SDN).
* Aan elk knooppunt wordt een /23-subnet (512 IP's) voor de pods toegewezen. Deze waarde kan niet worden gewijzigd.
* U kunt geen pod aan meer dan één netwerk koppelen.
* U kunt geen uitgaand statisch IP-adres configureren. (Dit is een OpenShift-functie. Zie [configuring egress IPs](https://docs.openshift.com/aro/4/networking/openshift_sdn/assigning-egress-ips.html)) (Uitgaande IP-adressen configureren) voor meer informatie).

## <a name="network-settings"></a>Netwerkinstellingen

De volgende netwerkinstellingen zijn beschikbaar in Azure Red Hat OpenShift 4:

* **API-zichtbaarheid**: stel de API-zichtbaarheid in wanneer u de [opdracht az aro create](tutorial-create-cluster.md#create-the-cluster) uitvoert.
    * 'Openbaar': API-server is toegankelijk voor externe netwerken.
    * 'Privé': aan API-server is een persoonlijk IP-adres toegewezen vanuit het masterssubnet, alleen toegankelijk via verbonden netwerken (gekoppelde virtuele netwerken, overige subnetten in het cluster). Namens de klant wordt een persoonlijke DNS-zone gemaakt.
* **Zichtbaarheid van inkomend verkeer**: stel de API-zichtbaarheid in wanneer u de [opdracht az aro create](tutorial-create-cluster.md#create-the-cluster) uitvoert.
    * 'Openbare' routes worden standaard ingesteld op een openbare Standard Load Balancer (dit kan worden gewijzigd).
    * 'Privé' routes worden standaard ingesteld op de interne load balancer (dit kan worden gewijzigd).

## <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
Netwerkbeveiligingsgroepen worden gemaakt in de resourcegroep van de knooppunten; deze is vergrendeld. De netwerkbeveiligingsgroepen worden rechtstreeks toegewezen aan de subnetten, niet aan de NIC's van het knooppunt. De netwerkbeveiligingsgroepen zijn onveranderbaar. Dit betekent dat u niet over de juiste machtigingen beschikt om ze te wijzigen. 

Met een openlijk zichtbare API-server kunt u echter geen netwerkbeveiligingsgroepen maken en deze toewijzen aan de NIC's.

## <a name="domain-forwarding"></a>Doorsturen van domeinen
Azure Red Hat OpenShift gebruikt CoreDNS. Het doorsturen van domeinen kan worden geconfigureerd (zie de documentatie over [het gebruik van DNS doorsturen](https://docs.openshift.com/aro/4/networking/dns-operator.html#nw-dns-forward_dns-operator) voor meer informatie (Engelstalig)).

Op dit moment kunt u uw eigen DNS niet naar uw virtuele netwerken meenemen.


Zie de documentatie over [ondersteuningsbeleid](support-policies-v4.md) voor meer informatie over uitgaand verkeer en wat Azure Red Hat OpenShift ondersteunt voor uitgaand verkeer.
