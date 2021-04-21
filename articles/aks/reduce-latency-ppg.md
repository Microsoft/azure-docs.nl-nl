---
title: Nabijheidsplaatsingsgroepen gebruiken om de latentie voor Azure Kubernetes Service AKS-clusters te verminderen
description: Leer hoe u nabijheidsplaatsingsgroepen kunt gebruiken om de latentie voor uw AKS-clusterworkloads te verminderen.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: fbcff37185b2cba71c405e917653d52397479007
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779593"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Latentie verminderen met nabijheidsplaatsingsgroepen

> [!Note]
> Wanneer u nabijheidsplaatsingsgroepen in AKS gebruikt, is co-locatie alleen van toepassing op de agentknooppunten. Knooppunt naar knooppunt en de bijbehorende gehoste pod naar podlatentie is verbeterd. De co-locatie heeft geen invloed op de plaatsing van het besturingsvlak van een cluster.

Bij het implementeren van uw toepassing in Azure zorgt het spreiden van VM-exemplaren (virtuele machines) over regio's of beschikbaarheidszones voor netwerklatentie, wat van invloed kan zijn op de algehele prestaties van uw toepassing. Een nabijheidsplaatsingsgroep is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure-rekenbronnen zich fysiek dicht bij elkaar bevinden. Sommige toepassingen, zoals gaming, technische simulaties en high-frequency trading (HFT), vereisen lage latentie en taken die snel worden uitgevoerd. Voor HPC-scenario's (High Performance Computing), [](../virtual-machines/co-location.md#proximity-placement-groups) zoals deze, kunt u overwegen om nabijheidsplaatsingsgroepen (PPG) te gebruiken voor de knooppuntgroepen van uw cluster.

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel moet u Azure CLI versie 2.14 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="limitations"></a>Beperkingen

* Een nabijheidsplaatsingsgroep kan worden toebesteed aan ten beste één beschikbaarheidszone.
* Een knooppuntgroep moet een Virtual Machine Scale Sets om een nabijheidsplaatsingsgroep te koppelen.
* Een knooppuntgroep kan alleen een nabijheidsplaatsingsgroep koppelen aan de maaktijd van een knooppuntgroep.

## <a name="node-pools-and-proximity-placement-groups"></a>Knooppuntgroepen en nabijheidsplaatsingsgroepen

De eerste resource die u implementeert met een nabijheidsplaatsingsgroep, is gekoppeld aan een specifiek datacenter. Aanvullende resources die zijn geïmplementeerd met dezelfde nabijheidsplaatsingsgroep, worden op dezelfde locatie in hetzelfde datacenter geplaatst. Zodra alle resources die de nabijheidsplaatsingsgroep gebruiken, zijn gestopt (toewijzing ervan is verwijderd) of verwijderd, is deze niet meer gekoppeld.

* Veel knooppuntgroepen kunnen worden gekoppeld aan één nabijheidsplaatsingsgroep.
* Een knooppuntgroep kan alleen worden gekoppeld aan één nabijheidsplaatsingsgroep.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Nabijheidsplaatsingsgroepen configureren met beschikbaarheidszones

> [!NOTE]
> Hoewel voor nabijheidsplaatsingsgroepen een knooppuntgroep is vereist voor het gebruik van ten meeste één beschikbaarheidszone, is de Azure VM SLA voor de basislijn [van 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) nog steeds van kracht voor VM's in één zone.

Nabijheidsplaatsingsgroepen zijn een concept van een knooppuntgroep en zijn gekoppeld aan elke afzonderlijke knooppuntgroep. Het gebruik van een PPG-resource heeft geen invloed op de beschikbaarheid van het AKS-besturingsvlak. Dit kan van invloed zijn op de manier waarop een cluster moet worden ontworpen met zones. Om ervoor te zorgen dat een cluster wordt verdeeld over meerdere zones, wordt het volgende ontwerp aanbevolen.

* Een cluster inrichten met de eerste systeemgroep met behulp van 3 zones en er is geen nabijheidsplaatsingsgroep gekoppeld. Dit zorgt ervoor dat de systeempods in een toegewezen knooppuntgroep landen die over meerdere zones wordt verdeeld.
* Voeg extra gebruikers-knooppuntgroepen toe met een unieke zone en nabijheidsplaatsingsgroep die aan elke groep zijn gekoppeld. Een voorbeeld is nodepool1 in zone 1 en PPG1, nodepool2 in zone 2 en PPG2, nodepool3 in zone 3 met PPG3. Dit zorgt ervoor dat knooppunten op clusterniveau worden verdeeld over meerdere zones en dat elke afzonderlijke knooppuntgroep zich in de aangewezen zone bevindt met een toegewezen PPG-resource.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Een nieuw AKS-cluster maken met een nabijheidsplaatsingsgroep

In het volgende voorbeeld wordt de [opdracht az group create][az-group-create] gebruikt om een resourcegroep met de naam *myResourceGroup* te maken in *de regio centralus.* Vervolgens wordt een AKS-cluster met de naam *myAKSCluster* gemaakt met behulp van [de opdracht az aks create.][az-aks-create]

Versneld netwerken verbetert de netwerkprestaties van virtuele machines sterk. In het ideale moment kunt u nabijheidsplaatsingsgroepen gebruiken in combinatie met versneld netwerken. AKS maakt standaard gebruik van versneld netwerken op ondersteunde [virtuele-machine-exemplaren,](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)waaronder de meeste virtuele Azure-machines met twee of meer vCCPUs.

Maak een nieuw AKS-cluster met een nabijheidsplaatsingsgroep die is gekoppeld aan de eerste systeemknooppuntgroep:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Voer de volgende opdracht uit en sla de geretourneerde id op:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

De opdracht produceert uitvoer, die de *id-waarde bevat* die u nodig hebt voor toekomstige CLI-opdrachten:

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

Gebruik de resource-id van de nabijheidsplaatsingsgroep voor *de waarde myPPGResourceID* in de onderstaande opdracht:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Een nabijheidsplaatsingsgroep toevoegen aan een bestaand cluster

U kunt een nabijheidsplaatsingsgroep toevoegen aan een bestaand cluster door een nieuwe knooppuntgroep te maken. Vervolgens kunt u eventueel bestaande workloads migreren naar de nieuwe knooppuntgroep en vervolgens de oorspronkelijke knooppuntgroep verwijderen.

Gebruik dezelfde nabijheidsplaatsingsgroep die u eerder hebt gemaakt. Dit zorgt ervoor dat agentknooppunten in beide knooppuntgroepen in uw AKS-cluster zich fysiek in hetzelfde datacenter bevinden.

Gebruik de resource-id van de nabijheidsplaatsingsgroep die u eerder hebt gemaakt en voeg een nieuwe knooppuntgroep toe met de [`az aks nodepool add`][az-aks-nodepool-add] opdracht :

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

Als u het cluster wilt verwijderen, gebruikt u [`az group delete`][az-group-delete] de opdracht om de AKS-resourcegroep te verwijderen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [nabijheidsplaatsingsgroepen.][proximity-placement-groups]

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az_aks_create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete