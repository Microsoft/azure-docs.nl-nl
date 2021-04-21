---
title: Ondersteuning Ultra Disk inschakelen op Azure Kubernetes Service (AKS)
description: Meer informatie over het inschakelen en configureren Ultra Disks in een AKS Azure Kubernetes Service cluster
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 7dbe0a75ce2079bdec752f7fee0c3e97e3ae2ffa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767345"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Azure Ultra Disks gebruiken op Azure Kubernetes Service (preview)

[Azure Ultra Disks](../virtual-machines/disks-enable-ultra-ssd.md) bieden hoge doorvoer, hoge IOPS en consistente schijfopslag met lage latentie voor uw stateful toepassingen. Een belangrijk voordeel van ultraschijven is de mogelijkheid om de prestaties van de SSD samen met uw werkbelastingen dynamisch te wijzigen zonder dat u de agentknooppunten opnieuw hoeft op te starten. Ultraschijven zijn geschikt voor gegevensintensieve workloads.

## <a name="before-you-begin"></a>Voordat u begint

Deze functie kan alleen worden ingesteld tijdens het maken van het cluster of het maken van een knooppuntgroep.

> [!IMPORTANT]
> Voor Azure Ultra Disks zijn knooppuntpools vereist die zijn geïmplementeerd in beschikbaarheidszones en regio's die ondersteuning bieden voor deze schijven, evenals alleen specifieke VM-serie. Zie het bereik en de beperkingen voor de ga naar Ultra [**disks.**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)

### <a name="register-the-enableultrassd-preview-feature"></a>De `EnableUltraSSD` preview-functie registreren

Als u een AKS-cluster of een knooppuntgroep wilt maken die gebruik kan maken van Ultra Disks, moet u de `EnableUltraSSD` functievlag inschakelen voor uw abonnement.

Registreer de `EnableUltraSSD` functievlag met behulp van [de opdracht az feature register,][az-feature-register] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Het duurt enkele minuten voordat de status Geregistreerd *we weergeven.* U kunt de registratiestatus controleren met behulp van de opdracht [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de resourceprovider *Microsoft.ContainerService* met behulp van [de opdracht az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

Als u een AKS-cluster of een knooppuntgroep wilt maken die gebruik kan maken van Ultra Disks, hebt u de meest recente *CLI-extensie aks-preview* nodig. Installeer de *Azure CLI-extensie aks-preview* met behulp van de [opdracht az extension add][az-extension-add] of installeer beschikbare updates met de opdracht az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Beperkingen
- Zie het bereik en de beperkingen voor de ga naar Ultra [ **Disks**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- Het ondersteunde groottebereik voor ultraschijven ligt tussen 100 en 1500

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Een nieuw cluster maken dat ultraschijven kan gebruiken

Maak een AKS-cluster dat gebruik kan maken van Ultra Disks behulp van de volgende CLI-opdrachten. Gebruik de `--aks-custom-headers` vlag om de functie in te `EnableUltraSSD` stellen.

Een Azure-resourcegroep maken:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Maak het AKS-cluster met ondersteuning voor Ultra Disks.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Als u clusters wilt maken zonder ultraschijfondersteuning, kunt u dit doen door de aangepaste parameter weg te `--aks-custom-headers` laten.

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Ultraschijven inschakelen op een bestaand cluster

U kunt ultraschijven inschakelen op bestaande clusters door een nieuwe knooppuntgroep toe te voegen aan uw cluster die ultraschijven ondersteunt. Configureer een nieuwe knooppuntgroep voor het gebruik van ultraschijven met behulp van de `--aks-custom-headers` vlag .

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Als u nieuwe knooppuntgroepen wilt maken zonder ondersteuning voor ultraschijven, kunt u dit doen door de aangepaste parameter weg te `--aks-custom-headers` laten.

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Ultraschijven dynamisch gebruiken met een opslagklasse

Als u ultraschijven wilt gebruiken in onze implementaties of stateful sets, kunt u een [opslagklasse gebruiken voor dynamische inrichting.](azure-disks-dynamic-pv.md)

### <a name="create-the-storage-class"></a>De opslagklasse maken

Een opslagklasse wordt gebruikt om te definiëren hoe een opslageenheid dynamisch wordt gemaakt met een permanent volume. Zie Kubernetes-opslagklassen voor meer informatie over [Kubernetes-opslagklassen.][kubernetes-storage-classes]

In dit geval maken we een opslagklasse die verwijst naar ultraschijven. Maak een bestand met de `azure-ultra-disk-sc.yaml` naam en kopieer het in het volgende manifest.

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

Maak de opslagklasse met de [opdracht kubectl apply][kubectl-apply] en geef het *bestand azure-ultra-disk-sc.yaml* op:

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Een permanente volumeclaim maken

Een permanente volumeclaim (PERSISTENT) wordt gebruikt om automatisch opslag in terichten op basis van een opslagklasse. In dit geval kan een PVC de eerder gemaakte opslagklasse gebruiken om een ultraschijf te maken.

Maak een bestand met de `azure-ultra-disk-pvc.yaml` naam en kopieer het in het volgende manifest. De claim vraagt een schijf met `ultra-disk` de naam aan die *1000 GB* groot is met *ReadWriteOnce-toegang.* De *ultra-disk-sc-opslagklasse* wordt opgegeven als de opslagklasse.

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

Maak de permanente volumeclaim met de [opdracht kubectl apply][kubectl-apply] en geef het bestand *azure-ultra-disk-pv.yaml* op:

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Het permanente volume gebruiken

Zodra de permanente volumeclaim is gemaakt en de schijf is ingericht, kan er een pod worden gemaakt met toegang tot de schijf. Met het volgende manifest maakt u een eenvoudige NGINX-pod die gebruikmaakt van de permanente volumeclaim met de naam ultra-disk om de *Azure-schijf* te mounten op het pad `/mnt/azure` .

Maak een bestand met de `nginx-ultra.yaml` naam en kopieer het in het volgende manifest.

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

Maak de pod met de [opdracht kubectl apply,][kubectl-apply] zoals wordt weergegeven in het volgende voorbeeld:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

U hebt nu een pod die wordt uitgevoerd met uw Azure-schijf die is bevestigd in de `/mnt/azure` map . Deze configuratie kan worden weergegeven bij het inspecteren van uw pod via , zoals `kubectl describe pod nginx-ultra` wordt weergegeven in het volgende verkorte voorbeeld:

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

- Zie Azure Ultra Disks gebruiken voor meer informatie over [ultraschijven.](../virtual-machines/disks-enable-ultra-ssd.md)
- Zie Best practices voor opslag en back-ups in Azure Kubernetes Service [(AKS)][operator-best-practices-storage] voor meer informatie over best practices voor opslag.

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
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
