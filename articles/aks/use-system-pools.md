---
title: Systeem-knooppuntgroepen gebruiken in Azure Kubernetes Service (AKS)
description: Informatie over het maken en beheren van systeem-knooppuntgroepen in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: 8b41b43c70f72ab327de2f1d59415cc1f49e5a5b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767399"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Systeem-knooppuntgroepen beheren in Azure Kubernetes Service (AKS)

In Azure Kubernetes Service (AKS) worden knooppunten van dezelfde configuratie gegroepeerd in *knooppuntgroepen.* Knooppuntgroepen bevatten de onderliggende VM's die uw toepassingen uitvoeren. Systeem-knooppuntgroepen en gebruikers-knooppuntgroepen zijn twee verschillende knooppuntgroepmodi voor uw AKS-clusters. Systeem-knooppuntgroepen dienen het primaire doel van het hosten van essentiële systeempods, `CoreDNS` zoals en `metrics-server` . Gebruikers-knooppuntgroepen dienen het primaire doel van het hosten van uw toepassingspods. Toepassingspods kunnen echter worden gepland op systeemknooppuntgroepen als u slechts één groep in uw AKS-cluster wilt hebben. Elk AKS-cluster moet ten minste één systeemknooppuntgroep met ten minste één knooppunt bevatten.

> [!Important]
> Als u één systeemknooppuntgroep voor uw AKS-cluster in een productieomgeving hebt, raden we u aan ten minste drie knooppunten voor de knooppuntgroep te gebruiken.

## <a name="before-you-begin"></a>Voordat u begint

* Azure CLI versie 2.3.1 of hoger moet zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="limitations"></a>Beperkingen

De volgende beperkingen gelden wanneer u AKS-clusters maakt en beheert die ondersteuning bieden voor systeem-knooppuntgroepen.

