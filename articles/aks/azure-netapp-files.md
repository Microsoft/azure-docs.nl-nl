---
title: Integratie Azure NetApp Files met Azure Kubernetes Service
description: Meer informatie over hoe u Azure NetApp Files integreert met Azure Kubernetes Service
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 28c5b77f06bc48bf06575e45194adfaed068b30f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776047"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integratie Azure NetApp Files met Azure Kubernetes Service

[Azure NetApp Files][anf] is een hoogwaardige bestandsopslagservice met hoge prestaties die wordt uitgevoerd in Azure. In dit artikel wordt beschreven hoe u Azure NetApp Files integreert met Azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Voordat u begint
In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Uw AKS-cluster moet zich ook [in een regio die ondersteuning biedt voor Azure NetApp Files][anf-regions].

Ook moet azure CLI versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="limitations"></a>Beperkingen

De volgende beperkingen gelden wanneer u Azure NetApp Files:

* Azure NetApp Files is alleen beschikbaar [in geselecteerde Azure-regio's.][anf-regions]
* Voordat u een Azure NetApp Files kunt gebruiken, moet u toegang krijgen tot de Azure NetApp Files service. Als u toegang wilt aanvragen, kunt u het formulier Azure NetApp Files [of][anf-waitlist] naar https://azure.microsoft.com/services/netapp/#getting-started gaan. U hebt geen toegang tot de Azure NetApp Files service totdat u de officiële bevestigingsmail van het Azure NetApp Files ontvangen.
* Na de eerste implementatie van een AKS-cluster wordt alleen statische inrichting voor Azure NetApp Files ondersteund.
* Als u dynamische inrichting wilt gebruiken met Azure NetApp Files, installeert en configureert u [NetApp Trident](https://netapp-trident.readthedocs.io/) versie 19.07 of hoger.

## <a name="configure-azure-netapp-files"></a>Een Azure NetApp Files

> [!IMPORTANT]
> Voordat u de  *resourceprovider Microsoft.NetApp* kunt registreren, moet u het formulier voor het indienen [Azure NetApp Files][anf-waitlist] de wachtlijst invullen of naar voor uw https://azure.microsoft.com/services/netapp/#getting-started abonnement gaan. U kunt de resource-provide pas registreren als u de officiële bevestigingsmail van het Azure NetApp Files ontvangen.

Registreer de *resourceprovider Microsoft.NetApp:*

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Dit kan enige tijd duren.

Wanneer u een Azure NetApp-account maakt voor gebruik met AKS, moet u het account maken in de **knooppuntresourcegroep.** Haal eerst de naam van de resourcegroep op met de [opdracht az aks show][az-aks-show] en voeg de `--query nodeResourceGroup` queryparameter toe. In het volgende voorbeeld wordt de knooppuntresourcegroep voor het AKS-cluster met de naam *myAKSCluster* in de *resourcegroepnaam myResourceGroup opgeslagen:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Maak een Azure NetApp Files-account in  de knooppuntresourcegroep en dezelfde regio als uw AKS-cluster met [az netappfiles account create.][az-netappfiles-account-create] In het volgende voorbeeld wordt een account met de *naam myaccount1* gemaakt in *MC_myResourceGroup_myAKSCluster_eastus* resourcegroep en regio *EASTUS:*

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Maak een nieuwe capaciteitspool met [az netappfiles pool create.][az-netappfiles-pool-create] In het volgende voorbeeld wordt een nieuwe capaciteitspool met de *naam mypool1* gemaakt met een grootte van 4 TB en *een Premium-serviceniveau:*

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Maak een subnet dat u [wilt delegeren Azure NetApp Files][anf-delegate-subnet] [met az network vnet subnet create.][az-network-vnet-subnet-create] *Dit subnet moet zich in hetzelfde virtuele netwerk als uw AKS-cluster.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Maak een volume met [az netappfiles volume create.][az-netappfiles-volume-create]

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Het Permanentevolume maken

Vermeld de details van uw volume met [az netappfiles volume show][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Maak een `pv-nfs.yaml` permanentvolume definiëren. Vervang `path` door het *creationToken* en `server` door *ipAddress* uit de vorige opdracht. Bijvoorbeeld:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Werk de *server en* *het pad* bij naar de waarden van het NFS-volume (Network File System) dat u in de vorige stap hebt gemaakt. Maak het Permanentevolume met de [opdracht kubectl apply:][kubectl-apply]

```console
kubectl apply -f pv-nfs.yaml
```

Controleer of *de status* van het Permanentevolume beschikbaar is *met behulp* van de [opdracht kubectl describe:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>De PersistentVolumeClaim maken

Maak een `pvc-nfs.yaml` permanentvolume definiëren. Bijvoorbeeld:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Maak de PersistentVolumeClaim met de [opdracht kubectl apply:][kubectl-apply]

```console
kubectl apply -f pvc-nfs.yaml
```

Controleer of *de Status* van de PersistentVolumeClaim Gebonden is *met behulp* van de [opdracht kubectl describe:][kubectl-describe]

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Met een pod aan een pod worden bevestigd

Maak een `nginx-nfs.yaml` pod die gebruikmaakt van de PersistentVolumeClaim. Bijvoorbeeld:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Maak de pod met de [opdracht kubectl apply:][kubectl-apply]

```console
kubectl apply -f nginx-nfs.yaml
```

Controleer of de pod *Wordt uitgevoerd is* met behulp van de opdracht [kubectl describe:][kubectl-describe]

```console
kubectl describe pod nginx-nfs
```

Controleer of het volume aan de pod is bevestigd met behulp van [kubectl exec][kubectl-exec] om verbinding te maken met de pod en controleer vervolgens of `df -h` het volume is bevestigd.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Volgende stappen

Zie Wat is Azure NetApp Files voor [meer informatie over Azure NetApp Files.][anf] Zie Handmatig een [NFS (Network File System) Linux Server-volume][aks-nfs]maken en gebruiken met Azure Kubernetes Service (AKS) voor meer informatie over het gebruik van NFS met AKS.


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
