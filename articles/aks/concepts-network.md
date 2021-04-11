---
title: Concepten-netwerken in azure Kubernetes Services (AKS)
description: Meer informatie over netwerken in azure Kubernetes service (AKS), waaronder kubenet-en Azure CNI-netwerken, ingangs controllers, load balancers en statische IP-adressen.
ms.topic: conceptual
ms.date: 03/11/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 56c98163434fbe2d29cf49bf750d6f7d1cfe0d2b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105337"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Netwerk concepten voor toepassingen in azure Kubernetes service (AKS)

In een op containers gebaseerde micro Services-benadering van het ontwikkelen van toepassingen werken toepassings onderdelen samen om hun taken te verwerken. Kubernetes biedt verschillende bronnen voor het inschakelen van deze samen werking:
* U kunt toepassingen intern of extern verbinden met en beschikbaar maken. 
* U kunt Maxi maal beschik bare toepassingen bouwen door de taak verdeling van uw toepassingen uit te sluiten. 
* Voor uw complexere toepassingen kunt u binnenkomend verkeer configureren voor het beëindigen van SSL/TLS of het routeren van meerdere onderdelen. 
* Uit veiligheids overwegingen kunt u de stroom van netwerk verkeer beperken tot of tussen peulen en knoop punten.

In dit artikel worden de belangrijkste concepten geïntroduceerd voor het bieden van netwerken aan uw toepassingen in AKS:

