---
title: AKS-Azure Kubernetes Service (AKS)-knooppunt bijwerken
description: Meer informatie over het upgraden van de afbeeldingen op AKS-clusterknooppunten en knooppuntgroepen.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 4f6ac01c1d4df288c823142abbc93e981048d8db
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767525"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Azure Kubernetes Service -knooppunt (AKS) upgraden

AKS ondersteunt het upgraden van de afbeeldingen op een knooppunt, zodat u op de hoogte bent van de nieuwste updates voor het besturingssysteem en runtime. AKS biedt één nieuwe afbeelding per week met de nieuwste updates, dus het is handig om de afbeeldingen van uw knooppunt regelmatig bij te werken voor de nieuwste functies, waaronder Linux- of Windows-patches. In dit artikel wordt beschreven hoe u AKS-clusterknooppuntafbeeldingen bij kunt werken en hoe u afbeeldingen van knooppuntpools kunt bijwerken zonder de versie van Kubernetes bij te werken.

Zie de opmerkingen bij de [AKS-release](https://github.com/Azure/AKS/releases)voor meer informatie over de meest recente afbeeldingen van AKS.

Zie Een [AKS-cluster][upgrade-cluster]upgraden voor meer informatie over het upgraden van de Kubernetes-versie voor uw cluster.

> [!NOTE]
> Het AKS-cluster moet virtuele-machineschaalsets gebruiken voor de knooppunten.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Controleer of uw knooppuntgroep zich op de meest recente knooppuntafbeelding

Met de volgende opdracht kunt u zien wat de meest recente versie van de knooppuntafbeelding is die beschikbaar is voor uw knooppuntgroep: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

In de uitvoer ziet u de like `latestNodeImageVersion` in het onderstaande voorbeeld:

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

Voor de `nodepool1` meest recente knooppuntafbeelding is dus `AKSUbuntu-1604-2020.10.28` beschikbaar. U kunt deze nu vergelijken met de huidige versie van de knooppuntafbeelding die wordt gebruikt door uw knooppuntgroep door het volgende uit te doen:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Een voorbeeld van uitvoer is:

```output
"AKSUbuntu-1604-2020.10.08"
```

In dit voorbeeld kunt u dus upgraden van de huidige versie `AKSUbuntu-1604-2020.10.08` van de afbeelding naar de nieuwste versie `AKSUbuntu-1604-2020.10.28` . 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Alle knooppunten in alle knooppuntgroepen upgraden

Het upgraden van de knooppuntafbeelding wordt uitgevoerd met `az aks upgrade` . Gebruik de volgende opdracht om de knooppuntafbeelding bij te upgraden:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Controleer tijdens de upgrade de status van de knooppuntafbeeldingen met de volgende opdracht om de labels op te halen en de huidige afbeeldingsgegevens van het `kubectl` knooppunt uit te filteren:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Wanneer de upgrade is voltooid, gebruikt u om `az aks show` de bijgewerkte details van de knooppuntgroep op te halen. De huidige knooppuntafbeelding wordt weergegeven in de `nodeImageVersion` eigenschap .

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Een specifieke knooppuntgroep upgraden

Het upgraden van de afbeelding in een knooppuntgroep is vergelijkbaar met het upgraden van de afbeelding in een cluster.

Als u de afbeelding van het besturingssysteem van de knooppuntgroep wilt bijwerken zonder een Kubernetes-clusterupgrade uit te voeren, gebruikt u de optie `--node-image-only` in het volgende voorbeeld:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Controleer tijdens de upgrade de status van de knooppuntafbeeldingen met de volgende opdracht om de labels op te halen en de huidige afbeeldingsgegevens van het `kubectl` knooppunt uit te filteren:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Wanneer de upgrade is voltooid, gebruikt u om `az aks nodepool show` de bijgewerkte details van de knooppuntgroep op te halen. De huidige knooppuntafbeelding wordt weergegeven in de `nodeImageVersion` eigenschap .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Knooppuntafbeeldingen upgraden met pieken in knooppunt

Als u het upgradeproces van de knooppuntafbeelding wilt versnellen, kunt u uw knooppuntafbeeldingen upgraden met behulp van een aanpasbare piekwaarde voor het knooppunt. AKS gebruikt standaard één extra knooppunt om upgrades te configureren.

Als u de snelheid van upgrades wilt verhogen, gebruikt u de waarde om het aantal knooppunten te configureren dat moet worden gebruikt voor upgrades, zodat `--max-surge` ze sneller worden voltooid. Zie Upgrade van knooppuntpieken aanpassen voor meer informatie over de afwegingen van `--max-surge` [verschillende instellingen.][max-surge]

Met de volgende opdracht stelt u de maximale piekwaarde in voor het uitvoeren van een upgrade van een knooppuntafbeelding:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Controleer tijdens de upgrade de status van de knooppuntafbeeldingen met de volgende opdracht om de labels op te halen en de huidige afbeeldingsgegevens van het `kubectl` knooppunt uit te filteren:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Gebruik `az aks nodepool show` om de bijgewerkte details van de knooppuntgroep op te halen. De huidige knooppuntafbeelding wordt weergegeven in de `nodeImageVersion` eigenschap .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Volgende stappen

- Zie de [opmerkingen bij de AKS-release](https://github.com/Azure/AKS/releases) voor informatie over de meest recente knooppuntafbeeldingen.
- Meer informatie over het upgraden van de Kubernetes-versie met [Een AKS-cluster upgraden.][upgrade-cluster]
- [Automatisch cluster- en knooppuntgroepupgrades toepassen met GitHub Actions][github-schedule]
- Meer informatie over meerdere knooppuntgroepen en het upgraden van knooppuntgroepen met [Meerdere knooppuntgroepen maken en beheren.][use-multiple-node-pools]

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
