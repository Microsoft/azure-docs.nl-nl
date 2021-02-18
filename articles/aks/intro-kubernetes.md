---
title: Kennismaking met Azure Kubernetes Service
description: Ontdek de functies en voordelen van Azure Kubernetes Service voor het implementeren en beheren van toepassingen op basis van containers in Azure.
services: container-service
ms.topic: overview
ms.date: 02/09/2021
ms.custom: mvc
ms.openlocfilehash: 1505366d9a91eac596b21804f93abb8245a84605
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590003"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Azure Kubernetes service (AKS) vereenvoudigt de implementatie van een beheerd Kubernetes-cluster in azure door een groot deel van de complexiteit en operationele overhead naar Azure te offloaden. Azure fungeert als een gehoste Kubernetes-service en zorgt voor kritieke taken, zoals status controle en onderhoud.  

Omdat de Kubernetes-Masters door Azure worden beheerd, beheert u alleen de agent knooppunten en houdt u deze bij. Als beheerde Kubernetes-service is AKS dus gratis; u betaalt alleen voor de agent knooppunten binnen uw clusters, niet voor de-modellen.  

U kunt een AKS-cluster maken met behulp van de Azure Portal, de Azure CLI, Azure PowerShell of met behulp van sjabloon gestuurde implementatie opties, zoals Resource Manager-sjablonen en terraform. Wanneer u een AKS-cluster implementeert, worden de Kubernetes-master en alle knooppunten voor u geïmplementeerd en geconfigureerd. Extra functies zoals geavanceerd netwerken, Azure Active Directory-integratie en bewaking kunnen ook tijdens het implementatieproces worden geconfigureerd. Windows Server-containers worden ondersteund in AKS.

Zie [Kubernetes-kernconcepten voor AKS][concepts-clusters-workloads] voor meer informatie over de grondbeginselen van Kubernetes.

Om aan de slag te gaan, voltooit u de AKS Snelstartgids [in de Azure Portal][aks-portal] of [met de Azure cli][aks-cli].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="access-security-and-monitoring"></a>Toegang, beveiliging en bewaking

Voor verbeterde beveiliging en beheer kunt u met AKS integreren met Azure Active Directory (Azure AD) en:
* Gebruik Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC). 
* Bewaak de status van uw cluster en bronnen.

### <a name="identity-and-security-management"></a>Identiteits- en beveiligingsbeheer

AKS ondersteunt [KUBERNETES RBAC][kubernetes-rbac]om de toegang tot cluster bronnen te beperken. Met Kubernetes RBAC kunt u de toegang en machtigingen voor Kubernetes-resources en-naam ruimten beheren.  

U kunt ook een AKS-cluster configureren om te integreren met Azure AD. Met Azure AD-integratie kunt u Kubernetes toegang configureren op basis van bestaande identiteit en groepslid maatschap. Uw bestaande Azure AD-gebruikers en-groepen kunnen worden geleverd met een geïntegreerde aanmeldings ervaring en toegang tot AKS-resources.  

Zie [Toegangs- en identiteitsopties voor AKS][concepts-identity] voor meer informatie over identiteit.

Zie [Azure Active Directory integreren met AKS][aks-aad] voor informatie over het beveiligen van uw AKS-clusters.

### <a name="integrated-logging-and-monitoring"></a>Geïntegreerde logboekregistratie en bewaking

Azure Monitor voor de status van de container worden de metrische gegevens over het geheugen en de processor prestaties verzameld van containers, knoop punten en controllers in uw AKS-cluster en geïmplementeerde toepassingen. U kunt de logboeken van de container en [de Kubernetes-hoofd logboeken][aks-master-logs]bekijken. Deze bewakings gegevens worden opgeslagen in een Azure Log Analytics-werk ruimte en zijn beschikbaar via de Azure Portal, Azure CLI of een REST-eind punt.

Zie [Containerstatus van Azure Kubernetes Service bewaken][container-health] voor meer informatie.

## <a name="clusters-and-nodes"></a>Clusters en knooppunten

