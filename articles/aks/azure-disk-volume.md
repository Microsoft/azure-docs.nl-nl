---
title: Een statisch volume maken voor pods in Azure Kubernetes Service (AKS)
description: Meer informatie over het handmatig maken van een volume met Azure-schijven voor gebruik met een pod in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 617ad75eda766963a91fe3d41b1dbfefae62b41b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776209"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Een volume met Azure-schijven in Azure Kubernetes Service (AKS) handmatig maken en gebruiken

Toepassingen op basis van containers moeten vaak toegang hebben tot gegevens in een extern gegevensvolume en deze persistent maken. Als één pod toegang tot opslag nodig heeft, kunt u Azure-schijven gebruiken om een native volume te presenteren voor toepassingsgebruik. In dit artikel wordt beschreven hoe u handmatig een Azure-schijf maakt en deze koppelt aan een pod in AKS.

> [!NOTE]
> Een Azure-schijf kan slechts aan één pod tegelijk worden bevestigd. Als u een permanent volume wilt delen over meerdere pods, gebruikt u [Azure Files][azure-files-volume].

Zie Opslagopties voor toepassingen in [AKS][concepts-storage]voor meer informatie over Kubernetes-volumes.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

Ook moet Azure CLI versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-azure-disk"></a>Een Azure-schijf maken

Wanneer u een Azure-schijf maakt voor gebruik met AKS, kunt u de schijfresource maken in de **knooppuntresourcegroep.** Op deze manier kan het AKS-cluster toegang krijgen tot de schijfresource en deze beheren. Als u in plaats daarvan de schijf in een afzonderlijke resourcegroep maakt, moet u de beheerde identiteit van de Azure Kubernetes Service (AKS) voor uw cluster de rol verlenen aan de resourcegroep van `Contributor` de schijf.

Voor dit artikel maakt u de schijf in de knooppuntresourcegroep. Haal eerst de naam van de resourcegroep op met de [opdracht az aks show][az-aks-show] en voeg de `--query nodeResourceGroup` queryparameter toe. In het volgende voorbeeld wordt de knooppuntresourcegroep voor de AKS-clusternaam *myAKSCluster* in de naam van de resourcegroep *myResourceGroup:*

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Maak nu een schijf met de [opdracht az disk create.][az-disk-create] Geef de naam van de knooppuntresourcegroep op die u in de vorige opdracht hebt verkregen en geef vervolgens een naam op voor de schijfresource, *zoals myAKSDisk.* In het volgende voorbeeld wordt een *schijf van 20* GiB gemaakt en wordt de id van de schijf uitgevoerd zodra deze is gemaakt. Als u een schijf moet maken voor gebruik met Windows Server-containers, voegt u de `--os-type windows` parameter toe om de schijf correct op te maken.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Azure-schijven worden per SKU gefactureerd voor een specifieke grootte. Deze SKU's variëren van 32GiB voor S4- of P4-schijven tot 32TiB voor S80- of P80-schijven (in preview). De doorvoer en IOPS-prestaties van een beheerde Premium-schijf zijn afhankelijk van zowel de SKU als de instantiegrootte van de knooppunten in het AKS-cluster. Zie [Prijzen en prestaties van Managed Disks][managed-disk-pricing-performance].

De resource-id van de schijf wordt weergegeven zodra de opdracht is voltooid, zoals wordt weergegeven in de volgende voorbeelduitvoer. Deze schijf-id wordt gebruikt om de schijf in de volgende stap te monteren.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Schijf als volume monteren

Als u de Azure-schijf aan uw pod wilt monteren, configureert u het volume in de containerspecificatie. Maak een nieuw bestand met de `azure-disk-pod.yaml` naam met de volgende inhoud. Werk bij met de naam van de schijf die u in de vorige stap hebt gemaakt en met de schijf-id die wordt weergegeven `diskName` in de uitvoer van de opdracht voor het maken van de `diskURI` schijf. Werk desgewenst de `mountPath` bij. Dit is het pad waar de Azure-schijf in de pod is bevestigd. Geef voor Windows Server-containers een *mountPath op met* behulp van de Windows-padconventie, zoals *'D:'.*

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

Gebruik de `kubectl` opdracht om de pod te maken.

```console
kubectl apply -f azure-disk-pod.yaml
```

U hebt nu een pod die wordt uitgevoerd met een Azure-schijf die is bevestigd op `/mnt/azure` . U kunt gebruiken `kubectl describe pod mypod` om te controleren of de schijf is bevestigd. In de volgende verkorte voorbeelduitvoer ziet u het volume dat in de container is bevestigd:

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

Zie Best practices voor opslag en [back-ups in AKS][operator-best-practices-storage]voor de bijbehorende best practices.

Zie de Kubernetes-invoegvoegsel voor Azure Disks voor meer informatie over de interactie tussen AKS-clusters [en Azure-schijven.][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az_resource_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
