---
title: Dynamisch Azure-schijfvolume maken
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het dynamisch maken van een permanent volume met Azure-schijven in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 066a52024e91610882889bb7fbe6b20efa262b71
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776137"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Dynamisch een permanent volume maken en gebruiken met Azure-schijven in Azure Kubernetes Service (AKS)

Een permanent volume vertegenwoordigt een opslagruimte die is ingericht voor gebruik met Kubernetes-pods. Een permanent volume kan worden gebruikt door een of meer pods en kan dynamisch of statisch worden ingericht. In dit artikel wordt beschreven hoe u dynamisch permanente volumes maakt met Azure-schijven voor gebruik door één pod in een Azure Kubernetes Service -cluster (AKS).

> [!NOTE]
> Een Azure-schijf kan alleen  worden bevestigd met het type *Toegangsmodus ReadWriteOnce,* waardoor deze beschikbaar is voor één knooppunt in AKS. Als u een permanent volume wilt delen op meerdere knooppunten, gebruikt [u Azure Files][azure-files-pvc].

Zie Opslagopties voor toepassingen in [AKS][concepts-storage]voor meer informatie over Kubernetes-volumes.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

Ook moet azure CLI versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="built-in-storage-classes"></a>Ingebouwde opslagklassen

Een opslagklasse wordt gebruikt om te definiëren hoe een opslageenheid dynamisch wordt gemaakt met een permanent volume. Zie Kubernetes-opslagklassen voor meer informatie over [Kubernetes-opslagklassen.][kubernetes-storage-classes]

Elk AKS-cluster bevat vier vooraf gemaakte opslagklassen, twee ervan zijn geconfigureerd voor gebruik met Azure-schijven:

* De *standaardopslagklasse* biedt een standaard SSD Azure-schijf.
    * Standard-opslag wordt geback-by door Standard -SD's en biedt voordelige opslag terwijl betrouwbare prestaties worden geleverd. 
* Met *de beheerde-premium-opslagklasse* wordt een premium Azure-schijf in de hand.
    * Premium-schijven worden ondersteund door hoogwaardige schijven met een lage latentie op basis van SSD. Ideaal voor virtuele machines met een productiewerkbelasting. Als de AKS-knooppunten in uw cluster premium-opslag gebruiken, selecteert u de *klasse managed-premium.*
    
Als u een van de standaardopslagklassen gebruikt, kunt u de volumegrootte niet bijwerken nadat de opslagklasse is gemaakt. Als u de volumegrootte wilt bijwerken nadat een opslagklasse is gemaakt, voegt u de regel toe aan een van de standaardopslagklassen of kunt u uw eigen aangepaste `allowVolumeExpansion: true` opslagklasse maken. Houd er rekening mee dat het niet wordt ondersteund om de grootte van een PVC te verkleinen (om gegevensverlies te voorkomen). U kunt een bestaande opslagklasse bewerken met behulp van de `kubectl edit sc` opdracht . 

Als u bijvoorbeeld een schijf van 4 TiB wilt gebruiken, moet u een opslagklasse maken die definieert omdat schijfopslag niet wordt ondersteund voor schijven van `cachingmode: None` [4 TiB](../virtual-machines/premium-storage-performance.md#disk-caching)en groter.

Zie Opslagopties voor toepassingen in AKS voor meer informatie over opslagklassen en het maken [van uw eigen opslagklasse.][storage-class-concepts]

Gebruik de [opdracht kubectl get sc][kubectl-get] om de vooraf gemaakte opslagklassen te bekijken. In het volgende voorbeeld ziet u de vooraf gemaakt opslagklassen die beschikbaar zijn in een AKS-cluster:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Permanente volumeclaims worden opgegeven in GiB, maar beheerde Azure-schijven worden per SKU gefactureerd voor een specifieke grootte. Deze SKU's variëren van 32GiB voor S4- of P4-schijven tot 32TiB voor S80- of P80-schijven (in preview). De doorvoer en IOPS-prestaties van een beheerde Premium-schijf zijn afhankelijk van zowel de SKU als de instantiegrootte van de knooppunten in het AKS-cluster. Zie Prijzen en prestaties van Managed Disks voor [meer Managed Disks.][managed-disk-pricing-performance]

## <a name="create-a-persistent-volume-claim"></a>Een permanente volumeclaim maken

Er wordt een persistente volumeclaim (PERSISTENT VOLUME Claim) gebruikt om automatisch opslag in terichten op basis van een opslagklasse. In dit geval kan een PVC een van de vooraf gemaakte opslagklassen gebruiken om een standaard of premium beheerde Azure-schijf te maken.

Maak een bestand met de `azure-premium.yaml` naam en kopieer het in het volgende manifest. De claim vraagt een schijf met `azure-managed-disk` de naam *die 5 GB* groot is met *ReadWriteOnce-toegang.* De *beheerde-premium-opslagklasse* wordt opgegeven als de opslagklasse.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Als u een schijf wilt maken die gebruikmaakt van Standard-opslag, gebruikt `storageClassName: default` u in plaats van *managed-premium.*

Maak de permanente volumeclaim met de [opdracht kubectl apply][kubectl-apply] en geef het *bestand azure-premium.yaml* op:

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Het permanente volume gebruiken

Zodra de permanente volumeclaim is gemaakt en de schijf is ingericht, kan er een pod worden gemaakt met toegang tot de schijf. Met het volgende manifest maakt u een eenvoudige NGINX-pod die gebruikmaakt van de permanente volumeclaim met de naam *azure-managed-disk* om de Azure-schijf te mounten op het pad `/mnt/azure` . Geef voor Windows Server-containers een *mountPath op met* behulp van de Windows-padconventie, zoals *'D:'.*

Maak een bestand met de `azure-pvc-disk.yaml` naam en kopieer het in het volgende manifest.

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
        claimName: azure-managed-disk
```

Maak de pod met de [opdracht kubectl apply,][kubectl-apply] zoals wordt weergegeven in het volgende voorbeeld:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

U hebt nu een pod die wordt uitgevoerd met uw Azure-schijf die is bevestigd in de `/mnt/azure` map . Deze configuratie kan worden weergegeven bij het inspecteren van uw pod via , zoals `kubectl describe pod mypod` wordt weergegeven in het volgende verkorte voorbeeld:

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="use-ultra-disks"></a>Gebruik Ultra Disks
Zie Use [Ultra Disks on Azure Kubernetes Service (AKS) als u](use-ultra-disks.md)gebruik wilt maken van ultraschijven.

## <a name="back-up-a-persistent-volume"></a>Een back-up maken van een permanent volume

Als u een back-up wilt maken van de gegevens in uw permanente volume, maakt u een momentopname van de beheerde schijf voor het volume. U kunt deze momentopname vervolgens gebruiken om een herstelde schijf te maken en aan pods te koppelen als een manier om de gegevens te herstellen.

Haal eerst de volumenaam op met de opdracht , bijvoorbeeld voor de PVC met `kubectl get pvc` de *naam azure-managed-disk*:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Deze volumenaam vormt de onderliggende Azure-schijfnaam. Zoek de schijf-id met [az disk list][az-disk-list] en geef de naam van uw VOLUMES-volume op, zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Gebruik de schijf-id om een momentopnameschijf te maken met [az snapshot create.][az-snapshot-create] In het volgende voorbeeld wordt een momentopname met de naam *pvcSnapshot* gemaakt in dezelfde resourcegroep als het AKS-cluster *(MC_myResourceGroup_myAKSCluster_eastus).* Er kunnen machtigingsproblemen zijn als u momentopnamen maakt en schijven herstelt in resourcegroepen waar het AKS-cluster geen toegang toe heeft.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Afhankelijk van de hoeveelheid gegevens op uw schijf kan het enkele minuten duren om de momentopname te maken.

## <a name="restore-and-use-a-snapshot"></a>Een momentopname herstellen en gebruiken

Als u de schijf wilt herstellen en wilt gebruiken met een Kubernetes-pod, gebruikt u de momentopname als bron bij het maken van een schijf [met az disk create.][az-disk-create] Met deze bewerking blijft de oorspronkelijke resource behouden als u vervolgens toegang nodig hebt tot de oorspronkelijke gegevensmomentopname. In het volgende voorbeeld wordt een schijf met de naam *pvcRestored gemaakt* van de momentopname met de naam *pvcSnapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Als u de herstelde schijf met een pod wilt gebruiken, geeft u de id van de schijf op in het manifest. Haal de schijf-id op met [de opdracht az disk show.][az-disk-show] In het volgende voorbeeld wordt de schijf-id voor *pvcRestored opgeslagen die* u in de vorige stap hebt gemaakt:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Maak een podmanifest `azure-restored.yaml` met de naam en geef de schijf-URI op die u in de vorige stap hebt verkregen. In het volgende voorbeeld wordt een eenvoudige NGINX-webserver gemaakt, met de herstelde schijf als een volume op */mnt/azure:*

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
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
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Maak de pod met de [opdracht kubectl apply,][kubectl-apply] zoals wordt weergegeven in het volgende voorbeeld:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

U kunt gebruiken om details van de pod weer te geven, zoals het volgende verkorte voorbeeld `kubectl describe pod mypodrestored` met de volumegegevens:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Volgende stappen

Zie Best practices voor opslag en [back-ups in AKS][operator-best-practices-storage]voor de bijbehorende best practices.

Meer informatie over permanente Kubernetes-volumes met behulp van Azure-schijven.

> [!div class="nextstepaction"]
> [Kubernetes-invoegsel voor Azure-schijven][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
