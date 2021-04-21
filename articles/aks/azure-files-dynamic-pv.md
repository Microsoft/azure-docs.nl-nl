---
title: Dynamisch een Azure Files maken
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het dynamisch maken van een permanent volume met Azure Files voor gebruik met meerdere gelijktijdige pods in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: f301a01e479d03647bebf7cb042564a258e9250e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776119"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Permanent volume dynamisch maken en gebruiken met Azure Files in Azure Kubernetes Service (AKS)

Een permanent volume vertegenwoordigt een stukje opslag dat is ingericht voor gebruik met Kubernetes-pods. Een permanent volume kan worden gebruikt door een of meer pods en dynamisch of statisch worden ingericht. Als meerdere pods gelijktijdig toegang tot hetzelfde opslagvolume nodig hebben, kunt u Azure Files gebruiken om verbinding te maken met behulp van [het Server Message Block (SMB)-protocol][smb-overview]. In dit artikel wordt beschreven hoe u dynamisch een Azure Files maakt voor gebruik door meerdere pods in een Azure Kubernetes Service (AKS)-cluster.

Zie Opslagopties voor toepassingen in [AKS][concepts-storage]voor meer informatie over Kubernetes-volumes.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

Ook moet Azure CLI versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-storage-class"></a>Een opslagklasse maken

Een opslagklasse wordt gebruikt om te definiëren hoe een Azure-bestands share wordt gemaakt. Er wordt automatisch een opslagaccount gemaakt in de [knooppuntresourcegroep][node-resource-group] voor gebruik met de opslagklasse voor het opslaan van de Azure-bestands shares. Kies de volgende [Azure-opslag redundantie][storage-skus] voor *skuName:*

* *Standard_LRS* - standaard lokaal redundante opslag (LRS)
* *Standard_GRS* - standaard geografisch redundante opslag (GRS)
* *Standard_ZRS* - standaard zone-redundante opslag (ZRS)
* *Standard_RAGRS-* standaard geografisch redundante opslag met leestoegang (RA-GRS)
* *Premium_LRS* - Premium lokaal redundante opslag (LRS)
* *Premium_ZRS* - Premium Zone Redundant Storage (ZRS)

> [!NOTE]
> Azure Files ondersteuning voor Premium Storage in AKS-clusters met Kubernetes 1.13 of hoger, is de minimale Premium-bestands share 100 GB

Zie Kubernetes Storage Classes (Kubernetes-opslagklassen) voor meer Azure Files [kubernetes-opslagklassen.][kubernetes-storage-classes]

Maak een bestand met de `azure-file-sc.yaml` naam en kopieer het in het volgende voorbeeldmanifest. Zie de sectie Opties voor het monteren voor meer informatie over *mountOptions.* [][mount-options]

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

Maak de opslagklasse met de [opdracht kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Een permanente volumeclaim maken

Een permanente volumeclaim (PERSISTENT) maakt gebruik van het opslagklasseobject om een Azure-bestands share dynamisch in te delen. De volgende YAML kan worden gebruikt om een permanente volumeclaim *van 5 GB* te maken met *ReadWriteMany-toegang.* Zie de documentatie over permanente [Kubernetes-volumes][access-modes] voor meer informatie over toegangsmodi.

Maak nu een bestand met de `azure-file-pvc.yaml` naam en kopieer dit in de volgende YAML. Zorg ervoor dat *de storageClassName overeenkomt* met de opslagklasse die u in de laatste stap hebt gemaakt:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: my-azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> Als u de *Premium_LRS* voor uw opslagklasse gebruikt, moet de minimumwaarde voor *opslag* *100Gi zijn.*

Maak de permanente volumeclaim met de [opdracht kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-pvc.yaml
```

Zodra dit is voltooid, wordt de bestands share gemaakt. Er wordt ook een Kubernetes-geheim gemaakt dat verbindingsgegevens en referenties bevat. U kunt de [opdracht kubectl get gebruiken][kubectl-get] om de status van de PVC weer te geven:

```console
$ kubectl get pvc my-azurefile

NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
my-azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            my-azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Het permanente volume gebruiken

Met de volgende YAML wordt een pod gemaakt die gebruikmaakt van de permanente volumeclaim *my-azurefile* om de Azure-bestands share te mounten op het *pad /mnt/azure.* Geef voor Windows Server-containers een *mountPath op met* behulp van de Windows-padconventie, zoals *'D:'.*

Maak een bestand met de `azure-pvc-files.yaml` naam en kopieer in de volgende YAML. Zorg ervoor dat de *claimName overeenkomt* met de PVC die u in de laatste stap hebt gemaakt.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: my-azurefile
```

Maak de pod met de [opdracht kubectl apply.][kubectl-apply]

```console
kubectl apply -f azure-pvc-files.yaml
```

U hebt nu een pod die wordt uitgevoerd met Azure Files-share die is bevestigd in de *map /mnt/azure.* Deze configuratie kan worden gezien bij het inspecteren van uw pod via `kubectl describe pod mypod` . In de volgende verkorte voorbeelduitvoer ziet u het volume dat in de container is bevestigd:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  my-azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Koppelingsopties

De standaardwaarde voor *fileMode* en *dirMode* is *0777* voor Kubernetes versie 1.13.0 en hoger. Als het permanente volume dynamisch wordt gemaakt met een opslagklasse, kunnen er opties voor het maken van een opslagklasse worden opgegeven voor het opslagklasseobject. In het volgende voorbeeld wordt *0777:*

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>Volgende stappen

Zie Best practices for [storage and backups in AKS (Best practices voor opslag en back-ups in AKS) voor de bijbehorende best practices.][operator-best-practices-storage]

Zie Dynamische inrichting voor [opslagklasseparameters.](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#dynamic-provision)

Meer informatie over permanente Kubernetes-volumes met behulp van Azure Files.

> [!div class="nextstepaction"]
> [Kubernetes-invoegsel voor Azure Files][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/aks#az_aks_show
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
