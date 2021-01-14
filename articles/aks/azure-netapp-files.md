---
title: Azure NetApp Files integreren met de Azure Kubernetes-service
description: Meer informatie over het integreren van Azure NetApp Files met de Azure Kubernetes-service
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 19727d3c3322b05f340463d94a2bc3884e5d9d93
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196007"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Azure NetApp Files integreren met de Azure Kubernetes-service

[Azure NetApp files][anf] is een hoogwaardige, op Data Services gebaseerde bestands opslag service die op Azure wordt uitgevoerd. In dit artikel wordt beschreven hoe u Azure NetApp Files integreert met Azure Kubernetes service (AKS).

## <a name="before-you-begin"></a>Voordat u begint
In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Uw AKS-cluster moet ook deel uitmaken [van een regio die Azure NetApp files ondersteunt][anf-regions].

Ook moet de Azure CLI-versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u Azure NetApp Files gebruikt:

* Azure NetApp Files is alleen beschikbaar [in de geselecteerde Azure-regio's][anf-regions].
* Voordat u Azure NetApp Files kunt gebruiken, moet u toegang krijgen tot de Azure NetApp Files-service. Als u de toegang wilt Toep assen, kunt u het [Waitlist-inzendings formulier van Azure NetApp files][anf-waitlist] gebruiken of gaat u naar https://azure.microsoft.com/services/netapp/#getting-started . U hebt geen toegang tot de Azure NetApp Files-service totdat u het officiële bevestigings bericht van het Azure NetApp Files team ontvangt.
* Na de eerste implementatie van een AKS-cluster wordt alleen statische inrichting van Azure NetApp Files ondersteund.
* Als u dynamische inrichting met Azure NetApp Files wilt gebruiken, installeert en configureert u [NetApp Trident](https://netapp-trident.readthedocs.io/) -versie 19,07 of hoger.

## <a name="configure-azure-netapp-files"></a>Azure NetApp Files configureren

> [!IMPORTANT]
> Voordat u de  *micro soft. NetApp* -resource provider kunt registreren, moet u het [Verzend formulier van de Azure NetApp files Waitlist][anf-waitlist] volt ooien of naar https://azure.microsoft.com/services/netapp/#getting-started uw abonnement gaan. U kunt de levering van resources pas registreren nadat u de officiële bevestigings-e-mail van het Azure NetApp Files-team hebt ontvangen.

Registreer de resource provider *micro soft. NetApp* :

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Dit kan enige tijd duren voordat de bewerking is voltooid.

Wanneer u een Azure NetApp-account maakt voor gebruik met AKS, moet u het account maken in de resource groep van het **knoop punt** . Haal eerst de naam van de resource groep op met de opdracht [AZ AKS show][az-aks-show] en voeg de `--query nodeResourceGroup` query parameter toe. In het volgende voor beeld wordt de resource groep node opgehaald voor het AKS-cluster met de naam *myAKSCluster* in de naam van de resource groep *myResourceGroup*:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Maak een Azure NetApp Files-account in de **knooppunt** resource groep en dezelfde regio als uw AKS-cluster met behulp van [AZ netappfiles account create][az-netappfiles-account-create]. In het volgende voor beeld wordt een account gemaakt met de naam *MyAccount1* in de *MC_myResourceGroup_myAKSCluster_eastus* resource groep en regio *oostus* :

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Maak een nieuwe capaciteits pool met behulp van [AZ netappfiles pool Create][az-netappfiles-pool-create]. In het volgende voor beeld wordt een nieuwe capaciteits groep gemaakt met de naam *mypool1* met 4 TB op grootte en *Premium* -service niveau:

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Maak een subnet om [te delegeren naar Azure NetApp files][anf-delegate-subnet] met [AZ Network vnet subnet Create][az-network-vnet-subnet-create]. *Dit subnet moet zich in hetzelfde virtuele netwerk bevinden als uw AKS-cluster.*

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

Maak een volume met behulp van [AZ netappfiles volume Create][az-netappfiles-volume-create].

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

## <a name="create-the-persistentvolume"></a>De PersistentVolume maken

De details van uw volume [weer geven met AZ netappfiles volume show][az-netappfiles-volume-show]

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

Maak een `pv-nfs.yaml` definitie van een PersistentVolume. Vervang door `path` de *creationToken* en `server` met *ipAddress* van de vorige opdracht. Bijvoorbeeld:

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

Werk de *Server* en het *pad* bij naar de waarden van het NFS-volume dat u in de vorige stap hebt gemaakt. Maak de PersistentVolume met de opdracht [kubectl apply][kubectl-apply] :

```console
kubectl apply -f pv-nfs.yaml
```

Controleer of de *status* van de PersistentVolume *beschikbaar* is met behulp van de kubectl-opdracht [beschrijven][kubectl-describe] :

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>De PersistentVolumeClaim maken

Maak een `pvc-nfs.yaml` definitie van een PersistentVolume. Bijvoorbeeld:

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

Maak de PersistentVolumeClaim met de opdracht [kubectl apply][kubectl-apply] :

```console
kubectl apply -f pvc-nfs.yaml
```

Controleer of de *status* van de PersistentVolumeClaim is *gekoppeld* met behulp van de kubectl-opdracht [beschrijven][kubectl-describe] :

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Koppelen aan een pod

Maak een `nginx-nfs.yaml` definitie van een pod die gebruikmaakt van de PersistentVolumeClaim. Bijvoorbeeld:

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

Maak de Pod met de opdracht [kubectl apply][kubectl-apply] :

```console
kubectl apply -f nginx-nfs.yaml
```

Controleer of de Pod wordt *uitgevoerd* met behulp van de kubectl-opdracht [beschrijven][kubectl-describe] :

```console
kubectl describe pod nginx-nfs
```

Controleer of het volume is gekoppeld in de Pod met behulp van [kubectl exec][kubectl-exec] om verbinding te maken met de pod vervolgens `df -h` te controleren of het volume is gekoppeld.

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

Zie [Wat is Azure NetApp files][anf]voor meer informatie over Azure NetApp files. Zie [hand matig een NFS (Network File System) Linux-server volume maken en gebruiken met Azure Kubernetes service (AKS)][aks-nfs]voor meer informatie over het gebruik van NFS met AKS.


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
