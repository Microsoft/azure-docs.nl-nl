---
title: Stuurprogramma'Container Storage Interface (CSI) gebruiken voor Azure Disks op Azure Kubernetes Service (AKS)
description: Meer informatie over het gebruik van Container Storage Interface (CSI)-stuurprogramma's voor Azure-schijven in een Azure Kubernetes Service (AKS)-cluster.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: c3421b767f465a4a705bdeb4882fd261c5cf914f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776227"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>De Stuurprogramma's voor Azure Disk Container Storage Interface (CSI) gebruiken in Azure Kubernetes Service (AKS) (preview)
Het stuurprogramma azure disk Container Storage Interface (CSI) is een CSI-specificatie-compatibel stuurprogramma dat wordt gebruikt door Azure Kubernetes Service (AKS) voor het beheren van de levenscyclus van Azure-schijven. [](https://github.com/container-storage-interface/spec/blob/master/spec.md)

Het CSI is een standaard voor het blootstellen van willekeurige blok- en bestandsopslagsystemen aan workloads in containers op Kubernetes. Door over te nemen en CSI te gebruiken, kan AKS in plug-ins schrijven, implementeren en itereren om nieuwe of bestaande opslagsystemen in Kubernetes weer te geven of te verbeteren zonder dat de belangrijkste Kubernetes-code moet worden bereikt en moet worden gewacht op de releasecycli.

Zie ENABLE CSI drivers for Azure disks and Azure Files on AKS (CSI-stuurprogramma's voor Azure-schijven inschakelen en Azure Files op AKS) voor informatie over het maken van een [AKS-cluster met CSI-stuurprogrammaondersteuning.](csi-storage-drivers.md)

>[!NOTE]
> *In-tree stuurprogramma's* verwijst naar de huidige opslagst stuurprogramma's die deel uitmaken van de belangrijkste Kubernetes-code ten opzichte van de nieuwe CSI-stuurprogramma's, die in plug-ins zijn.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Permanente CSI-volumes gebruiken met Azure-schijven

Een [permanent volume](concepts-storage.md#persistent-volumes) (PV) vertegenwoordigt een stukje opslag dat is ingericht voor gebruik met Kubernetes-pods. Een PV kan worden gebruikt door een of meer pods en kan dynamisch of statisch worden ingericht. In dit artikel wordt beschreven hoe u dynamisch PV's maakt met Azure-schijven voor gebruik door één pod in een AKS-cluster. Zie Handmatig een volume maken en gebruiken met Azure-schijven voor [statische inrichting.](azure-disk-volume.md)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Zie Opslagopties voor toepassingen in [AKS][concepts-storage]voor meer informatie over Kubernetes-volumes.

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Dynamisch Azure-schijf-PV's maken met behulp van de ingebouwde opslagklassen

Een opslagklasse wordt gebruikt om te definiëren hoe een opslageenheid dynamisch wordt gemaakt met een permanent volume. Zie Kubernetes-opslagklassen voor meer informatie over [Kubernetes-opslagklassen.][kubernetes-storage-classes] Wanneer u opslag CSI-stuurprogramma's op AKS gebruikt, zijn er twee extra ingebouwde functies die gebruikmaken van de `StorageClasses` Azure Disk CSI-opslagst stuurprogramma's. De extra CSI-opslagklassen worden gemaakt met het cluster naast de standaardopslagklassen in de structuur.

- `managed-csi`: Maakt gebruik van Azure Standard - SSD lokaal redundante opslag (LRS) om een beheerde schijf te maken.
- `managed-csi-premium`: Maakt gebruik van Azure Premium LRS om een beheerde schijf te maken.

Het claimbeleid in beide opslagklassen zorgt ervoor dat de onderliggende Azure-schijf wordt verwijderd wanneer de respectieve PV wordt verwijderd. De opslagklassen configureren ook de PV's om uit te breiden. U hoeft alleen de permanente volumeclaim (PVC) te bewerken met de nieuwe grootte.

Als u gebruik wilt maken van deze opslagklassen, maakt u een [PVC](concepts-storage.md#persistent-volume-claims) en een respectieve pod die naar deze klassen verwijst en deze gebruikt. Een PVC wordt gebruikt voor het automatisch inrichten van opslag op basis van een opslagklasse. Een PVC kan een van de vooraf gemaakte opslagklassen of een door de gebruiker gedefinieerde opslagklasse gebruiken om een door Azure beheerde schijf te maken voor de gewenste SKU en grootte. Wanneer u een poddefinitie maakt, wordt de PVC opgegeven om de gewenste opslag aan te vragen.

Maak een voorbeeldpod en respectieve PVC met de [opdracht kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Nadat de pod actief is, maakt u een nieuw bestand met de naam `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

U kunt nu controleren of de schijf correct is bevestigd door de volgende opdracht uit te voeren en te controleren of u het `test.txt` bestand in de uitvoer ziet:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Een aangepaste opslagklasse maken

De standaardopslagklassen zijn geschikt voor de meest voorkomende scenario's, maar niet voor alle. In sommige gevallen wilt u mogelijk uw eigen opslagklasse aanpassen met uw eigen parameters. We hebben bijvoorbeeld een scenario waarin u de klasse kunt `volumeBindingMode` wijzigen.

U kunt een klasse `volumeBindingMode: Immediate` gebruiken die garandeert dat deze onmiddellijk plaatsvindt nadat de PVC is gemaakt. In gevallen waarin uw knooppuntgroepen topologie beperkt zijn, bijvoorbeeld door gebruik te maken van beschikbaarheidszones, worden PV's gebonden of ingericht zonder kennis van de planningsvereisten van de pod (in dit geval om zich in een specifieke zone te bevinden).

U kunt dit scenario aanpakken met , waarmee de binding en inrichting van een PV worden vertraagd totdat een pod wordt gemaakt die gebruikmaakt van `volumeBindingMode: WaitForFirstConsumer` de PVC. Op deze manier voldoet de PV aan de eisen en wordt deze ingericht in de beschikbaarheidszone (of andere topologie) die is opgegeven door de planningsbeperkingen van de pod. De standaardopslagklassen gebruiken `volumeBindingMode: WaitForFirstConsumer` klasse.

Maak een bestand met de `sc-azuredisk-csi-waitforfirstconsumer.yaml` naam en plak het volgende manifest.
De opslagklasse is hetzelfde als onze `managed-csi` opslagklasse, maar met een andere `volumeBindingMode` klasse.

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

Maak de opslagklasse met de [opdracht kubectl apply][kubectl-apply] en geef het bestand `sc-azuredisk-csi-waitforfirstconsumer.yaml` op:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Momentopnamen van volumes

Het stuurprogramma Azure Disk CSI ondersteunt het maken [van momentopnamen van permanente volumes.](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) Als onderdeel van deze mogelijkheid kan  het stuurprogramma volledige of [ *incrementele*](../virtual-machines/disks-incremental-snapshots.md) momentopnamen uitvoeren, afhankelijk van de waarde die is ingesteld in de `incremental` parameter (standaard is dit waar).

Zie Parameters voor volumemomentopnameklasse voor meer informatie over [alle parameters.](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass)

### <a name="create-a-volume-snapshot"></a>Een momentopname van een volume maken

Voor een voorbeeld van deze mogelijkheid maakt u een [momentopnameklasse voor het volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) met de opdracht [kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

We gaan nu een volumemomentopname [maken](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) van de PVC die we dynamisch hebben gemaakt aan het begin van [deze zelfstudie](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes), `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Controleer of de momentopname correct is gemaakt:

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

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Een nieuwe PVC maken op basis van een volumemomentopname

U kunt een nieuwe PVC maken op basis van een volumemomentopname. Gebruik de momentopname die u in de vorige stap hebt gemaakt en maak een [nieuwe PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) en een nieuwe [pod om](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) deze te gebruiken.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Laten we ten slotte controleren of het dezelfde PVC is die we eerder hebben gemaakt door de inhoud te controleren.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Zoals verwacht zien we nog steeds ons eerder gemaakte `test.txt` bestand.

## <a name="clone-volumes"></a>Volumes klonen

Een gekloond volume wordt gedefinieerd als een duplicaat van een bestaand Kubernetes-volume. Zie de conceptuele documentatie voor het klonen van volumes voor meer informatie over het klonen [van volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)in Kubernetes.

Het CSI-stuurprogramma voor Azure-schijven ondersteunt het klonen van volumes. Maak ter demonstratie een [gekloond volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) van de [eerder gemaakte en](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) een nieuwe pod om deze te `azuredisk-pvc` [gebruiken.](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

We kunnen nu de inhoud van het gekloonde volume controleren door de volgende opdracht uit te voeren en te bevestigen dat we het gemaakte bestand `test.txt` nog steeds zien.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>De omvang van een permanent volume aanpassen

U kunt in plaats daarvan een groter volume voor een PVC aanvragen. Bewerk het OBJECT PVC en geef een groter formaat op. Deze wijziging activeert de uitbreiding van het onderliggende volume dat de PV back-is.

> [!NOTE]
> Er wordt nooit een nieuwe PV gemaakt om aan de claim te voldoen. In plaats daarvan wordt de omvang van een bestaand volume van het volume aanpassen.

In AKS staat de ingebouwde opslagklasse al uitbreiding toe. Gebruik daarom de PVC die u eerder hebt gemaakt `managed-csi` [met deze opslagklasse](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes). De PVC heeft een permanent volume van 10 Gi aangevraagd. We kunnen dit bevestigen door het volgende uit te gaan:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> Op dit moment ondersteunt het stuurprogramma Azure Disk CSI alleen het formaat van PVC's zonder gekoppelde pods (en het volume niet gekoppeld aan een specifiek knooppunt).

Daarom verwijderen we de pod die we eerder hebben gemaakt:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

We gaan de PVC uitbreiden door het veld uit te `spec.resources.requests.storage` breiden:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Laten we controleren of het volume nu groter is:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> De PVC geeft de nieuwe grootte pas weer als er weer een pod aan is gekoppeld.

Laten we een nieuwe pod maken:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

En bevestig ten slotte de grootte van de PVC en in de pod:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Gedeelde schijf

[Gedeelde Azure-schijven](../virtual-machines/disks-shared.md) is een azure-functie voor beheerde schijven waarmee een Azure-schijf tegelijkertijd kan worden gekoppeld aan agentknooppunten. Door een beheerde schijf aan meerdere agentknooppunten te koppelen, kunt u bijvoorbeeld nieuwe of bestaande geclusterde toepassingen naar Azure implementeren.

> [!IMPORTANT] 
> Op dit moment wordt alleen onbewerkte blokapparaat ( `volumeMode: Block` ) ondersteund door het stuurprogramma Azure Disk CSI. Toepassingen moeten de coördinatie en controle van schrijf-, lees-, vergrendelingen, caches, bevestigingen en fencing op de gedeelde schijf beheren, die wordt blootgesteld als een onbewerkt blokapparaat.

We gaan een bestand maken met de naam door de volgende opdracht te kopiëren die de opslagklasse voor gedeelde schijven en `shared-disk.yaml` PVC bevat:

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

Maak de opslagklasse met de [opdracht kubectl apply][kubectl-apply] en geef het bestand `shared-disk.yaml` op:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

We gaan nu een bestand maken met de naam `deployment-shared.yml` door de volgende opdracht te kopiëren:

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

Maak de implementatie met de [opdracht kubectl apply][kubectl-apply] en geef het bestand `deployment-shared.yml` op:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Ten slotte gaan we het blokapparaat in de pod controleren:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Windows-containers

Het stuurprogramma Azure Disk CSI ondersteunt ook Windows-knooppunten en -containers. Als u Windows-containers wilt gebruiken, volgt u de [zelfstudie Windows-containers om](windows-container-cli.md) een Windows-knooppuntgroep toe te voegen.

Nadat u een Windows-knooppuntgroep hebt, kunt u nu de ingebouwde opslagklassen gebruiken, zoals `managed-csi` . U kunt een voorbeeld van een [stateful windows-set](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) implementeren die tijdstempels opgeslagen in het bestand door de volgende opdracht te implementeren met de `data.txt` opdracht [kubectl apply:][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

U kunt nu de inhoud van het volume valideren door het volgende uit te gaan:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Volgende stappen

- Zie Voor meer informatie over het gebruik van CSI-Azure Files voor Azure Files [CSI-stuurprogramma's.](azure-files-csi.md)
- Zie Best practices voor opslag en back-ups in Azure Kubernetes Service voor meer informatie over [best practices voor Azure Kubernetes Service.][operator-best-practices-storage]


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