AKS-knoop punten worden uitgevoerd op Azure virtual machines (Vm's). Met AKS-knoop punten kunt u opslag aansluiten op knoop punten en peulen, cluster onderdelen upgraden en Gpu's gebruiken. AKS ondersteunt Kubernetes-clusters waar meerdere knooppuntpools worden uitgevoerd om gemengde besturingssystemen en Windows Server-containers te ondersteunen.  

Zie [Kubernetes core-concepten voor AKS][concepts-clusters-workloads]voor meer informatie over de mogelijkheden van het Kubernetes-cluster, het knoop punt en de knooppunt groep.

### <a name="cluster-node-and-pod-scaling"></a>Clusterknooppunten en pods schalen

Naarmate de vraag naar resources verandert, kan het aantal clusterknooppunten of -pods waarop uw services worden uitgevoerd, automatisch omhoog of omlaag worden geschaald. U kunt zowel de horizontale automatische schaalaanpassing voor pods als de automatische schaalaanpassing voor clusters gebruiken. Met deze schalingsmethode kan het AKS-cluster automatisch worden aangepast aan de eisen, zodat alleen de benodigde resources worden uitgevoerd.

Zie [Een Azure Kubernetes Service-cluster (AKS) schalen][aks-scale] voor meer informatie.

### <a name="cluster-node-upgrades"></a>Clusterknooppuntupgrades

AKS biedt meerdere versies van Kubernetes. Wanneer nieuwe versies beschikbaar komen in AKS, kan uw cluster worden bijgewerkt met behulp van Azure Portal of Azure-CLI. Tijdens het upgradeproces worden knooppunten zorgvuldig afgebakend en geleegd om onderbreking van actieve toepassingen te minimaliseren.  

Zie [Ondersteunde Kubernetes-versies in AKS][aks-supported versions] voor meer informatie over de levenscyclus van versies. Zie [Een upgrade uitvoeren van een AKS-cluster (Azure Kubernetes Service)][aks-upgrade] voor stappen voor het uitvoeren van een upgrade.

### <a name="gpu-enabled-nodes"></a>GPU-knooppunten

AKS ondersteunt het maken van GPU-knooppuntpools. Azure biedt momenteel VM's met een of meerdere GPU's. GPU-VM's zijn ontworpen voor rekenintensieve, grafisch-intensieve en visualisatiewerkbelastingen.

Zie [GPU's gebruiken op AKS][aks-gpu] voor meer informatie.

### <a name="confidential-computing-nodes-public-preview"></a>Confidential Computing-knooppunten (openbare preview)

AKS biedt ondersteuning voor het maken van op Intel SGX gebaseerde, vertrouwelijk computing-knooppunt Pools (DCSv2-Vm's). Met de knoop punten vertrouwelijk computing kunnen containers worden uitgevoerd in een enclaves (Trusted Execution Environment) op basis van hardware. De isolatie tussen containers in combinatie met code-integriteit via attestation kan helpen bij uw diepgaande beveiligingsstrategie voor containers. Vertrouwelijke computing knooppunten ondersteunen zowel vertrouwelijke containers (bestaande docker-apps) als enclave containers.

Zie [Vertrouwelijke rekenknooppunten op AKS][conf-com-node] voor meer informatie.

### <a name="storage-volume-support"></a>Opslagvolumeondersteuning

Ter ondersteuning van werkbelastingen kunt u opslagvolumes koppelen voor uw permanente gegevens. U kunt zowel statische als dynamische volumes gebruiken. Afhankelijk van het aantal verbonden peulen verwacht om de opslag volumes te delen, kunt u opslag gebruiken die wordt ondersteund door Azure-schijven voor toegang tot één Pod of Azure Files voor meerdere gelijktijdige toegang tot pod.

Zie [Opslagopties voor toepassingen in AKS][concepts-storage] voor meer informatie.

Ga aan de slag met dynamische permanente volumes met [Azure Disks][azure-disk] of [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Virtual Networks en inkomend verkeer

Een AKS-cluster kan worden geïmplementeerd in een bestaand virtueel netwerk. In deze configuratie wordt elke pod in het cluster toegewezen aan een IP-adres in het virtuele netwerk en kan rechtstreeks communiceren met andere peulen in het cluster en andere knoop punten in het virtuele netwerk. Het is ook mogelijk om verbinding te maken met andere services in een gekoppeld virtueel netwerk en op on-premises netwerken via ExpressRoute of S2S-VPN-verbindingen (site-naar-site).  

Zie [Netwerkconcepten voor toepassingen in AKS][aks-networking] voor meer informatie.

### <a name="ingress-with-http-application-routing"></a>Ingress met HTTP-toepassingsroutering

De invoegtoepassing voor HTTP-toepassingsroutering vergemakkelijkt de toegang tot toepassingen die in uw AKS-cluster zijn geïmplementeerd. Indien ingeschakeld, configureert de HTTP-toepassingsrouteringsoplossing een ingangscontroller in uw AKS-cluster.  

Wanneer toepassingen worden geïmplementeerd, worden openbaar toegankelijke DNS-namen automatisch geconfigureerd. De HTTP-toepassings routering stelt een DNS-zone in en integreert deze met het AKS-cluster. Daarna kunt u inkomende Kubernetes-resources op de gebruikelijke manier implementeren.  

Zie [HTTP-toepassingsroutering][aks-http-routing] om aan de slag te gaan met inkomend verkeer.

## <a name="development-tooling-integration"></a>Integratie van ontwikkelingshulpprogramma’s

Kubernetes heeft een rijk ecosysteem van hulpprogram ma's voor ontwikkeling en beheer die naadloos samen werken met AKS. Deze hulpprogram ma's zijn onder andere helm en de Kubernetes-extensie voor Visual Studio code. Deze hulpprogramma's werken naadloos met AKS.  

Daarnaast biedt Azure verschillende hulpprogram ma's die u helpen bij het stroom lijnen van Kubernetes, zoals Azure dev Spaces en DevOps starter.  

Azure Dev Spaces biedt een snelle, iteratieve Kubernetes-ontwikkelervaring voor teams. Met een minimale configuratie kunt u containers rechtstreeks in AKS uitvoeren en debuggen. Zie [Azure Dev Spaces][azure-dev-spaces] om aan de slag te gaan.

DevOps Starter biedt een eenvoudige oplossing voor het overbrengen van bestaande code en Git-opslagplaatsen naar Azure. DevOps starter automatisch:
* Hiermee maakt u Azure-resources (zoals AKS). 
* Hiermee configureert u een release pijplijn in azure DevOps services die een build-pijp lijn voor CI bevat. 
* Hiermee stelt u een release pijplijn in voor de CD. maar 
* Hiermee wordt een Azure-toepassing Insights-resource gegenereerd voor bewaking. 

Zie [DevOps Starter][azure-devops] voor meer informatie.

## <a name="docker-image-support-and-private-container-registry"></a>Ondersteuning voor Docker-installatiekopieën en registers voor persoonlijke containers

AKS ondersteunt de Docker-installatiekopie-indeling. Voor privéopslag van uw Docker-installatiekopieën kunt u AKS integreren met Azure Container Registry (ACR).

Zie [Azure Container Registry][acr-docs] om opslag voor persoonlijke installatiekopieën te maken.

## <a name="kubernetes-certification"></a>Kubernetes-certificering

AKS is CNCF-gecertificeerd als Kubernetes-conformiteit.

## <a name="regulatory-compliance"></a>Naleving van regelgeving

AKS is compatibel met SOC, ISO, PCI DSS en HIPAA. Zie voor [Overview of Microsoft Azure compliance][compliance-doc] (Overzicht van compliance in Microsoft Azure).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het implementeren en beheren van AKS met de Snelstartgids van Azure CLI.

> [!div class="nextstepaction"]
> [AKS-quickstart][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: ../dev-spaces/index.yml
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/containers/container-insights-overview.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
