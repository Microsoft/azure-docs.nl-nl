---
title: Beschikbaarheids zones gebruiken in azure Kubernetes service (AKS)
description: Meer informatie over het maken van een cluster dat knoop punten distribueert over beschikbaarheids zones in azure Kubernetes service (AKS)
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 09/04/2020
ms.openlocfilehash: 15f66e836a2900349007fb5068a172b89f39d4de
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352793"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Een AKS-cluster (Azure Kubernetes service) maken dat gebruikmaakt van beschikbaarheids zones

Een Azure Kubernetes service (AKS)-cluster distribueert bronnen als knoop punten en opslag over logische secties van de onderliggende Azure-infra structuur. Dit implementatie model wanneer u beschikbaarheids zones gebruikt, zorgt ervoor dat knoop punten in een bepaalde beschikbaarheids zone fysiek worden gescheiden van de waarden die zijn gedefinieerd in een andere beschikbaarheids zone. AKS-clusters die zijn geïmplementeerd met meerdere beschikbaarheids zones die in een cluster zijn geconfigureerd, bieden een hoger niveau van Beschik baarheid om te beschermen tegen een hardwarestoring of een geplande onderhouds gebeurtenis.

Door knooppunt groepen in een cluster te definiëren om meerdere zones te omvatten, kunnen knoop punten in een bepaalde knooppunt groep blijven werken, zelfs als een enkele zone niet actief is. Uw toepassingen kunnen nog steeds beschikbaar blijven, zelfs als er sprake is van een fysieke storing in één Data Center als er een fout optreedt in een subset van knoop punten.

In dit artikel wordt beschreven hoe u een AKS-cluster maakt en hoe u de knooppunt onderdelen over beschikbaarheids zones distribueert.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.76 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="limitations-and-region-availability"></a>Beperkingen en beschik baarheid van regio's

AKS-clusters kunnen momenteel worden gemaakt met beschikbaarheids zones in de volgende regio's:

* Australië - oost
* Canada - midden
* VS - centraal
* VS - oost 
* VS - oost 2
* Frankrijk - centraal
* Japan East
* Europa - noord
* Azië - zuidoost
* Verenigd Koninkrijk Zuid
* Europa -west
* West US 2

De volgende beperkingen zijn van toepassing wanneer u een AKS-cluster maakt met beschikbaarheids zones:

* U kunt alleen beschikbaarheids zones definiëren wanneer het cluster of de knooppunt groep wordt gemaakt.
* De instellingen van de beschikbaarheids zone kunnen niet worden bijgewerkt nadat het cluster is gemaakt. U kunt ook een bestaand niet-beschikbaarheids zone cluster bijwerken om beschikbaarheids zones te gebruiken.
* De geselecteerde knooppunt grootte (VM-SKU) moet beschikbaar zijn in alle geselecteerde beschikbaarheids zones.
* Voor clusters waarvoor beschikbaarheids zones zijn ingeschakeld, moet Azure Standard load balancers worden gebruikt voor distributie in meerdere zones. Dit type load balancer kan alleen worden gedefinieerd tijdens het maken van het cluster. Zie [Azure Load Balancer Standard SKU-beperkingen][standard-lb-limitations]voor meer informatie en de beperkingen van de standaard Load Balancer.

### <a name="azure-disks-limitations"></a>Beperkingen voor Azure-schijven

Volumes die gebruikmaken van Azure Managed disks zijn momenteel geen zone-redundante bronnen. Volumes kunnen niet worden gekoppeld aan zones en moeten zich in dezelfde zone bevinden als een bepaald knoop punt dat als host fungeert voor de doel-pod.

Kubernetes is op de hoogte van Azure-beschikbaarheids zones sinds versie 1,12. U kunt een PersistentVolumeClaim-object dat verwijst naar een Azure Managed disk in een AKS-cluster met meerdere zones, implementeren om pod te [plannen](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones) die dit PVC claimen in de juiste beschikbaarheids zone.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Overzicht van beschikbaarheids zones voor AKS-clusters

Beschikbaarheids zones zijn een aanbieding met hoge Beschik baarheid die uw toepassingen en gegevens beveiligt tegen Data Center-fouten. Zones zijn unieke fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Om tolerantie te garanderen, is er altijd meer dan één zone in alle regio's waarvoor zone is ingeschakeld. De fysieke scheiding tussen beschikbaarheidszones binnen een Azure-regio beschermt toepassingen en gegevens tegen storingen van het datacenter.

Zie [Wat zijn beschikbaarheids zones in azure?][az-overview]voor meer informatie.

AKS-clusters die zijn geïmplementeerd met beschikbaarheids zones, kunnen knoop punten distribueren over meerdere zones binnen één regio. Zo kan een cluster in de regio  *VS-Oost 2*   knoop punten maken in alle drie beschikbaarheids zones in *VS-Oost 2*. Deze distributie van AKS-cluster resources verbetert de beschik baarheid van het cluster, omdat ze robuust zijn in het mislukken van een specifieke zone.

![Distributie van AKS-knoop punten in beschikbaarheids zones](media/availability-zones/aks-availability-zones.png)

Als een enkele zone niet beschikbaar is, blijven uw toepassingen worden uitgevoerd als het cluster over meerdere zones is verspreid.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Een AKS-cluster maken in verschillende beschikbaarheids zones

Wanneer u een cluster maakt met behulp van de opdracht [AZ AKS Create][az-aks-create] , `--zones` definieert de para meter welke zone-agent knooppunten worden geïmplementeerd in. De besturings vlak onderdelen, zoals etcd of de API, worden verdeeld over de beschik bare zones in de regio als u de `--zones` para meter definieert tijdens het maken van het cluster. De specifieke zones waarin de besturings vlak onderdelen zijn verdeeld, zijn onafhankelijk van welke expliciete zones zijn geselecteerd voor de eerste knooppunt groep.

Als u geen zones voor de standaard agent groep definieert wanneer u een AKS-cluster maakt, zijn de Control-onderdelen niet gegarandeerd over de verschillende beschikbaarheids zones. U kunt extra knooppunt Pools toevoegen met behulp van de opdracht [AZ AKS nodepool add][az-aks-nodepool-add] en opgeven `--zones` voor nieuwe knoop punten, maar de manier waarop het besturings vlak over zones is verspreid, wordt niet gewijzigd. Instellingen voor de beschikbaarheids zone kunnen alleen worden gedefinieerd in het cluster of in de groep met knooppunt groepen.

In het volgende voor beeld wordt een AKS-cluster gemaakt met de naam *myAKSCluster* in de resource groep met de naam *myResourceGroup*. Er zijn in totaal *drie* knoop punten gemaakt: één agent in zone *1*, één in *2*, en vervolgens een in *3*.

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

Bij het bepalen van de zone waarvan een nieuw knoop punt deel moet uitmaken, gebruikt een bepaalde AKS-knooppunt groep een [Aanbevolen zone verdeling die wordt geboden door de onderliggende Azure-virtual machine Scale sets][vmss-zone-balancing]. Een bepaalde AKS-knooppunt groep wordt beschouwd als ' evenwichtig ' als elke zone hetzelfde aantal Vm's of + \- 1 virtuele machine heeft in alle andere zones voor de schaalset.

## <a name="verify-node-distribution-across-zones"></a>Knooppunt distributie in zones verifiëren

Wanneer het cluster gereed is, vermeldt u de agent knooppunten in de schaalset om te zien in welke beschikbaarheids zone ze zijn geïmplementeerd.

Haal eerst de AKS-cluster referenties op met de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Gebruik vervolgens de opdracht [kubectl beschrijven][kubectl-describe] om de knoop punten in het cluster weer te geven en te filteren op de waarde *failure-Domain.beta.kubernetes.io/zone* . Het volgende voor beeld is voor een bash-shell.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

In de volgende voorbeeld uitvoer ziet u de drie knoop punten verdeeld over de opgegeven regio en beschikbaarheids zones, zoals *eastus2-1* voor de eerste beschikbaarheids zone en *eastus2-2* voor de tweede beschikbaarheids zone:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Wanneer u extra knoop punten aan een agent groep toevoegt, distribueert het Azure-platform automatisch de onderliggende virtuele machines over de opgegeven beschikbaarheids zones.

Houd er rekening mee dat in nieuwere Kubernetes-versies (1.17.0 en hoger), `topology.kubernetes.io/zone` naast de afgeschaft, het nieuwere label wordt gebruikt `failure-domain.beta.kubernetes.io/zone` . U kunt hetzelfde resultaat bereiken als hierboven met door het volgende script uit te voeren:

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

Hiermee krijgt u een beknoptere uitvoer:

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>Pod-distributie in zones controleren

Zoals beschreven in [bekende labels, annotaties en taints][kubectl-well_known_labels], gebruikt Kubernetes het `failure-domain.beta.kubernetes.io/zone` label om automatisch een Peul te distribueren in een replicatie controller of service in de verschillende zones die beschikbaar zijn. Als u dit wilt testen, kunt u het cluster van 3 tot 5 knoop punten omhoog schalen om de juiste pod-sprei ding te controleren:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Wanneer de schaal bewerking na een paar minuten is voltooid, moet de opdracht `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` in een bash-shell een uitvoer bevatten die vergelijkbaar is met dit voor beeld:

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

We hebben nu twee extra knoop punten in zones 1 en 2. U kunt een toepassing implementeren die bestaat uit drie replica's. NGINX wordt als voor beeld gebruikt:

```console
kubectl create deployment nginx --image=nginx
kubectl scale deployment nginx --replicas=3
```

Door knoop punten weer te geven waarop uw peulen worden uitgevoerd, ziet u dat er peulen worden uitgevoerd op de knoop punten die overeenkomen met drie verschillende beschikbaarheids zones. Met de opdracht `kubectl describe pod | grep -e "^Name:" -e "^Node:"` in een bash-shell zou u bijvoorbeeld een uitvoer zien die er ongeveer als volgt uitziet:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Zoals u kunt zien in de vorige uitvoer, wordt de eerste pod uitgevoerd op het knoop punt 0, dat zich in de beschikbaarheids zone bevindt `eastus2-1` . De tweede pod wordt uitgevoerd op knoop punt 2, dat overeenkomt met en `eastus2-3` de derde in knoop punt 4, in `eastus2-2` . Zonder enige aanvullende configuratie Kubernetes de gehele wereld op de juiste wijze over in alle drie de beschikbaarheids zones.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u een AKS-cluster maakt dat gebruikmaakt van beschikbaarheids zones. Zie [Aanbevolen procedures voor bedrijfs continuïteit en herstel na nood gevallen in AKS][best-practices-bc-dr]voor meer informatie over Maxi maal beschik bare clusters.

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