* Zie [Quota, groottebeperkingen voor virtuele machines en beschikbaarheid in regio's in Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Het AKS-cluster moet worden gebouwd met virtuele-machineschaalsets als het VM-type en de *Standard* SKU-load balancer.
* De naam van een knooppuntgroep mag alleen kleine alfanumerieke tekens bevatten en moet beginnen met een kleine letter. Voor Linux-knooppuntgroepen moet de lengte tussen 1 en 12 tekens zijn. Voor Windows-knooppuntgroepen moet de lengte tussen 1 en 6 tekens lang zijn.
* Een API-versie van 2020-03-01 of hoger moet worden gebruikt om de modus voor een knooppuntgroep in te stellen. Clusters die zijn gemaakt op API-versies die ouder zijn dan 2020-03-01 bevatten alleen gebruikers-knooppuntgroepen, maar kunnen worden gemigreerd om systeem-knooppuntgroepen te bevatten door de stappen in de [modus updatepool](#update-existing-cluster-system-and-user-node-pools)te volgen.
* De modus van een knooppuntgroep is een vereiste eigenschap en moet expliciet worden ingesteld wanneer u ARM-sjablonen of directe API-aanroepen gebruikt.

## <a name="system-and-user-node-pools"></a>Systeem- en gebruikers-knooppuntgroepen

Voor een systeemknooppuntgroep wijst AKS automatisch het label **kubernetes.azure.com/mode: systeem toe** aan de knooppunten. Hierdoor geeft AKS de voorkeur aan het plannen van systeempods op knooppuntgroepen die dit label bevatten. Dit label voorkomt niet dat u toepassingspods in systeem-knooppuntgroepen kunt plannen. We raden u echter aan kritieke systeempods te isoleren van uw toepassingspods om te voorkomen dat onjuist geconfigureerde of rogue toepassingspods per ongeluk systeempods kunnen stoppen. U kunt dit gedrag afdwingen door een toegewezen systeem-knooppuntgroep te maken. Gebruik de `CriticalAddonsOnly=true:NoSchedule` taint om te voorkomen dat toepassingspods worden gepland in systeem-knooppuntgroepen.

Systeem-knooppuntgroepen hebben de volgende beperkingen:

* Systeemgroepen osType moet Linux zijn.
* OsType van gebruikers-knooppuntgroepen kan Linux of Windows zijn.
* Systeemgroepen moeten ten minste één knooppunt bevatten en gebruikersknooppuntgroepen kunnen nul of meer knooppunten bevatten.
* Voor systeem-knooppuntgroepen is een VM-SKU van ten minste 2 vCKU's en 4 GB geheugen vereist. Maar burstable-VM (B-serie) wordt niet aanbevolen.
* Minimaal twee knooppunten 4 vCCPUs wordt aanbevolen (bijvoorbeeld Standard_DS4_v2), met name voor grote clusters (Replica's van Meerdere CoreDNS Pods, 3-4+ invoegtoepassingen, enzovoort).
* Systeem-knooppuntgroepen moeten ten minste 30 pods ondersteunen, zoals wordt beschreven door de formule voor de minimum- en [maximumwaarde voor pods.][maximum-pods]
* Voor spot-knooppuntgroepen zijn gebruikers-knooppuntgroepen vereist.
* Door een extra systeem-knooppuntgroep toe te voegen of te wijzigen welke knooppuntgroep een systeem-knooppuntgroep *is,* worden systeempods NIET automatisch verplaatst. Systeempods kunnen blijven worden uitgevoerd op dezelfde knooppuntgroep, zelfs als u deze wijzigt in een gebruikers-knooppuntgroep. Als u een knooppuntgroep met systeempods verwijdert of omlaag schaalt die voorheen een systeem-knooppuntgroep was, worden deze systeempods opnieuw geïmplementeerd met een voorkeursplanning voor de nieuwe systeem-knooppuntgroep.

U kunt de volgende bewerkingen uitvoeren met knooppuntgroepen:

* Maak een toegewezen systeem-knooppuntgroep (liever planning van systeempods dan knooppuntgroepen van `mode:system` )
* Wijzig een systeemknooppuntgroep in een gebruikersknooppuntgroep, mits u een andere systeemknooppuntgroep hebt die in het AKS-cluster moet worden gebruikt.
* Wijzig een gebruikers-knooppuntgroep in een systeem-knooppuntgroep.
* Verwijder gebruikers-knooppuntgroepen.
* U kunt systeemknooppuntgroepen verwijderen, mits u een andere systeemknooppuntgroep hebt die in het AKS-cluster moet worden gebruikt.
* Een AKS-cluster kan meerdere systeemknooppuntgroepen hebben en vereist ten minste één systeemknooppuntgroep.
* Als u verschillende onveranderbare instellingen voor bestaande knooppuntgroepen wilt wijzigen, kunt u nieuwe knooppuntgroepen maken om deze te vervangen. Een voorbeeld hiervan is het toevoegen van een nieuwe knooppuntgroep met een nieuwe maxPods-instelling en het verwijderen van de oude knooppuntgroep.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Een nieuw AKS-cluster maken met een systeemknooppuntgroep

Wanneer u een nieuw AKS-cluster maakt, maakt u automatisch een systeemknooppuntgroep met één knooppunt. De initiële knooppuntgroep wordt standaard ingesteld op een modus van het type systeem. Wanneer u nieuwe knooppuntgroepen maakt met , zijn deze `az aks nodepool add` knooppuntgroepen gebruikers-knooppuntgroepen, tenzij u expliciet de modusparameter opgeeft.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt in de regio *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Gebruik de opdracht [az aks create][az-aks-create] om een AKS-cluster te maken. In het volgende voorbeeld wordt een cluster met de *naam myAKSCluster* gemaakt met één toegewezen systeemgroep met één knooppunt. Zorg ervoor dat u voor uw productieworkloads gebruik maakt van systeemknooppuntgroepen met ten minste drie knooppunten. Deze bewerking kan enkele minuten duren.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>Een toegewezen systeemknooppuntgroep toevoegen aan een bestaand AKS-cluster

> [!Important]
> U kunt geen knooppunt-taints wijzigen via de CLI nadat de knooppuntgroep is gemaakt.

U kunt een of meer systeem-knooppuntgroepen toevoegen aan bestaande AKS-clusters. Het is raadzaam om uw toepassingspods op gebruikers-knooppuntgroepen te plannen en systeem-knooppuntgroepen te besteden aan alleen kritieke systeempods. Dit voorkomt dat rogue-toepassingspods per ongeluk systeempods kunnen stoppen. Dwing dit gedrag af met de `CriticalAddonsOnly=true:NoSchedule` [taint][aks-taints] voor uw systeem-knooppuntgroepen. 

Met de volgende opdracht wordt een toegewezen knooppuntgroep van het modustype systeem toegevoegd met een standaard aantal van drie knooppunten.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>Details voor uw knooppuntgroep tonen

U kunt de details van uw knooppuntgroep controleren met de volgende opdracht.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

Een modus van het type **Systeem** wordt gedefinieerd voor systeem-knooppuntgroepen en er wordt een modus van het type **Gebruiker** gedefinieerd voor gebruikers-knooppuntgroepen. Controleer voor een systeemgroep of de taint is ingesteld op , waarmee wordt voorkomen dat toepassingspods op deze `CriticalAddonsOnly=true:NoSchedule` knooppuntgroep worden gepland.

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

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Bestaande clustersysteem- en gebruikersknooppuntgroepen bijwerken

> [!NOTE]
> Een API-versie van 2020-03-01 of hoger moet worden gebruikt om de modus voor een systeem-knooppuntgroep in te stellen. Clusters die zijn gemaakt op API-versies ouder dan 2020-03-01 bevatten als gevolg hiervan alleen gebruikers-knooppuntgroepen. Werk de modus van bestaande knooppuntgroepen bij met de volgende opdrachten in de nieuwste versie van Azure CLI om de functionaliteit en voordelen van systeem-knooppuntgroepen op oudere clusters te ontvangen.

U kunt modi wijzigen voor zowel systeem- als gebruikers-knooppuntgroepen. U kunt een systeemknooppuntgroep alleen wijzigen in een gebruikersgroep als er al een andere systeemknooppuntgroep in het AKS-cluster bestaat.

Met deze opdracht wordt een systeem-knooppuntgroep gewijzigd in een gebruikers-knooppuntgroep.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Met deze opdracht wordt een gebruikers-knooppuntgroep gewijzigd in een systeem-knooppuntgroep.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Een systeem-knooppuntgroep verwijderen

> [!Note]
> Als u systeem-knooppuntgroepen op AKS-clusters wilt gebruiken vóór API-versie 2020-03-02, voegt u een nieuwe systeem-knooppuntgroep toe en verwijdert u vervolgens de oorspronkelijke standaard knooppuntgroep.

U moet ten minste twee systeemknooppuntgroepen in uw AKS-cluster hebben voordat u er een kunt verwijderen.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u het cluster wilt verwijderen, gebruikt u [de opdracht az group delete][az-group-delete] om de AKS-resourcegroep te verwijderen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u systeemknooppuntgroepen maakt en beheert in een AKS-cluster. Zie Meerdere knooppuntgroepen gebruiken voor meer informatie over het gebruik van meerdere [knooppuntgroepen.][use-multiple-node-pools]

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
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
