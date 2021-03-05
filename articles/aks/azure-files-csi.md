---
title: Gebruik de Stuur Programma's van de container Storage interface (CSI) voor Azure Files op de Azure Kubernetes-service (AKS)
description: Meer informatie over het gebruik van de Stuur Programma's voor de container Storage interface (CSI) voor Azure Files in een AKS-cluster (Azure Kubernetes service).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 93f7f7a3c59beca362145ac16f7cf727df773f81
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174058"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Azure Files-Stuur Programma's van de container Storage interface (CSI) gebruiken in azure Kubernetes service (AKS) (preview)

Het stuur programma voor de Azure Files-container opslag interface (CSI) is een stuur programma voor [CSI-specificatie](https://github.com/container-storage-interface/spec/blob/master/spec.md)dat door Azure Kubernetes service (AKS) wordt gebruikt voor het beheren van de levens cyclus van Azure files-shares.

De CSI is een standaard voor het beschikbaar maken van wille keurige blok-en opslag systemen in containers op Kubernetes. Door CSI te maken en te gebruiken, kan AKS nu invoeg toepassingen schrijven, implementeren en herhalen om nieuwe of bestaande opslag systemen in Kubernetes beschikbaar te stellen zonder dat u de kern Kubernetes code hoeft aan te raken en te wachten op de release cycli.

Als u een AKS-cluster wilt maken met de ondersteuning van CSI-Stuur Programma's, raadpleegt u de [Stuur Programma's voor de Azure-schijven inschakelen en Azure files op AKS](csi-storage-drivers.md).

>[!NOTE]
> *In-structuur Stuur Programma's* verwijzen naar de huidige opslag Stuur Programma's die deel uitmaken van de kern Kubernetes-code versus de nieuwe CSI-Stuur Programma's, die invoeg toepassingen zijn.

## <a name="use-a-persistent-volume-with-azure-files"></a>Een permanent volume gebruiken met Azure Files