- [Services](#services)
- [Virtuele netwerken van Azure](#azure-virtual-networks)
- [Toegangsbeheerobjectcontrollers](#ingress-controllers)
- [Netwerk beleid](#network-policies)

## <a name="kubernetes-basics"></a>Basisbeginselen voor Kubernetes

Kubernetes biedt een abstractie laag voor virtuele netwerken om toegang tot uw toepassingen of tussen toepassings onderdelen toe te staan. Kubernetes knoop punten maken verbinding met een virtueel netwerk en bieden inkomende en uitgaande connectiviteit voor peulen. Het *uitvoeren-proxy* onderdeel wordt op elk knoop punt uitgevoerd om deze netwerk functies te leveren.

In Kubernetes:
* *Services* groeperen van een logisch groepslid om directe toegang toe te staan op een specifieke poort via een IP-adres of een DNS-naam. 
* U kunt verkeer distribueren met behulp van een *Load Balancer*. 
* Meer complexe route ring van toepassings verkeer kan ook worden bereikt met *ingangs controllers*. 
* Het netwerk verkeer voor peulen kan worden beveiligd en gefilterd met Kubernetes- *netwerk beleid*.

Het Azure-platform vereenvoudigt ook virtuele netwerken voor AKS-clusters. Wanneer u een Kubernetes-load balancer maakt, moet u ook de onderliggende Azure load balancer-resource maken en configureren. Wanneer u netwerk poorten opent op een Peul, worden de bijbehorende regels van de Azure-netwerk beveiligings groep geconfigureerd. Voor HTTP-toepassings routering kan Azure ook *externe DNS* configureren als nieuwe ingangs routes worden geconfigureerd.

## <a name="services"></a>Services

Ter vereenvoudiging van de netwerkconfiguratie maakt Kubernetes gebruik van *Services* om een set pods logisch te groeperen en netwerkconnectiviteit mogelijk te maken. De volgende service typen zijn beschikbaar:

- **Cluster-IP** 
  
  Hiermee maakt u een intern IP-adres voor gebruik binnen het AKS-cluster. Geschikt voor interne toepassingen die andere werk belastingen in het cluster ondersteunen.

    ![Diagram van het cluster-IP-verkeers stroom in een AKS-cluster][aks-clusterip]

- **NodePort** 

  Hiermee maakt u een poort toewijzing op het onderliggende knoop punt waarmee de toepassing rechtstreeks kan worden geopend met het IP-adres en de poort van het knoop punt.

    ![Diagram van weer gave van NodePort verkeers stroom in een AKS-cluster][aks-nodeport]

- **LoadBalancer** 

  Hiermee maakt u een Azure load balancer-resource, configureert u een extern IP-adres en verbindt u het aangevraagde peul met de back-endadresgroep van load balancer. Als u het verkeer van klanten wilt toestaan om de toepassing te bereiken, worden er regels voor taak verdeling gemaakt op de gewenste poorten. 

    ![Diagram van Load Balancer verkeers stroom in een AKS-cluster][aks-loadbalancer]

    Voor extra controle en route ring van het binnenkomende verkeer kunt u in plaats daarvan een [ingangs controller](#ingress-controllers)gebruiken.

- **Externenaam** 

  Hiermee maakt u een specifieke DNS-vermelding voor eenvoudiger toegang tot toepassingen.

Het IP-adres van de load balancers en services kan dynamisch worden toegewezen, maar u kunt ook een bestaand statisch IP-adres opgeven. U kunt zowel interne als externe statische IP-adressen toewijzen. Bestaande vaste IP-adressen zijn vaak gekoppeld aan een DNS-vermelding.

U kunt zowel *interne* als *externe* load balancers maken. Interne load balancers krijgen alleen een privé-IP-adres, zodat ze niet toegankelijk zijn via internet.

## <a name="azure-virtual-networks"></a>Virtuele netwerken van Azure.

In AKS kunt u een cluster implementeren dat gebruikmaakt van een van de volgende twee netwerkmodellen:

- *Kubenet* -netwerken

  De netwerk bronnen worden doorgaans gemaakt en geconfigureerd als het AKS-cluster wordt geïmplementeerd.

- *Azure container Network Interface (cni)-* netwerken
 
  Het AKS-cluster is verbonden met bestaande virtuele netwerk bronnen en-configuraties.

### <a name="kubenet-basic-networking"></a>Kubenet (Basic)-netwerken

De *kubenet* -netwerk optie is de standaard configuratie voor het maken van AKS-clusters. Met *kubenet*:
1. Knoop punten ontvangen een IP-adres uit het subnet van het virtuele netwerk van Azure. 
1. Elk ontvangen IP-adres van een logische andere adres ruimte dan het subnet van het virtuele netwerk van Azure. 
1. NAT (Network Address Translation) wordt vervolgens zo geconfigureerd dat de pods resources kunnen bereiken in het virtuele Azure-netwerk. 
1. Het bron-IP-adres van het verkeer wordt omgezet naar het primaire IP-adres van het knoop punt.

Knoop punten gebruiken de [kubenet][kubenet] Kubernetes-invoeg toepassing. U kunt:
* Laat het Azure-platform de virtuele netwerken voor u maken en configureren, of 
* Kies ervoor om uw AKS-cluster te implementeren in een bestaand subnet van een virtueel netwerk. 

Houd er rekening mee dat alleen de knoop punten een routeerbaar IP-adres ontvangen. De peulen gebruiken NAT om te communiceren met andere bronnen buiten het AKS-cluster. Deze aanpak vermindert het aantal IP-adressen dat u in uw netwerk ruimte moet reserveren voor gebruik.

Zie [kubenet Networking configureren voor een AKS-cluster][aks-configure-kubenet-networking]voor meer informatie.

### <a name="azure-cni-advanced-networking"></a>Azure CNI (Geavanceerd) netwerken

Met Azure CNI haalt elke pod een IP-adres uit het subnet en kan het rechtstreeks worden geopend. Deze IP-adressen moeten vooraf en uniek in uw netwerk ruimte worden gepland. Elk knoop punt heeft een configuratie parameter voor het maximum aantal peulen dat wordt ondersteund. Het equivalente aantal IP-adressen per knoop punt wordt vervolgens vooraf gereserveerd. Zonder planning kan deze aanpak leiden tot een aflopende IP-adres of de nood zaak om clusters opnieuw te bouwen in een groter subnet naarmate uw toepassings vereisten groeien.

In tegens telling tot kubenet, is verkeer naar eind punten in hetzelfde virtuele netwerk niet NAT volgens het primaire IP-adres van het knoop punt. Het bron adres voor verkeer in het virtuele netwerk is het Pod-IP. Verkeer dat buiten het virtuele netwerk is, is nog steeds Nat's voor het primaire IP-adres van het knoop punt.

Knoop punten gebruiken de [Azure cni][cni-networking] Kubernetes-invoeg toepassing.

![Diagram met twee knoop punten met bruggen die elk met één Azure VNet verbinden][advanced-networking-diagram]

Zie [Azure cni configureren voor een AKS-cluster][aks-configure-advanced-networking]voor meer informatie.

### <a name="compare-network-models"></a>Netwerk modellen vergelijken

Zowel kubenet als Azure CNI bieden netwerk connectiviteit voor uw AKS-clusters. Er zijn echter voor delen en nadelen. Op hoog niveau zijn de volgende overwegingen van toepassing:

* **kubenet**
    * Hiermee wordt de IP-adres ruimte bespaard.
    * Maakt gebruik van Kubernetes intern of extern load balancer om het bereik van buiten het cluster te bereiken.
    * U kunt door de gebruiker gedefinieerde routes (Udr's) hand matig beheren en onderhouden.
    * Maxi maal 400 knoop punten per cluster.
* **Azure-CNI**
    * Het is de volledige connectiviteit van een virtueel netwerk en kan rechtstreeks worden bereikt via het privé-IP-adres van verbonden netwerken.
    * Vereist meer IP-adres ruimte.

De volgende gedrags verschillen bestaan tussen kubenet en Azure CNI:

| Mogelijkheid                                                                                   | Kubenet   | Azure-CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Cluster implementeren in bestaand of nieuw virtueel netwerk                                            | Ondersteund-Udr's hand matig toegepast | Ondersteund |
| Pod-pod-connectiviteit                                                                         | Ondersteund | Ondersteund |
| Pod-VM-connectiviteit; VM in hetzelfde virtuele netwerk                                          | Werkt als gestart door Pod | Werkt op beide manieren |
| Pod-VM-connectiviteit; Virtuele machine in een gekoppeld virtueel netwerk                                            | Werkt als gestart door Pod | Werkt op beide manieren |
| On-premises toegang via VPN of Express route                                                | Werkt als gestart door Pod | Werkt op beide manieren |
| Toegang tot resources die zijn beveiligd door service-eind punten                                             | Ondersteund | Ondersteund |
| Kubernetes services beschikbaar maken met behulp van een load balancer service, app-gateway of ingangs controller | Ondersteund | Ondersteund |
| Standaard Azure DNS en privé zones                                                          | Ondersteund | Ondersteund |

Met betrekking tot DNS worden DNS met zowel kubenet-als Azure CNI-invoeg toepassingen aangeboden door CoreDNS, een implementatie die wordt uitgevoerd in AKS met een eigen automatische schaal functie. Zie [Customizing DNS service](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/)(Engelstalig) voor meer informatie over CoreDNS op Kubernetes. CoreDNS is standaard zo geconfigureerd dat onbekende domeinen worden doorgestuurd naar de DNS-functionaliteit van de Azure-Virtual Network waar het AKS-cluster wordt geïmplementeerd. Azure DNS en privé zones werken daarom samen voor een Peul dat wordt uitgevoerd in AKS.

### <a name="support-scope-between-network-models"></a>Ondersteunings bereik tussen netwerk modellen

Welk netwerk model u gebruikt, zowel kubenet als Azure CNI kunnen op een van de volgende manieren worden geïmplementeerd:

* Het Azure-platform kan automatisch de virtuele netwerk bronnen maken en configureren wanneer u een AKS-cluster maakt.
* U kunt hand matig de virtuele netwerk resources maken en configureren en aan deze resources koppelen wanneer u uw AKS-cluster maakt.

Hoewel functies, zoals service-eind punten of Udr's worden ondersteund met zowel kubenet als Azure CNI, definiëren het [ondersteunings beleid voor AKS][support-policies] welke wijzigingen u kunt aanbrengen. Bijvoorbeeld:

* Als u de virtuele netwerk resources voor een AKS-cluster hand matig maakt, wordt u ondersteund bij het configureren van uw eigen Udr's of service-eind punten.
* Als het Azure-platform automatisch de virtuele netwerk resources voor uw AKS-cluster maakt, kunt u deze bronnen die door AKS worden beheerd, niet hand matig wijzigen om uw eigen Udr's of service-eind punten te configureren.

## <a name="ingress-controllers"></a>Toegangsbeheerobjectcontrollers

Wanneer u een Load Balancer-type-service maakt, maakt u ook een onderliggende Azure load balancer-resource. De load balancer is zo geconfigureerd dat verkeer naar het Peul in uw service op een bepaalde poort wordt gedistribueerd. 

De LoadBalancer werkt alleen op laag 4. Op laag 4 is de service niet op de hoogte van de werkelijke toepassingen en kunnen er geen routerings overwegingen meer worden gemaakt.

*Ingangs controllers* werken op laag 7 en kunnen intelligentere regels gebruiken voor het distribueren van toepassings verkeer. Ingangs controllers routeren doorgaans HTTP-verkeer naar verschillende toepassingen op basis van de inkomende URL.

![Diagram van ingangs verkeers stroom in een AKS-cluster][aks-ingress]

### <a name="create-an-ingress-resource"></a>Een ingangs resource maken
In AKS kunt u een ingangs bron maken met behulp van NGINX, een vergelijkbaar hulp programma of de functie voor het routeren van HTTP-toepassingen van AKS. Wanneer u HTTP-toepassings routering inschakelt voor een AKS-cluster, maakt het Azure-platform de ingangs controller en een *externe DNS* -controller. Wanneer er nieuwe ingangs bronnen worden gemaakt in Kubernetes, worden de vereiste DNS A-records gemaakt in een cluster-specifieke DNS-zone. 

Zie [HTTP-toepassings routering implementeren][aks-http-routing]voor meer informatie.

### <a name="application-gateway-ingress-controller-agic"></a>Application Gateway ingangs controller (AGIC)

Met de invoeg toepassing Application Gateway ingangs controller (AGIC) kunnen AKS-klanten gebruikmaken van de systeem eigen Load Balancer van Azure Application Gateway niveau 7 om cloud software op internet beschikbaar te stellen. AGIC bewaakt het host Kubernetes-cluster en werkt voortdurend een Application Gateway, waarbij geselecteerde services worden blootgesteld aan Internet. 

Zie [Wat is Application Gateway ingress-controller?][agic-overview]voor meer informatie over de INVOEG toepassing AGIC voor AKS.

### <a name="ssltls-termination"></a>SSL/TLS-beëindiging

Het beëindigen van SSL/TLS is een andere algemene functie van inkomend verkeer. Bij grote webtoepassingen die via HTTPS worden geopend, verwerkt de ingangs bron de TLS-beëindiging in plaats van in de toepassing zelf. Als u het genereren en configureren van TLS-certificering wilt bieden, kunt u de bron van de ingang configureren voor het gebruik van providers als ' laten versleutelen '. 

Zie binnenkomend [en TLS][aks-ingress-tls]voor meer informatie over het configureren van een NGINX ingress-controller met de code ring.

### <a name="client-source-ip-preservation"></a>IP-behoud van client bron

Configureer uw ingangs controller om het client bron-IP te behouden op aanvragen voor containers in uw AKS-cluster. Wanneer de ingangs controller een aanvraag van een client naar een container in uw AKS-cluster stuurt, is de oorspronkelijke bron-IP van die aanvraag niet beschikbaar voor de doel container. Wanneer u *IP-behoud van client bron* inschakelt, is het bron-IP-adres voor de client beschikbaar in de aanvraag header onder *X-doorgestuurd-voor*. 

Als u client bron-IP-behoud gebruikt op uw ingangs controller, kunt u geen TLS Pass-Through gebruiken. IP-behoud van client bron en TLS Pass-Through kunnen worden gebruikt in combi natie met andere services, zoals het type *Load Balancer* .

## <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

Een netwerk beveiligings groep filtert verkeer voor virtuele machines, zoals de AKS-knoop punten. Wanneer u Services maakt, zoals een LoadBalancer, configureert het Azure-platform automatisch alle benodigde regels voor de netwerk beveiligings groep. 

U hoeft de regels voor de netwerk beveiligings groep niet hand matig te configureren voor het filteren van verkeer voor een peuling in een AKS-cluster. U hoeft alleen de vereiste poorten te definiëren en door te sturen als onderdeel van uw Kubernetes-service manifesten. Laat het Azure-platform de juiste regels maken of bijwerken. 

U kunt ook netwerk beleid gebruiken om automatisch Traffic filter regels toe te passen op een Peul.

## <a name="network-policies"></a>Netwerk beleid

Standaard kan alle peulen in een AKS-cluster verkeer verzenden en ontvangen zonder beperkingen. Voor een betere beveiliging definieert u regels die de stroom van verkeer bepalen, zoals:
* Back-end-toepassingen worden alleen blootgesteld aan de vereiste frontend-Services. 
* Database onderdelen zijn alleen toegankelijk voor de toepassings lagen waarmee ze zijn verbonden.

Netwerk beleid is een Kubernetes-functie die beschikbaar is in AKS waarmee u de verkeers stroom tussen de peulen kunt beheren. U kunt het verkeer naar de pod toestaan of weigeren op basis van instellingen, zoals toegewezen labels, naam ruimten of netwerk verkeer poort. Hoewel netwerk beveiligings groepen beter zijn voor AKS-knoop punten, is netwerk beleid een beterere, in de Cloud gerichte manier om de stroom van verkeer voor de gehele groep te beheren. Als er een Peul dynamisch wordt gemaakt in een AKS-cluster, kunnen de vereiste netwerk beleidsregels automatisch worden toegepast.

Zie voor meer informatie [beveiligd verkeer tussen peulen met netwerk beleid in azure Kubernetes service (AKS)][use-network-policies].

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met AKS-netwerken, maakt en configureert u een AKS-cluster met uw eigen IP-adresbereiken met behulp van [kubenet][aks-configure-kubenet-networking] of [Azure cni][aks-configure-advanced-networking].

Zie [Aanbevolen procedures voor netwerk connectiviteit en beveiliging in AKS][operator-best-practices-network]voor gekoppelde aanbevolen procedures.

Raadpleeg de volgende artikelen voor meer informatie over de belangrijkste Kubernetes-en AKS-concepten:

- [Kubernetes/AKS-clusters en-workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-toegang en-identiteit][aks-concepts-identity]
- [Kubernetes/AKS-beveiliging][aks-concepts-security]
- [Kubernetes/AKS-opslag][aks-concepts-storage]
- [Kubernetes/AKS-schaal][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
