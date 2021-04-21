---
title: Handmatig een Azure Files maken
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het handmatig maken van een volume met Azure Files voor gebruik met meerdere gelijktijdige pods in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 7f3c8ae63e908f440740277084293a011b80b9d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776084"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Handmatig een volume maken en gebruiken met Azure Files share in Azure Kubernetes Service (AKS)

Toepassingen op basis van containers moeten vaak toegang krijgen tot gegevens en deze persistent maken in een extern gegevensvolume. Als meerdere pods gelijktijdige toegang tot hetzelfde opslagvolume nodig hebben, kunt u Azure Files gebruiken om verbinding te maken met behulp van het [SMB-protocol (Server Message Block).][smb-overview] In dit artikel wordt beschreven hoe u handmatig een Azure Files en deze aan een pod in AKS koppelt.

Zie Opslagopties voor toepassingen in [AKS][concepts-storage]voor meer informatie over Kubernetes-volumes.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

Ook moet azure CLI versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken

Voordat u een Azure Files kubernetes-volume kunt gebruiken, moet u een Azure Storage-account en de bestands share maken. Met de volgende opdrachten maakt u een resourcegroep met de *naam myAKSShare,* een opslagaccount en een bestandsshare met de *naam aksshare:*

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Noteer de naam en sleutel van het opslagaccount die aan het einde van de scriptuitvoer worden weergegeven. Deze waarden zijn nodig wanneer u het Kubernetes-volume in een van de volgende stappen maakt.

## <a name="create-a-kubernetes-secret"></a>Een Kubernetes-geheim maken

Kubernetes heeft referenties nodig voor toegang tot de bestands share die u in de vorige stap hebt gemaakt. Deze referenties worden opgeslagen in een [Kubernetes-geheim][kubernetes-secret]waarnaar wordt verwezen wanneer u een Kubernetes-pod maakt.

Gebruik de `kubectl create secret` opdracht om het geheim te maken. In het volgende voorbeeld wordt een gedeelde met de naam *azure-secret* gemaakt en worden de *azurestorageaccountname en* *azurestorageaccountkey uit* de vorige stap ingevuld. Als u een bestaand Azure-opslagaccount wilt gebruiken, geeft u de accountnaam en -sleutel op.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-file-share-as-an-inline-volume"></a>Een bestands share als een inline-volume toevoegen
> Opmerking: vanaf 1.18.15, 1.19.7, 1.20.2, 1.21.0 kunnen geheime naamruimten in een inline volume alleen worden ingesteld als naamruimte. Als u een andere geheime naamruimte wilt opgeven, gebruikt u in plaats daarvan het onderstaande voorbeeld van een `azureFile` `default` permanent volume.

Als u de Azure Files aan uw pod wilt, configureert u het volume in de containerspecificatie. Maak een nieuw bestand met de `azure-files-pod.yaml` naam met de volgende inhoud. Als u de naam van de bestands share of geheime naam hebt gewijzigd, moet u *de shareName en* *secretName bijwerken.* Werk indien gewenst de `mountPath` bij. Dit is het pad waar de bestands share in de pod is bevestigd. Geef voor Windows Server-containers een *mountPath op* met behulp van de Windows-padconventie, zoals *'D:'.*

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Gebruik de `kubectl` opdracht om de pod te maken.

```console
kubectl apply -f azure-files-pod.yaml
```

U hebt nu een pod die wordt uitgevoerd met een Azure Files-share die is bevestigd op */mnt/azure.* U kunt gebruiken `kubectl describe pod mypod` om te controleren of de share is bevestigd. In de volgende verkorte voorbeelduitvoer ziet u het volume dat is bevestigd in de container:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-file-share-as-an-persistent-volume"></a>Een bestands share als een permanent volume toevoegen
 - Koppelingsopties

De standaardwaarde voor *fileMode* en *dirMode* is *0777* voor Kubernetes versie 1.15 en hoger. In het volgende voorbeeld wordt *0755 voor* het *object PersistentVolume* gebruikt:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    secretNamespace: default
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0755
  - file_mode=0755
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Maak een bestand *azurefile-mount-options-pv.yaml* met een *PermanentVolume* om uw mountopties bij te werken. Bijvoorbeeld:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Maak een *azurefile-mount-options-pvc.yaml-bestand* met een *PersistentVolumeClaim* die gebruikmaakt van *het PersistentVolume*. Bijvoorbeeld:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 5Gi
```

Gebruik de `kubectl` opdrachten om de *PersistentVolume en* *PersistentVolumeClaim te maken.*

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Controleer of *uw PersistentVolumeClaim* is gemaakt en is gebonden aan het *PersistentVolume*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Werk de containerspecificatie bij om te verwijzen *naar uw PersistentVolumeClaim* en werk uw pod bij. Bijvoorbeeld:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Volgende stappen

Zie Best practices voor opslag en [back-ups in AKS][operator-best-practices-storage]voor de bijbehorende best practices.

Zie de [Kubernetes-Azure Files][kubernetes-files]voor meer informatie over de interactie van AKS-clusters Azure Files .

Zie Statische inrichting [(bring your own file share) voor opslagklasseparameters.](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share)

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
