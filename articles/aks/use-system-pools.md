---
title: Systeem knooppunt groepen gebruiken in azure Kubernetes service (AKS)
description: Meer informatie over het maken en beheren van groepen met systeem knooppunten in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: 9c53cb53517c4696a1bb47c2cb72335979d58d3a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178827"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Systeem knooppunt groepen beheren in azure Kubernetes service (AKS)

In azure Kubernetes service (AKS) worden knoop punten van dezelfde configuratie samen in *knooppunt groepen* gegroepeerd. Knooppunt groepen bevatten de onderliggende virtuele machines waarop uw toepassingen worden uitgevoerd. Systeem knooppunt groepen en gebruikers knooppunt groepen zijn twee verschillende modi voor de knooppunt groep voor uw AKS-clusters. Systeem knooppunt groepen gebruiken het primaire doel om essentiële systemen te hosten, zoals `CoreDNS` en `metrics-server` . Gebruikers knooppunt groepen gebruiken het primaire doel om uw toepassing te hosten. Toepassing peul kan echter worden gepland op systeem knooppunt groepen als u slechts één groep in uw AKS-cluster wilt hebben. Elk AKS-cluster moet ten minste één systeem knooppunt groep bevatten met ten minste één knoop punt.

> [!Important]
> Als u één systeem knooppunt groep voor uw AKS-cluster in een productie omgeving uitvoert, raden we u aan om ten minste drie knoop punten voor de knooppunt groep te gebruiken.

## <a name="before-you-begin"></a>Voordat u begint

* U moet de Azure CLI-versie 2.3.1 of later installeren en configureren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die ondersteuning bieden voor systeem knooppunt groepen.

