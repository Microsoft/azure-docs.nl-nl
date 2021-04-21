---
title: Een AKS (Azure Kubernetes Service) starten en stoppen
description: Meer informatie over het stoppen of starten van Azure Kubernetes Service cluster (AKS).
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 2d3c946bc2f98b0c06fe33dcaaa77a5399f6d56b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782725"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Een AKS-cluster (Azure Kubernetes Service) stoppen en starten

Uw AKS-workloads hoeven mogelijk niet continu te worden uitgevoerd, bijvoorbeeld een ontwikkelcluster dat alleen tijdens bedrijfsuren wordt gebruikt. Dit leidt tot momenten waarop uw AKS-cluster (Azure Kubernetes Service) mogelijk inactief is en niet meer wordt uitgevoerd dan de systeemonderdelen. U kunt de footprint van het cluster verminderen door alle knooppuntgroepen te schalen naar [ `User` 0,](scale-cluster.md#scale-user-node-pools-to-0)maar uw [ `System` pool](use-system-pools.md) is nog steeds vereist om de systeemonderdelen uit te voeren terwijl het cluster wordt uitgevoerd. Als u uw kosten tijdens deze perioden verder wilt optimaliseren, kunt u uw cluster volledig uitschakelen (stoppen). Met deze actie worden uw besturingsvlak en agentknooppunten helemaal gestopt, zodat u kunt besparen op alle rekenkosten, terwijl u al uw objecten en clustertoestand behoudt die zijn opgeslagen voor wanneer u deze opnieuw start. U kunt vervolgens verder gaan waar u na een weekend bent gebleven of om uw cluster alleen uit te voeren terwijl u uw batchtaken hebt uitgevoerd.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

### <a name="limitations"></a>Beperkingen

Wanneer u de clusterfunctie start/stop gebruikt, gelden de volgende beperkingen:

- Deze functie wordt alleen ondersteund voor Virtual Machine Scale Sets ondersteunde clusters.
- De clustertoestand van een gestopt AKS-cluster blijft maximaal 12 maanden bewaard. Als uw cluster langer dan 12 maanden is gestopt, kan de clustertoestand niet worden hersteld. Zie AKS Support [Policies (AKS-ondersteuningsbeleid) voor meer informatie.](support-policies.md)
- U kunt alleen een gestopt AKS-cluster starten of verwijderen. Als u een bewerking wilt uitvoeren, zoals schalen of upgraden, start u eerst uw cluster.

## <a name="stop-an-aks-cluster"></a>Een AKS-cluster stoppen

U kunt de opdracht gebruiken `az aks stop` om een actief AKS-clusterknooppunten en besturingsvlak te stoppen. In het volgende voorbeeld wordt een cluster met de *naam myAKSCluster gestopt:*

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

U kunt controleren wanneer uw cluster is gestopt met behulp van de [opdracht az aks show][az-aks-show] en de weergegeven zoals in de `powerState` `Stopped` onderstaande uitvoer bevestigen:

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

Als de `provisioningState` laat zien dat uw cluster nog niet volledig is `Stopping` gestopt.

> [!IMPORTANT]
> Als u [podonderbrekingsbudgetten](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) gebruikt, kan de stopbewerking langer duren omdat het opslekken meer tijd in beslag neemt.

## <a name="start-an-aks-cluster"></a>Een AKS-cluster starten

U kunt de opdracht gebruiken om de knooppunten en het besturingsvlak van een `az aks start` gestopt AKS-cluster te starten. Het cluster wordt opnieuw opgestart met de vorige status van het besturingsvlak en het aantal agentknooppunten.  
In het volgende voorbeeld wordt een cluster met de *naam myAKSCluster gestart:*

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

U kunt controleren wanneer uw cluster is gestart met behulp van de [opdracht az aks show][az-aks-show] en de shows als bevestigen in de `powerState` `Running` onderstaande uitvoer:

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

Als de `provisioningState` laat zien dat uw cluster nog niet volledig is `Starting` gestart.

## <a name="next-steps"></a>Volgende stappen

- Zie Pools schalen naar 0 voor meer informatie over het schalen `User` [van pools naar `User` 0.](scale-cluster.md#scale-user-node-pools-to-0)
- Zie Een spot-knooppuntgroep toevoegen aan AKS voor meer informatie over het besparen van kosten [met spot-exemplaren.](spot-node-pool.md)
- Zie AKS-ondersteuningsbeleid voor meer informatie over [het AKS-ondersteuningsbeleid.](support-policies.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-show]: /cli/azure/aks#az_aks_show
