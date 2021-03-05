---
title: Proximity-plaatsings groepen gebruiken om de latentie voor Azure Kubernetes service (AKS)-clusters te verminderen
description: Meer informatie over het gebruik van proximity placement groups om de latentie van uw AKS-cluster workloads te verminderen.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: c0c1bf83bf5a816debe61cab0ceab856bfbd062f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181173"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Minder latentie met proximity-plaatsings groepen

> [!Note]
> Wanneer u proximity-plaatsings groepen gebruikt op AKS, is de co-locatie alleen van toepassing op de agent knooppunten. Knoop punt naar knoop punt en de bijbehorende gehoste Pod naar pod latentie is verbeterd. De co-locatie heeft geen invloed op de plaatsing van het besturings vlak van een cluster.

Wanneer u uw toepassing in azure implementeert, wordt er netwerk latentie gemaakt door het verspreiden van virtuele machine-exemplaren (VM) in regio's of beschikbaarheids zones. Dit kan van invloed zijn op de algehele prestaties van uw toepassing. Een proximity-plaatsings groep is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure Compute-resources zich fysiek dicht bij elkaar bevinden. Sommige toepassingen, zoals gaming, technische simulaties en hoge frequentie handel (HFT) vereisen een lage latentie en taken die snel kunnen worden voltooid. Voor scenario's met High Performance Computing (HPC) zoals deze kunt u overwegen om [proximity placement groups](../virtual-machines/co-location.md#proximity-placement-groups) (PPG) te gebruiken voor de knooppunt groepen van uw cluster.

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel moet u de Azure CLI-versie 2,14 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="limitations"></a>Beperkingen

* Een proximity-plaatsings groep kan worden toegewezen aan Maxi maal één beschikbaarheids zone.
* Een knooppunt groep moet Virtual Machine Scale Sets gebruiken om een proximity-plaatsings groep te koppelen.
* Een knooppunt groep kan alleen een proximity-plaatsings groep koppelen aan de groep van de nodegroep.

## <a name="node-pools-and-proximity-placement-groups"></a>Knooppunt groepen en proximity-plaatsings groepen

De eerste resource die u implementeert met een proximity-plaatsings groep, wordt gekoppeld aan een specifiek Data Center. Aanvullende resources die zijn geïmplementeerd met dezelfde proximity-plaatsings groep, worden in hetzelfde Data Center geplaatst. Wanneer alle resources die gebruikmaken van de Proximity-plaatsings groep zijn gestopt (toewijzing opgeheven) of verwijderd, is deze niet meer gekoppeld.

* Veel knooppunt groepen kunnen worden gekoppeld aan één proximity-plaatsings groep.
* Een knooppunt groep mag alleen worden gekoppeld aan één proximity-plaatsings groep.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Proximity-plaatsings groepen configureren met beschikbaarheids zones

> [!NOTE]
> Voor proximity-plaatsings groepen moet een knooppunt groep Maxi maal één beschikbaarheids zone gebruiken. de [basis-sla voor Azure VM van 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) is nog steeds van toepassing op vm's in één zone.

Proximity-plaatsings groepen zijn een concept van een knooppunt groep en zijn gekoppeld aan elke afzonderlijke knooppunt groep. Het gebruik van een PPG-resource heeft geen invloed op de beschik baarheid van AKS-besturings vlak. Dit kan van invloed zijn op hoe een cluster met zones moet worden ontworpen. Het volgende ontwerp wordt aanbevolen om ervoor te zorgen dat een cluster over meerdere zones wordt verdeeld.

* Richt een cluster in met de eerste systeem groep met behulp van drie zones en er is geen proximity-plaatsings groep gekoppeld. Dit zorgt ervoor dat het systeem Peule grond in een specifieke knooppunt groep wordt gespreid over meerdere zones.
* Voeg extra gebruikers knooppunt Pools toe met een unieke zone en proximity-plaatsings groep die is gekoppeld aan elke pool. Een voor beeld is nodepool1 in zone 1 en PPG1, nodepool2 in zone 2 en PPG2, nodepool3 in zone 3 met PPG3. Dit zorgt voor een cluster niveau, knoop punten worden verdeeld over meerdere zones en elke afzonderlijke knooppunt groep wordt in de aangewezen zone geplaatst met een toegewezen PPG-resource.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Een nieuw AKS-cluster maken met een proximity-plaatsings groep

In het volgende voor beeld wordt de opdracht [AZ Group Create][az-group-create] gebruikt voor het maken van een resource groep met de naam *myResourceGroup* in de regio *middenus* . Er wordt een AKS-cluster met de naam *myAKSCluster* gemaakt met behulp van de opdracht [AZ AKS Create][az-aks-create] .

Versneld netwerken verbetert de netwerk prestaties van virtuele machines aanzienlijk. In het ideale geval moet u proximity-plaatsings groepen gebruiken in combi natie met versneld netwerken. AKS maakt standaard gebruik van versnelde netwerken op [ondersteunde instanties van virtuele machines](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), waaronder de meeste virtuele machines van Azure met twee of meer vcpu's.

Maak een nieuw AKS-cluster met een proximity-plaatsings groep die is gekoppeld aan de eerste groep van het systeem knooppunt:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Voer de volgende opdracht uit en sla de geretourneerde ID op:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

De opdracht produceert uitvoer, die de *id-* waarde bevat die u nodig hebt voor aanstaande cli-opdrachten:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Gebruik de resource-ID voor de Proximity-plaatsings groep voor de waarde *myPPGResourceID* in de onderstaande opdracht:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Een proximity-plaatsings groep toevoegen aan een bestaand cluster

U kunt een proximity-plaatsings groep toevoegen aan een bestaand cluster door een nieuwe knooppunt groep te maken. Vervolgens kunt u eventueel bestaande werk belastingen naar de nieuwe knooppunt groep migreren en vervolgens de oorspronkelijke knooppunt groep verwijderen.

Gebruik dezelfde plaatsings groep die u eerder hebt gemaakt. Dit zorgt ervoor dat agent knooppunten in beide knooppunt groepen in uw AKS-cluster fysiek zich in hetzelfde Data Center bevinden.

Gebruik de resource-ID uit de plaatsings groep die u eerder hebt gemaakt en voeg een nieuwe knooppunt groep toe met de [`az aks nodepool add`][az-aks-nodepool-add] opdracht:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Opschonen

Als u het cluster wilt verwijderen, gebruikt u de [`az group delete`][az-group-delete] opdracht om de resource groep AKS te verwijderen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [proximity placement groups][proximity-placement-groups].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete