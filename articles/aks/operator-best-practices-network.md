---
title: Aanbevolen procedures voor netwerk bronnen
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de aanbevolen procedures voor cluster operators voor virtuele netwerk bronnen en connectiviteit in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: f004e0e78d7a626f878ba3651e4c6078f9cd21e8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366565"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Best practices voor netwerkverbinding en -beveiliging in Azure Kubernetes Service (AKS)

Wanneer u clusters maakt en beheert in azure Kubernetes service (AKS), biedt u netwerk connectiviteit voor uw knoop punten en toepassingen. Deze netwerk bronnen zijn IP-adresbereiken, load balancers en ingangs controllers. Als u een hoge kwaliteit van de service voor uw toepassingen wilt behouden, moet u deze resources plannen en vervolgens configureren.

In deze best practices wordt het artikel gericht op netwerk connectiviteit en-beveiliging voor cluster operators. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Vergelijk de netwerk modi kubenet en Azure container Networking interface (CNI) in AKS
> * Plan voor vereiste IP-adres sering en connectiviteit
> * Verkeer distribueren met load balancers, ingangs controllers of een Web Application Firewall (WAF)
> * Veilig verbinding maken met cluster knooppunten

## <a name="choose-the-appropriate-network-model"></a>Kies het juiste netwerk model

**Richt lijnen voor best practices** : voor de integratie met bestaande virtuele netwerken of on-premises netwerken gebruikt u Azure cni-netwerken in AKS. Dit netwerk model maakt ook een grotere schei ding van resources en besturings elementen mogelijk in een bedrijfs omgeving.

Virtuele netwerken bieden de basis connectiviteit voor AKS-knoop punten en klanten om toegang te krijgen tot uw toepassingen. Er zijn twee verschillende manieren voor het implementeren van AKS-clusters in virtuele netwerken:

* **Kubenet-netwerken** : Azure beheert de virtuele netwerk resources wanneer het cluster is geïmplementeerd en maakt gebruik van de [Kubenet][kubenet] Kubernetes-invoeg toepassing.
* **Azure cni-netwerken** : implementeert in een virtueel netwerk en maakt gebruik van de [Azure container Networking interface (cni) Kubernetes-][cni-networking] invoeg toepassing. Elk van de verschillende Ip's ontvangen die kunnen worden doorgestuurd naar andere netwerk services of on-premises bronnen.

Voor productie-implementaties zijn zowel kubenet als Azure CNI geldige opties.

### <a name="cni-networking"></a>CNI-netwerken

De container Networking interface (CNI) is een Vendor-neutraal protocol waarmee de container runtime aanvragen voor een netwerk provider kan indienen. De Azure CNI wijst IP-adressen toe aan de peulen en knoop punten en biedt functies voor IP-adres beheer (IPAM) wanneer u verbinding maakt met bestaande Azure Virtual Networks. Elk knoop punt en pod-resource ontvangt een IP-adres in het virtuele netwerk van Azure en er is geen extra route ring nodig om te communiceren met andere resources of services.

![Diagram met twee knoop punten met bruggen die elk met één Azure VNet verbinden](media/operator-best-practices-network/advanced-networking-diagram.png)

Een belang rijk voor deel van Azure CNI Networking voor productie is het netwerk model maakt schei ding van beheer en beheer van resources mogelijk. Vanuit een beveiligings perspectief wilt u vaak dat verschillende teams deze bronnen beheren en beveiligen. Met Azure CNI-netwerken kunt u rechtstreeks verbinding maken met bestaande Azure-resources, on-premises resources of andere services via IP-adressen die zijn toegewezen aan elke pod.

