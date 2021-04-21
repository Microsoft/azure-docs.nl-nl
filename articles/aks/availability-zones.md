---
title: Beschikbaarheidszones gebruiken in Azure Kubernetes Service (AKS)
description: Meer informatie over het maken van een cluster dat knooppunten verdeelt over beschikbaarheidszones in Azure Kubernetes Service (AKS)
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 796cb0e2b76dc2a04834df61c053c5ad2ceb25fd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769621"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Een AKS Azure Kubernetes Service cluster (AKS) maken dat gebruikmaakt van beschikbaarheidszones

Een Azure Kubernetes Service (AKS) distribueert resources zoals knooppunten en opslag over logische secties van de onderliggende Azure-infrastructuur. Dit implementatiemodel zorgt er bij het gebruik van beschikbaarheidszones voor dat knooppunten in een bepaalde beschikbaarheidszone fysiek worden gescheiden van de knooppunten die zijn gedefinieerd in een andere beschikbaarheidszone. AKS-clusters die zijn geïmplementeerd met meerdere beschikbaarheidszones die zijn geconfigureerd in een cluster, bieden een hoger beschikbaarheidsniveau ter bescherming tegen hardwarestoringen of gepland onderhoud.

Door knooppuntgroepen in een cluster te definiëren voor meerdere zones, kunnen knooppunten in een bepaalde knooppuntgroep blijven werken, zelfs als één zone is uitgegaan. Uw toepassingen kunnen beschikbaar blijven, zelfs als er een fysieke fout in één datacenter is als deze is geconfigureerd om fouten van een subset van knooppunten te tolereren.

In dit artikel wordt beschreven hoe u een AKS-cluster maakt en de knooppuntonderdelen over beschikbaarheidszones distribueert.

## <a name="before-you-begin"></a>Voordat u begint

Azure CLI versie 2.0.76 of hoger moet zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="limitations-and-region-availability"></a>Beperkingen en beschikbaarheid in regio's

AKS-clusters kunnen momenteel worden gemaakt met behulp van beschikbaarheidszones in de volgende regio's:

* Australië - oost
* Brazilië - zuid
* Canada - midden
* VS - centraal
* VS - oost 
* VS - oost 2
* Frankrijk - centraal
* Duitsland - west-centraal
* Japan - oost
* Europa - noord
* Azië - zuidoost
* VS - zuid-centraal
* Verenigd Koninkrijk Zuid
* VS (overheid) - Virginia
* Europa -west
* VS - west 2

De volgende beperkingen gelden wanneer u een AKS-cluster maakt met behulp van beschikbaarheidszones:

