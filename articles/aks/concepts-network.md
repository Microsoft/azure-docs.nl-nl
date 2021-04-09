---
title: Concepten-netwerken in azure Kubernetes Services (AKS)
description: Meer informatie over netwerken in azure Kubernetes service (AKS), waaronder kubenet-en Azure CNI-netwerken, ingangs controllers, load balancers en statische IP-adressen.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: edb195fae2e05a1f746c10482576f7e0b1bff7c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88243901"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Netwerk concepten voor toepassingen in azure Kubernetes service (AKS)

In een op containers gebaseerde micro Services-aanpak voor toepassings ontwikkeling moeten toepassings onderdelen samen werken om hun taken te verwerken. Kubernetes biedt verschillende bronnen voor het inschakelen van deze toepassings communicatie. U kunt toepassingen intern of extern verbinden met en beschikbaar maken. Als u Maxi maal beschik bare toepassingen wilt bouwen, kunt u uw toepassingen gelijkmatig verdelen. Complexere toepassingen vereisen mogelijk configuratie van binnenkomend verkeer voor het beëindigen van SSL/TLS of het routeren van meerdere onderdelen. Uit veiligheids overwegingen moet u mogelijk ook de stroom van het netwerk verkeer beperken tot of tussen de verschillende en knoop punten.

In dit artikel worden de belangrijkste concepten geïntroduceerd voor het bieden van netwerken aan uw toepassingen in AKS:

- [Services](#services)
- [Virtuele netwerken van Azure](#azure-virtual-networks)
- [Toegangsbeheerobjectcontrollers](#ingress-controllers)
- [Netwerk beleid](#network-policies)

## <a name="kubernetes-basics"></a>Basisbeginselen voor Kubernetes

Om toegang tot uw toepassingen toe te staan, of om toepassings onderdelen met elkaar te laten communiceren, biedt Kubernetes een abstractie laag voor virtuele netwerken. Kubernetes-knoop punten zijn verbonden met een virtueel netwerk en kunnen een inkomende en uitgaande connectiviteit bieden voor een Peul. Het *uitvoeren-proxy* onderdeel wordt op elk knoop punt uitgevoerd om deze netwerk functies te leveren.

In Kubernetes is het logisch groeperen van *Services* , waarmee directe toegang mogelijk is via een IP-adres of een DNS-naam en op een specifieke poort. U kunt ook verkeer distribueren met behulp van een *Load Balancer*. Meer complexe route ring van toepassings verkeer kan ook worden bereikt met *ingangs controllers*. Het netwerk verkeer voor peulen kan worden beveiligd en gefilterd met Kubernetes- *netwerk beleid*.

Het Azure-platform helpt u ook om virtuele netwerken te vereenvoudigen voor AKS-clusters. Wanneer u een Kubernetes-load balancer maakt, wordt de onderliggende Azure load balancer-resource gemaakt en geconfigureerd. Wanneer u netwerk poorten opent op een Peul, worden de bijbehorende regels van de Azure-netwerk beveiligings groep geconfigureerd. Voor HTTP-toepassings routering kan Azure ook *externe DNS* configureren als nieuwe ingangs routes worden geconfigureerd.

## <a name="services"></a>Services

Ter vereenvoudiging van de netwerkconfiguratie maakt Kubernetes gebruik van *Services* om een set pods logisch te groeperen en netwerkconnectiviteit mogelijk te maken. De volgende service typen zijn beschikbaar:

- **Cluster-IP** : Hiermee maakt u een intern IP-adres voor gebruik binnen het AKS-cluster. Geschikt voor interne toepassingen die andere werk belastingen in het cluster ondersteunen.

    ![Diagram van het cluster-IP-verkeers stroom in een AKS-cluster][aks-clusterip]

- **NodePort** : maakt een poort toewijzing op het onderliggende knoop punt waarmee de toepassing rechtstreeks kan worden geopend met het IP-adres en de poort van het knoop punt.

    ![Diagram van weer gave van NodePort verkeers stroom in een AKS-cluster][aks-nodeport]

- **LoadBalancer** : Hiermee maakt u een Azure Load Balancer-resource, configureert u een extern IP-adres en verbindt u de aangevraagde peul met de back-endadresgroep van Load Balancer. Als u het verkeer van klanten wilt toestaan om de toepassing te bereiken, worden er regels voor taak verdeling gemaakt op de gewenste poorten. 

    ![Diagram van Load Balancer verkeers stroom in een AKS-cluster][aks-loadbalancer]

    Voor extra controle en route ring van het binnenkomende verkeer kunt u in plaats daarvan een [ingangs controller](#ingress-controllers)gebruiken.

- **Externe** naam: Hiermee maakt u een specifieke DNS-vermelding voor eenvoudiger toegang tot toepassingen.

Het IP-adres voor load balancers en services kan dynamisch worden toegewezen, maar u kunt ook een bestaand statisch IP-adres opgeven dat u wilt gebruiken. Zowel interne als externe statische IP-adressen kunnen worden toegewezen. Dit bestaande vaste IP-adres is vaak gekoppeld aan een DNS-vermelding.

Zowel *interne* als *externe* load balancers kunnen worden gemaakt. Interne load balancers krijgen alleen een privé-IP-adres, zodat ze niet toegankelijk zijn via internet.

## <a name="azure-virtual-networks"></a>Virtuele netwerken van Azure.

In AKS kunt u een cluster implementeren dat gebruikmaakt van een van de volgende twee netwerkmodellen:

- *Kubenet* -netwerken: de netwerk bronnen worden doorgaans gemaakt en geconfigureerd als het AKS-cluster wordt geïmplementeerd.
- *Azure container Network Interface (cni)* -netwerken: het AKS-cluster is verbonden met bestaande virtuele netwerk bronnen en-configuraties.

### <a name="kubenet-basic-networking"></a>Kubenet (Basic)-netwerken

De *kubenet* -netwerk optie is de standaard configuratie voor het maken van AKS-clusters. Met *kubenet* krijgen knoop punten een IP-adres uit het subnet van het virtuele Azure-netwerk. Pods krijgen een IP-adres van een logisch verschillende adresruimte van het subnet van het virtuele Azure-netwerk van de knooppunten. NAT (Network Address Translation) wordt vervolgens zo geconfigureerd dat de pods resources kunnen bereiken in het virtuele Azure-netwerk. Het bron-IP-adres van het verkeer is NAT naar het primaire IP-adres van het knoop punt.

Knoop punten gebruiken de [kubenet][kubenet] Kubernetes-invoeg toepassing. U kunt het Azure-platform de virtuele netwerken voor u laten maken en configureren, of uw AKS-cluster implementeren in een bestaand subnet van een virtueel netwerk. Opnieuw, alleen de knoop punten ontvangen een routeerbaar IP-adres en de peulen gebruiken NAT om te communiceren met andere bronnen buiten het AKS-cluster. Met deze methode wordt het aantal IP-adressen dat u in uw netwerk ruimte moet reserveren, aanzienlijk verminderd voor gebruik.

Zie [kubenet Networking configureren voor een AKS-cluster][aks-configure-kubenet-networking]voor meer informatie.

### <a name="azure-cni-advanced-networking"></a>Azure CNI (Geavanceerd) netwerken

Met Azure CNI haalt elke pod een IP-adres uit het subnet en kan het rechtstreeks worden geopend. Deze IP-adressen moeten uniek zijn binnen uw netwerk ruimte en moeten vooraf worden gepland. Elk knoop punt heeft een configuratie parameter voor het maximum aantal peulen dat wordt ondersteund. Het equivalente aantal IP-adressen per knoop punt wordt vervolgens vóór dat knoop punt gereserveerd. Deze benadering vereist meer planning, zoals anders kan leiden tot een afzuig van IP-adressen of de nood zaak om clusters opnieuw te bouwen in een groter subnet naarmate uw toepassings vereisten groeien.

In tegens telling tot kubenet, is verkeer naar eind punten in hetzelfde virtuele netwerk niet NAT volgens het primaire IP-adres van het knoop punt. Het bron adres voor verkeer in het virtuele netwerk is het Pod-IP. Verkeer dat buiten het virtuele netwerk is, is nog steeds Nat's voor het primaire IP-adres van het knoop punt.

Knoop punten gebruiken de [Azure container Networking interface (cni) Kubernetes-][cni-networking] invoeg toepassing.

![Diagram met twee knoop punten met bruggen die elk met één Azure VNet verbinden][advanced-networking-diagram]

Zie [Azure cni configureren voor een AKS-cluster][aks-configure-advanced-networking]voor meer informatie.

### <a name="compare-network-models"></a>Netwerk modellen vergelijken

Zowel kubenet als Azure CNI bieden netwerk connectiviteit voor uw AKS-clusters. Er zijn echter voor delen en nadelen. Op hoog niveau zijn de volgende overwegingen van toepassing:

* **kubenet**
    * Hiermee wordt de IP-adres ruimte bespaard.
    * Maakt gebruik van Kubernetes intern of extern load balancer om het bereik van buiten het cluster te bereiken.
    * U moet door de gebruiker gedefinieerde routes (Udr's) hand matig beheren en onderhouden.
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

Met betrekking tot DNS wordt met behulp van de kubenet-en Azure CNI-invoeg toepassingen DNS aangeboden door CoreDNS, een implementatie die wordt uitgevoerd in AKS met een eigen automatische schaal functie. Zie [Customizing DNS service](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/)(Engelstalig) voor meer informatie over CoreDNS op Kubernetes. CoreDNS is standaard geconfigureerd om onbekende domeinen door te sturen naar de DNS-server van het knoop punt, met andere woorden, naar de DNS-functionaliteit van de Azure Virtual Network waar het AKS-cluster wordt geïmplementeerd. Azure DNS en privé zones werken daarom samen voor een Peul dat wordt uitgevoerd in AKS.

### <a name="support-scope-between-network-models"></a>Ondersteunings bereik tussen netwerk modellen

Ongeacht het netwerk model dat u gebruikt, kunnen zowel kubenet als Azure CNI op een van de volgende manieren worden geïmplementeerd:

* Het Azure-platform kan automatisch de virtuele netwerk bronnen maken en configureren wanneer u een AKS-cluster maakt.
* U kunt hand matig de virtuele netwerk resources maken en configureren en aan deze resources koppelen wanneer u uw AKS-cluster maakt.

Hoewel functies, zoals service-eind punten of Udr's worden ondersteund met zowel kubenet als Azure CNI, definiëren het [ondersteunings beleid voor AKS][support-policies] welke wijzigingen u kunt aanbrengen. Bijvoorbeeld:

* Als u de virtuele netwerk resources voor een AKS-cluster hand matig maakt, wordt u ondersteund bij het configureren van uw eigen Udr's of service-eind punten.
* Als het Azure-platform automatisch de virtuele netwerk resources voor uw AKS-cluster maakt, wordt het niet ondersteund om deze AKS-beheerde resources hand matig te wijzigen om uw eigen Udr's of service-eind punten te configureren.

## <a name="ingress-controllers"></a>Toegangsbeheerobjectcontrollers

Wanneer u een Load Balancer-type service maakt, wordt er een onderliggende Azure load balancer-resource gemaakt. De load balancer is zo geconfigureerd dat verkeer naar het Peul in uw service op een bepaalde poort wordt gedistribueerd. De LoadBalancer werkt alleen op laag 4: de service is niet op de hoogte van de daad werkelijke toepassingen en kan geen verdere routerings overwegingen nemen.

*Ingangs controllers* werken op laag 7 en kunnen intelligentere regels gebruiken voor het distribueren van toepassings verkeer. Een normaal gebruik van een ingangs controller is het routeren van HTTP-verkeer naar verschillende toepassingen op basis van de inkomende URL.

![Diagram van ingangs verkeers stroom in een AKS-cluster][aks-ingress]

In AKS kunt u een ingangs bron maken met behulp van iets zoals NGINX, of de AKS HTTP-toepassings routerings functie gebruiken. Wanneer u HTTP-toepassings routering inschakelt voor een AKS-cluster, maakt het Azure-platform de ingangs controller en een *externe DNS* -controller. Wanneer er nieuwe ingangs bronnen worden gemaakt in Kubernetes, worden de vereiste DNS A-records gemaakt in een cluster-specifieke DNS-zone. Zie [HTTP-toepassings routering implementeren][aks-http-routing]voor meer informatie.

Met de invoeg toepassing Application Gateway ingangs controller (AGIC) kunnen AKS-klanten gebruikmaken van de systeem eigen Load Balancer van Azure Application Gateway niveau 7 om cloud software op internet beschikbaar te stellen. AGIC bewaakt het Kubernetes-cluster dat wordt gehost op een Application Gateway, zodat de geselecteerde services worden blootgesteld aan Internet. Zie [Wat is Application Gateway ingress-controller?][agic-overview] voor meer informatie over de INVOEG toepassing AGIC voor AKS.

Een andere algemene functie van inkomend verkeer is SSL/TLS-beëindiging. Bij grote webtoepassingen die via HTTPS worden geopend, kan de TLS-beëindiging worden verwerkt door de ingangs bron in plaats van in de toepassing zelf. Als u het genereren en configureren van TLS-certificering wilt bieden, kunt u de bron van de ingang configureren voor het gebruik van providers zoals versleutelen. Zie binnenkomend [en TLS][aks-ingress-tls]voor meer informatie over het configureren van een NGINX ingress-controller met de code ring.

U kunt ook uw ingangs controller configureren om het client bron-IP te behouden op aanvragen voor containers in uw AKS-cluster. Wanneer de aanvraag van een client wordt doorgestuurd naar een container in uw AKS-cluster via uw ingangs controller, is de oorspronkelijke bron-IP van die aanvraag niet beschikbaar voor de doel container. Wanneer u *IP-behoud van client bron* inschakelt, is het bron-IP-adres voor de client beschikbaar in de aanvraag header onder *X-doorgestuurd-voor*. Als u client bron-IP-behoud gebruikt op uw ingangs controller, kunt u geen TLS Pass-Through gebruiken. IP-behoud van client bron en TLS Pass-Through kunnen worden gebruikt in combi natie met andere services, zoals het type *Load Balancer* .

## <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

Een netwerk beveiligings groep filtert het verkeer voor virtuele machines, zoals de AKS-knoop punten. Wanneer u Services maakt, zoals een LoadBalancer, configureert het Azure-platform automatisch alle regels voor netwerk beveiligings groepen die nodig zijn. Configureer de regels voor de netwerk beveiligings groep niet hand matig om verkeer te filteren voor een Peul in een AKS-cluster. Definieer de vereiste poorten en door sturen als onderdeel van uw Kubernetes-service manifesten, en laat het Azure-platform de juiste regels maken of bijwerken. U kunt ook netwerk beleid, zoals beschreven in de volgende sectie, gebruiken om automatisch regels voor het filteren van verkeer toe te passen op peul.

## <a name="network-policies"></a>Netwerk beleid

Standaard kan alle peulen in een AKS-cluster verkeer verzenden en ontvangen zonder beperkingen. Voor een betere beveiliging kunt u regels definiëren die de stroom van verkeer regelen. Back-end-toepassingen worden vaak alleen blootgesteld aan de vereiste frontend-Services of database onderdelen zijn alleen toegankelijk voor de toepassings lagen waarmee ze verbinding maken.

Netwerk beleid is een Kubernetes-functie die beschikbaar is in AKS waarmee u de verkeers stroom tussen de peulen kunt beheren. U kunt ervoor kiezen om verkeer toe te staan of te weigeren op basis van instellingen, zoals toegewezen labels, de naam ruimte of de netwerk poort. Netwerk beveiligings groepen zijn meer voor de AKS-knoop punten, niet van Peul. Het gebruik van netwerk beleid is een beter geschikte Cloud manier om de stroom van verkeer te beheren. Als het Peul dynamisch wordt gemaakt in een AKS-cluster, kunnen de vereiste netwerk beleidsregels automatisch worden toegepast.

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