Wanneer u Azure CNI-netwerken gebruikt, bevindt de virtuele netwerk resource zich in een afzonderlijke resource groep voor het AKS-cluster. Delegeer machtigingen voor de Service-Principal AKS voor toegang tot en beheer van deze resources. De service-principal die wordt gebruikt door het AKS-cluster moet ten minste [netwerkinzender](../role-based-access-control/built-in-roles.md#network-contributor) machtigingen hebben voor het subnet binnen het virtuele netwerk. Als u een [aangepaste rol](../role-based-access-control/custom-roles.md) wilt definiëren in plaats van de ingebouwde rol netwerk bijdrager te gebruiken, zijn de volgende machtigingen vereist:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Zie [toegang tot andere Azure-resources delegeren][sp-delegation]voor meer informatie over delegering van AKS-Service-Principal. In plaats van een Service-Principal kunt u ook de door het systeem toegewezen beheerde identiteit voor machtigingen gebruiken. Zie [Beheerde identiteiten gebruiken](use-managed-identity.md) voor meer informatie.

Plan de adresbereiken voor de AKS-subnetten, aangezien elk knoop punt en pod zijn eigen IP-adres ontvangen. Het subnet moet groot genoeg zijn om IP-adressen op te geven voor elk knoop punt, elk van beide en netwerk bronnen die u implementeert. Elk AKS-cluster moet in een eigen subnet worden geplaatst. Om verbinding te kunnen maken met on-premises of peered netwerken in azure, kunt u geen IP-adresbereiken gebruiken die overlappen met bestaande netwerk bronnen. Er zijn standaard limieten voor het aantal peulen dat elke knoop punt wordt uitgevoerd met zowel kubenet als Azure CNI-netwerken. Voor het afhandelen van scale-out gebeurtenissen of cluster upgrades hebt u ook extra IP-adressen beschikbaar voor gebruik in het toegewezen subnet. Deze extra adres ruimte is vooral belang rijk als u Windows Server-containers gebruikt, omdat voor deze knooppunt groepen een upgrade moet worden uitgevoerd om de meest recente beveiligings patches toe te passen. Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor meer informatie over Windows Server-knoop punten.

Zie [Azure cni-netwerken configureren in AKS][advanced-networking]voor het berekenen van het vereiste IP-adres.

Wanneer u een cluster met Azure CNI-netwerken maakt, kunt u andere adresbereiken opgeven voor gebruik door het cluster, zoals het docker-brug adres, de DNS-service-IP en het adres bereik van de service. Over het algemeen mogen deze adresbereiken elkaar niet overlappen en hoeven ze niet te overlappen met netwerken die aan het cluster zijn gekoppeld, met inbegrip van virtuele netwerken, subnetten, on-premises en peered netwerken. Zie [Azure cni-netwerken configureren in AKS][advanced-networking]voor specifieke informatie over de limieten en grootte van deze adresbereiken.

### <a name="kubenet-networking"></a>Kubenet-netwerken

Hoewel u voor kubenet de virtuele netwerken niet hoeft in te stellen voordat het cluster wordt geïmplementeerd, zijn er nadelen:

* Knoop punten en peulen worden op verschillende IP-subnetten geplaatst. Door de gebruiker gedefinieerde route ring (UDR) en door sturen via IP wordt gebruikt voor het routeren van verkeer tussen peulen en knoop punten. Deze extra route ring kan de netwerk prestaties verminderen.
* Verbindingen met bestaande on-premises netwerken of peering met andere virtuele netwerken van Azure kunnen ingewikkeld zijn.

Kubenet is geschikt voor kleine ontwikkel-en test werkbelastingen, omdat u het virtuele netwerk en de subnetten niet afzonderlijk van het AKS-cluster hoeft te maken. Eenvoudige websites met weinig verkeer of voor het optillen en verplaatsen van workloads in containers, kunnen ook profiteren van de eenvoud van AKS-clusters die zijn geïmplementeerd met kubenet-netwerken. Voor de meeste productie-implementaties moet u Azure CNI-netwerken plannen en gebruiken.

U kunt ook [uw eigen IP-adresbereiken en virtuele netwerken configureren met behulp van kubenet][aks-configure-kubenet-networking]. Net als bij Azure CNI-netwerken, mogen deze adresbereiken elkaar niet overlappen en hoeven ze niet overeen te komen met netwerken die aan het cluster zijn gekoppeld, met inbegrip van virtuele netwerken, subnetten, on-premises en peered netwerken. Zie [kubenet-netwerken gebruiken met uw eigen IP-adresbereiken in AKS][aks-configure-kubenet-networking]voor specifieke details over de limieten en grootte van deze adresbereiken.

## <a name="distribute-ingress-traffic"></a>Binnenkomend verkeer distribueren

**Richt lijnen voor best practices** : als u http-of HTTPS-verkeer naar uw toepassingen wilt distribueren, gebruikt u de bronnen en controllers van de ingang. Ingangs controllers bieden extra functies via een gewone Azure-load balancer en kunnen worden beheerd als systeem eigen Kubernetes-resources.

Een Azure-load balancer kan klant verkeer distribueren naar toepassingen in uw AKS-cluster, maar dit is beperkt in wat het verkeer begrijpt. Een load balancer resource werkt op laag 4 en distribueert verkeer op basis van protocollen of poorten. De meeste webtoepassingen die gebruikmaken van HTTP of HTTPS, moeten Kubernetes inkomende bronnen en controllers gebruiken, die op laag 7 werken. Bij inkomend verkeer kunnen gegevens worden gedistribueerd op basis van de URL van de toepassing en worden de TLS/SSL-beëindiging afhandeld. Deze mogelijkheid vermindert ook het aantal IP-adressen dat u beschikbaar maakt en toewijst. Met een load balancer moet voor elke toepassing doorgaans een openbaar IP-adres zijn toegewezen en toegewezen aan de service in het AKS-cluster. Met een ingangs resource kan één IP-adres verkeer distribueren naar meerdere toepassingen.

![Diagram van ingangs verkeers stroom in een AKS-cluster](media/operator-best-practices-network/aks-ingress.png)

 Er zijn twee onderdelen voor inkomend verkeer:

 * Een ingangs *bron* en
 * Een ingangs *controller*

De ingangs resource is een YAML-manifest van `kind: Ingress` waarmee de host, certificaten en regels worden gedefinieerd voor het routeren van verkeer naar services die worden uitgevoerd in uw AKS-cluster. In het volgende voor beeld YAML-manifest wordt het verkeer voor *MyApp.com* gedistribueerd naar een van de twee services, *blogservice* of *storeservice*. De klant wordt omgeleid naar één service of het andere op basis van de URL waartoe ze toegang hebben.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

Een ingangs controller is een daemon die wordt uitgevoerd op een AKS-knoop punt en controleert op inkomende aanvragen. Verkeer wordt vervolgens gedistribueerd op basis van de regels die zijn gedefinieerd in de bron ingang. De meest voorkomende ingress-controller is gebaseerd op [NGINX]. AKS beperkt u niet tot een specifieke controller, zodat u andere controllers, zoals [Contour][contour], [HAProxy][haproxy]of [Traefik][traefik], kunt gebruiken.

Ingangs controllers moeten worden gepland op een Linux-knoop punt. Windows Server-knooppunten mogen de ingangscontroller niet uitvoeren. Gebruik een knooppunt kiezer in uw YAML-manifest of helm-grafiek implementatie om aan te geven dat de resource moet worden uitgevoerd op een Linux-knoop punt. Zie [knooppunt selectie vakjes gebruiken om te bepalen waar peulingen worden gepland in AKS][concepts-node-selectors]voor meer informatie.

Er zijn veel scenario's voor inkomend verkeer, met inbegrip van de volgende hand leidingen:

* [Een eenvoudige ingangs controller met externe netwerk verbinding maken][aks-ingress-basic]
* [Een ingangs controller maken die gebruikmaakt van een intern, privé netwerk en IP-adres][aks-ingress-internal]
* [Een ingangs controller maken die gebruikmaakt van uw eigen TLS-certificaten][aks-ingress-own-tls]
* Een ingangs controller maken die gebruikmaakt van een versleuteling om automatisch TLS-certificaten te genereren [met een dynamisch openbaar IP-adres][aks-ingress-tls] of [met een statisch openbaar IP-adres][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Verkeer beveiligen met een Web Application Firewall (WAF)

**Richt lijnen voor best practices** : als u inkomend verkeer voor mogelijke aanvallen wilt scannen, gebruikt u een Web Application firewall (WAF) zoals [Barracuda WAF voor Azure][barracuda-waf] of Azure-toepassing gateway. Deze geavanceerde netwerk bronnen kunnen ook verkeer routeren buiten de HTTP-en HTTPS-verbindingen of door eenvoudige TLS-beëindiging.

Een ingangs controller die verkeer naar Services en toepassingen distribueert, is doorgaans een Kubernetes-bron in uw AKS-cluster. De controller wordt uitgevoerd als een daemon op een AKS-knoop punt en verbruikt enkele bronnen van het knoop punt, zoals CPU, geheugen en netwerk bandbreedte. In grotere omgevingen wilt u vaak een deel van deze verkeers routering of TLS-beëindiging naar een netwerk bron buiten het AKS-cluster offloaden. U wilt ook inkomend verkeer scannen op mogelijke aanvallen.

![Een Web Application Firewall (WAF) zoals Azure-app gateway kan verkeer voor uw AKS-cluster beveiligen en distribueren](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Een Web Application Firewall (WAF) biedt een extra beveiligingslaag door het binnenkomende verkeer te filteren. Het OWASP (open Web Application Security project) biedt een reeks regels om te controleren op aanvallen zoals cross-site scripting of cookie-vergiftiging. [Azure-toepassing gateway][app-gateway] (momenteel als preview in AKS) is een WAF die kan worden geïntegreerd met AKS-clusters om deze beveiligings functies te bieden, voordat het verkeer uw AKS cluster en toepassingen bereikt. Andere oplossingen van derden voeren deze functies ook uit, zodat u bestaande investeringen of expertise in een bepaald product kunt blijven gebruiken.

Load Balancer-of ingangs bronnen blijven actief in uw AKS-cluster om de distributie van verkeer verder te verfijnen. App gateway kan centraal worden beheerd als een ingangs controller met een resource definitie. [Maak een Application Gateway ingangs controller][app-gateway-ingress]om aan de slag te gaan.

## <a name="control-traffic-flow-with-network-policies"></a>Verkeers stroom beheren met netwerk beleid

**Richt lijnen voor best practices** : gebruik netwerk beleid om verkeer naar peul toe te staan of te weigeren. Standaard wordt al het verkeer toegestaan tussen een peulheid binnen een cluster. Voor een betere beveiliging definieert u regels die de pod-communicatie beperken.

Netwerk beleid is een Kubernetes-functie waarmee u de verkeers stroom tussen de peulen kunt beheren. U kunt ervoor kiezen om verkeer toe te staan of te weigeren op basis van instellingen, zoals toegewezen labels, de naam ruimte of de netwerk poort. Het gebruik van netwerk beleid biedt een Cloud-systeem eigen manier om de stroom van verkeer te beheren. Als het Peul dynamisch wordt gemaakt in een AKS-cluster, kunnen de vereiste netwerk beleidsregels automatisch worden toegepast. Gebruik Azure-netwerk beveiligings groepen niet om Pod-to-pod-verkeer te beheren. gebruik netwerk beleid.

Als u netwerk beleid wilt gebruiken, moet u de functie inschakelen wanneer u een AKS-cluster maakt. U kunt netwerk beleid niet inschakelen op een bestaand AKS-cluster. Plan vooruit om ervoor te zorgen dat u netwerk beleid inschakelt voor clusters en dit als nodig kunt gebruiken. Netwerk beleid mag alleen worden gebruikt voor Linux-knoop punten en een Peul in AKS.

Er wordt een netwerk beleid gemaakt als een Kubernetes-bron met behulp van een YAML-manifest. De beleids regels worden toegepast op gedefinieerde peulen, vervolgens worden de inkomend of het uitgangs beleid bepalen hoe het verkeer kan stromen. In het volgende voor beeld wordt een netwerk beleid toegepast op peul met de *app:* het label back-end toegepast. De ingangs regel staat vervolgens alleen verkeer van Peul toe met de *app: frontend* -label:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

Om aan de slag te gaan met beleids regels, Zie [beveiligde verkeer tussen peulen met netwerk beleid in azure Kubernetes service (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Veilig verbinding maken met knoop punten via een bastion-host

**Best Practice-instructies** : Maak geen externe verbinding met uw AKS-knoop punten beschikbaar. Maak een bastion-host of een Jump box in een virtueel beheer netwerk. Gebruik de bastion-host om verkeer veilig door te sturen naar uw AKS-cluster naar taken voor extern beheer.

De meeste bewerkingen in AKS kunnen worden voltooid met behulp van de Azure-beheer hulpprogramma's of via de Kubernetes-API-server. AKS-knoop punten zijn niet verbonden met het open bare Internet en zijn alleen beschikbaar in een particulier netwerk. Om verbinding te maken met knoop punten en onderhoud uit te voeren of problemen op te lossen, kunt u uw verbindingen routeren via een bastion-host of een Jump box. Deze host moet zich in een afzonderlijk beheer virtueel netwerk bevindt dat veilig is gekoppeld aan het virtuele netwerk van het AKS-cluster.

![Verbinding maken met AKS-knoop punten met behulp van een bastion-host of een Jump box](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Het beheer netwerk voor de bastion-host moet ook worden beveiligd. Een [Azure ExpressRoute][expressroute] of [VPN-gateway][vpn-gateway] gebruiken om verbinding te maken met een on-premises netwerk en de toegang te beheren met netwerk beveiligings groepen.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is gericht op netwerk connectiviteit en beveiliging. Zie [netwerk concepten voor toepassingen in azure Kubernetes service (AKS)][aks-concepts-network] voor meer informatie over de basis principes van netwerken in Kubernetes.

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool