---
title: Kennismaking met Azure Kubernetes Service
description: Ontdek de functies en voordelen van Azure Kubernetes Service voor het implementeren en beheren van toepassingen op basis van containers in Azure.
services: container-service
ms.topic: overview
ms.date: 02/24/2021
ms.custom: mvc
ms.openlocfilehash: 1cddd39d0b95e021478235fcdafbacd40eb4097c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105252"
---
# <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes service (AKS) vereenvoudigt de implementatie van een beheerd Kubernetes-cluster in azure door de operationele overhead naar Azure te offloaden. Azure fungeert als een gehoste Kubernetes-service en zorgt voor kritieke taken, zoals status controle en onderhoud. Aangezien Kubernetes-Masters worden beheerd door Azure, kunt u alleen de agent knooppunten beheren en onderhouden. AKS is dus gratis; u betaalt alleen voor de agent knooppunten binnen uw clusters, niet voor de-modellen.  

U kunt een AKS-cluster maken met behulp van:
* [De Azure CLI](kubernetes-walkthrough.md)
* [Azure Portal](kubernetes-walkthrough-portal.md)
* [Azure PowerShell](kubernetes-walkthrough-powershell.md)
* Met sjabloon gestuurde implementatie opties, zoals [Azure Resource Manager sjablonen](kubernetes-walkthrough-rm-template.md) en terraform 

Wanneer u een AKS-cluster implementeert, worden de Kubernetes-master en alle knooppunten voor u geïmplementeerd en geconfigureerd. Geavanceerde netwerken, Azure Active Directory (Azure AD) integratie, bewaking en andere functies kunnen tijdens het implementatie proces worden geconfigureerd. 

Zie [Kubernetes-kernconcepten voor AKS][concepts-clusters-workloads] voor meer informatie over de grondbeginselen van Kubernetes.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]
> AKS biedt ook ondersteuning voor Windows Server-containers.

## <a name="access-security-and-monitoring"></a>Toegang, beveiliging en bewaking

Voor verbeterde beveiliging en beheer kunt u met AKS integreren met Azure AD:
* Gebruik Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC). 
* Bewaak de status van uw cluster en bronnen.

### <a name="identity-and-security-management"></a>Identiteits- en beveiligingsbeheer

#### <a name="kubernetes-rbac"></a>Kubernetes RBAC

AKS ondersteunt [KUBERNETES RBAC][kubernetes-rbac]om de toegang tot cluster bronnen te beperken. Kubernetes RBAC beheert de toegang tot en machtigingen voor Kubernetes resources en naam ruimten.  

#### <a name="azure-ad"></a>Azure AD

U kunt een AKS-cluster configureren om te integreren met Azure AD. Met Azure AD-integratie kunt u Kubernetes toegang instellen op basis van bestaande identiteit en groepslid maatschap. Uw bestaande Azure AD-gebruikers en-groepen kunnen worden geleverd met een geïntegreerde aanmeldings ervaring en toegang tot AKS-resources.  

Zie [Toegangs- en identiteitsopties voor AKS][concepts-identity] voor meer informatie over identiteit.

Zie [Azure Active Directory integreren met AKS][aks-aad] voor informatie over het beveiligen van uw AKS-clusters.

### <a name="integrated-logging-and-monitoring"></a>Geïntegreerde logboekregistratie en bewaking

Azure Monitor voor de status van de container worden de metrische gegevens over het geheugen en de processor prestaties verzameld van containers, knoop punten en controllers in uw AKS-cluster en geïmplementeerde toepassingen. U kunt zowel container Logboeken als [de Kubernetes-hoofd logboeken][aks-master-logs]bekijken:
* Opgeslagen in een Azure Log Analytics-werk ruimte.
* Beschikbaar via de Azure Portal, Azure CLI of een REST-eind punt.

Zie [Containerstatus van Azure Kubernetes Service bewaken][container-health] voor meer informatie.

## <a name="clusters-and-nodes"></a>Clusters en knooppunten