Een [permanent volume (HW)](concepts-storage.md#persistent-volumes) vertegenwoordigt een opslag ruimte die is ingericht voor gebruik met Kubernetes peul. Een HW kan worden gebruikt door een of meer peulen en kan dynamisch of statisch worden ingericht. Als meerdere peulen gelijktijdig gelijktijdige toegang tot hetzelfde opslag volume nodig hebben, kunt u Azure Files gebruiken om verbinding te maken met behulp van het [SMB-protocol (Server Message Block)][smb-overview]. In dit artikel wordt beschreven hoe u een Azure Files share dynamisch kunt maken voor gebruik door meerdere peulen in een AKS-cluster. Zie voor statische inrichting [hand matig een volume maken en gebruiken met een Azure Files share](azure-files-volume.md).

Zie [opslag opties voor toepassingen in AKS][concepts-storage]voor meer informatie over Kubernetes-volumes.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Azure Files PVs dynamisch maken met behulp van de ingebouwde opslag klassen

Een opslag klasse wordt gebruikt om te bepalen hoe een Azure Files share wordt gemaakt. Er wordt in de resource groep van het [knoop punt][node-resource-group] automatisch een opslag account gemaakt voor gebruik met de opslag klasse om de Azure files-shares op te slaan. Kies een van de volgende [Azure Storage redundantie sku's][storage-skus] voor *skuName*:

* **Standard_LRS**: lokaal redundante opslag
* **Standard_GRS**: standaard geografisch redundante opslag
* **Standard_ZRS**: standaard zone-redundante opslag
* **Standard_RAGRS**: standaard geografisch redundante opslag met lees toegang
* **Premium_LRS**: Premium lokaal redundante opslag

> [!NOTE]
> Azure Files ondersteunt Azure Premium Storage. De minimale Premium-bestands share is 100 GB.

Wanneer u Storage CSI-Stuur Programma's gebruikt op AKS, zijn er twee extra ingebouwde `StorageClasses` die gebruikmaken van de Azure files CSI-opslag Stuur Programma's. De extra CSI-opslag klassen worden samen met het cluster gemaakt naast de standaard opslag klassen in de structuur.

- `azurefile-csi`: Maakt gebruik van Azure Standard-opslag om een Azure Files share te maken.
- `azurefile-csi-premium`: Maakt gebruik van Azure Premium Storage voor het maken van een Azure Files share.

Het beleid voor het opnieuw claimen van beide opslag klassen zorgt ervoor dat de onderliggende Azure Files share wordt verwijderd wanneer de respectieve hw wordt verwijderd. De opslag klassen configureren ook de bestands shares die kunnen worden uitgebreid, u hoeft alleen de permanente volume claim (PVC) te bewerken met de nieuwe grootte.

Als u deze opslag klassen wilt gebruiken, maakt u een [PVC](concepts-storage.md#persistent-volume-claims) en de bijbehorende pod die verwijzingen naar en gebruikt. Er wordt een PVC gebruikt voor het automatisch inrichten van opslag op basis van een opslag klasse. Een PVC kan een van de vooraf gemaakte opslag klassen of een door de gebruiker gedefinieerde opslag klasse gebruiken om een Azure Files share te maken voor de gewenste SKU en grootte. Wanneer u een pod-definitie maakt, wordt het PVC opgegeven om de gewenste opslag aan te vragen.

Maak een [voor beeld van een PVC en pod die de huidige datum `outfile` afdrukt in een](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) met de opdracht [kubectl apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Nadat de pod de status actief heeft, kunt u controleren of de bestands share correct is gekoppeld door de volgende opdracht uit te voeren en de uitvoer te controleren `outfile` :

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Een aangepaste opslag klasse maken

De standaard-opslag klassen zijn de meest voorkomende scenario's, maar niet alle. In sommige gevallen wilt u misschien uw eigen opslag klasse aanpassen met uw eigen para meters. Gebruik bijvoorbeeld het volgende manifest om de `mountOptions` Bestands share te configureren.

De standaard waarde voor *file mode* en *dirMode* is *0777* voor gekoppelde bestands shares van Kubernetes. U kunt de verschillende koppelings opties voor het opslag klassen object opgeven.

Maak een bestand `azure-file-sc.yaml` met de naam en plak het volgende manifest:

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

Maak de opslag klasse met de opdracht [kubectl apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Het Azure Files CSI-stuur programma ondersteunt het maken [van moment opnamen van permanente volumes](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) en de onderliggende bestands shares.

Maak een [volume snap shot-klasse](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) met de opdracht [kubectl apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Maak een [moment opname](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) van een volume van de PVC [die dynamisch aan het begin van deze zelf studie is gemaakt](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Controleer of de moment opname correct is gemaakt:

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

## <a name="resize-a-persistent-volume"></a>Het formaat van een permanent volume wijzigen

U kunt een groter volume aanvragen voor een PVC. Bewerk het PVC-object en geef een grotere grootte op. Met deze wijziging wordt de uitbrei ding van het onderliggende volume dat de HW maakt, geactiveerd.

> [!NOTE]
> Er wordt nooit een nieuwe HW gemaakt om te voldoen aan de claim. In plaats daarvan wordt het formaat van een bestaand volume gewijzigd.

In AKS biedt de ingebouwde `azurefile-csi` opslag klasse al ondersteuning voor uitbrei ding, dus gebruik het [PVC dat eerder is gemaakt met deze opslag klasse](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes). Het PVC heeft een 100Gi-bestands share aangevraagd. We kunnen controleren dat door de volgende handelingen uit te voeren:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Vouw het PVC uit door het veld te verg Roten `spec.resources.requests.storage` :

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Controleer of het PVC en het bestands systeem binnen de pod de nieuwe grootte tonen:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>NFS-bestands shares
[Azure files ondersteunt nu het protocol NFS v 4.1](../storage/files/storage-files-how-to-create-nfs-shares.md). NFS 4,1-ondersteuning voor Azure Files biedt u een volledig beheerd NFS-bestands systeem als een service die is gebouwd op een Maxi maal beschikbaar en zeer duurzaam gedistribueerd robuust opslag platform.

 Deze optie is geoptimaliseerd voor wille keurige toegangs werkbelastingen met in-place gegevens updates en biedt volledige ondersteuning van het POSIX-bestands systeem. In deze sectie wordt beschreven hoe u NFS-shares gebruikt met het Azure file CSI-stuur programma in een AKS-cluster.

Controleer de [beperkingen](../storage/files/storage-files-compare-protocols.md#limitations) en [Beschik baarheid van regio's](../storage/files/storage-files-compare-protocols.md#regional-availability) tijdens de preview-fase.

### <a name="register-the-allownfsfileshares-preview-feature"></a>De `AllowNfsFileShares` Preview-functie registreren

Als u een bestands share wilt maken die gebruikmaakt van NFS 4,1, moet u de `AllowNfsFileShares` functie vlag inschakelen voor uw abonnement.

Registreer de `AllowNfsFileShares` functie vlag met behulp van de opdracht [AZ feature REGI ster][az-feature-register] , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Het duurt enkele minuten voordat de status is *geregistreerd*. Controleer de registratie status met behulp van de opdracht [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider *micro soft. Storage* met behulp van de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>Een opslag account maken voor de NFS-bestands share

[Maak een `Premium_LRS` Azure Storage-account](../storage/files/storage-how-to-create-file-share.md) met de volgende configuraties ter ondersteuning van NFS-shares:
- soort account: FileStorage
- beveiligde overdracht vereist (alleen HTTPS-verkeer inschakelen): onwaar
- Selecteer het virtuele netwerk van uw agent knooppunten in firewalls en virtuele netwerken. u kunt het beste daarom het opslag account maken in de resource groep MC_.

### <a name="create-nfs-file-share-storage-class"></a>Opslag klasse voor NFS-bestands share maken

Sla een `nfs-sc.yaml` bestand met het onderstaande manifest op om de betreffende tijdelijke aanduidingen te bewerken.

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

Nadat u het bestand hebt bewerkt en opgeslagen, maakt u de opslag klasse met de opdracht [kubectl apply][kubectl-apply] :

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>Een implementatie met een bestands share met NFS-back-up maken
U kunt een voor beeld [stateful set](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) implementeren waarmee tijds tempels worden opgeslagen in een bestand `data.txt` door de volgende opdracht te implementeren met de opdracht [kubectl apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

De inhoud van het volume valideren door het volgende uit te voeren:

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> Houd er rekening mee dat omdat de NFS-bestands share zich in Premium-account bevindt, de minimale grootte van de bestands share 100 GB is. Als u een PVC met een kleine opslag grootte maakt, kan er een fout optreden ' kan bestands share niet maken... grootte (5)....


## <a name="windows-containers"></a>Windows-containers

Het Azure Files CSI-stuur programma biedt ook ondersteuning voor Windows-knoop punten en-containers. Als u Windows-containers wilt gebruiken, volgt u de [zelf studie over Windows-containers](windows-container-cli.md) om een Windows-knooppunt groep toe te voegen.

Nadat u een Windows-knooppunt groep hebt, gebruikt u de ingebouwde opslag klassen `azurefile-csi` en maakt u er aangepaste. U kunt een voor beeld [van een op Windows gebaseerde stateful set](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) implementeren waarmee tijds tempels worden opgeslagen in een bestand `data.txt` door de volgende opdracht te implementeren met de opdracht [kubectl apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

De inhoud van het volume valideren door het volgende uit te voeren:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Volgende stappen

- Zie [use Azure disks with CSI drivers](azure-disk-csi.md)voor meer informatie over het gebruik van CSI-Stuur Programma's voor Azure-schijven.
- Zie [Aanbevolen procedures voor opslag en back-ups in azure Kubernetes service][operator-best-practices-storage]voor meer informatie over aanbevolen procedures voor opslag.


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md