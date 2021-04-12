---
title: Concepten-beveiliging in azure Kubernetes Services (AKS)
description: Meer informatie over beveiliging in azure Kubernetes service (AKS), met inbegrip van Master-en knooppunt communicatie, netwerk beleid en Kubernetes geheimen.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: mlearned
ms.openlocfilehash: 3fafbe3f4b1c53f929682f4ca160fb19a5e91918
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105303"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Beveiligingsconcepten voor toepassingen en clusters in Azure Kubernetes Service (AKS)

Met cluster beveiliging worden uw klant gegevens beschermd tijdens het uitvoeren van werk belastingen van toepassingen in azure Kubernetes service (AKS). 

Kubernetes bevat beveiligings onderdelen, zoals *netwerk beleid* en *geheimen*. Ondertussen bevat Azure onderdelen zoals netwerk beveiligings groepen en gegroepeerde cluster upgrades. AKS combineert deze beveiligings onderdelen tot:
* Zorg ervoor dat uw AKS-cluster de meest recente OS-beveiligings updates en Kubernetes-releases uitvoert.
* Beveiligde pod-verkeer en toegang tot gevoelige referenties bieden.

In dit artikel worden de belangrijkste concepten geïntroduceerd voor het beveiligen van uw toepassingen in AKS:

- [Beveiligingsconcepten voor toepassingen en clusters in Azure Kubernetes Service (AKS)](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Master beveiliging](#master-security)
  - [Knooppunt beveiliging](#node-security)
    - [Reken isolatie](#compute-isolation)
  - [Cluster upgrades](#cluster-upgrades)
    - [Cordon en afvoer](#cordon-and-drain)
  - [Netwerkbeveiliging](#network-security)
    - [Netwerkbeveiligingsgroepen in Azure](#azure-network-security-groups)
  - [Kubernetes geheimen](#kubernetes-secrets)
  - [Volgende stappen](#next-steps)

## <a name="master-security"></a>Master beveiliging

In AKS maken de Kubernetes-hoofd onderdelen deel uit van de beheerde service die door micro soft wordt verschaft, beheerd en onderhouden. Elk AKS-cluster heeft een eigen Kubernetes-Master met één Tenant om de API-server, scheduler, enzovoort te bieden.

De Kubernetes API-server gebruikt standaard een openbaar IP-adres en een Fully Qualified Domain Name (FQDN). U kunt de toegang tot het API-server eindpunt beperken met behulp van [geautoriseerde IP-bereiken][authorized-ip-ranges]. U kunt ook een volledig [particulier cluster][private-clusters] maken om de API-server toegang tot uw virtuele netwerk te beperken.

U kunt de toegang tot de API-server beheren met behulp van Kubernetes op basis van rollen (Kubernetes RBAC) en Azure RBAC. Zie [Azure AD-integratie met AKS][aks-aad]voor meer informatie.

## <a name="node-security"></a>Knooppunt beveiliging

AKS-knoop punten zijn virtuele Azure-machines (Vm's) die u beheert en onderhoudt. 
* Linux-knoop punten voeren een geoptimaliseerde Ubuntu-distributie uit met behulp van de `containerd` of Moby container runtime. 
* Windows Server-knoop punten voeren een geoptimaliseerde versie van Windows Server 2019 uit met behulp van de- `containerd` of Moby-container runtime. 

Wanneer een AKS-cluster wordt gemaakt of geschaald, worden de knoop punten automatisch geïmplementeerd met de meest recente beveiligings updates en-configuraties van het besturings systeem.

> [!NOTE]
> AKS-clusters met:
> * Kubernetes versie 1,19-knooppunt groepen en meer gebruiken `containerd` als container runtime. 
> * Kubernetes vóór v 1.19-knooppunt Pools gebruiken [Moby](https://mobyproject.org/) (upstream docker) als container runtime.

### <a name="node-security-patches"></a>Beveiligings patches voor knoop punten

#### <a name="linux-nodes"></a>Linux-knoop punten
Het Azure-platform past automatisch de beveiligings patches van het besturings systeem op een nacht in op Linux-knoop punten. Als voor een installatie van een Linux-beveiligings update een host opnieuw moet worden opgestart, wordt deze niet automatisch opnieuw opgestart. U hebt de volgende mogelijkheden:
* Start de Linux-knoop punten hand matig opnieuw op.
* Gebruik [Kured][kured], een open source-daemon voor opnieuw opstarten voor Kubernetes. Kured wordt uitgevoerd als een [daemonset][aks-daemonsets] en bewaakt elk knoop punt voor een bestand dat aangeeft dat de computer opnieuw moet worden opgestart. 

Opnieuw opstarten wordt via het cluster beheerd met hetzelfde Cordon- [en afvoer proces](#cordon-and-drain) als een cluster upgrade.

#### <a name="windows-server-nodes"></a>Windows Server-knoop punten

Voor Windows Server-knoop punten wordt Windows Update niet automatisch uitgevoerd en worden de meest recente updates toegepast. Windows Server-knooppunt groeps upgrades plannen in uw AKS-cluster rond de reguliere Windows Update-release cyclus en uw eigen validatie proces. Dit upgrade proces maakt knoop punten waarop de nieuwste installatie kopie en patches van Windows Server worden uitgevoerd, waarna de oudere knoop punten worden verwijderd. Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor meer informatie over dit proces.

### <a name="node-deployment"></a>Implementatie van knoop punten
Knoop punten worden geïmplementeerd in een particulier subnet van een virtueel netwerk, waaraan geen open bare IP-adressen zijn toegewezen. Voor het oplossen van problemen en beheer doeleinden is SSH standaard ingeschakeld en alleen toegankelijk via het interne IP-adres.

### <a name="node-storage"></a>Knooppunt opslag
Om opslag te bieden, gebruiken de knoop punten Azure Managed Disks. Voor de meeste VM-knooppunt grootten zijn Azure-Managed Disks Premium-schijven ondersteund door Ssd's met hoge prestaties. De gegevens die op Managed disks zijn opgeslagen, worden automatisch versleuteld in het Azure-platform. Om redundantie te verbeteren, worden Azure-Managed Disks veilig gerepliceerd in het Azure-Data Center.

### <a name="hostile-multi-tenant-workloads"></a>Multi tenant-workloads met vijand

Momenteel zijn Kubernetes omgevingen niet veilig voor gebruik van multi tenants. Extra beveiligings functies, zoals *pod-beveiligings beleid* of Kubernetes RBAC voor knoop punten, blok keren aanvallen op efficiënte wijze. Alleen een Hyper Visor vertrouwen voor echte beveiliging bij het uitvoeren van vijandelijke multi tenant-workloads. Het beveiligings domein voor Kubernetes wordt het hele cluster, niet een afzonderlijk knoop punt. 

Voor dit soort vijandelijke multi tenant-workloads moet u fysiek geïsoleerde clusters gebruiken. Zie [Aanbevolen procedures voor cluster isolatie in AKS][cluster-isolation]voor meer informatie over manieren om workloads te isoleren.

### <a name="compute-isolation"></a>Reken isolatie

Vanwege nalevings-of regelgeving kunnen bepaalde werk belastingen een hoge mate van isolatie van andere werk belastingen van de klant vereisen. Voor deze werk belastingen biedt Azure [geïsoleerde vm's](../virtual-machines/isolation.md) die kunnen worden gebruikt als agent knooppunten in een AKS-cluster. Deze Vm's zijn geïsoleerd voor een specifiek hardwaretype en zijn specifiek voor één klant. 

Selecteer [een van de geïsoleerde vm's](../virtual-machines/isolation.md) grootte als de grootte van het **knoop punt** bij het maken van een AKS-cluster of het toevoegen van een knooppunt groep.

## <a name="cluster-upgrades"></a>Cluster upgrades

Azure biedt upgrade-hulpprogram ma's voor het bijwerken van een AKS-cluster en-onderdelen, onderhoud van beveiliging en naleving, en toegang tot de nieuwste functies. Deze upgrade-indeling omvat zowel de Kubernetes-Master als de agent onderdelen. 

Als u het upgrade proces wilt starten, geeft u een van de [vermelde beschik bare Kubernetes-versies](supported-kubernetes-versions.md)op. In azure wordt elk AKS-knoop punt en-upgrades veilig cordons en verwaterd.

### <a name="cordon-and-drain"></a>Cordon en afvoer

Tijdens het upgrade proces worden AKS-knoop punten afzonderlijk afgebakend van het cluster om te voor komen dat nieuwe peulen worden gepland. De knoop punten worden vervolgens als volgt geleegd en geüpgraded:

1.  Er wordt een nieuw knoop punt in de knooppunt groep geïmplementeerd. 
    * Dit knoop punt voert de meest recente installatie kopie van het besturings systeem en patches uit.
1. Een van de bestaande knoop punten wordt geïdentificeerd voor de upgrade. 
1. Het Peul op het geïdentificeerde knoop punt wordt op de juiste wijze beëindigd en gepland op de andere knoop punten in de knooppunt groep.
1. Het geleegde knoop punt wordt uit het AKS-cluster verwijderd.
1. Stap 1-4 wordt herhaald totdat alle knoop punten zijn vervangen als onderdeel van het upgrade proces.

Zie [een AKS-cluster upgraden][aks-upgrade-cluster]voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

Voor connectiviteit en beveiliging met on-premises netwerken kunt u uw AKS-cluster implementeren in bestaande subnetten van het virtuele Azure-netwerk. Deze virtuele netwerken maken verbinding met uw on-premises netwerk met behulp van Azure site-naar-site VPN of Express route. Definieer Kubernetes ingress-controllers met persoonlijke, interne IP-adressen om de toegang van services tot de interne netwerk verbinding te beperken.

### <a name="azure-network-security-groups"></a>Netwerkbeveiligingsgroepen in Azure

Als u de stroom van het virtuele netwerk verkeer wilt filteren, maakt Azure gebruik van regels voor de netwerk beveiligings groep. Deze regels definiëren de bron-en doel-IP-bereiken, poorten en protocollen die toegang tot bronnen hebben of geweigerd. Standaard regels worden gemaakt om TLS-verkeer toe te staan voor de Kubernetes-API-server. U maakt Services met load balancers, poort toewijzingen of ingangs routes. AKS wijzigt automatisch de netwerk beveiligings groep voor de verkeers stroom.

Als u uw eigen subnet voor uw AKS-cluster opgeeft, moet u de netwerk beveiligings groep op subnetniveau die wordt beheerd door AKS **niet** wijzigen. Maak in plaats daarvan meer netwerk beveiligings groepen op subnetniveau om de stroom van verkeer te wijzigen. Zorg ervoor dat ze geen invloed hebben op het verkeer dat nodig is voor het beheren van het cluster, zoals load balancer toegang, communicatie met het besturings vlak en de [uitgang][aks-limit-egress-traffic].

### <a name="kubernetes-network-policy"></a>Kubernetes-netwerk beleid

AKS biedt ondersteuning voor [Kubernetes-netwerk beleid][network-policy]om het netwerk verkeer tussen de peulen in uw cluster te beperken. Met netwerk beleid kunt u specifieke netwerk paden binnen het cluster toestaan of weigeren op basis van naam ruimten en label selectie.

## <a name="kubernetes-secrets"></a>Kubernetes Secrets

Met een Kubernetes- *geheim* kunt u gevoelige gegevens in peul injecteren, zoals toegangs referenties of sleutels. 
1. Maak een geheim met behulp van de Kubernetes-API. 
1. Definieer uw Pod of implementatie en vraag een specifiek geheim aan. 
    * Geheimen worden alleen door gegeven aan knoop punten met een geplande pod waarvoor ze nodig zijn.
    * Het geheim wordt opgeslagen in *tmpfs*, niet naar schijf geschreven. 
1. Wanneer u de laatste pod verwijdert op een knoop punt dat een geheim vereist, wordt het geheim verwijderd uit de tmpfs van het knoop punt. 
   * Geheimen worden opgeslagen in een opgegeven naam ruimte en kunnen alleen worden gebruikt door een Peul binnen dezelfde naam ruimte.

Het gebruik van geheimen vermindert de gevoelige informatie die is gedefinieerd in het Pod-of service YAML-manifest. In plaats daarvan vraagt u het geheim op dat is opgeslagen in de Kubernetes API-server als onderdeel van uw YAML-manifest. Deze aanpak biedt alleen de specifieke pod toegang tot het geheim. 

> [!NOTE]
> De onbewerkte geheime manifest bestanden bevatten de geheime gegevens in Base64-indeling (Zie de [officiële documentatie][secret-risks] voor meer informatie). Behandel deze bestanden als gevoelige informatie en sla ze nooit op in broncode beheer.

Kubernetes geheimen worden opgeslagen in etcd, een gedistribueerd sleutel-value Store. Etcd Store wordt volledig beheerd door AKS en [gegevens worden op het Azure-platform versleuteld in rust][encryption-atrest]. 

## <a name="next-steps"></a>Volgende stappen

Zie [een AKS-cluster upgraden][aks-upgrade-cluster]om aan de slag te gaan met het beveiligen van uw AKS-clusters.

Zie [Aanbevolen procedures voor het beveiligen van clusters en upgrades in AKS][operator-best-practices-cluster-security] en [Aanbevolen procedures voor pod Security in AKS][developer-best-practices-pod-security]voor de bijbehorende aanbevolen procedures.

Zie voor meer informatie over Core Kubernetes-en AKS-concepten:

- [Kubernetes/AKS-clusters en-workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-identiteit][aks-concepts-identity]
- [Kubernetes/AKS virtuele netwerken][aks-concepts-network]
- [Kubernetes/AKS-opslag][aks-concepts-storage]
- [Kubernetes/AKS-schaal][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