AKS-knoop punten worden uitgevoerd op Azure virtual machines (Vm's). Met AKS-knoop punten kunt u opslag aansluiten op knoop punten en peulen, cluster onderdelen upgraden en Gpu's gebruiken. AKS ondersteunt Kubernetes-clusters waar meerdere knooppuntpools worden uitgevoerd om gemengde besturingssystemen en Windows Server-containers te ondersteunen.  

Zie [Kubernetes core-concepten voor AKS][concepts-clusters-workloads]voor meer informatie over de mogelijkheden van het Kubernetes-cluster, het knoop punt en de knooppunt groep.

### <a name="cluster-node-and-pod-scaling"></a>Clusterknooppunten en pods schalen

Als de vraag naar resources verandert, wordt het aantal cluster knooppunten of het Peul waarmee uw services worden uitgevoerd, automatisch omhoog of omlaag geschaald. U kunt zowel de horizontale pod voor automatisch schalen als de cluster-automatische schaal aanpassing aanpassen aan de vereisten en alleen de benodigde resources uitvoeren.

Zie [Een Azure Kubernetes Service-cluster (AKS) schalen][aks-scale] voor meer informatie.

### <a name="cluster-node-upgrades"></a>Clusterknooppuntupgrades

AKS biedt meerdere versies van Kubernetes. Wanneer er nieuwe versies beschikbaar zijn in AKS, kunt u uw cluster bijwerken met behulp van de Azure Portal of Azure CLI. Tijdens het upgradeproces worden knooppunten zorgvuldig afgebakend en geleegd om onderbreking van actieve toepassingen te minimaliseren.  

Zie [Ondersteunde Kubernetes-versies in AKS][aks-supported versions] voor meer informatie over de levenscyclus van versies. Zie [Een upgrade uitvoeren van een AKS-cluster (Azure Kubernetes Service)][aks-upgrade] voor stappen voor het uitvoeren van een upgrade.

### <a name="gpu-enabled-nodes"></a>GPU-knooppunten

AKS ondersteunt het maken van GPU-knooppuntpools. Azure biedt momenteel VM's met een of meerdere GPU's. GPU-VM's zijn ontworpen voor rekenintensieve, grafisch-intensieve en visualisatiewerkbelastingen.

Zie [GPU's gebruiken op AKS][aks-gpu] voor meer informatie.

### <a name="confidential-computing-nodes-public-preview"></a>Confidential Computing-knooppunten (openbare preview)

AKS biedt ondersteuning voor het maken van op Intel SGX gebaseerde, vertrouwelijk computing-knooppunt Pools (DCSv2-Vm's). Met de knoop punten vertrouwelijk computing kunnen containers worden uitgevoerd in een enclaves (Trusted Execution Environment) op basis van hardware. De isolatie tussen containers in combinatie met code-integriteit via attestation kan helpen bij uw diepgaande beveiligingsstrategie voor containers. Vertrouwelijke computing knooppunten ondersteunen zowel vertrouwelijke containers (bestaande docker-apps) als enclave containers.

Zie [Vertrouwelijke rekenknooppunten op AKS][conf-com-node] voor meer informatie.

### <a name="storage-volume-support"></a>Opslagvolumeondersteuning

Voor de ondersteuning van toepassings werkbelastingen kunt u statische of dynamische opslag volumes koppelen voor permanente gegevens. Afhankelijk van het aantal verbonden peulen verwacht om de opslag volumes te delen, kunt u opslag gebruiken die wordt ondersteund door ofwel:
* Azure-schijven voor toegang tot één Pod of 
* Azure Files voor meerdere, gelijktijdige toegang tot pod.

Zie [Opslagopties voor toepassingen in AKS][concepts-storage] voor meer informatie.

Ga aan de slag met dynamische permanente volumes met [Azure Disks][azure-disk] of [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Virtual Networks en inkomend verkeer

Een AKS-cluster kan worden geïmplementeerd in een bestaand virtueel netwerk. In deze configuratie wordt elke pod in het cluster toegewezen aan een IP-adres in het virtuele netwerk en kan het rechtstreeks communiceren met:
* Andere peulen in het cluster 
* Andere knoop punten in het virtuele netwerk. 

Het is ook mogelijk om verbinding te maken met andere services in een gekoppeld virtueel netwerk en op on-premises netwerken via ExpressRoute of S2S-VPN-verbindingen (site-naar-site).  

Zie [Netwerkconcepten voor toepassingen in AKS][aks-networking] voor meer informatie.

### <a name="ingress-with-http-application-routing"></a>Ingress met HTTP-toepassingsroutering

Met de invoeg toepassing HTTP-toepassings routering kunt u eenvoudig toegang krijgen tot toepassingen die zijn geïmplementeerd op uw AKS-cluster. Indien ingeschakeld, configureert de HTTP-toepassingsrouteringsoplossing een ingangscontroller in uw AKS-cluster.  

Wanneer toepassingen worden geïmplementeerd, worden openbaar toegankelijke DNS-namen automatisch geconfigureerd. De HTTP-toepassings routering stelt een DNS-zone in en integreert deze met het AKS-cluster. Daarna kunt u inkomende Kubernetes-resources op de gebruikelijke manier implementeren.  

Zie [HTTP-toepassingsroutering][aks-http-routing] om aan de slag te gaan met inkomend verkeer.

## <a name="development-tooling-integration"></a>Integratie van ontwikkelingshulpprogramma’s

Kubernetes heeft een rijk ecosysteem van hulpprogram ma's voor ontwikkeling en beheer die naadloos samen werken met AKS. Deze hulpprogram ma's zijn onder andere helm en de Kubernetes-extensie voor Visual Studio code.   

Azure biedt verschillende hulpprogram ma's die u helpen bij het stroom lijnen van Kubernetes, zoals Azure dev Spaces en DevOps starter.  

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Azure Dev Spaces biedt een snelle, iteratieve Kubernetes-ontwikkelervaring voor teams. Met een minimale configuratie kunt u containers rechtstreeks in AKS uitvoeren en debuggen. Zie [Azure Dev Spaces][azure-dev-spaces] om aan de slag te gaan.

### <a name="devops-starter"></a>DevOps starter

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
> [Een AKS-cluster implementeren met behulp van Azure CLI][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://azure.microsoft.com/en-us/overview/trusted-cloud/compliance/

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
[aks-master-logs]: ./view-control-plane-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