* U kunt beschikbaarheidszones alleen definiëren wanneer de cluster- of knooppuntgroep wordt gemaakt.
* Instellingen voor beschikbaarheidszone kunnen niet worden bijgewerkt nadat het cluster is gemaakt. U kunt ook geen bestaand, niet-beschikbaarheidszonecluster bijwerken om beschikbaarheidszones te gebruiken.
* De geselecteerde knooppuntgrootte (VM-SKU) moet beschikbaar zijn in alle geselecteerde beschikbaarheidszones.
* Clusters waarvoor beschikbaarheidszones zijn ingeschakeld, vereisen het gebruik van Azure Standard Load Balancers voor distributie over meerdere zones. Dit load balancer kan alleen worden gedefinieerd tijdens het maken van het cluster. Zie Azure load balancer standard SKU limitations (Standaard-SKU-beperkingen voor Azure load balancer) voor meer informatie en de beperkingen van load balancer [standaard-SKU's.][standard-lb-limitations]

### <a name="azure-disks-limitations"></a>Beperkingen voor Azure-schijven

Volumes die gebruikmaken van beheerde Azure-schijven zijn momenteel geen zone-redundante resources. Volumes kunnen niet worden gekoppeld tussen zones en moeten zich in dezelfde zone bevinden als een bepaald knooppunt dat als host voor de doelpod wordt gebruikt.

Kubernetes is op de hoogte van Azure-beschikbaarheidszones sinds versie 1.12. U kunt een PermanentVolumeClaim-object implementeren dat verwijst naar een Azure Managed Disk in een AKS-cluster met meerdere zones. [Kubernetes](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones) zorgt voor het plannen van pods die deze PERSISTENT claimen in de juiste beschikbaarheidszone.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Overzicht van beschikbaarheidszones voor AKS-clusters

Beschikbaarheidszones zijn een aanbieding voor hoge beschikbaarheid die uw toepassingen en gegevens beschermt tegen storingen in datacenters. Zones zijn unieke fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Om tolerantie te garanderen, is er altijd meer dan één zone in alle zone-ingeschakelde regio's. De fysieke scheiding tussen beschikbaarheidszones binnen een Azure-regio beschermt toepassingen en gegevens tegen storingen van het datacenter.

Zie Wat zijn beschikbaarheidszones [in Azure? voor meer informatie.][az-overview]

AKS-clusters die zijn geïmplementeerd met behulp van beschikbaarheidszones kunnen knooppunten verdelen over meerdere zones binnen één regio. Een cluster in de regio VS - oost  *2 kan* bijvoorbeeld knooppunten maken in alle   drie beschikbaarheidszones in VS - oost *2.* Deze distributie van AKS-clusterresources verbetert de beschikbaarheid van clusters, omdat ze bestand zijn tegen fouten in een specifieke zone.

![AKS-knooppuntdistributie over beschikbaarheidszones](media/availability-zones/aks-availability-zones.png)

Als één zone niet meer beschikbaar is, blijven uw toepassingen worden uitgevoerd als het cluster is verdeeld over meerdere zones.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Een AKS-cluster maken in meerdere beschikbaarheidszones

Wanneer u een cluster maakt met behulp van [de opdracht az aks create,][az-aks-create] definieert de parameter in welke `--zones` agentknooppunten zones worden geïmplementeerd. De onderdelen van het besturingsvlak, zoals etcd of de API, worden verdeeld over de beschikbare zones in de regio als u de parameter definieert tijdens `--zones` het maken van het cluster. De specifieke zones waarover de onderdelen van het besturingsvlak zijn verdeeld, zijn onafhankelijk van welke expliciete zones zijn geselecteerd voor de initiële knooppuntgroep.

Als u geen zones voor de standaardagentpool definieert wanneer u een AKS-cluster maakt, worden onderdelen van het besturingsvlak niet gegarandeerd verspreid over beschikbaarheidszones. U kunt extra knooppuntgroepen toevoegen met behulp van de [opdracht az aks nodepool add][az-aks-nodepool-add] en opgeven voor nieuwe knooppunten, maar dit verandert niet hoe het besturingsvlak is verdeeld over `--zones` zones. Instellingen voor beschikbaarheidszone kunnen alleen worden gedefinieerd tijdens het maken van een cluster of knooppuntgroep.

In het volgende voorbeeld wordt een AKS-cluster met de *naam myAKSCluster* gemaakt in de resourcegroep met de *naam myResourceGroup.* Er worden *in totaal 3* knooppunten gemaakt: één agent in zone *1,* één in *2* en vervolgens één in *3*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Het duurt een paar minuten om het AKS-cluster te maken.

Bij het bepalen van de zone waar een nieuw knooppunt deel van moet uitmaken, gebruikt een bepaalde AKS-knooppuntgroep een [best effort zone balancing][vmss-zone-balancing]die wordt aangeboden door onderliggende Azure Virtual Machine Scale Sets . Een bepaalde AKS-knooppuntgroep wordt beschouwd als 'evenwichtig' als elke zone hetzelfde aantal VM's of + 1 VM heeft in alle andere zones voor \- de schaalset.

## <a name="verify-node-distribution-across-zones"></a>Knooppuntdistributie tussen zones controleren

Wanneer het cluster klaar is, vermeldt u de agentknooppunten in de schaalset om te zien in welke beschikbaarheidszone ze zijn geïmplementeerd.

Haal eerst de AKS-clusterreferenties op met de [opdracht az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Gebruik vervolgens de [opdracht kubectl describe][kubectl-describe] om de knooppunten in het cluster weer te geven en te filteren op *de failure-domain.beta.kubernetes.io/zone* waarde. Het volgende voorbeeld is voor een Bash-shell.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

In de volgende voorbeelduitvoer ziet u de drie knooppunten die zijn verdeeld over de opgegeven regio en beschikbaarheidszones, zoals *eastus2-1* voor de eerste beschikbaarheidszone en *eastus2-2* voor de tweede beschikbaarheidszone:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Wanneer u extra knooppunten aan een agentpool toevoegt, verdeelt het Azure-platform automatisch de onderliggende VM's over de opgegeven beschikbaarheidszones.

Houd er rekening mee dat in nieuwere Kubernetes-versies (1.17.0 en hoger) AKS het nieuwere label gebruikt naast `topology.kubernetes.io/zone` het afgeschafte `failure-domain.beta.kubernetes.io/zone` . U kunt hetzelfde resultaat krijgen als hierboven met door het volgende script uit te voeren:

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

Dit geeft u een beknopte uitvoer:

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>Poddistributie tussen zones controleren

Zoals beschreven in Bekende labels, Aantekeningen en [Taints,][kubectl-well_known_labels]gebruikt Kubernetes het label om pods automatisch te distribueren in een replicatiecontroller of -service over de verschillende `failure-domain.beta.kubernetes.io/zone` beschikbare zones. Als u dit wilt testen, kunt u uw cluster omhoog schalen van 3 naar 5 knooppunten om te controleren of de pod correct is verspreid:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Wanneer de schaalbewerking na enkele minuten is voltooid, moet de opdracht in een Bash-shell een uitvoer geven die vergelijkbaar is `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` met dit voorbeeld:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

We hebben nu twee extra knooppunten in zones 1 en 2. U kunt een toepassing implementeren die bestaat uit drie replica's. We gebruiken NGINX als voorbeeld:

```console
kubectl create deployment nginx --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
kubectl scale deployment nginx --replicas=3
```

Door knooppunten te bekijken waarin uw pods worden uitgevoerd, ziet u dat pods worden uitgevoerd op de knooppunten die overeenkomen met drie verschillende beschikbaarheidszones. Met de opdracht in een Bash-shell krijgt u bijvoorbeeld een uitvoer die vergelijkbaar `kubectl describe pod | grep -e "^Name:" -e "^Node:"` is met deze:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Zoals u in de vorige uitvoer kunt zien, wordt de eerste pod uitgevoerd op knooppunt 0, dat zich in de beschikbaarheidszone `eastus2-1` bevindt. De tweede pod wordt uitgevoerd op knooppunt 2, wat overeenkomt met , en de `eastus2-3` derde in knooppunt 4, in `eastus2-2` . Zonder extra configuratie verspreidt Kubernetes de pods correct over alle drie de beschikbaarheidszones.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u een AKS-cluster maakt dat gebruikmaakt van beschikbaarheidszones. Zie Best practices for business [continuity and disaster recovery in AKS (Aanbevolen procedures][best-practices-bc-dr]voor bedrijfscontinuïteit en herstel na noodherstel in AKS) voor meer overwegingen over clusters met hoge beschikbare gegevens.

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/labels-annotations-taints/
