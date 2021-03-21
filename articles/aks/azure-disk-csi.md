---
title: Gebruik de container Storage interface (CSI)-Stuur Programma's voor Azure-schijven op de Azure Kubernetes-service (AKS)
description: Meer informatie over het gebruik van de container Storage interface (CSI)-Stuur Programma's voor Azure-schijven in een Azure Kubernetes service-cluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 2b4079b6d4eb39b65a7a60cd4d149c7748ab39ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178878"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Gebruik de Azure Disk container Storage interface (CSI)-Stuur Programma's in azure Kubernetes service (AKS) (preview)
Het stuur programma voor de Azure Disk container Storage interface (CSI) is een stuur programma voor [CSI-specificatie](https://github.com/container-storage-interface/spec/blob/master/spec.md)dat door Azure Kubernetes service (AKS) wordt gebruikt voor het beheren van de levens cyclus van Azure-schijven.

De CSI is een standaard voor het beschikbaar maken van wille keurige blok-en opslag systemen in containers op Kubernetes. Door met behulp van CSI te zorgen en te gebruiken, kan AKS invoeg toepassingen schrijven, implementeren en laten lopen om nieuwe of bestaande opslag systemen in Kubernetes beschikbaar te maken, zonder dat u de kern Kubernetes code hoeft aan te raken en te wachten op de release cycli.

Als u een AKS-cluster wilt maken met de ondersteuning van CSI-Stuur Programma's, raadpleegt u de [Stuur Programma's voor de Azure-schijven inschakelen en Azure files op AKS](csi-storage-drivers.md).

>[!NOTE]
> *In-structuur Stuur Programma's* verwijzen naar de huidige opslag Stuur Programma's die deel uitmaken van de kern Kubernetes-code versus de nieuwe CSI-Stuur Programma's, die invoeg toepassingen zijn.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Vaste volumes met CSI gebruiken met Azure-schijven