* Zie [quota's, beperkingen voor de grootte van virtuele machines en beschik baarheid van regio's in azure Kubernetes service (AKS)][quotas-skus-regions].
* Het AKS-cluster moet worden gebouwd met virtuele-machine schaal sets als VM-type en de *standaard* -SKU Load Balancer.
* De naam van een knooppunt groep mag alleen kleine letters bevatten en moet beginnen met een kleine letter. Voor Linux-knooppunt Pools moet de lengte tussen de 1 en 12 tekens liggen. Voor Windows-knooppunt groepen moet de lengte tussen de 1 en 6 tekens zijn.
* Een API-versie van 2020-03-01 of hoger moet worden gebruikt om een modus voor de knooppunt groep in te stellen. Clusters die zijn gemaakt op API-versies ouder dan 2020-03-01, bevatten alleen groepen met gebruikers knooppunten, maar kunnen worden gemigreerd om systeem knooppunt groepen te bevatten door de stappen in de [groeps modus bijwerken](#update-existing-cluster-system-and-user-node-pools)te volgen.
* De modus van een knooppunt groep is een vereiste eigenschap en moet expliciet worden ingesteld wanneer ARM-sjablonen of directe API-aanroepen worden gebruikt.

## <a name="system-and-user-node-pools"></a>Systeem-en gebruikers knooppunt groepen

Voor een groep systeem knooppunten wijst AKS automatisch het label **kubernetes.Azure.com/mode: System** toe aan de knoop punten. Dit leidt ertoe dat AKS een systeem-peul plant op knooppunt groepen die dit label bevatten. Dit label voor komt niet dat u toepassings-peulen plant op systeem knooppunt groepen. We raden u echter aan om essentiële systeem namen van uw toepassing te isoleren, om te voor komen dat een onjuist geconfigureerde of Rogue-toepassing het systeem van de peulen per ongeluk afloopt. U kunt dit gedrag afdwingen door een toegewezen systeem knooppunt groep te maken. Gebruik de `CriticalAddonsOnly=true:NoSchedule` Taint om te voor komen dat toepassings-peulen worden gepland op systeem knooppunt groepen.

Systeem knooppunt groepen hebben de volgende beperkingen:

* Systeem groepen osType moet Linux zijn.
* OsType van gebruikers knooppunten kunnen Linux of Windows zijn.
* Systeem groepen moeten ten minste één knoop punt bevatten en gebruikers knooppunt groepen kunnen nul of meer knoop punten bevatten.
* Systeem knooppunt groepen vereisen een VM-SKU van ten minste 2 Vcpu's en 4 GB geheugen.
* Systeem knooppunt groepen moeten ten minste 30 peul ondersteunen, zoals wordt beschreven door de [formule minimale en maximale waarde voor peulen][maximum-pods].
* Voor spot knooppunt Pools zijn gebruikers knooppunt Pools vereist.
* Het toevoegen van een extra groep van het systeem knooppunt of het wijzigen van de groep van knoop punten is een systeem knooppunt groep zal *niet* automatisch het systeem van de peul verplaatsen. Systeem-peul kan worden uitgevoerd op dezelfde knooppunt groep, zelfs als u het wijzigt in een groep van gebruikers knooppunten. Als u een groep knoop punten verwijdert of uitschaalt die ouder is dan een systeem knooppunt groep, worden deze systemen van het gehele systeem opnieuw geïmplementeerd met de voorkeurs planning naar de nieuwe groep systeem knooppunten.

U kunt de volgende bewerkingen uitvoeren met knooppunt groepen:

* Een speciale systeem knooppunt groep maken (planning van systeem-peulen voor knooppunt groepen van `mode:system` )
* Wijzig een groep van het systeem knooppunt in een groep met gebruikers knooppunten, tenzij u een andere groep van het systeem knooppunt hebt die u in het AKS-cluster kunt opnemen.
* Wijzig de groep van een gebruikers knooppunt in een groep met systeem knooppunten.
* Gebruikers knooppunt groepen verwijderen.
* U kunt systeem knooppunt groepen verwijderen. u hebt een andere groep van het systeem knooppunt nodig om in het AKS-cluster te plaatsen.
* Een AKS-cluster kan meerdere systeem knooppunt groepen bevatten en moet ten minste één groep systeem knooppunten hebben.
* Als u verschillende onveranderbare instellingen voor bestaande knooppunt groepen wilt wijzigen, kunt u nieuwe knooppunt groepen maken om ze te vervangen. Een voor beeld hiervan is het toevoegen van een nieuwe groep van een knoop punt met een nieuwe maxPods-instelling en het verwijderen van de oude knooppunt groep.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Een nieuw AKS-cluster maken met een systeem knooppunt groep

Wanneer u een nieuw AKS-cluster maakt, maakt u automatisch een systeem knooppunt groep met één knoop punt. De eerste knooppunt groep wordt standaard ingesteld op een modus van het type systeem. Wanneer u nieuwe knooppunt Pools maakt met `az aks nodepool add` , zijn die knooppunt groepen gebruikers knooppunt groepen, tenzij u expliciet de modus para meter opgeeft.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt in de regio *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Gebruik de opdracht [az aks create][az-aks-create] om een AKS-cluster te maken. In het volgende voor beeld wordt een cluster met de naam *myAKSCluster* gemaakt met één specifieke systeem groep met één knoop punt. Zorg ervoor dat u voor de werk belasting van uw productie gebruikmaakt van systeem knooppunt groepen met ten minste drie knoop punten. Deze bewerking kan enkele minuten duren.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>Een speciale systeem knooppunt groep toevoegen aan een bestaand AKS-cluster

> [!Important]
> U kunt de taints van het knoop punt niet wijzigen via de CLI nadat de knooppunt groep is gemaakt.

U kunt een of meer systeem knooppunt groepen toevoegen aan bestaande AKS-clusters. Het is raadzaam om uw toepassing heel gemakkelijk te plannen voor gebruikers knooppunt groepen, en u kunt alleen systeem knooppunt groepen toewijzen aan een essentieel systeem. Dit voor komt dat Rogue-toepassingen van het ene per ongeluk systeem worden gedood. Dit gedrag afdwingen met de `CriticalAddonsOnly=true:NoSchedule` [Taint][aks-taints] voor uw systeem knooppunt groepen. 

Met de volgende opdracht wordt een toegewezen knooppunt groep van het type modus systeem toegevoegd met een standaard aantal van drie knoop punten.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>Details voor de knooppunt groep weer geven

U kunt de details van de knooppunt groep controleren met de volgende opdracht.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

Er is een modus van het type **systeem** gedefinieerd voor systeem knooppunt groepen en een modus van het type **gebruiker** is gedefinieerd voor gebruikers knooppunt groepen. Controleer voor een systeem groep of de Taint is ingesteld op `CriticalAddonsOnly=true:NoSchedule` , waarmee wordt voor komen dat toepassings-peulen worden gepland voor deze knooppunt groep.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Bestaande cluster systeem-en gebruikers knooppunt groepen bijwerken

> [!NOTE]
> Er moet een API-versie van 2020-03-01 of hoger worden gebruikt om de groeps modus voor een systeem knooppunt in te stellen. Clusters die zijn gemaakt op API-versies ouder dan 2020-03-01, bevatten als resultaat alleen groepen van gebruikers knooppunten. Als u de functionaliteit en voor delen van systeem knooppunt groepen op oudere clusters wilt ontvangen, moet u de modus van bestaande knooppunt groepen bijwerken met de volgende opdrachten in de meest recente versie van Azure CLI.

U kunt de modus voor zowel systeem-als gebruikers knooppunt groepen wijzigen. U kunt een systeem knooppunt groep alleen wijzigen in een gebruikers groep als er al een andere systeem knooppunt groep bestaat op het AKS-cluster.

Met deze opdracht wordt een systeem knooppunt groep gewijzigd in een groep met gebruikers knooppunten.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Met deze opdracht wordt een groep van een gebruikers knooppunt gewijzigd in een systeem knooppunt groep.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Een systeem knooppunt groep verwijderen

> [!Note]
> Als u systeem knooppunt groepen op AKS-clusters vóór API-versie 2020-03-02 wilt gebruiken, voegt u een nieuwe groep systeem knooppunten toe en verwijdert u de oorspronkelijke standaard knooppunt groep.

U moet ten minste twee systeem knooppunt groepen hebben op uw AKS-cluster voordat u er een kunt verwijderen.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u het cluster wilt verwijderen, gebruikt u de opdracht [AZ Group delete][az-group-delete] om de resource groep AKS te verwijderen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u systeem knooppunt groepen kunt maken en beheren in een AKS-cluster. Zie [meerdere knooppunt groepen gebruiken][use-multiple-node-pools]voor meer informatie over het gebruik van meerdere knooppunt groepen.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#setting-nodepool-taints
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
