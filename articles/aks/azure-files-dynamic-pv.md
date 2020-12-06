---
title: Azure Files share dynamisch maken
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het dynamisch maken van een permanent volume met Azure Files voor gebruik met meerdere gelijktijdige peulingen in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 2ad2affee34348e8c2fc7b734c8b49d0aec8db40
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744906"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Dynamisch een permanent volume maken en gebruiken met Azure Files in azure Kubernetes service (AKS)

Een permanent volume vertegenwoordigt een opslag ruimte die is ingericht voor gebruik met Kubernetes peul. Een permanent volume kan worden gebruikt door een of meer peulen en kan dynamisch of statisch worden ingericht. Als meerdere peulen gelijktijdig gelijktijdige toegang tot hetzelfde opslag volume nodig hebben, kunt u Azure Files gebruiken om verbinding te maken met het [SMB-protocol (Server Message Block)][smb-overview]. In dit artikel wordt beschreven hoe u een Azure Files share dynamisch kunt maken voor gebruik door meerdere peulingen in een Azure Kubernetes service-cluster (AKS).

Zie [opslag opties voor toepassingen in AKS][concepts-storage]voor meer informatie over Kubernetes-volumes.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

Ook moet de Azure CLI-versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-storage-class"></a>Een opslag klasse maken

Een opslag klasse wordt gebruikt om te bepalen hoe een Azure-bestands share wordt gemaakt. Er wordt automatisch een opslag account gemaakt in de [knooppunt resource groep][node-resource-group] voor gebruik met de opslag klasse om de Azure-bestands shares op te slaan. Kies een van de volgende [Azure Storage-redundantie][storage-skus] voor *skuName*:

* *Standard_LRS* -standaard lokaal redundante opslag (LRS)
* *Standard_GRS* -standaard geo-redundante opslag (GRS)
* *Standard_ZRS* -standaard zone redundante opslag (ZRS)
* *Standard_RAGRS* -standaard geografisch redundante opslag met lees toegang (RA-GRS)
* *Premium_LRS* -Premium lokaal redundante opslag (LRS)
* *Premium_ZRS* -Premium zone-redundante opslag (ZRS)

> [!NOTE]
> Azure Files Premium Storage ondersteunen in AKS-clusters met Kubernetes 1,13 of hoger, is de minimale Premium-bestands share 100 GB

Zie [Kubernetes-opslag klassen][kubernetes-storage-classes]voor meer informatie over Kubernetes-opslag klassen voor Azure files.

Maak een bestand `azure-file-sc.yaml` met de naam en kopieer het in het volgende voor beeld-manifest. Zie de sectie [koppelings opties][mount-options] voor meer informatie over *mountOptions*.

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

Maak de opslag klasse met de opdracht [kubectl apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Een permanente volume claim maken

Een permanente volume claim (PVC) maakt gebruik van het opslag klassen object om een Azure-bestands share dynamisch in te richten. De volgende YAML kunnen worden gebruikt voor het maken van een permanente volume claim *5 GB* in grootte met *ReadWriteMany* -toegang. Zie de documentatie over het [permanente volume van Kubernetes][access-modes] voor meer informatie over de toegangs modi.

Maak nu een bestand `azure-file-pvc.yaml` met de naam en kopieer de volgende YAML. Zorg ervoor dat de *storageClassName* overeenkomt met de opslag klasse die in de laatste stap is gemaakt:

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
> Als u de *Premium_LRS* SKU voor uw opslag klasse gebruikt, moet de minimale *storage* waarde voor opslag *100Gi* zijn.

Maak de permanente volume claim met de opdracht [kubectl apply][kubectl-apply] :

```console
kubectl apply -f azure-file-pvc.yaml
```

Zodra het is voltooid, wordt de bestands share gemaakt. Er wordt ook een Kubernetes-geheim gemaakt dat verbindings gegevens en referenties bevat. U kunt de opdracht [kubectl Get][kubectl-get] gebruiken om de status van het PVC weer te geven:

```console
$ kubectl get pvc my-azurefile

NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
my-azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            my-azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Het permanente volume gebruiken

De volgende YAML maakt een pod die gebruikmaakt van de permanente volume claim *mijn-azurefile* om de Azure-bestands share te koppelen aan het */mnt/Azure* -pad. Voor Windows Server-containers geeft u een *mountPath* op met behulp van de Windows Path-Conventie, zoals *":"*.

Maak een bestand `azure-pvc-files.yaml` met de naam en kopieer de volgende YAML. Zorg ervoor dat de *claim* naam overeenkomt met het PVC dat in de laatste stap is gemaakt.

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

Maak de Pod met de opdracht [kubectl apply][kubectl-apply] .

```console
kubectl apply -f azure-pvc-files.yaml
```

U hebt nu een actieve pod met uw Azure Files-share die is gekoppeld in de */mnt/Azure* -map. Deze configuratie kan worden weer gegeven bij het controleren van uw Pod via `kubectl describe pod mypod` . De volgende gecomprimeerde voorbeeld uitvoer toont het volume dat in de container is gekoppeld:

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

De standaard waarde voor *file mode* en *dirMode* is *0777* voor Kubernetes-versie 1.13.0 en hoger. Als het permanente volume dynamisch wordt gemaakt met een opslag klasse, kunnen er koppelings opties worden opgegeven voor het opslag klassen object. In het volgende voor beeld wordt *0777* ingesteld:

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

Zie [Aanbevolen procedures voor opslag en back-ups in AKS][operator-best-practices-storage]voor gekoppelde aanbevolen procedures.

Zie [Dynamic Provisioning](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#dynamic-provision)(Engelstalig) voor para meters voor de opslag klasse.

Meer informatie over Kubernetes permanente volumes met behulp van Azure Files.

> [!div class="nextstepaction"]
> [Kubernetes-invoeg toepassing voor Azure Files][kubernetes-files]

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
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
