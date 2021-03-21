---
title: Een statisch volume maken voor een Peul in azure Kubernetes service (AKS)
description: Meer informatie over het hand matig maken van een volume met Azure-schijven voor gebruik met een pod in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 7d8a038926fc6bf3234b43a82c0259ba633df11e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506647"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Hand matig een volume maken en gebruiken met Azure-schijven in azure Kubernetes service (AKS)

Op containers gebaseerde toepassingen moeten vaak toegang hebben tot gegevens en deze persistent maken in een extern gegevens volume. Als één pod toegang moet hebben tot opslag, kunt u Azure-schijven gebruiken om een systeem eigen volume te presen teren voor het gebruik van toepassingen. In dit artikel wordt beschreven hoe u hand matig een Azure-schijf maakt en deze koppelt aan een pod in AKS.

> [!NOTE]
> Een Azure-schijf kan slechts worden gekoppeld aan één pod tegelijk. Als u een permanent volume op meerdere peulen wilt delen, gebruikt u [Azure files][azure-files-volume].

Zie [opslag opties voor toepassingen in AKS][concepts-storage]voor meer informatie over Kubernetes-volumes.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

Ook moet de Azure CLI-versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-azure-disk"></a>Een Azure-schijf maken

Wanneer u een Azure-schijf maakt voor gebruik met AKS, kunt u de schijf resource in de **knooppunt** resource groep maken. Met deze methode kan het AKS-cluster toegang krijgen tot de schijf bron en deze beheren. Als u in plaats daarvan de schijf in een afzonderlijke resource groep maakt, moet u de beheerde identiteit van de Azure Kubernetes-service (AKS) voor uw cluster de `Contributor` rol geven aan de resource groep van de schijf.

Voor dit artikel maakt u de schijf in de knooppunt resource groep. Haal eerst de naam van de resource groep op met de opdracht [AZ AKS show][az-aks-show] en voeg de `--query nodeResourceGroup` query parameter toe. In het volgende voor beeld wordt de resource groep node opgehaald voor de AKS-cluster naam *myAKSCluster* in de naam van de resource groep *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Maak nu een schijf met behulp van de opdracht [AZ Disk Create][az-disk-create] . Geef de naam op van de resource groep van het knoop punt die u hebt verkregen in de vorige opdracht en vervolgens een naam voor de schijf bron, zoals *myAKSDisk*. In het volgende voor beeld *wordt een GiB*-schijf gemaakt en wordt de id van de schijf uitgevoerd nadat deze is gemaakt. Als u een schijf moet maken voor gebruik met Windows Server-containers, voegt u de `--os-type windows` para meter toe om de schijf correct te Format teren.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Azure-schijven worden in rekening gebracht op basis van de SKU voor een specifieke grootte. Deze Sku's variëren van 32GiB voor S4-of P4-schijven naar 32TiB voor S80-of P80-schijven (in preview-versie). De prestaties van de door Voer en IOPS van een Premium Managed disk zijn afhankelijk van de SKU en de instantie grootte van de knoop punten in het AKS-cluster. Bekijk de [prijzen en prestaties van Managed disks][managed-disk-pricing-performance].

De bron-ID van de schijf wordt weer gegeven zodra de opdracht is voltooid, zoals wordt weer gegeven in de volgende voorbeeld uitvoer. Deze schijf-ID wordt gebruikt om de schijf in de volgende stap te koppelen.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Schijf koppelen als volume

Als u de Azure-schijf wilt koppelen aan uw Pod, configureert u het volume in de container specificatie. Maak een nieuw bestand `azure-disk-pod.yaml` met de naam met de volgende inhoud. Werk `diskName` bij met de naam van de schijf die u in de vorige stap hebt gemaakt en `diskURI` met de schijf-id die wordt weer gegeven in de uitvoer van de opdracht schijf maken. Indien gewenst, werkt u de `mountPath` , die het pad is naar de Azure-schijf, in het pod. Voor Windows Server-containers geeft u een *mountPath* op met behulp van de Windows Path-Conventie, zoals *":"*.

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
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Gebruik de `kubectl` opdracht om de Pod te maken.

```console
kubectl apply -f azure-disk-pod.yaml
```

U hebt nu een actieve pod met een Azure-schijf die is gekoppeld aan `/mnt/azure` . U kunt gebruiken `kubectl describe pod mypod` om te controleren of de schijf is gekoppeld. De volgende gecomprimeerde voorbeeld uitvoer toont het volume dat in de container is gekoppeld:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor opslag en back-ups in AKS][operator-best-practices-storage]voor gekoppelde aanbevolen procedures.

Zie de [Kubernetes-invoeg toepassing voor Azure-schijven][kubernetes-disks]voor meer informatie over AKS-clusters die communiceren met Azure-schijven.

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