Een [permanent volume](concepts-storage.md#persistent-volumes) (HW) vertegenwoordigt een opslag ruimte die is ingericht voor gebruik met Kubernetes peul. Een HW kan worden gebruikt door een of meer peulen en kan dynamisch of statisch worden ingericht. In dit artikel wordt beschreven hoe u dynamisch PVs maakt met Azure-schijven voor gebruik door één pod in een AKS-cluster. Zie voor statische inrichting [hand matig een volume maken en gebruiken met Azure-schijven](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Zie [opslag opties voor toepassingen in AKS][concepts-storage]voor meer informatie over Kubernetes-volumes.

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Dynamisch een Azure-schijf PVs maken met behulp van de ingebouwde opslag klassen

Een opslag klasse wordt gebruikt om te definiëren hoe een opslag eenheid dynamisch wordt gemaakt met een permanent volume. Zie [Kubernetes Storage klassen][kubernetes-storage-classes](Engelstalig) voor meer informatie over Kubernetes-opslag klassen. Wanneer u Storage CSI-Stuur Programma's gebruikt op AKS, zijn er twee extra ingebouwde `StorageClasses` die gebruikmaken van de Azure schijf CSI-opslag Stuur Programma's. De extra CSI-opslag klassen worden samen met het cluster gemaakt naast de standaard opslag klassen in de structuur.

- `managed-csi`: Maakt gebruik van Azure Standard-SSD lokaal redundante opslag (LRS) voor het maken van een beheerde schijf.
- `managed-csi-premium`: Maakt gebruik van Azure Premium LRS voor het maken van een beheerde schijf.

Het beleid voor het opnieuw claimen in beide opslag klassen zorgt ervoor dat de onderliggende Azure-schijf wordt verwijderd wanneer de respectieve hw wordt verwijderd. De opslag klassen configureren ook de PVs die kan worden uitgebreid. U hoeft alleen de permanente volume claim (PVC) te bewerken met de nieuwe grootte.

Als u gebruik wilt maken van deze opslag klassen, maakt u een [PVC](concepts-storage.md#persistent-volume-claims) en de respectieve pod die deze gebruiken. Er wordt een PVC gebruikt voor het automatisch inrichten van opslag op basis van een opslag klasse. Een PVC kan een van de vooraf gemaakte opslag klassen of een door de gebruiker gedefinieerde opslag klasse gebruiken om een door Azure beheerde schijf te maken voor de gewenste SKU en grootte. Wanneer u een pod-definitie maakt, wordt het PVC opgegeven om de gewenste opslag aan te vragen.

Maak een voor beeld van een pod en een respectievelijke PVC met de opdracht [kubectl apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Nadat de Pod is uitgevoerd, maakt u een nieuw bestand met de naam `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

U kunt nu controleren of de schijf correct is gekoppeld door de volgende opdracht uit te voeren en te controleren of het `test.txt` bestand in de uitvoer wordt weer geven:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Een aangepaste opslag klasse maken

De standaard-opslag klassen zijn de meest voorkomende scenario's, maar niet alle. In sommige gevallen wilt u misschien uw eigen opslag klasse aanpassen met uw eigen para meters. We hebben bijvoorbeeld een scenario waarin u de klasse wellicht wilt wijzigen `volumeBindingMode` .

De standaard-opslag klassen gebruiken een `volumeBindingMode: Immediate` klasse die garandeert dat deze onmiddellijk plaatsvindt zodra het PVC is gemaakt. Als uw knooppunt groepen zijn voorzien van een topologie, bijvoorbeeld met behulp van beschikbaarheids zones, wordt PVs gebonden of ingericht zonder kennis van de plannings vereisten van de Pod (in dit geval voor een specifieke zone).

Als u dit scenario wilt aanpakken, kunt u gebruiken `volumeBindingMode: WaitForFirstConsumer` , dat de binding en inrichting van een HW vertraagt tot een pod die gebruikmaakt van het PVC wordt gemaakt. Op deze manier wordt de HW conform en ingericht in de beschikbaarheids zone (of een andere topologie) die is opgegeven door de plannings beperkingen van de pod.

Maak een bestand `sc-azuredisk-csi-waitforfirstconsumer.yaml` met de naam en plak het volgende manifest.
De opslag klasse is hetzelfde als onze `managed-csi` opslag klasse, maar met een andere `volumeBindingMode` klasse.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Maak de opslag klasse met de opdracht [kubectl apply][kubectl-apply] en geef uw `sc-azuredisk-csi-waitforfirstconsumer.yaml` bestand op:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Volume momentopnamen

Het Azure Disk CSI-stuur programma ondersteunt het maken [van moment opnamen van permanente volumes](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). Als onderdeel van deze mogelijkheid kan het stuur programma *volledige* of [ *incrementele* moment opnamen](../virtual-machines/disks-incremental-snapshots.md) uitvoeren, afhankelijk van de waarde die in de `incremental` para meter is ingesteld (de standaard instelling is True).

Zie [para meters voor volume snap shot-klasse](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass)voor meer informatie over alle para meters.

### <a name="create-a-volume-snapshot"></a>Een moment opname van een volume maken

Voor een voor beeld van deze mogelijkheid maakt u een [volume snap shot-klasse](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) met de opdracht [kubectl apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Nu gaan we een [moment opname](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) maken van het PVC-volume dat u [dynamisch aan het begin van deze zelf studie hebt gemaakt](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Controleer of de moment opname correct is gemaakt:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Een nieuw PVC maken op basis van een moment opname van een volume

U kunt een nieuw PVC maken op basis van een moment opname van een volume. Gebruik de moment opname die u in de vorige stap hebt gemaakt, en maak een [Nieuw PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) en een [nieuwe pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) om deze te gebruiken.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Ten slotte moet u controleren of het hetzelfde PVC is gemaakt voor door de inhoud te controleren.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Zoals verwacht, kunnen we nog steeds het eerder gemaakte `test.txt` bestand zien.

## <a name="clone-volumes"></a>Volumes klonen

Een gekloond volume wordt gedefinieerd als een duplicaat van een bestaand Kubernetes-volume. Voor meer informatie over het klonen van volumes in Kubernetes raadpleegt u de conceptuele documentatie voor het [klonen](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)van volumes.

Het CSI-stuur programma voor Azure-schijven ondersteunt het klonen van volumes. Maak een [gekloond volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) van het [eerder gemaakte](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `azuredisk-pvc` en [een nieuwe pod om dit te verbruiken](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)om te demonstreren.


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

We kunnen nu de inhoud van het gekloonde volume controleren door de volgende opdracht uit te voeren en te bevestigen dat het gemaakte bestand nog steeds wordt weer geven `test.txt` .

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Het formaat van een permanent volume wijzigen

U kunt in plaats daarvan een groter volume aanvragen voor een PVC. Bewerk het PVC-object en geef een grotere grootte op. Met deze wijziging wordt de uitbrei ding van het onderliggende volume dat de HW maakt, geactiveerd.

> [!NOTE]
> Er wordt nooit een nieuwe HW gemaakt om te voldoen aan de claim. In plaats daarvan wordt het formaat van een bestaand volume gewijzigd.

In AKS is de ingebouwde `managed-csi` opslag klasse al in staat om uit te breiden, dus gebruik het [PVC dat eerder is gemaakt met deze opslag klasse](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes). Het PVC heeft een permanent 10-GI-volume aangevraagd. We kunnen controleren dat door de volgende handelingen uit te voeren:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> Het Azure Disk CSI-stuur programma ondersteunt momenteel alleen het wijzigen van de grootte van Pvc's zonder dat er een Peul is gekoppeld (en het volume niet is gekoppeld aan een specifiek knoop punt).

Zo kunt u de pod die u eerder hebt gemaakt, verwijderen:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

We gaan het PVC uitbreiden door het veld te verg Roten `spec.resources.requests.storage` :

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

We bevestigen dat het volume nu groter is:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> Het PVC weerspiegelt niet de nieuwe grootte totdat hieraan een Pod is gekoppeld.

We gaan een nieuwe pod maken:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

En ten slotte de grootte van het PVC en binnen de pod bevestigen:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Gedeelde schijf

[Gedeelde Azure-schijven](../virtual-machines/disks-shared.md) is een Azure Managed disks-functie waarmee u tegelijkertijd een Azure-schijf kunt koppelen aan agent knooppunten. Als u een beheerde schijf aan meerdere agent knooppunten koppelt, kunt u bijvoorbeeld nieuwe, geclusterde toepassingen implementeren of migreren naar Azure.

> [!IMPORTANT] 
> Op dit moment wordt alleen het onbewerkte blok apparaat ( `volumeMode: Block` ) ondersteund door het Azure Disk CSI-stuur programma. Toepassingen moeten de coördinatie en controle van schrijf bewerkingen, lees-, vergrendelings-, cache-, koppelings-en omheinings gegevens op de gedeelde schijf beheren, die wordt weer gegeven als een onbewerkt blok apparaat.

We gaan een bestand maken met de naam `shared-disk.yaml` door de volgende opdracht te kopiëren die de gedeelde schijf opslag klasse en PVC bevat:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Maak de opslag klasse met de opdracht [kubectl apply][kubectl-apply] en geef uw `shared-disk.yaml` bestand op:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

We gaan nu een bestand maken `deployment-shared.yml` met de naam door de volgende opdracht te kopiëren:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Maak de implementatie met de opdracht [kubectl apply][kubectl-apply] en geef uw `deployment-shared.yml` bestand op:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Ten slotte gaan we het blok apparaat in de pod controleren:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Windows-containers

Het Azure Disk CSI-stuur programma biedt ook ondersteuning voor Windows-knoop punten en-containers. Als u Windows-containers wilt gebruiken, volgt u de [zelf studie over Windows-containers](windows-container-cli.md) om een Windows-knooppunt groep toe te voegen.

Nadat u een Windows-knooppunt groep hebt, kunt u nu de ingebouwde opslag klassen gebruiken, zoals `managed-csi` . U kunt een voor beeld [van een op Windows gebaseerde stateful set](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) implementeren waarmee tijds tempels worden opgeslagen in het bestand `data.txt` door de volgende opdracht te implementeren met de opdracht [kubectl apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

U kunt nu de inhoud van het volume valideren door het volgende uit te voeren:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure files gebruiken met CSI-Stuur Programma's](azure-files-csi.md)voor meer informatie over het gebruik van CSI-Stuur programma's voor Azure files.
- Zie [Aanbevolen procedures voor opslag en back-ups in azure Kubernetes service][operator-best-practices-storage]voor meer informatie over aanbevolen procedures voor opslag.


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