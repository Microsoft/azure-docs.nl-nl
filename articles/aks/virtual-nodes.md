---
title: Virtuele knooppunten gebruiken
titleSuffix: Azure Kubernetes Service
description: Overzicht van het gebruik van virtueel knoop punt met Azure Kubernetes Services (AKS)
services: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.custom: references_regions
ms.openlocfilehash: 3bba1155ec57db67968aec95d1d3386fc6cda006
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634444"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>Een AKS-cluster (Azure Kubernetes Services) maken en configureren voor het gebruik van virtuele knoop punten

Als u werk belastingen van toepassingen snel wilt schalen in een AKS-cluster, kunt u virtuele knoop punten gebruiken. Met virtuele knoop punten hebt u een snelle inrichting van Peul en betaalt u slechts per seconde voor de uitvoerings tijd. U hoeft niet te wachten op de Kubernetes-cluster-automatische schaal functie om VM-reken knooppunten te implementeren om de extra peulen uit te voeren. Virtuele knoop punten worden alleen ondersteund met Linux-en knoop punten.

De virtuele knoop punten-invoeg toepassing voor AKS is gebaseerd op de [virtuele Kubelet][virtual-kubelet-repo]van het open source-project.

In dit artikel vindt u een overzicht van de beschik baarheid van regio's en netwerk vereisten voor het gebruik van virtuele knoop punten, evenals de bekende beperkingen.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Alle regio's, waarbij ACI ondersteuning biedt voor VNET Sku's, worden ondersteund voor implementaties van virtuele knoop punten.

Voor de beschik bare cpu's CPU en geheugen in elke regio controleert u de [Beschik baarheid van Azure container instances resources voor Azure container instances in azure-regio's-Linux-container groepen](../container-instances/container-instances-region-availability.md#linux-container-groups)

## <a name="network-requirements"></a>Netwerkvereisten

Virtuele knoop punten scha kelen netwerk communicatie in tussen de peulen die worden uitgevoerd in Azure Container Instances (ACI) en het AKS-cluster. Om deze communicatie te bieden, wordt een subnet van een virtueel netwerk gemaakt en worden gedelegeerde machtigingen toegewezen. Virtuele knoop punten werken alleen met AKS-clusters die zijn gemaakt met *Advanced* Network (Azure cni). Standaard worden AKS-clusters gemaakt met *Basic* -netwerken (kubenet).

Voor het uitvoeren van een in-Azure Container Instances (ACI) is toegang tot het AKS API-server eindpunt vereist om netwerken te kunnen configureren.

## <a name="known-limitations"></a>Bekende beperkingen

De functionaliteit van virtuele knoop punten is sterk afhankelijk van de functieset van ACI. Naast de [quota en limieten voor Azure container instances](../container-instances/container-instances-quotas.md), worden de volgende scenario's nog niet ondersteund met virtuele knoop punten:

* Service-Principal gebruiken voor het ophalen van ACR-installatie kopieën. [Tijdelijke oplossing](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) is het gebruik van [Kubernetes-geheimen](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Virtual Network beperkingen](../container-instances/container-instances-vnet.md) , waaronder VNet-peering, Kubernetes-netwerk beleid en uitgaand verkeer naar Internet met netwerk beveiligings groepen.
* Init-containers
* [Host-aliassen](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumenten](../container-instances/container-instances-exec.md#restrictions) voor exec in ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) implementeert geen peuling voor de virtuele knoop punten
* Virtuele knoop punten ondersteunen de planning van Linux. U kunt de open source [Virtual KUBELET ACI](https://github.com/virtual-kubelet/azure-aci) -provider hand matig installeren om Windows Server-containers te plannen voor ACI.
* Virtuele knoop punten vereisen AKS-clusters met Azure CNI-netwerken.
* Het gebruik van met API server geautoriseerde IP-adresbereiken voor AKS.
* Volume koppeling Azure Files share ondersteuning voor [algemeen gebruik v1](../storage/common/storage-account-overview.md#types-of-storage-accounts). Volg de instructies voor het koppelen van [een volume met Azure Files share](azure-files-volume.md)
* Het gebruik van IPv6 wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

Virtuele knoop punten voor uw clusters configureren:

- [Virtuele knoop punten maken met behulp van Azure CLI](virtual-nodes-cli.md)
- [Virtuele knoop punten maken met behulp van de portal in azure Kubernetes Services (AKS)](virtual-nodes-portal.md)

Virtuele knoop punten zijn vaak één onderdeel van een schaal oplossing in AKS. Raadpleeg de volgende artikelen voor meer informatie over het schalen van oplossingen:

- [De Kubernetes-functie voor horizontale pod automatisch schalen gebruiken][aks-hpa]
- [De automatische schaal functie van het Kubernetes-cluster gebruiken][aks-cluster-autoscaler]
- [Bekijk het voor beeld van automatisch schalen voor virtuele knoop punten][virtual-node-autoscale]
- [Meer informatie over de open source-bibliotheek van de virtuele-Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
