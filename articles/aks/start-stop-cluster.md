---
title: Een Azure Kubernetes-service starten en stoppen (AKS)
description: Meer informatie over het stoppen of starten van een Azure Kubernetes service-cluster (AKS).
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 026da4237fe14726766b265e55930346293c71df
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662894"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster-preview"></a>Een Azure Kubernetes service (AKS)-cluster stoppen en starten (preview)

Uw AKS-workloads hoeven mogelijk niet continu te worden uitgevoerd, bijvoorbeeld een ontwikkelings cluster dat alleen tijdens kantoor uren wordt gebruikt. Dit leidt ertoe dat uw Azure Kubernetes service (AKS)-cluster niet actief is en dat niet meer dan de systeem onderdelen uitvoert. U kunt de cluster footprint verminderen door [alle `User` knooppunt groepen te schalen naar 0](scale-cluster.md#scale-user-node-pools-to-0), maar uw [ `System` pool](use-system-pools.md) is nog steeds vereist voor het uitvoeren van de systeem onderdelen terwijl het cluster actief is. Als u uw kosten verder in deze peri Oden wilt optimaliseren, kunt u het cluster volledig uitschakelen (stoppen). Met deze actie worden uw besturings vlak en agent knooppunten helemaal gestopt, zodat u op alle berekenings kosten kunt besparen, terwijl u alle objecten en de cluster status opslaat voor wanneer u het opnieuw start. U kunt vervolgens direct naar rechts gaan waar u na het weekend bent of dat uw cluster alleen actief is terwijl u uw batch-taken uitvoert.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].


### <a name="limitations"></a>Beperkingen

Bij het gebruik van de functie voor het starten/stoppen van het cluster gelden de volgende beperkingen:

- Deze functie wordt alleen ondersteund voor Virtual Machine Scale Sets-back-upclusters.
- De cluster status van een gestopt AKS-cluster wordt Maxi maal 12 maanden bewaard. Als uw cluster langer dan 12 maanden wordt gestopt, kan de status van het cluster niet worden hersteld. Zie het [AKS-ondersteunings beleid](support-policies.md)voor meer informatie.
- Tijdens de preview-periode moet u de cluster-auto Scaler (CA) stoppen voordat u probeert het cluster te stoppen.
- U kunt een gestopt AKS-cluster alleen starten of verwijderen. Als u een bewerking wilt uitvoeren zoals schalen of upgraden, start u eerst uw cluster.

### <a name="install-the-aks-preview-azure-cli"></a>De `aks-preview` Azure cli installeren 

U hebt ook de *AKS-preview* Azure cli-extensie versie 0.4.64 of hoger nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] . Of installeer alle beschik bare updates met behulp van de opdracht [AZ extension update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="register-the-startstoppreview-preview-feature"></a>De `StartStopPreview` Preview-functie registreren

Als u de functie cluster starten/stoppen wilt gebruiken, moet u de `StartStopPreview` functie vlag inschakelen voor uw abonnement.

Registreer de `StartStopPreview` functie vlag met behulp van de opdracht [AZ feature REGI ster][az-feature-register] , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "StartStopPreview"
```

Het duurt enkele minuten voordat de status is *geregistreerd*. Controleer de registratie status met behulp van de opdracht [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/StartStopPreview')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

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
[install-azure-cli]: /cli/azure/install-azure-cli&preserve-view=true
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az_aks_show
