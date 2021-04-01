---
title: Installatie kopieën van het AKS-knoop punt (Azure Kubernetes service) upgraden
description: Meer informatie over het bijwerken van de installatie kopieën op AKS-cluster knooppunten en-knooppunt groepen.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 83d7d48922806334e2b49494fe0ef1d15e1a7a6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96531476"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Upgrade van installatie kopie van knoop punt Azure Kubernetes service (AKS)

AKS biedt ondersteuning voor het upgraden van installatie kopieën op een knoop punt zodat u up-to-date bent met de nieuwste besturings systemen en runtime-updates. AKS biedt een nieuwe installatie kopie per week met de nieuwste updates. het is dus handig om de installatie kopieën van uw knoop punt regel matig bij te werken voor de nieuwste functies, waaronder Linux-of Windows-patches. In dit artikel wordt beschreven hoe u installatie kopieën van AKS-cluster knooppunten bijwerkt en hoe u installatie kopieën van groeps knooppunten bijwerkt zonder de versie van Kubernetes bij te werken.

Zie de [release opmerkingen voor AKS](https://github.com/Azure/AKS/releases)voor meer informatie over de nieuwste installatie kopieën van AKS.

Zie [een AKS-cluster upgraden][upgrade-cluster]voor informatie over het bijwerken van de Kubernetes-versie voor uw cluster.

> [!NOTE]
> Het AKS-cluster moet virtuele-machine schaal sets gebruiken voor de knoop punten.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Controleren of de knooppunt groep zich op de laatste kopie van het knoop punt bevindt

Met de volgende opdracht kunt u zien wat de nieuwste versie van de knooppunt installatie kopie beschikbaar is voor de knooppunt groep: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

In de uitvoer ziet u het `latestNodeImageVersion` als in het voor beeld hieronder:

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

`nodepool1`De meest recente knooppunt installatie kopie is dus beschikbaar `AKSUbuntu-1604-2020.10.28` . U kunt dit nu vergelijken met de huidige versie van de installatie kopie die door de knooppunt groep wordt gebruikt door de volgende handelingen uit te voeren:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Een voor beeld van een uitvoer is:

```output
"AKSUbuntu-1604-2020.10.08"
```

In dit voor beeld kunt u een upgrade uitvoeren van de huidige versie van de `AKSUbuntu-1604-2020.10.08` installatie kopie naar de meest recente versie `AKSUbuntu-1604-2020.10.28` . 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Alle knoop punten in alle knooppunt groepen upgraden

De upgrade van de knooppunt installatie kopie wordt uitgevoerd met `az aks upgrade` . Als u de installatie kopie van het knoop punt wilt bijwerken, gebruikt u de volgende opdracht:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Controleer tijdens de upgrade de status van de installatie kopie van het knoop punt met de volgende `kubectl` opdracht om de labels op te halen en de gegevens van de huidige knooppunt afbeelding te filteren:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Wanneer de upgrade is voltooid, gebruikt `az aks show` u om de bijgewerkte Details van de knooppunt groep op te halen. De huidige knooppunt afbeelding wordt weer gegeven in de `nodeImageVersion` eigenschap.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Een specifieke knooppunt groep upgraden

Het bijwerken van de installatie kopie op een knooppunt groep is vergelijkbaar met het bijwerken van de installatie kopie in een cluster.

Als u de installatie kopie van het besturings systeem van de knooppunt groep wilt bijwerken zonder een Kubernetes-cluster upgrade uit te voeren, gebruikt u de `--node-image-only` optie in het volgende voor beeld:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Controleer tijdens de upgrade de status van de installatie kopie van het knoop punt met de volgende `kubectl` opdracht om de labels op te halen en de gegevens van de huidige knooppunt afbeelding te filteren:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Wanneer de upgrade is voltooid, gebruikt `az aks nodepool show` u om de bijgewerkte Details van de knooppunt groep op te halen. De huidige knooppunt afbeelding wordt weer gegeven in de `nodeImageVersion` eigenschap.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Knooppunt installatie kopieën bijwerken met overspanning van knoop punt

Als u het upgrade proces van de knooppunt installatie kopie wilt versnellen, kunt u uw knooppunt installatie kopieën bijwerken met behulp van een aanpas bare overspannings waarde. AKS gebruikt standaard één extra knoop punt voor het configureren van upgrades.

Als u de snelheid van upgrades wilt verhogen, gebruikt u de `--max-surge` waarde voor het configureren van het aantal knoop punten dat moet worden gebruikt voor upgrades zodat ze sneller kunnen worden uitgevoerd. Zie de upgrade van het `--max-surge` [knoop punt piek aanpassen][max-surge]voor meer informatie over de verschillende instellingen.

Met de volgende opdracht stelt u de maximale piek waarde in voor het uitvoeren van een upgrade van een knooppunt installatie kopie:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Controleer tijdens de upgrade de status van de installatie kopie van het knoop punt met de volgende `kubectl` opdracht om de labels op te halen en de gegevens van de huidige knooppunt afbeelding te filteren:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Gebruiken `az aks nodepool show` om de bijgewerkte Details van de knooppunt groep op te halen. De huidige knooppunt afbeelding wordt weer gegeven in de `nodeImageVersion` eigenschap.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Volgende stappen

- Zie de [opmerkingen](https://github.com/Azure/AKS/releases) bij de release van AKS voor informatie over de nieuwste knooppunt installatie kopieën.
- Meer informatie over het bijwerken van de Kubernetes-versie met [een upgrade voor een AKS-cluster][upgrade-cluster].
- [Automatisch cluster-en knooppunt groeps upgrades Toep assen met GitHub-acties][github-schedule]
- Meer informatie over meerdere knooppunt Pools en het upgraden van knooppunt Pools met het [maken en beheren van meerdere knooppunt groepen][use-multiple-node-pools].

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
