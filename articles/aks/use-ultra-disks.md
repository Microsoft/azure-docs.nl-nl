---
title: Ondersteuning van ultra Disk inschakelen op de Azure Kubernetes-service (AKS)
description: Meer informatie over het inschakelen en configureren van ultra schijven in een Azure Kubernetes service-cluster (AKS)
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: c743162ed3f75386287e050443e82069e797ced9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502566"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Gebruik Azure Ultra disks in azure Kubernetes service (preview)

[Azure Ultra disks](../virtual-machines/disks-enable-ultra-ssd.md) biedt hoge door Voer, hoge IOPS en een consistente schijf opslag met lage latentie voor uw stateful toepassingen. Een belang rijk voor deel van ultra schijven is de mogelijkheid om de prestaties van de SSD in combi natie met uw workloads dynamisch te wijzigen zonder dat u de agent knooppunten opnieuw hoeft op te starten. Ultra disks zijn geschikt voor gegevensintensieve workloads.

## <a name="before-you-begin"></a>Voordat u begint

Deze functie kan alleen worden ingesteld tijdens het maken van het cluster of het maken van een knooppunt groep.

> [!IMPORTANT]
> Voor Azure Ultra disks is nodepools geïmplementeerd in beschikbaarheids zones en regio's die ondersteuning bieden voor deze schijven en alleen specifieke VM-reeksen. Bekijk het [**bereik en de beperkingen van ultra disks**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations).

### <a name="register-the-enableultrassd-preview-feature"></a>De `EnableUltraSSD` Preview-functie registreren

Als u een AKS-cluster of een knooppunt groep wilt maken die kan profiteren van ultra schijven, moet u de `EnableUltraSSD` functie vlag inschakelen voor uw abonnement.

Registreer de `EnableUltraSSD` functie vlag met de opdracht [AZ feature REGI ster][az-feature-register] , zoals weer gegeven in het volgende voor beeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Het duurt enkele minuten voordat de status is *geregistreerd*. U kunt de registratiestatus controleren met behulp van de opdracht [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

Als u een AKS-cluster of een knooppunt groep wilt maken die ultra disks kan gebruiken, hebt u de meest recente *AKS-preview cli-* extensie nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] of installeer beschik bare updates met behulp van de opdracht [AZ extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Beperkingen
- Bekijk het [ **bereik en de beperkingen van ultra disks** .](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- Het ondersteunde grootte bereik voor een ultra schijven ligt tussen 100 en 1500

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Een nieuw cluster maken dat ultra schijven kan gebruiken

Maak een AKS-cluster dat kan profiteren van ultra schijven met behulp van de volgende CLI-opdrachten. Gebruik de `--aks-custom-headers` markering om de functie in te stellen `EnableUltraSSD` .

Een Azure-resource groep maken:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Maak het AKS-cluster met ondersteuning voor Ultra disks.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Als u clusters wilt maken zonder ondersteuning voor een ultra schijf, kunt u dit doen door de aangepaste para meter weg te laten `--aks-custom-headers` .

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Ultra disks inschakelen op een bestaand cluster

U kunt Ultra schijven op bestaande clusters inschakelen door een nieuwe knooppunt groep toe te voegen aan uw cluster die ondersteuning biedt voor Ultra disks. Configureer een nieuwe knooppunt groep om ultra disks te gebruiken met behulp van de `--aks-custom-headers` vlag.

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Als u nieuwe knooppunt groepen wilt maken zonder ondersteuning voor Ultra schijven, kunt u dit doen door de aangepaste para meter weg te laten `--aks-custom-headers` .

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Ultra schijven dynamisch gebruiken met een opslag klasse

Voor het gebruik van ultra disk in onze implementaties of stateful sets kunt u een [opslag klasse voor dynamische inrichting](azure-disks-dynamic-pv.md)gebruiken.

### <a name="create-the-storage-class"></a>De opslag klasse maken

Een opslag klasse wordt gebruikt om te definiëren hoe een opslag eenheid dynamisch wordt gemaakt met een permanent volume. Zie [Kubernetes Storage klassen][kubernetes-storage-classes](Engelstalig) voor meer informatie over Kubernetes-opslag klassen.

In dit geval gaan we een opslag klasse maken die verwijst naar ultra disks. Maak een bestand `azure-ultra-disk-sc.yaml` met de naam en kopieer het in het volgende manifest.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

Maak de opslag klasse met de opdracht [kubectl apply][kubectl-apply] en geef uw *Azure-Ultra-Disk-SC. yaml-* bestand op:

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Een permanente volume claim maken

Een permanente volume claim (PVC) wordt gebruikt voor het automatisch inrichten van opslag op basis van een opslag klasse. In dit geval kan een PVC de eerder gemaakte opslag klasse gebruiken om een ultra schijf te maken.

Maak een bestand `azure-ultra-disk-pvc.yaml` met de naam en kopieer het in het volgende manifest. De claim vraagt een schijf met de naam `ultra-disk` die *1000 GB* groot is met *ReadWriteOnce* -toegang. De *Ultra-Disk-SC-* opslag klasse is opgegeven als de opslag klasse.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

Maak de permanente volume claim met de opdracht [kubectl apply][kubectl-apply] en geef uw *Azure-Ultra-Disk-PVC. yaml-* bestand op:

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Het permanente volume gebruiken

Als de permanente volume claim is gemaakt en de schijf is ingericht, kan een pod worden gemaakt met toegang tot de schijf. In het volgende manifest wordt een eenvoudige NGINX-pod gemaakt die gebruikmaakt van de permanente volume claim met de naam *Ultra-Disk* om de Azure-schijf te koppelen aan het pad `/mnt/azure` .

Maak een bestand `nginx-ultra.yaml` met de naam en kopieer het in het volgende manifest.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
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
        claimName: ultra-disk
```

Maak de Pod met de opdracht [kubectl apply][kubectl-apply] , zoals wordt weer gegeven in het volgende voor beeld:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

U hebt nu een actieve pod met uw Azure-schijf die is gekoppeld in de `/mnt/azure` Directory. Deze configuratie kan worden weer gegeven bij het controleren van uw Pod via `kubectl describe pod nginx-ultra` , zoals wordt weer gegeven in het volgende versmald-voor beeld:

```console
$ kubectl describe pod nginx-ultra

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


## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Ultra Disk gebruiken](../virtual-machines/disks-enable-ultra-ssd.md)voor meer informatie over Ultra disks.
- Zie [Aanbevolen procedures voor opslag en back-ups in azure Kubernetes service (AKS)][operator-best-practices-storage] voor meer informatie over aanbevolen procedures voor opslag.

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
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
