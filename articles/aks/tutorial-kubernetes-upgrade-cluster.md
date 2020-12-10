---
title: 'Zelfstudie voor Kubernetes op Azure: Een cluster bijwerken'
description: In deze zelfstudie Azure Kubernetes Service (AKS) leert u hoe u een bestaand AKS-cluster kunt bijwerken naar de nieuwste Kubernetes-versie.
services: container-service
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 517172b919552a24e9cb12bbaad14eb8cb71b3fd
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007531"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Zelfstudie: Kubernetes bijwerken in AKS (Azure Kubernetes Service)

Gedurende de levenscyclus van de toepassing en het cluster, kunt u desgewenst Kubernetes bijwerken naar de nieuwste versie, en nieuwe functies gebruiken. Een AKS-cluster (Azure Kubernetes Service) kan worden bijgewerkt met de Azure CLI.

In deze zelfstudie, deel zeven, wordt een upgrade uitgevoerd van een Kubernetes-cluster. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Huidige en beschikbare Kubernetes-versies identificeren
> * De Kubernetes-knooppunten upgraden
> * Een geslaagde upgrade valideren

## <a name="before-you-begin"></a>Voordat u begint

In eerdere zelfstudies is een toepassing verpakt in een containerinstallatiekopie. Deze installatiekopie is geüpload naar Azure Container Registry en u hebt een AKS-cluster gemaakt. De toepassing is vervolgens geïmplementeerd in het AKS-cluster. Als u deze stappen niet hebt uitgevoerd en u deze zelfstudie toch wilt volgen, begint u met [zelfstudie 1: Containerinstallatiekopieën maken][aks-tutorial-prepare-app].

Voor deze zelfstudie moet u Azure CLI versie 2.0.53 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="get-available-cluster-versions"></a>Beschikbare clusterversies verkrijgen

Voordat u een cluster bijwerkt, gebruikt u de opdracht [az aks get-upgrades][] om te controleren welke Kubernetes-releases beschikbaar zijn voor de upgrade:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

In het volgende voorbeeld is de huidige versie *1.15.11* en worden de beschikbare versies weergegeven onder *Upgrades*.

```json
{
  "agentPoolProfiles": null,
  "controlPlaneProfile": {
    "kubernetesVersion": "1.15.11",
    ...
    "upgrades": [
      {
        "isPreview": null,
        "kubernetesVersion": "1.16.8"
      },
      {
        "isPreview": null,
        "kubernetesVersion": "1.16.9"
      }
    ]
  },
  ...
}
```

## <a name="upgrade-a-cluster"></a>Een cluster upgraden

Om onderbreking van actieve toepassingen te minimaliseren, worden AKS-knooppunten zorgvuldig afgebakend en geleegd. In dit proces worden de volgende stappen uitgevoerd:

1. Kubernetes Scheduler voorkomt dat er extra pods worden gepland op een knooppunt dat moet worden bijgewerkt.
1. Actieve pods op het knooppunt worden gepland voor uitvoering op andere knooppunten in het cluster.
1. Er wordt een knooppunt gemaakt waarop de meest recente Kubernetes-onderdelen worden uitgevoerd.
1. Als het nieuwe knooppunt klaar en is toegevoegd aan het cluster, worden er pods gepland op het knooppunt.
1. Het oude knooppunt wordt verwijderd en het proces van afbakenen en legen begint op het volgende knooppunt in het cluster.

Gebruik de opdracht [az aks upgrade][] als u het AKS-cluster wilt bijwerken.

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

> [!NOTE]
> U kunt slechts één secundaire versie per keer bijwerken. U kunt bijvoorbeeld een upgrade uitvoeren van *1.14.x* naar *1.15.x*, maar niet rechtstreeks van *1.14.x* naar *1.16.x*. Als u een upgrade wilt uitvoeren van *1.14.x* naar *1.16.x*, moet u eerst een upgrade uitvoeren van *1.14.x* naar *1.15.x* en vervolgens van *1.15.x* naar *1.16.x*.

In de volgende verkorte voorbeelduitvoer ziet u het resultaat van de upgrade naar *1.16.8*. U ziet dat bij *kubernetesVersion* nu *1.16.8* wordt aangegeven:

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.16.8",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Een upgrade valideren

Controleer als volgt of de upgrade is geslaagd met de opdracht [az aks show][]:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

De volgende voorbeelduitvoer laat zien dat *KubernetesVersion 1.16.8* door het AKS-cluster wordt uitgevoerd:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.16.8               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Het cluster verwijderen

Omdat deze zelfstudie de laatste is van de reeks zelfstudies, kunt u het AKS-cluster verwijderen. Aangezien de Kubernetes-knooppunten worden uitgevoerd op virtuele Azure machines (VM's), worden er nog steeds kosten in rekening gebracht, zelfs als u het cluster niet gebruikt. Gebruik de opdracht [az group delete][az-group-delete] om de resourcegroep, de containerservice en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal. Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft u geen geheimen in te richten of te draaien.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een upgrade van Kubernetes in een AKS-cluster uitgevoerd. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Huidige en beschikbare Kubernetes-versies identificeren
> * De Kubernetes-knooppunten upgraden
> * Een geslaagde upgrade valideren

Zie [Overzicht van AKS][aks-intro]voor meer informatie over AKS. Zie [Azure Kubernetes Service solution journey][aks-solution-guidance]voor hulp bij het maken van volledige oplossingen met AKS.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[aks-solution-guidance]: /azure/architecture/reference-architectures/containers/aks-start-here?WT.mc_id=AKSDOCSPAGE
