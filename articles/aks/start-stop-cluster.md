---
title: Een Azure Kubernetes-service starten en stoppen (AKS)
description: Meer informatie over het stoppen of starten van een Azure Kubernetes service-cluster (AKS).
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 87d51f9c1d084faf79c7ec1cf1255a6fb3c8245d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201006"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Een AKS-cluster (Azure Kubernetes service) stoppen en starten

Uw AKS-workloads hoeven mogelijk niet continu te worden uitgevoerd, bijvoorbeeld een ontwikkelings cluster dat alleen tijdens kantoor uren wordt gebruikt. Dit leidt ertoe dat uw Azure Kubernetes service (AKS)-cluster niet actief is en dat niet meer dan de systeem onderdelen uitvoert. U kunt de cluster footprint verminderen door [alle `User` knooppunt groepen te schalen naar 0](scale-cluster.md#scale-user-node-pools-to-0), maar uw [ `System` pool](use-system-pools.md) is nog steeds vereist voor het uitvoeren van de systeem onderdelen terwijl het cluster actief is. Als u uw kosten verder in deze peri Oden wilt optimaliseren, kunt u het cluster volledig uitschakelen (stoppen). Met deze actie worden uw besturings vlak en agent knooppunten helemaal gestopt, zodat u op alle berekenings kosten kunt besparen, terwijl u alle objecten en de cluster status opslaat voor wanneer u het opnieuw start. U kunt vervolgens direct naar rechts gaan waar u na het weekend bent of dat uw cluster alleen actief is terwijl u uw batch-taken uitvoert.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

### <a name="limitations"></a>Beperkingen

Bij het gebruik van de functie voor het starten/stoppen van het cluster gelden de volgende beperkingen:

- Deze functie wordt alleen ondersteund voor Virtual Machine Scale Sets-back-upclusters.
- De cluster status van een gestopt AKS-cluster wordt Maxi maal 12 maanden bewaard. Als uw cluster langer dan 12 maanden wordt gestopt, kan de status van het cluster niet worden hersteld. Zie het [AKS-ondersteunings beleid](support-policies.md)voor meer informatie.
- U kunt een gestopt AKS-cluster alleen starten of verwijderen. Als u een bewerking wilt uitvoeren zoals schalen of upgraden, start u eerst uw cluster.

## <a name="stop-an-aks-cluster"></a>Een AKS-cluster stoppen

Met de opdracht kunt u de `az aks stop` knoop punten en het besturings vlak van een actief AKS-cluster stoppen. In het volgende voor beeld wordt een cluster met de naam *myAKSCluster* gestopt:

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

U kunt controleren wanneer het cluster wordt gestopt door de opdracht [AZ AKS show][az-aks-show] te gebruiken en de `powerState` weer gave te bevestigen zoals `Stopped` op de onderstaande uitvoer:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Als de `provisioningState` laat zien `Stopping` , betekent dit dat uw cluster nog niet volledig is gestopt.

> [!IMPORTANT]
> Als u pod- [Verstorings budgetten](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) gebruikt, kan de stop bewerking langer duren omdat het proces voor het uitvoeren van de onderbreking meer tijd in beslag neemt.

## <a name="start-an-aks-cluster"></a>Een AKS-cluster starten

Met de opdracht kunt u de `az aks start` knoop punten en het besturings vlak van een gestopt AKS-cluster starten. Het cluster wordt opnieuw opgestart met de vorige status van het besturings systeem en het aantal agent knooppunten.  
In het volgende voor beeld wordt een cluster met de naam *myAKSCluster* gestart:

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

U kunt controleren wanneer het cluster is gestart met behulp van de opdracht [AZ AKS show][az-aks-show] en de `powerState` weer gave bevestigen `Running` zoals op de onderstaande uitvoer:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Als de `provisioningState` laat zien `Starting` , betekent dit dat uw cluster nog niet volledig is gestart.

## <a name="next-steps"></a>Volgende stappen

- `User`Zie [ `User` groepen schalen tot 0](scale-cluster.md#scale-user-node-pools-to-0)voor meer informatie over het schalen van groepen naar 0.
- Zie [een steun knooppunt groep toevoegen aan AKS](spot-node-pool.md)voor meer informatie over het besparen van kosten met behulp van spot-exemplaren.
- Zie [AKS-ondersteunings beleid](support-policies.md)voor meer informatie over het AKS-ondersteunings beleid.

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-show]: /cli/azure/aks#az_aks_show
