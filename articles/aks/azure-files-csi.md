---
title: Stuurprogramma'Container Storage Interface (CSI) gebruiken voor Azure Files op Azure Kubernetes Service (AKS)
description: Meer informatie over het gebruik van Container Storage Interface stuurprogramma's (CSI) voor Azure Files in een Azure Kubernetes Service (AKS)-cluster.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: a83d2222862db6bc3e3ff86ba4074114c1a872e5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776155"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Stuurprogramma'Azure Files Container Storage Interface (CSI) gebruiken in Azure Kubernetes Service (AKS) (preview)

Het Azure Files Container Storage Interface (CSI) is [](https://github.com/container-storage-interface/spec/blob/master/spec.md)een CSI-specificatie-compatibel stuurprogramma dat wordt gebruikt door Azure Kubernetes Service (AKS) voor het beheren van de levenscyclus van Azure Files shares.

Het CSI is een standaard voor het blootstellen van willekeurige blok- en bestandsopslagsystemen aan workloads in containers op Kubernetes. Door csi te gebruiken en te gebruiken, kan AKS nu in plug-ins schrijven, implementeren en itereren om nieuwe of bestaande opslagsystemen in Kubernetes weer te geven of te verbeteren zonder dat de belangrijkste Kubernetes-code moet worden bereikt en moet worden gewacht op de releasecycli.

Zie ENABLE CSI drivers for Azure disks and Azure Files on AKS (CSI-stuurprogramma's voor Azure-schijven inschakelen en Azure Files op AKS) voor informatie over het maken van een [AKS-cluster met CSI-stuurprogrammaondersteuning.](csi-storage-drivers.md)

>[!NOTE]
> *In-tree stuurprogramma's* verwijst naar de huidige opslagst stuurprogramma's die deel uitmaken van de belangrijkste Kubernetes-code ten opzichte van de nieuwe CSI-stuurprogramma's, die in plug-ins zijn.

## <a name="use-a-persistent-volume-with-azure-files"></a>Een permanent volume gebruiken met Azure Files

Een [permanent volume (PV)](concepts-storage.md#persistent-volumes) vertegenwoordigt een stukje opslag dat is ingericht voor gebruik met Kubernetes-pods. Een PV kan worden gebruikt door een of meer pods en kan dynamisch of statisch worden ingericht. Als meerdere pods gelijktijdig toegang tot hetzelfde opslagvolume nodig hebben, kunt u Azure Files gebruiken om verbinding te maken met behulp van het [Server Message Block (SMB)-protocol][smb-overview]. In dit artikel wordt beschreven hoe u dynamisch een Azure Files voor gebruik door meerdere pods in een AKS-cluster. Zie Handmatig een volume maken en gebruiken met een Azure Files-share voor [statische inrichting.](azure-files-volume.md)

Zie Opslagopties voor toepassingen in [AKS][concepts-storage]voor meer informatie over Kubernetes-volumes.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Dynamische Azure Files's maken met behulp van de ingebouwde opslagklassen

Een opslagklasse wordt gebruikt om te definiëren hoe een Azure Files wordt gemaakt. Er wordt automatisch een opslagaccount gemaakt in de knooppuntresourcegroep voor gebruik met de opslagklasse voor het opslaan van de Azure Files shares. [][node-resource-group] Kies een van de volgende [Azure-opslag redundantie-SKU's][storage-skus] voor *skuName:*

* **Standard_LRS:** Standaard lokaal redundante opslag
* **Standard_GRS:** standaard geografisch redundante opslag
* **Standard_ZRS:** Standaard zone-redundante opslag
* **Standard_RAGRS:** standaard geografisch redundante opslag met leestoegang
* **Premium_LRS:** Lokaal redundante Premium-opslag

> [!NOTE]
> Azure Files ondersteunt Azure Premium Storage. De minimale Premium-bestands share is 100 GB.

Wanneer u stuurprogramma's voor opslag CSI op AKS gebruikt, zijn er twee extra ingebouwde functies die gebruikmaken van `StorageClasses` de Azure Files CSI-opslagst stuurprogramma's. De extra CSI-opslagklassen worden gemaakt met het cluster naast de standaardopslagklassen in de structuur.

- `azurefile-csi`: gebruikt Azure Standard Storage om een Azure Files maken.
- `azurefile-csi-premium`: Maakt gebruik van Azure Premium Storage om een Azure Files maken.

Het claimbeleid voor beide opslagklassen zorgt ervoor dat de onderliggende Azure Files share wordt verwijderd wanneer de respectieve PV wordt verwijderd. De opslagklassen configureren ook de bestands shares om uit te breiden. U hoeft alleen de permanente volumeclaim (PVC) te bewerken met de nieuwe grootte.

Als u deze opslagklassen wilt gebruiken, maakt u een [PVC](concepts-storage.md#persistent-volume-claims) en een respectieve pod die naar deze klassen verwijst en gebruikt. Een PVC wordt gebruikt om automatisch opslag in terichten op basis van een opslagklasse. Een PVC kan een van de vooraf gemaakte opslagklassen of een door de gebruiker gedefinieerde opslagklasse gebruiken om een Azure Files share te maken voor de gewenste SKU en grootte. Wanneer u een poddefinitie maakt, wordt de PVC opgegeven om de gewenste opslag aan te vragen.

Maak een [voorbeeld van EENEEN POD `outfile` die de huidige datum in een afdrukt](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) met de [opdracht kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Nadat de pod de status Actief heeft, kunt u controleren of de bestands share correct is bevestigd door de volgende opdracht uit te voeren en te controleren of de uitvoer de `outfile` bevat:

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Een aangepaste opslagklasse maken

De standaardopslagklassen zijn geschikt voor de meest voorkomende scenario's, maar niet voor alle. In sommige gevallen wilt u mogelijk uw eigen opslagklasse aanpassen met uw eigen parameters. Gebruik bijvoorbeeld het volgende manifest om de van de `mountOptions` bestands share te configureren.

De standaardwaarde voor *fileMode* en *dirMode* is *0777* voor aan Kubernetes toegevoegde bestands shares. U kunt de verschillende opties voor het monteren van het opslagklasseobject opgeven.

Maak een bestand met de `azure-file-sc.yaml` naam en plak het volgende voorbeeldmanifest:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

Maak de opslagklasse met de [opdracht kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Het Azure Files CSI ondersteunt het maken van [momentopnamen van permanente volumes](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) en de onderliggende bestands shares.

Maak een [momentopnameklasse van het volume](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) met de [opdracht kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Maak een [volumemomentopname](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) van de PVC [die we dynamisch hebben gemaakt aan het begin van deze zelfstudie](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes), `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Controleer of de momentopname correct is gemaakt:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>De omvang van een permanent volume aanpassen

U kunt een groter volume aanvragen voor een PVC. Bewerk het OBJECT PVC en geef een groter formaat op. Deze wijziging activeert de uitbreiding van het onderliggende volume dat de PV-back-is.

> [!NOTE]
> Er wordt nooit een nieuwe PV gemaakt om aan de claim te voldoen. In plaats daarvan wordt de omvang van een bestaand volume geseed.

In AKS biedt de ingebouwde opslagklasse al ondersteuning voor uitbreiding. Gebruik daarom de PVC die u eerder hebt gemaakt `azurefile-csi` [met deze opslagklasse](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes). De PVC heeft een 100Gi-bestands share aangevraagd. We kunnen dit bevestigen door het volgende uit te gaan:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Vouw de PVC uit door het veld uit te `spec.resources.requests.storage` breiden:

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Controleer of zowel de PVC als het bestandssysteem in de pod de nieuwe grootte toont:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>NFS-bestands shares
[Azure Files biedt nu ondersteuning voor het NFS v4.1-protocol](../storage/files/storage-files-how-to-create-nfs-shares.md). NFS 4.1-ondersteuning voor Azure Files biedt u een volledig beheerd NFS-bestandssysteem als een service die is gebouwd op een zeer beschikbaar en uiterst duurzaam gedistribueerd opslagplatform.

 Deze optie is geoptimaliseerd voor workloads voor willekeurige toegang met in-place gegevensupdates en biedt volledige posix-bestandssysteemondersteuning. In deze sectie ziet u hoe u NFS-shares gebruikt met het Azure File CSI-stuurprogramma op een AKS-cluster.

Controleer de beperkingen [en](../storage/files/storage-files-compare-protocols.md#limitations) beschikbaarheid [van regio's](../storage/files/storage-files-compare-protocols.md#regional-availability) tijdens de preview-fase.

### <a name="register-the-allownfsfileshares-preview-feature"></a>De `AllowNfsFileShares` preview-functie registreren

Als u een bestands share wilt maken die gebruik maakt van NFS 4.1, moet u de `AllowNfsFileShares` functievlag voor uw abonnement inschakelen.

Registreer de `AllowNfsFileShares` functievlag met behulp van [de opdracht az feature register,][az-feature-register] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Het duurt enkele minuten voordat de status Geregistreerd *we weergeven.* Controleer de registratiestatus met behulp van [de opdracht az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de *resourceprovider Microsoft.Storage* met behulp van [de opdracht az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>Een opslagaccount voor de NFS-bestands share maken

[Een maken `Premium_LRS` Azure-opslagaccount](../storage/files/storage-how-to-create-file-share.md) met de volgende configuraties ter ondersteuning van NFS-shares:
- soort account: FileStorage
- veilige overdracht vereist (alleen HTTPS-verkeer inschakelen): onwaar
- selecteer het virtuele netwerk van uw agentknooppunten in Firewalls en virtuele netwerken. U kunt dus het opslagaccount maken in de MC_ resourcegroep.

### <a name="create-nfs-file-share-storage-class"></a>Opslagklasse voor NFS-bestands share maken

Sla een `nfs-sc.yaml` bestand op met het manifest onder het bewerken van de respectieve tijdelijke aanduidingen.

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  resourceGroup: EXISTING_RESOURCE_GROUP_NAME  # optional, required only when storage account is not in the same resource group as your agent nodes
  storageAccount: EXISTING_STORAGE_ACCOUNT_NAME
  protocol: nfs
```

Nadat u het bestand hebt bewerkt en opgeslagen, maakt u de opslagklasse met de [opdracht kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>Een implementatie maken met een NFS-bestands share
U kunt een voorbeeld van [een stateful set](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) implementeren die tijdstempels opgeslagen in een bestand door de volgende opdracht te implementeren met de `data.txt` opdracht [kubectl apply:][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

Valideer de inhoud van het volume door het volgende uit te gaan:

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> Houd er rekening mee dat de minimale bestandsgrootte 100 GB is, omdat de NFS-bestands share zich in een Premium-account. Als u een PVC met een kleine opslaggrootte maakt, kan de foutmelding 'Kan bestands share niet maken... grootte (5)....


## <a name="windows-containers"></a>Windows-containers

Het Azure Files CSI ondersteunt ook Windows-knooppunten en -containers. Als u Windows-containers wilt gebruiken, volgt u de [zelfstudie Windows-containers om](windows-container-cli.md) een Windows-knooppuntgroep toe te voegen.

Nadat u een Windows-knooppuntgroep hebt, gebruikt u de ingebouwde opslagklassen zoals `azurefile-csi` of maakt u aangepaste opslagklassen. U kunt een voorbeeld van een [stateful windows-set](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) implementeren die tijdstempels opgeslagen in een bestand door de volgende opdracht te implementeren met de `data.txt` opdracht [kubectl apply:][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

Valideer de inhoud van het volume door het volgende uit te gaan:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Volgende stappen

- Zie Azure-schijven gebruiken met CSI-stuurprogramma's voor meer informatie over het gebruik van [CSI-stuurprogramma's voor Azure-schijven.](azure-disk-csi.md)
- Zie Best practices voor opslag en back-ups [in][operator-best-practices-storage]Azure Kubernetes Service .


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md