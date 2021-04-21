---
title: Een AKS-cluster (Azure Kubernetes Service) schalen
description: Meer informatie over het schalen van het aantal knooppunten in een Azure Kubernetes Service cluster (AKS).
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: 1468f9a0a23935022ed14488dfb65d789828d310
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782883"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Aantal knooppunten in een AKS-cluster (Azure Kubernetes Service) schalen

Als de resourcebehoeften van uw toepassingen veranderen, kunt u een AKS-cluster handmatig schalen om een ander aantal knooppunten uit te voeren. Wanneer u omlaag schaalt, worden knooppunten zorgvuldig afgesloten en [leeggemalen om][kubernetes-drain] onderbreking van toepassingen die worden uitgevoerd te minimaliseren. Wanneer u omhoog schaalt, wacht AKS totdat knooppunten zijn gemarkeerd door het `Ready` Kubernetes-cluster voordat pods op de knooppunten zijn gepland.

## <a name="scale-the-cluster-nodes"></a>De clusterknooppunten schalen

Haal eerst de naam *van uw* knooppuntgroep op met de opdracht [az aks show.][az-aks-show] In het volgende voorbeeld wordt de naam van de knooppuntgroep voor het cluster met de naam *myAKSCluster* in de resourcegroep *myResourceGroup* opgeslagen:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

In de volgende voorbeelduitvoer ziet u *dat de naam* *nodepool1 is:*

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Gebruik de [opdracht az aks scale om][az-aks-scale] de clusterknooppunten te schalen. In het volgende voorbeeld wordt een cluster met de *naam myAKSCluster geschaald* naar één knooppunt. Geef uw eigen `--nodepool-name` op uit de vorige opdracht, zoals *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

In de volgende voorbeelduitvoer ziet u dat het cluster is geschaald naar één knooppunt, zoals wordt weergegeven in de *sectie agentPoolProfiles:*

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```


## <a name="scale-user-node-pools-to-0"></a>`User`Knooppuntgroepen schalen naar 0

In `System` tegenstelling tot knooppuntgroepen waarvoor altijd knooppunten moeten worden uitgevoerd, `User` kunt u met knooppuntgroepen schalen naar 0. Zie Systeem- en gebruikers-knooppuntgroepen voor meer informatie over de verschillen tussen systeem- en [gebruikers-knooppuntgroepen.](use-system-pools.md)

Als u een gebruikersgroep wilt schalen naar 0, kunt u de [schaal az aks nodepool][az-aks-nodepool-scale] gebruiken als alternatief voor de bovenstaande opdracht en 0 instellen als het `az aks scale` aantal knooppunt.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

U kunt knooppuntgroepen ook automatisch schalen naar 0 knooppunten door de parameter van de automatische schaalverdeder voor clusters in te `User` `--min-count` stellen op 0. [](cluster-autoscaler.md)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u handmatig een AKS-cluster geschaald om het aantal knooppunten te vergroten of te verlagen. U kunt ook de automatische [schaalvergroting van clusters gebruiken om][cluster-autoscaler] uw cluster automatisch te schalen.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale